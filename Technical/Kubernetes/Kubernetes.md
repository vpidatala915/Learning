Kubernetes Troubleshooting Cheat Sheet
🔍 Initial Assessment Commands
bash
kubectl get pods -n <namespace>                    # Pod status overview
kubectl get pods -n <namespace> -o wide            # Include node, IP info
kubectl describe pod <pod-name> -n <namespace>     # Detailed pod info
kubectl get events -n <namespace> --sort-by='.lastTimestamp'  # Recent events
kubectl top pods -n <namespace>                    # Resource usage
kubectl get deployment <name> -n <namespace>       # Deployment status
kubectl get svc,ep -n <namespace>                  # Services & endpoints
📋 Logs Investigation
bash
kubectl logs <pod> -n <namespace>                  # Current container logs
kubectl logs <pod> -n <namespace> -p               # Previous container (after crash)
kubectl logs <pod> -n <namespace> -f               # Follow/tail logs
kubectl logs <pod> -n <namespace> -c <container>   # Specific container in pod
kubectl logs -l app=<label> -n <namespace>         # All pods with label
🔄 Deployment Management
bash
kubectl rollout status deployment/<name> -n <namespace>
kubectl rollout history deployment/<name> -n <namespace>
kubectl rollout undo deployment/<name> -n <namespace>
kubectl rollout restart deployment/<name> -n <namespace>
kubectl scale deployment/<name> --replicas=5 -n <namespace>
kubectl autoscale deployment/<name> --min=2 --max=10 --cpu-percent=70
🔧 Debugging Inside Pods
bash
kubectl exec <pod> -n <namespace> -- <command>
kubectl exec -it <pod> -n <namespace> -- /bin/bash
kubectl exec <pod> -n <namespace> -- nslookup <service>
kubectl exec <pod> -n <namespace> -- curl <service>:<port>
kubectl exec <pod> -n <namespace> -- cat /sys/fs/cgroup/cpu/cpu.stat  # CPU throttling
🔐 Secrets & ConfigMaps
bash
kubectl get secrets -n <namespace>
kubectl describe secret <name> -n <namespace>
kubectl get secret <name> -n <namespace> -o yaml
kubectl create secret generic <name> --from-literal=password=xxx
kubectl edit secret <name> -n <namespace>
🏥 Pod Health States
Status	Ready	Meaning
Running	1/1	Container running + passing readiness probe ✅
Running	0/1	Container running but failing readiness probe ⚠️
CrashLoopBackOff	0/1	Container repeatedly crashing 🔴
Pending	0/1	Waiting to be scheduled 🕐
ImagePullBackOff	0/1	Can't pull container image 🔴
🎯 Resource Troubleshooting
bash
# Check resource limits/requests
kubectl describe pod <pod> -n <namespace> | grep -A3 "Limits\|Requests"

# Check node resources
kubectl top nodes
kubectl describe node <node-name>

# Check CPU throttling
kubectl exec <pod> -- cat /sys/fs/cgroup/cpu/cpu.stat | grep throttled
🌐 Service Discovery Flow
503 Error → Check Service → Check Endpoints → Check Pod Readiness
bash
kubectl get svc <service> -n <namespace>
kubectl get endpoints <service> -n <namespace>
kubectl describe svc <service> -n <namespace>  # Check selector
kubectl get pods -l <selector> -n <namespace>  # Pods matching selector
💡 Key Concepts to Remember
Readiness vs Liveness:
Readiness: Should pod receive traffic?
Liveness: Should pod be restarted?
Secret Updates:
Env vars: Never update in running pods
Volume mounts: Update with delay (~1 min)
Need pod restart for immediate effect
CPU Limits:
1000m = 1 CPU core
Throttling happens at limit
Request = scheduling, Limit = enforcement
Endpoints:
Only READY pods appear
Automatically updated by Service
No manual intervention needed
Debugging Order:
Events → Describe → Logs → Exec
🚨 Common Issues & Fixes
Problem	Check	Fix
CrashLoopBackOff	kubectl logs -p	Fix app error/config
ImagePullBackOff	describe pod	Fix image name/registry
0/1 Ready	Readiness probe	Fix health endpoint
No endpoints	Pod labels/readiness	Fix selector/probes
High CPU	top pods + limits	Increase limits or scale
OOMKilled	Memory limits	Increase memory/fix leak
Pending	describe pod	Check node resources
Ready for your quiz tomorrow? 🚀







