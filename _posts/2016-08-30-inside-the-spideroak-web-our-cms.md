---
layout: post
title:  "Inside The SpiderOak Web: Our CMS"
date:   2016-08-30 09:00:00
categories: blog
tags:
    - spideroak
---

*[Cross-posted at spideroak.com](https://spideroak.com/articles/inside-the-spideroak-web-our-cms)*

This post is the first of hopefully many through which I shed some light on the design and implementation of the SpiderOak web apps. Our web ecosystem has evolved over the years and is certainly something quite unique to SpiderOak. I make no claim that what we've built in our CMS is proper or even a solution we'd land on if we decided to do it all over again. But it is what we have and it works quite well for us. I'm hopeful that others out there will find this exposé somewhat useful.
 
### Quick Facts
 
- **Backend**: Python, Django
- **Database**: Postgres
- **JavaScript**: We use very little JS on the public site except for our analytics platform, which was written in-house. We do have some heavy JS on the internal side of our CMS, but we use no JS "frameworks" and rely on JQuery almost exclusively for selectors and ajax requests.
- **HTML Framework**: Bootstrap, although heavily modified
- **Styles**: We develop in LESS and compile to CSS before each release
- **Hosting**: Public spideroak.com resolves to multiple public nodes running nginx on Linux machines which we manage ourselves. We also have an internal node behind a VPN + LDAP that hosts the Django admin interface for generating/modifying content in the CMS.
 
 
 
### Design Idea: Ribbons
The core design idea behind the  CMS is a concept that Mike (CMO, President) had about "ribbons". Essentially, we don't write any full pages; rather, we put all of our content into individual template blocks we call "ribbons" and then we compile pages by simply combining a sequence of ribbons.
 
Every page on our CMS has only 3 important attributes: title, url, and the list of ribbons contained on that page:
 
![PageRibbonModelDiagram](https://spideroak.com/media/cms/cms/PageRibbonClassDiagram.png)
 
In this way the Marketing team can build one ribbon, and then re-use it on several different pages. This product ribbon, for example, is re-used in several pages:
 
![ProductRibbon](https://spideroak.com/media/cms/cms/Screen_Shot_2016-08-29_at_8.47.48_AM.png)
 
Each individual page on spideroak.com is built by compiling a sequence of ribbons on demand and rendering them as one large block of HTML. In doing so we can selectively show some ribbons to one set of users, while substituting all or some of those ribbons to another set. This allows us to have A/B tests for individual ribbons or for entire pages; which we find quite powerful.
 
Of course, compiling each page from a set of multiple ribbon objects on demand for every request is expensive for our database, so we've set up a cache in nginx to render the same HTML result for an hour at a time per user, per URL.
 
### Ribbon Content
Each ribbon has basically 2 important properties: `ribbon_type` and `custom_attributes`
The `ribbon_type` relates the ribbon content to a specific template file. When Django is building the html for a particular page, it loads the ribbon content for each ribbon to the correct template according to that ribbon's `ribbon_type`.
 
The content for the template comes from each ribbon's `custom_attributes` property. This is simply a key-value datastore that maps template features to content in the ribbon model. We use Postgres' [hstore](https://www.postgresql.org/docs/9.5/static/hstore.html) data type to hold this information so that we don't need a separate table for each ribbon type.
 
Here is a simple example of ribbon that might exist in our db:
 
```
Ribbon(
    ribbon_type='one_column',
    custom_attributes={
        "content": 'Hello World.'
        "theme": 'semaphor-dark.'
    }
)
```
 
This ribbon is then set inside a `one_column` template with placeholders for each of those attributes:
 
```
<div class='theme-{{ ribbon.custom_attributes.theme }}' >
    <div>{{ ribbon.custom_attributes.content }}</div>
</div>
```
 
The above examples are slightly simplified version of our ribbon templates, but they are not far off; we have about 15 different ribbon types and each one has a template like the one above with specific placeholders for all of that ribbon's attributes.
 
### Ribbon Styles
As you may have noticed above, all of our styles are defined by a master stylesheet; when a member of the Marketing team goes to create a ribbon they very rarely touch anything related to colors, spacing, padding, margins, heights, etc. Instead, they simply select a theme which is attached to the ribbon as a class name (see above). Our stylesheets have a very strict prescription for themes for each ribbon type. In theory, this means we can construct pages with consistent and great-looking styles without ever touching styles directly.
 
In practice, this has been a little tricky to achieve. Since we have several different products, and each product has it's own style-theme, we still have to use some discretion when it comes to using the same ribbon on multiple different pages, where styles may compete.
 
It's also been a bit tricky building a consistent page from top to bottom when you only ever see one ribbon at a time.
 
In general, though, I believe that restricting the freedom on the content editor has helped us achieve a more consistent style across the site.
 
### The Ribbon Editor
All of these attributes are manipulated in the one feature of our backend that does not come with bare-bones Django admin: our Ribbon Editor!
 
The Ribbon Editor is a console for creating our ribbons, and it looks different for each ribbon type. For `one_column` ribbons, the Ribbon Editor displays a list of generic features like theme and background-image, and also has a textbox for inputing **Markdown** content for the ribbon's main content area.
 
![OneColumnRibbonEditor](https://spideroak.com/media/cms/cms/cmsRibbonEditorOneColumn.png)
 
A `two_column` ribbon looks essentially the same as a `one_column` ribbon except that is has an extra Markdown content box.
 
![TwoColumnRibbonEditor](https://spideroak.com/media/cms/cms/RibbonEditorTwoColumns.png)
 
Over the years I've discovered that Markdown is simultaneously powerful and confusing (especially to users not already used to it) and should always be accompanied by a live editor. For this reason, all of our Markdown editor input boxes produce live feedback so that the Marketing team member can see what the ribbon will look like as soon as they start typing new content.
 
#### Extended Markdown
In addition to the basic Markdown syntax, all of the content input boxes provide for some basic macros that allow the team to quickly insert buttons, links, images, quotes, etc. that all conform to the master style.
 
Since the CMS has been live in production, we've added several macros after discovering a pattern in content that could be easily automated. Our legal pages, for example, utilize recently added shorthand for those long underscore lines, and for inputting the current date (using python datetime's `stfrtime` formatting).
 
### Forms
Our form tool was actually written before any of the rest of the CMS was in place, and it's where we first experimented with using Postgres' `hstore` type to store schemaless objects. Essentially, every form on the CMS has 3 important attributes: form name, success url, and an aggregation of form fields.
 
Each "form field" object contains attributes which describe the field type (email, input, textarea, choice, etc.) the field_id, name, help_text, and whether or not the field is required.
 
![FormClassModel](https://spideroak.com/media/cms/cms/FormClassDiagram.png)
 
We then have dedicated form-ribbons that set a form into a template with the appropriate Django form widgets based on the form field set for each form.
 
When a user submits a form, we generate a key-value dataset for each field_id and user-supplied value in the POST request and save it into an `hstore` column in the database. In this way, every single form submission is saved in one table, so we don't need separate tables for individual forms, and we can change the fields on a form without having to change a database schema for the form.
 
All of this is also managed via a nearly out-of-the-box Django admin interface:
 
![FormEditor](https://spideroak.com/media/cms/cms/Screen_Shot_2016-08-29_at_9.02.59_AM.png)
 
### Articles
Before we built the CMS we had a Word Press blog where we would post all our blog-like articles. But we also had other "article-like" things on the main website: release notes, warrant canaries, support docs, FAQ's etc. When we built the CMS we just collected all those things into one place: the Articles model.
 
An article has a title, subtitle, published date, author, and a content field that contains markdown-syntaxed data. (The markdown for articles is the same markdown for content in ribbons, and contains the same extensions mentioned above).
 
While the article model is simple, we've found that this has been the most difficult aspect of the CMS to perfect. Since we've combined release notes, blog posts, support docs, and more into one place, we're still struggling to find a good "discoverability" mechanism.
 
Since we launched the CMS we've added category tags and group articles by similarity in tags belong to each article (when you visit an article page on our website, you'll see a list of Related Article: these are the other articles in the db that have the most related tags to the article being viewed).
 
Honestly, we have a long way to improving discoverability of Articles (and of content in general in the CMS). We have search ribbons throughout the site that match a search string with Article titles and subtitles, but I think the next step is to implement a Postgres full text search to really improve search behavior.
 
### Lessons
In general, I'm quite happy with how the CMS has served us over the last 1.5 years since it's been fully implemented. From an engineering perspective its required very little maintenance. From the marketing side, I think we've done a good job separating the two most difficult aspects of content creation: words and style. Marketing has been able to focus almost all of their time on coming up with the words for the site while the design is already pre-determined on a per-ribbon basis.
 
That's not to say we don't have any style challenges: regularly we invent new sequences of ribbons that we've not used before that simply don't look good together; in such cases we have to tweak our designs or re-think our page structure.
 
We recently went through (and are still tweaking) a design refresh on the website to be more inline with our new collaboration tool [Semaphor](https://www.spideroak.com/solutions/semaphor). Because our pages are built ribbon by ribbon but the designs are created whole-pages at a time, we have to carefully deconstruct those designs into individual ribbon parts.
 
There are also odds-and-ends that simply do not fit inside the Ribbons design pattern; pages like our old login pages must simply live on as hard-coded individual templates that inherit some very minimal elements from the rest of the CMS (like a standard header and footer ribbon set and the master stylesheet).
 
When building this CMS the most important thing we did was to only add features that were *specifically requested by Marketing*. It's really impossible to overstate that: the rabbit hole that is building a new CMS is essentially bottomless, and you should really only build the bare minimum number of things needed to get the job done (this is practically true for all software projects, but especially true for CMS's).
 
At the same time, it's equally important to only build and release one feature at a time because Marketing's needs change constantly, especially after they start using some of the early features already available to them. We have probably 15 different ribbon types on the site, but 90% of our content is almost certainly in 2-3 different ribbon types. In hindsight we should have only ever implemented one ribbon at a time and then added new ribbons only after a given design was literally impossible for the existing ribbon templates.
 
### Questions, comments?
Well, that's a very brief introduction to our CMS. If you have a question or comment about what we've built, please feel free to email me at [quentin@spideroak.com](mailto:quentin@spideroak.com). If you're curious about something else we do on the web, ask me that as well, your request may turn into my next article!