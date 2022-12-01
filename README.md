<!--
author:   André Dietrich

email:    LiaScript@web.de

version:  0.0.1

language: en

narrator: US English Female

comment:  Demo of creating a custom loader for code-highlighting of code that has to be fetched...

@load.java: @load(java,@0)

@load
<script style="display: block" modify="false" run-once="true">
    fetch("@1")
    .then((response) => {
        if (response.ok) {
            response.text()
            .then((text) => {
                send.lia("LIASCRIPT:\n``` @0\n" + text + "\n```")
            })
        } else {
            send.lia("HTML: <span style='color: red'>Something went wrong, could not load <a href='@1'>@1</a></span>")
        }
    })
    "loading: @1"
</script>
@end

-->

# Embedding external code-snippets

Scripts are native building blocks of LiaScript and they can be used nearly everywhere.
Scripts do not necessarily have to be attached to another element, such as a quiz, survey, or code-snippet.
Thus, if there is a free script, the result of the execution defines its body.
The result send back to LiaScript is always interpreted as a string, but in this way it also possible to construct new Markdown/LiaScript elements or HTML and to tell the interpreter to display it properly.

Thus, if a script returns null or undefined it will not be shown to the user at all.

## Creating a custom Macro for loading code

The following example shows how such a custom loader might look like.
There are two macros defined, whereby the first is only used as a shortcut for the second one.

We have to tell the loading script:

* that it is ok to run only once, when the slide is loaded, so that it will not be updated on every visit
* that it should not be modifyable, otherwise the user might double-click on the result and inspect and modify the script
* the result has to be displayed as a block...

The script that gets injected uses the fetch-API to GET the code-file, which is displayed as a code-block.

The first result of the script is the string at the bottom, since the fetch-api works asynchronous.
By using the `send.lia` function, we have a direct connection from our javascript source code to the counterpart-body in LiaScript.
What is send back is also only a string, but in the first case we construct a LiaScript/Markdown - codeblock and by starting the string with `LIASCRIPT:` we say that this tiny string should be interpreted and parsed as LiaScript, which could also be a quiz, a table, or an entire chapter.
`HTML:` is just a shortcut for embedding HTML-code, which will be displayed otherwise with all tags.
These later sending to lia will overwrite the former body, which was only used as a placeholder for the user.

The first macro-call might not work in this case, since LiaScript will directly pass it to the fetch-api, that will fail, since it will try to fetch a relative URL on another server, where your project is not in the root.
By using the Markdown-link like version of the macro call, the macro is called within brackets and the URL in parenthesis is passed in as the last parameter.
This way, LiaScript will know, that this is a URL and it will check whether it is relative or absolute and do some corrections in the first case.
Another benefit is, that the meaning will be preserved, it is a link and another Markdown-viewer will present the content accordingly and also link to your resource.

```` markdown
<!--
@load.java: @load(java,@0)

@load
<script style="display: block" modify="false" run-once="true">
    fetch("@1")
    .then((response) => {
        if (response.ok) {
            response.text()
            .then((text) => {
                send.lia("LIASCRIPT:\n``` @0\n" + text + "\n```")
            })
        } else {
            send.lia("HTML: <span style='color: red'>Something went wrong, could not load <a href='@1'>@1</a></span>")
        }
    })
    "loading: @1"
</script>
@end
-->

# Loading

@load.java(App.java) --> might not work

@[load.java](App.java)

@[load(java)](App.java)

will return an error message

@[load.java](DoesNotExist.java)
````

## Demo

@load.java(App.java) --> might not work

---

@[load.java](App.java)

---

@[load(java)](App.java)

---

will return an error message

@[load.java](DoesNotExist.java)
