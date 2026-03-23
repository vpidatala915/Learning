abstract class Dialog {
    abstract Button createButton();  // subclasses decide what to create
}

// class creational pattern is about the parent
class here the dialog determines what creates a generic function note -- //create Button

// Then the child class inherits the Dialog class, and inside the createButton function we choose to return WindowsButton -- so we did not touch the parent created function that is what the parent declares on the other hand the child says I will decide what specfic thing to do folloing the same pattern as parent.

Creational patterns are widely used in scenarios where object creation needs to be controlled, flexible, or standardized.


class WindowsDialog extends Dialog {
    Button createButton() {
        return new WindowsButton();
    }
}

class WebDialog extends Dialog {
    Button createButton() {
        return new HTMLButton();
    }
}
