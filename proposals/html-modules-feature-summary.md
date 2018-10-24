# HTML Modules Summary

By [@dandclark](https://github.com/dandclark), [@samsebree](https://github.com/samsebree)
with [@bocupp](https://github.com/BoCupp) and [@travisleithead](https://github.com/travisleithead)

HTML Modules are intended as a replacement for HTML Imports.  A [separate document](https://github.com/w3c/webcomponents/blob/0abde89fe573b80f1dee72c503cd32f574424975/proposals/html-modules-proposal.md) lists the perceived problems with HTML Imports and describes how HTML Modules offers solutions to those problems.  This document simply summarizes what an HTML Module is without attempting to explain its reason for being. 

## Importing an HTML Module

The file module.html contains some content that index.html would like to use.  Index.html imports the HTML Module, module.html, using a script module import statement.  

**module.html:**

> ```html
> <template id="the-template"></template>
> ```
 
**index.html:**

> ```js
> <script type="module">
>     import doc from "./module.html"
>     let template = doc.querySelector("#the-template");
> </script>
> ```

## Exporting from an HTML Module

Inline script modules in an HTML Module redirect their exports via the HTML Module's Record so that the importer of the HTML Module can import them.

**logger.html:**

> ```html
> <template id="log-template">
>     <div class="log-entry">
>         <span class="entry-number"></span>
>         <span class="entry-text"></span>
>     </div>
> </template>
> <script type="module">
>     // export log API from logger.html
>     export { log }
>
>     // access declarative content from the HTML module by importing it
>     import doc from "./logger.html"
>     let logTemplate = doc.querySelector("#log-template")
>
>     function log(message) {
>         let entry = logTemplate.content.cloneNode(true)
>         entry.querySelector(".entry-text").append(message)
>         return entry
>     }
> </script>
> ```

**index.html:**

> ```html
> <div id="log-container"></div>
> <script type="module">
>     import { log } from "./logger.html"
>     let container = document.querySelector("#log-container")
>     container.append(log("test log message"));
> </script>
> ```

This is done by computing the exports for the HTML Module's record during its instantiation phase as per the following pseudocode:

```js
for (ModuleScript in HtmlModuleRecord.[[RequestedModules]]) {
    if (ModuleScript.IsFromInlineScriptElement) {
        export * from ModuleScript;
    }
}
```

## All Scripts in HTML Modules are Script Modules
All scripts in HTML Modules are automatically module scripts regardless of *type* attribute.  This keeps the model that modules only import other modules and avoids questions as to when the console.log code would execute in module.html if it where classic script.

**module.html:**

> ```html
> <script>
>     console.log("module script is executing")
> </script>
> ```

**module.js:**

> ```html
> import "./module.html"
> ```

**index.html:**

> ```html
> <script type="module" src="./module.js"></script>
> <script>
>     console.log("classic script is executing")
> </script>
> ```
