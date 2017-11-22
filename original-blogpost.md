# What my PhD would have been on

Last year, in March, I joined the Software Engineering team at LaBRI to start a PhD. In late October, 
I dropped it. This post will describe what I worked on and the definition of my PhD subject as I left it.

## Beginner wanderings

“Design and implementation of mobile applications in a REST ecosystem” was the vague topic 
I started with. My PhD was funded by a project aiming at studying software engineering practices 
in the realm of client-side web programming and apply it to a new approach to e-commerce

### From REST…

I remember again a project I was involved in three years ago. I was a student and the project was a partnership between my school and a company. Some meetings or discussions involved words like “REST”, “Ajax”, “Drag and Drop”, “an HTML div”… All this jargon was unknown at the beginning and was understood at the end. With the exception of REST for which no one really had a definition I could find satisfying. And the PhD topic I was given had this word. So a first thing I did was reading Roy Fielding’s dissertation.

I recommend this reading to anyone who write web applications. Alongside with this recommendation comes a warning that from now on, I’ll slap in the face anyone talking to me about a “REST API” (call them “Web API”, “URL API”, “HTTP API” if you wish instead) or putting “REST” in a list that already includes “XML, JSON”.

Along the way, I discussed once with my friend Thomas who is a PhD student at UCI (where coincidentally Roy Fielding works) who mentionned CREST (Computational REST). The idea is interesting, but I didn’t find really how I could use it in practice. Worth mentioning, though, especially the first part that studies how REST principles have been misapplied and some nowadays good practices.

### … to cookies …

A part of Fielding’s dissertation discusses cookies and how they violate REST:

> Cookies also violate REST because they allow data to be passed without sufficiently identifying its semantics, thus becoming a concern for both security and privacy. The combination of cookies with the Referer [sic] header field makes it possible to track a user as they browse between sites.
>
> As a result, cookie-based applications on the Web will never be reliable. The same functionality should have been accomplished via anonymous authentication and true client-side state. A state mechanism that involves preferences can be more efficiently implemented using judicious use of context-setting URI rather than cookies, where judicious means one URI per state rather than an unbounded number of URI due to the embedding of a user-id. Likewise, the use of cookies to identify a user-specific “shopping basket” within a server-side database could be more efficiently implemented by defining the semantics of shopping items within the hypermedia data formats, allowing the user agent to select and store those items within their own client-side shopping basket, complete with a URI to be used for check-out when the client is ready to purchase.

“anonymous authentication” is still to be understood for me. Fortunately, I recently (December, so after leaving the PhD) came across a different idea that could probably help fill the same role.

However, “true client-side state” is a technology that now exists and is well-deployed: local storage

### … to client-side storage …

Cookies have been used as local storage for a long time, so I made a brief study of what was available to replace that. Actually, most of the work had already been done by someone else, so it was quite easy.

To be noted is a meeting where I presented this and someone asked me “oh, you know HTML5? Have you heard about canvas?”. It was probably the strongest proof that I was in a different world. Different from what I was used to when talking about the web. Before this PhD, my discussions about the tech side of the web were mostly with people in standard mailing-lists and Mozilla (the overlap between both is big). So yeah, I’ve heard about canvas…

## Shift to repository mining and programming languages

While I was working on web architecture, the rest of the software engineering research team was working on… software engineering. They are developing a tool called VPraxis. In a nutshell, this tool allows one to query a repository. For instance questions like “in 2011, who worked on classes that implement the interface X?”. Actual repository (SVN, Git, Mercurial…) is abstracted out and the tool is expected to be language agnostic (and why not cross-language (imagine queries dealing with HTML classes used in CSS)).

Several discussions on this topic with the team and guests who stayed for a couple of days increased my interest. What I consider to be the most interesting part of my work was the definition of the “dependency upgrade problem” and ideas to help solving it.

### The Dependency Upgrade Problem

#### The problem

Initial conditions are as follow: A developer (or a team) has a codebase using a dependency (for now, only one dependency is considered since it’s already enough work). A dependency can be a library (the developer writes an application using jQuery), or a platform (the developer writes a software on top of Linux, or a Firefox add-on). Over time, this dependency changes (bugfixes, performance improvements, API changes…). The developer wants her code to work with the new dependency version. Most of the time, the developer can do that when she wishes, but in the case of Firefox add-on, for instance, you have to adapt to the platform at a pace you do not decide (because “imposed” by the 6 weeks release schedule).

![problem description](images/dcia1.png)

Here is how it works currently to adapt code to a modifying dependency: The dependency author (or team) writes a changelog, the developer reads this changelog, figures out how the described changes affects her code and starts adapting her code.

![how the problem is currently addressed](images/dcia2.png)

This is hugely error-prone for 2 reasons. First of all, the dependency author (or team) is/are a human being(s), so the changelog (if it exists at all!) may be incomplete or inaccurate. Second of all, the developer needs to match how this changelog describes things that may affect her code. Even if the changelog was perfect, it would still require a lot of work and work that is error prone (because places can be missed, introducing new bugs).

An error-prone process on top of another error prone process, no wonder people avoid as much as they can to upgrade. Another consequence is what has become a good practice in library authoring which is to never (or almost never) break an API. The only reason this is a good practice is because breaking APIs require more (error-prone) work to library clients. The downside is libraries that keep old code around forever, having “deprecated methods” that are never removed and growing in size, making them harder to read and maintain. Size of a library is a particular problem on the web. So that jQuery is considering removing parts of the API.

#### Towards a partial solution

The potential of mining a repository and extracted fine-grained information about code that is changing gave me two complementary ideas to help solving the aforementioned problem.

First of all, the changelog. Dependency code is in a repository. All changes between a version and another version are stored somewhere in this respository. One idea is to build a tool that reads in a repository all changes that may affect client code and generate a semantic (with information like “such function has a new argument”, “the implementation of such function changed”, “such classes haven’t been touched at all”), machine-understandable (not sentences written in a human language) changelog. First, this changelog would be complete, by definition of reading in the repository. And, the notion of “all changes that affect may client code” is probably undecidable, but conservative assumptions can be made; it would just make the machine-understandable changelog a bit bigger. It has to be noted that closed-source libraries (for which there is no public access to a repository) could release a semantic changelog to their clients without providing access to the repository itself.

The second step is to have another tool that takes the developer code and the semantic changelog (hence the need for the changelog to not be in human sentences form) as input and provides suggestions on how to transition the code as output. Some adaptations could be fully automated (public method rename), but most cannot, so a recommendation engine is probably the best that can be done. Associated with decent UI, it would certainly be a big win.

![Towards a solution](images/dcia3.png)


Of course, the two tools I suggest wouldn’t entirely solve the problem (since it’s certainly undecidable). The human beings would still need to do some work, but I intuit it could be reduced to parts than cannot be done by a human being. On the good sides is the ability for a program to tell you that some parts of your code are not affected by the change. I intuit that having such information would be a powerful motivation to adapt the code. Imagine a tool that would tell you “the 80% of your code base that is in these files are unaffected by the dependency change”.
The end

I planned to work on such two tools for JavaScript (obviously?). I wrote a JavaScript static analysis tool to retrieve an API exposed by a file. This experience taught me that static analysis isn’t enough, so I gave up the static analysis tool. The definition JavaScript API by itself is actually difficult and I’m not sure I’ve been able to find the perfect answer to this question yet. The only thing I’m sure of is that answering it requires some static AND dynamic analysis.

Anyway, I didn’t go further, but thought it was worth sharing where I left my work.
