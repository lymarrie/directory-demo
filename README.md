# Sites - Location Directory Demo
https://banner-childish-destiny.sbx.pgsdemo.com/

### Author: Luc Yuki Marrie

This repository powers a location directory based on [sandbox account ID 2992605](https://sandbox.yext.com/s/2992605/entities2#).
This repo aims to help you visualize the interaction between a developer's front-end code vs. a directory structure in the Knowledge Graph.

#### Steps to Replicate (Command Line)
- `git clone https://github.com/lymarrie/directory-demo`
- `yext init 2992605 -u sandbox`
- `yext sites generate-test-data`
  - (note, you will have to change the [locales array here](https://github.com/lymarrie/directory-demo/blob/main/templates/location.hbs#L26) to `["en"]` in order for this command to work... unknown bug) 
- `yext sites develop`

## Background 
At a high level, the Directory Manager:
1. Accepts a set of location entities as input ("base entities")
2. Creates several CaC resources based on user configuration - such as entity types, entities, fields, saved filters, streams
3. Adds these resources to the account
4. Assigns relationships between all entities in the resulting Knowledge Graph (locations <--> cities, cities <--> states, states <--> countries, etc.)

The end result is a "directory" of entities. 

## Knowledge Graph
<div>
  <img src="https://a.mktgcdn.com/p/Prm0afEGm0AbjpossLKl_xwAiLQa86W5ye-kBDW4XGQ/100x100.png">
</div>

The example Knowledge Graph contains 7 entities:
- 5 in the United States
- 1 in the U.K.
- 1 in Germany

As such, the resulting Knowledge Graph directory consists of the following entity types - and appropriately, you will find a Handlebars template in this repository dedicated to each one:
- ce_root (`index.hbs`)
- ce_country (`country.hbs`)
- ce_state (`state.hbs`)
- ce_city (`city.hbs`)
- location (`location.hbs`)

## Stream / Page Template Configuration
<div>
  <img src="https://a.mktgcdn.com/p/oDWUmHKDb6fLTy_U9VEgPcJqpsVnHLU58CoFLe_Q2c4/100x100.png">
</div>

Some important custom fields to call out are:
- c_parents (array containing full ancestry of parents for each entity)
  - e.g. c_parents for Yext Headquarters = `[root, US, New York, NYC]`
- c_children (array containing each entity's direct children - i.e. entities one level below it in terms of the directory tree)
  - e.g. c_children for New York State = `[New York City]`
  - c_children for United States = `[California, Florida, New York]`

## Directory Features
#### Breadcrumbs
If you navigate to [partials/breadcrumbs.hbs](https://github.com/lymarrie/directory-demo/blob/main/partials/breadcrumbs.hbs), you can see my implementation of breadcrumbs. Each entity profile from Streams will include a "parents" array that contains the full ancestry of that entity. Therefore, all that needs to be done is iterate through the array - start to finish - to generate breadcrumbs.

```
<div class="py-5 text-2xl flex justify-right gap-x-3">
    {{#each c_parents}}
        <a href="/{{this.slug}}" class="font-bold hover:underline">{{this.name}}<a/>
        <span class="pr-5"> > </span>
    {{/each}}
    <span class="">{{name}}</span>
</div>
```

#### Skip-Level Links
- There are existing implementations configured to "skip a level" if a link on a page only has a single child entity. One example is [Taco Bell](https://locations.tacobell.com/ct.html); upon clicking "Stamford", you are directed straight to the location entity in Stamford (rather than to a Stamford city page - which can still be accessed via URL).
  - This type of functionality is easily configurable in Sites as well - and can be seen on [line 59 of my state.hbs file](https://github.com/lymarrie/directory-demo/blob/main/templates/state.hbs#L59).
```    
{{!-- Skip level if city only has 1 child --}}
    {{#if (compare (length c_children) 1) }}
        <a href="/{{c_children.[0].slug}}" class="text-xl pl-3 hover:underline">
            {{this.name}} ({{length c_children}})
        </a>
    {{else}}
        <a href="/{{this.slug}}" class="text-xl pl-3 hover:underline">
            {{this.name}} ({{length c_children}})
        </a>                
    {{/if}}
```
- You can observe two behaviors I've implemented for fun from the [countries page](https://github.com/lymarrie/directory-demo/blob/main/templates/country.hbs) as well:
  - Clicking on a city in the UK kicks you straight to the leaf page
  - Clicking on a city in Germany takes you to a city page insteaad 

### Sorting
This is a small feature, but you'll notice all alphabetical sorting of locations is performed at the [template level](https://github.com/lymarrie/directory-demo/blob/main/templates/country.hbs#L68).
```
{{#each (sortBy c_children "name")}}
   ... pseudo code listing all children
{{/each}}
```

### i18n Pages
- The Knowledge Graph also contains Spanish and German language profiles for entities across each level of the directory.
- For example, if you view the [United States entity](https://sandbox.yext.com/s/2992605/entity/edit3?entityIds=16838628), you can examine the alternate language profiles towards the bottom-right.
- To make sure this Graph plays nicely with the frontend code:
  - I added `[[localeCode]]` as an embedded value for the slug field on the primary language profile for all entities
    - e.g. Slug = us/ny/new-york/loc1-[[localeCode]].html
  - At the template level, ensured that the localization array reflected the language profiles defined in the Knowledge Graph
    - e.g. `"locales": ["en", "es", "de"]`

Putting that all together, Knowledge Graph and Streams ensure that any slug values used as hrefs on the Pages side are embedded with the appropriate locale codes. As such, you can traverse the entire directory site in a single locale with ease. Refer to the Spanish and German versions of the site below:
- [https://banner-childish-destiny.sbx.pgsdemo.com/index-es.html](https://banner-childish-destiny.sbx.pgsdemo.com/index-es.html)
- [https://banner-childish-destiny.sbx.pgsdemo.com/index-de.html](https://banner-childish-destiny.sbx.pgsdemo.com/index-de.html)

### One Caveat
<div><img src="https://i.ytimg.com/vi/XPib9KoFYw4/maxresdefault.jpg" width="300px"></div>
- The Knowledge Graph in the account was created manually by me, not by the Directory Manager. I have structured the graph more or less identically to the expected output of the Directory Manager 
