---
title: "Recommended Podcast: Interview with SQLFluff Creator, Alan Cruickshank"
date: 2025-11-03
tags: ['podcast', 'sqlfluff']
---

<iframe allow="autoplay *; encrypted-media *; fullscreen *; clipboard-write" frameborder="0" height="175" style="width:100%;max-width:660px;overflow:hidden;border-radius:10px;" sandbox="allow-forms allow-popups allow-same-origin allow-scripts allow-storage-access-by-user-activation allow-top-navigation-by-user-activation" src="https://embed.podcasts.apple.com/au/podcast/keep-your-analytics-lint-free-with-sqlfluff/id981834425?i=1000524721593"></iframe>

Keeping a team's SQL codebase tidy and maintainable can be challenging. A decent code linter is an essential tool in this regard, but SQL developers aren't exactly spoiled for choice. SQL has some unique linting challenges, which perhaps explains the small number of linter options available for it.

If you want to learn more about the best SQL linter (in my opinion), [SQLFluff](https://www.sqlfluff.com/), have a listen to this interview with the project's creator and maintainer, Alan Cruickshank. Alan talks about the problems in his workplace he was trying to solve with this tool, the challenge of dealing with the non-standardisation of SQL and the large number of SQL dialects, and the buy-in from the DBT community his project received by tackling the problem of linting heavily parameterised SQL.

Here are some of Alan's comments on the challenge of linting parameterised SQL:

> __Alan:__ The other thing that makes this a little different is the way that people tend to use SQL, and this is a more recent development, is around templating. So I think the first place that I came across this was with Airflow, but even more so with the DBT community. It's quite common to template your SQL, where the SQL files that are stored aren't themselves valid SQL, they had to be pre-processed before they become valid SQL. And this was in some ways one of the toughest pieces to address to putting together a linter, but it's also one of the things I think has been most important for SQLFluff as a project and one of the things that has built some of the momentum around it.
> 
> The challenge is to make something that is valid SQL. In a very simple case, if people are doing just templating to insert variable names or table names, then you could kinda guess. You could do some kind of heuristic to guess what the result would be if you replaced the tag with with something else. But if you're templating or doing loops in larger chunks of SQL, it's much harder to guess what the valid SQL would be at the end. So the initial approach we took to this was to allow people to configure variables themselves. Effectively, if you're templating using Jinja, which I realize particularly for the Airflow community, that's mostly what people are doing. If you're putting variables in there, give SQLFluff some configuration, which allows it to insert some at least comparable variables, and then it will be able to render your SQL and then lint it as normal.
> That's worked for people who are using large SQL files in in an airflow context.
>  
> What makes that doubly difficult with the dbt community is that the extent to which people are templating the SQL is, I think it's safe to say, an order of magnitude more complicated. And people are templating much more than just, you know, a field name or a variable name here or there. What we introduced into SQLFluff, is to actually tie into the dbt compilation engine in the background, so that SQLFluff will use dbt's native compiler to compile your SQL, which means that if it's valid for dbt, it's valid for us, and we will lint it appropriately.

Here's my favourite quote from the podcast, partly becausing Alan validated my preference for trailing commas, but mainly because he reinforces the idea that consistency for an organisation or team is more important than any specific formatting preferences:

> __Interviewer:__ ...And it's interesting that you pointed out the trailing versus leading commas debate. I was actually going to ask what your preference is.  
> __Alan:__ I'm thoroughly on team trailing commas. But even within the comparatively small analytics community at tails.com, there are a selection of people with strong views either way. But it brings up an interesting point because, like, we joke around people having strong views either way. But I think that the thing that SQLFluff, and I guess tools like this in general, enable teams to do is to have productive discussions about this stuff. Like, it really doesn't,  people are gonna hate me for this, but it doesn't matter whether you have trailing commas or whether you have leading commas. What matters is that you all agree, and you all do the same thing. And we can talk later about whether all of you implies your team or your organization or the industry as a whole. At a micro level, as long as you all agree, it doesn't matter.

Another interesting point that Alan makes, and I've heard more than one linting tool developer make it, is that people respond to feedback about code formatting much more positively when it comes from a machine, rather than another developer. He makes the point that sorting out the sometimes annoying aspects of code formatting early on and seamlessly, makes subsequent code review sessions more pleasant and more productive.

Alan mentioned taking some inspiration from two SQL style guides in particular when it came to creating the linting rules in SQLFluff. If you want to have a look at these style guides, they can be found here:
* [DBT SQL Style Guide](https://docs.getdbt.com/best-practices/how-we-style/2-how-we-style-our-sql)
* [Mozilla SQL Style Guide](https://docs.telemetry.mozilla.org/concepts/sql_style.html)

If you want to follow Alan Cruickshank, he can be found on [LinkedIn](https://www.linkedin.com/in/amcruickshank) and [GitHub](https://github.com/alanmcruickshank).

Link to the Podcast Episode:

{{< cards cols="1" >}}
  {{< card link="https://www.pythonpodcast.com/sqlfluff-sql-linter-episode-318" title="The Python Podcast - Keep Your Analytics Lint Free With SQLFluff" >}}
{{< /cards >}}