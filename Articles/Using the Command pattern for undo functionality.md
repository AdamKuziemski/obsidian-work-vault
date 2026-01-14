---
tags:
  - topic/programming/csharp
source: https://matt.berther.io/2004/09/16/using-the-command-pattern-for-undo-functionality/
author: Matt Berther
read: false
---
Command is a very powerful design pattern, whose intent is to encapsulate a request as an object, thereby letting you parameterize clients with different requests, queue or log requests, and support undoable operations.

One of the biggest advantages to this pattern is that it decouples the object that invokes the operation from the one that actually knows how to perform it.

Today, I want to show you how to implement undo functionality using this command design pattern. For our example, we will be developing a very simple Notepad clone. Nothing too fancy, but enough to show the power of the pattern.

**Update**: This project is now available on [github](http://github.com/mattberther/sharp-editor).

The first thing we want to do is to create an abstraction around our textbox control. In the Command pattern, this abstraction is called the Receiver. The reciever in our example is an object called Document.

```c#
class Document
{
    private TextBox textbox;
    public Document(TextBox textbox)
    {
        this.textbox = textbox;
    }

    public void BoldSelection()
    {
        Text = String.Format("<b>{0}</b>", Text);
    }

    public void UnderlineSelection()
    {
        Text = String.Format("<u>{0}</u>", Text);
    }

    public void ItalicizeSelection()
    {
        Text = String.Format("<i>{0}</i>", Text);
    }

    public void Cut()
    {
        textbox.Cut();
    }

    public void Copy()
    {
        textbox.Copy();
    }

    public void Paste()
    {
        textbox.Paste();
    }

    public string Text
    {
        get { return textbox.Text; }
        set { textbox.Text = value; }
    }
}
```

What we have defined with the Document object are the operations that could be performed against this document, completely decoupling this functionality from our main application. If we want to change what happens when we bold a selection, we go to this object, rather than to the presentation code.

Secondly, we will need to design our Command interfaces. Since it is possible that we have commands that will not require undo functionality (for example, Copy), we have created two base classes (Command and UndoableCommand). We’ll see how UndoableCommand ties in a little later in the article. For now, just keep in mind that this is the class to derive from if you need your command to be able to undo itself.

```c#
public abstract class Command
{
    public abstract void Execute();
}

public abstract class UndoableCommand : Command
{
    public abstract void Undo();
}
```

As we work through our application and start adding menu items to it, we will start to see that we need a Command object to handle each of this actions. So, to handle bold, let’s create the following:

```c#
class BoldCommand : UndoableCommand
{
    private Document document;
    private string previousText;
    public BoldCommand(Document doc)
    {
        this.document = doc;
        previousText = this.document.Text;
    }

    public override void Execute()
    {
        document.BoldSelection();
    }

    public override void Undo()
    {
        document.Text = previousText;
    }
}
```

By creating a document object that wraps the textbox, we were able to completely decouple the object that will invoke the operation (a menu item) from the one that knows how to perform it (the document object).

The remaining command objects are very similar to the above. Because of this, I wont present the entire code here in print, although it is available via the download.

The remaining piece that we will need to bring everything together is a CommandManager. The CommandManager is a very simple class that has an internal stack that keeps track of our commands for our undo functionality.

```c#
class CommandManager
{
    private Stack commandStack = new Stack();

    public void ExecuteCommand(Command cmd)
    {
        cmd.Execute();
        if (cmd is UndoableCommand)
        {
            commandStack.Push(cmd);
        }
    }


    public void Undo()
    {
        if (commandStack.Count > 0)
        {
            UndoableCommand cmd = (UndoableCommand)commandStack.Pop();
            cmd.Undo();
        }
    }
}
```

We see in the above code, that we only add the command to the undo stack if it is an UndoableCommand. Remember, when I said that we would see how it ties in. Here it is. We dont want commands that dont have any undo functionality to be added to the stack. If the user tries to undo something that doesnt support undo, it would appear to be unresponsive.

The remaining thing that we now have to do is wire up the event handlers for the menu items (and the toolbars if you’re so inclined).

```c#
public class MainForm : System.Windows.Forms.Form
{
    private System.Windows.Forms.TextBox documentTextbox;
    private CommandManager commandManager = new CommandManager();
    private Document document;

    public MainForm()
    {
        //
        // Required for Windows Form Designer support
        //
        InitializeComponent();

        document = new Document(this.documentTextbox);
    }

    // a bunch of snipped code

    private void cutMenuItem_Click(object sender, System.EventArgs e)
    {
        commandManager.ExecuteCommand(new CutCommand(document));
    }

    private void pasteMenuItem_Click(object sender, System.EventArgs e)
    {
        commandManager.ExecuteCommand(new PasteCommand(document));
    }

    // etc...
}
```

I’ve made the entire sample application available for [download](http://github.com/mattberther/sharp-editor). Keep in mind, at this point, this is a very rudimentary text editor. Your mission, should you choose to accept it, is to add redo functionality to this application.

I hope that I’ve been able to illustrate the power of this pattern and how it could be easily used to add complex functionality. It’s really easy to add new Commands, because you dont have to change any existing objects.