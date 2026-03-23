## Resume statement - -Built an LLM/RAG agent workflow that decomposes developer/support questions into sub-queries, retrieves authoritative sources, and produces structured outputs with evaluation rubrics and citation validation. AWS, Co-pilot, Python, Vector Store. 

We built a system that takes a developer/support question, retrieves relevant information from internal documents using vector search, and uses an LLM to generate a grounded, structured answer.

                 ┌──────────────────────┐
                 │     User Query       │
                 └─────────┬────────────┘
                           │
                           ▼
              ┌─────────────────────────┐
              │   Query Parsing (LLM)   │
              │ intent + entities       │
              └─────────┬──────────────┘
                        │
                        ▼
          ┌──────────────────────────────┐
          │  Query Decomposition (LLM)   │
          │ generate sub-queries         │
          └─────────┬────────────────────┘
                    │
                    ▼
          ┌──────────────────────────────┐
          │     Retrieval Layer          │
          │ (Embedding + Vector Search)  │
          └─────────┬────────────────────┘
                    │
                    ▼
          ┌──────────────────────────────┐
          │ Context Builder              │
          │ rank + dedupe + trim tokens  │
          └─────────┬────────────────────┘
                    │
                    ▼
          ┌──────────────────────────────┐
          │   Answer Generation (LLM)    │
          │ grounded response            │
          └─────────┬────────────────────┘
                    │
                    ▼
          ┌──────────────────────────────┐
          │   Validation Layer           │
          │ citation + rubric checks     │
          └─────────┬────────────────────┘
                    │
                    ▼
          ┌──────────────────────────────┐
          │   Structured Response        │
          │ JSON (summary, evidence...)  │
          └──────────────────────────────┘

## VERY IMPORATNT THIS IS IN PREVIEW -- NOT IN PRODUCTION
## AWAITING SECURITIY REVIEW

## TIME: 1 MONTH OF DESIGN + IMPLMENTATION + TEST DEPLOYMENT
## 1 PERSON (SIDE PROJECT)
User → 
    API → 
        Question → LLM → Parsed Query
        {intent: debugging/how-to/root-cause,
        entities: service name, customer, time
        keywords}
            ->  Multiple Sub-Queries
                {
                    "deployment logs customer X yesterday",
                    "recent config changes model, deployment",
                    "known deployment failure causes",
                     "runbook deployment failure steps",
                }
            { Retrieval layer } -> Vector DB / Search → Documents
                {Top-K chunks per sub-query}
                -> Retrieved Docs → Ranked + Merged Context

## Q: Why multiple steps?
Queries can be complicated hence, to avoid hallucination, we broke it in multiple steps.

## Psuedo code
def answer_support_question(user_query: str) -> dict:
    # Step 1: parse the question
    parsed_query = parse_query(user_query)

    # Step 2: break into multiple search queries
    sub_queries = decompose_query(parsed_query)

    # Step 3: retrieve evidence
    retrieved_chunks = []
    for q in sub_queries:
        chunks = retrieve_documents(q, top_k=5)
        retrieved_chunks.extend(chunks)

    # Step 4: clean and rank evidence
    ranked_chunks = rank_and_deduplicate(retrieved_chunks, user_query)
    final_context = build_context_window(ranked_chunks, max_tokens=4000)

    # Step 5: ask LLM to generate grounded answer
    llm_response = generate_answer(
        user_query=user_query,
        parsed_query=parsed_query,
        context_chunks=final_context
    )

    # Step 6: validate answer
    validation_result = validate_answer(
        answer=llm_response,
        context_chunks=final_context
    )

    # Step 7: retry or downgrade confidence if validation fails
    if not validation_result["is_grounded"]:
        llm_response = regenerate_with_stricter_prompt(
            user_query=user_query,
            parsed_query=parsed_query,
            context_chunks=final_context
        )

    # Step 8: return structured response
    return format_final_response(llm_response, validation_result)

 ## parse_query

 def parse_query(user_query: str) -> dict:
    prompt = f"""
    Extract structured fields from the user query.

    Query:
    {user_query}

    Return JSON with:
    - intent
    - service
    - customer
    - time_range
    - question_type
    """

    response = llm_generate(
        model="gpt-4.1", // Security approved
        system_message="You extract structured fields from support queries.",
        user_message=prompt,
        temperature=0, // we do not want creativity, hence zero
        response_format="json" // AI readable hence json
    )

    return parse_json(response)

## Decompose query
def decompose_query(parsed_query: dict) -> list[str]:
    prompt = f"""
    Generate multiple focused search queries.

    Parsed Query:
    {parsed_query}

    Generate 3-5 search queries covering:
    - logs
    - configs
    - known issues
    - runbooks
    """

    response = llm_generate(
        model=""gpt-4.1",
        system_message="Generate search queries for retrieval.",
        user_message=prompt,
        temperature=0.2, // 0.2 or zero again becaue we do not want creativity
        response_format="json" // machine readable
    )

    return parse_json(response)["queries"]

## retrieve_documents
def retrieve_documents(query: str, top_k: int = 5) -> list[dict]:
    # Step 1: preprocess query
    cleaned_query = normalize_query(query)

    # Step 2: generate embedding
    query_embedding = embed(
        text=cleaned_query,
        model="text-embedding-3-large",
        normalize=True, # cosine
    )

    # Step 3: search vector DB
    results = vector_db.search(
        vector=query_embedding,
        top_k=top_k,
        metric="cosine",                 # similarity function
        filter={                         # optional metadata filtering
            "doc_type": ["runbook", "incident", "kb"],
            "service": extract_service(query)
        },
        include_metadata=True, // main thing to get results
        include_score=True
    )

    # Step 4: format results
    return [
        {
            "id": r.id,
            "text": r.text,
            "score": r.score,
            "metadata": r.metadata
        }
        for r in results
    ]

## What embedding options we had?
text-embedding-3-large - 3072 dimensions vector length
text-embedding-3-small - 1536 - small vector length

## What is normalize true mean?
normalize=True means embedding vector/unit length.
This makes matching content in vector DB with text we have
map more on content similarity than length of the content

## What is embedding?
A trained neural network that converts text into a vector where semantic relationships are preserved

## Why do we need?
Because computer doen't understand english, hence the vector comparision is what used to perform to search meaning in the vector DB

## What should you think about when choosing a vector DB?
### A. Scale
how many vectors?
millions? billions?
how fast does corpus grow?
### B. Latency
interactive search?
sub-100ms needed?
batch/offline acceptable?
### C. Recall quality
approximate search acceptable?
do you need high recall?
### D. Filtering support
does it support metadata filtering efficiently?
simple filters or complex boolean filters?
### E. Update pattern
mostly static corpus?
frequent upserts/deletes?
real-time indexing needed?
### F. Operational model
managed service vs self-hosted
backup, durability, monitoring
multi-region needs
### G. Ecosystem / integration
SDK quality
cloud support
hybrid lexical + vector support
reranking support
### H. Cost
storage cost
memory cost
query cost
replication cost

I chose chroma DB, as this was a POC hence the scale was small
## Production Grade Vector DBs
#### Chroma -> Simple, not built for scale
### Production grade Vector DB
#### Pinecone, Weaviate, Qdrant, Milvus
#### Elasticsearch, OpenSearch, Azure Cognitive Serach


### Popular Interview Questions
What is a vector database?
A DB optimized to store and search high-dimensional vectors

What are embeddings?
Dense numerical representations of data capturing semantic meaning

How is vector search different from traditional search?
Traditional: keyword / exact match
Vector: semantic similarity (meaning-based)

What is ANN (Approximate Nearest Neighbor)?
Used because exact search doesn’t scale.
Approximate Nearest Neighbor (ANN) is an algorithm designed to find a data point in a dataset that is very close to a given query point, but not necessarily the absolute closest one
This algorithm is used in Vector DBs to perform a similarity serach, unlike NN which finds the absolute nearet neighbor.

Key words --> ANN sacrifices accuracy for speed, better when the system has scaled and vector DB is large

What is indexing a VECTOR DB, how is it differnt from a search?
indexing is the process of organizing and structuring the stored vectors to enabe fast similarity queries. So this is organizing part.

What indexing techniques are used in vector DBs?
HNSW, Product Quantization, Product Quantization 
<No need to memorize the definition, just be aware of these terms>

What is the time complexity of ANN?
Average Case O(logn), Worst Case (O sqrt(n))

Why do we need hybrid search?
vector search fails on exact tokens (IDs, error codes)

Why does vector DB return wrong results sometimes?
Expected answer:
embedding limitations
ANN approximation
poor chunking
missing filters

def rank_and_deduplicate(chunks, user_query):
    # Remove duplicates
    unique = {}
    for c in chunks:
        if c["text"] not in unique:
            unique[c["text"]] = c

    unique_chunks = list(unique.values())

    # Re-rank using similarity
    ranked = sorted(
        unique_chunks,
        key=lambda c: similarity(user_query, c["text"]),
        reverse=True
    )

    return ranked[:10]
    This function is about generating the unique chunks from the list we computed.

    def generate_answer(user_query: str, parsed_query: dict, context_chunks: list[dict]) -> dict:
    context_text = "\n\n".join([
        f"[{chunk['id']}] {chunk['text']}" for chunk in context_chunks
    ])

    system_message = """
    You are a technical support assistant.
    Use only the provided evidence.
    Do not make unsupported claims.
    If the answer is uncertain, explicitly say so.
    Return JSON only.
    """

    user_message = f"""
    User Question:
    {user_query}

    Parsed Query:
    {parsed_query}

    Retrieved Evidence:
    {context_text}

    Return JSON with fields:
    - summary
    - likely_root_cause
    - evidence_ids
    - confidence
    - next_steps

    Rules:
    - Cite evidence IDs
    - Do not use information not present in the evidence
    - If evidence is insufficient, set confidence to low
    """

    response = llm_generate(
        model="gpt-4.1",
        system_message=system_message,
        user_message=user_message,
        temperature=0.1,
        max_tokens=700,
        response_format="json"
    )

    return parse_json(response)


def build_context_window(chunks: list[dict], max_tokens: int) -> list[dict]:
    selected = []
    token_count = 0

    for chunk in chunks:
        chunk_tokens = estimate_tokens(chunk["text"])
        if token_count + chunk_tokens > max_tokens:
            break
        selected.append(chunk)
        token_count += chunk_tokens

    return selected

## build_context_window
def build_context_window(chunks: list[dict], max_tokens: int) -> list[dict]:
    selected = []
    token_count = 0

    for chunk in chunks:
        chunk_tokens = estimate_tokens(chunk["text"])
        if token_count + chunk_tokens > max_tokens:
            break
        selected.append(chunk)
        token_count += chunk_tokens

    return selected

def validate_answer(answer, context_chunks):
    context_text = " ".join([c["text"] for c in context_chunks])

    issues = []

    # Check citations exist
    for eid in answer.get("evidence_ids", []):
        if eid not in [c["id"] for c in context_chunks]:
            issues.append("invalid citation")

    # Check grounding
    if not overlaps(answer["root_cause"], context_text):
        issues.append("not grounded")

    # Confidence sanity
    if answer["confidence"] == "high" and issues:
        issues.append("overconfident")

    return {
        "is_grounded": len(issues) == 0,
        "issues": issues
    }

## How was the vector DB created?
Raw Documents → Chunking → Embedding → Indexing → Storage


### Raw documents
Runbooks
Incident reports
KB articles
Logs (processed/summarized)
Architecture docs

Deployment fails when config version mismatch occurs..."


def build_vector_db(documents):
    for doc in documents:
        chunks = chunk_document(doc.text)

        for i, chunk in enumerate(chunks):
            embedding = embed(chunk)

            vector_db.upsert(
                id=f"{doc.id}_{i}",
                vector=embedding,
                text=chunk,
                metadata={
                    "doc_type": doc.type,
                    "service": doc.service
                }
            )

### What is the chunking stategy?
Mid chunk size, too small means irrelavent answer, too many vectors created, if too large you loose context
Too big chunk, will give incorrect results
def chunk_document(doc_text: str, chunk_size=500, overlap=50):
    chunks = []
    for i in range(0, len(doc_text), chunk_size - overlap):
        chunk = doc_text[i:i + chunk_size]
        chunks.append(chunk)
    return chunks

## Challenges
##### Vague requirement given, broke the problem in query understanding, retrieval quality, and answer reliability steps, worked with customers/stakeholders to understand customer needed -- structured outputs, citation requirements, and evaluation rubrics.
One of the main challenges was that the requirements were initially ambiguous. The ask was broadly to ‘help developers and support engineers find answers faster,’ but there was no clear definition of what a good answer looked like — whether it should be concise, detailed, or how much trust and traceability was required. It also wasn’t clear which data sources were authoritative or how to handle conflicting information.

To address this, I broke the problem into smaller components — query understanding, retrieval quality, and answer reliability. I worked with stakeholders to define what success meant, which led us to introduce structured outputs, citation requirements, and evaluation rubrics. This helped us move from a generic chatbot to a system that produced consistent, evidence-backed answers that users could trust

AWS System design <this is a preview/POC>

User / Internal UI
        │
        ▼
 API Gateway
        │
        ▼
 Lambda / Python API
        │
        ├── Parse query / decompose query
        ├── Embed query
        ├── Search vector index
        ├── Build prompt
        ├── Call LLM
        └── Return structured response
        │
        ├──────────────► OpenSearch / Vector Store
        │
        └──────────────► LLM endpoint (Bedrock or external model)

Offline ingestion flow:
Documents / runbooks / KBs
        │
        ▼
        S3
        │
        ▼
 Lambda / batch job
        │
        ├── extract text
        ├── chunk documents
        ├── generate embeddings
        └── upsert chunks + metadata
        ▼
 OpenSearch / Vector Store