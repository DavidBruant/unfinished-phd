# unfinished-phd

See [original blogpost](original-blogpost.md) (2011) for context.

## Changed landscape

In 2017, [semver](https://semver.org/) is the prefered practice to work around not knowing whether/how
a change in a dependency will affect the dependent code. It's a discipline on the dependency author behalf
where the version number shape sets expectations on how much the dependant can break.

In 2017, for JavaScript, there is now [npm](http://npmjs.com/) which holds most of the open source js code
and the dependency graph.
Code can be downloaded from github, most high-profile libs do changelogs

In 2017, JavaScript now has standard static module syntax

There is also [greenkeeper](https://greenkeeper.io/) which is an interesting tool helping devs keeping up with knowing one of their dependency has evolved

## Attack plan in this age

If i took the time for that or had the money to hire someone...

Start with the first tool (the one that looks at git to generate the changelog automatically)

- [ ] Find a handful of common well-maintained dependencies (maybe lodash, d3)
- [ ] Find in bug reports people who had trouble updating
- [ ] Build a tool that matches lodash/d3 level of accuracy (figure out why not if not possible). See if it would have helped with the updating
- [ ] Apply the tool on upcoming versions see if it helps 
