# Sites - Location Directory Demo
https://banner-childish-destiny.sbx.pgsdemo.com/

## Author: Luc Yuki Marrie

This repository powers a location directory based on [sandbox account ID 2992605](https://sandbox.yext.com/s/2992605/entities2#).
This repo should give you a good idea of the interaction between the developer's front-end code and the Knowledge Graph.

### Background
At a high level, the Directory Manager:
1. Accepts a set of location entities as input ("base entities")
2. Creates several CaC resources based on user configuration - such as entity types, entities, fields, saved filters, streams
3. Adds these resources to the account
4. Assigns relationships between all entities in the resulting Knowledge Graph (locations <--> cities, cities <--> states, states <--> countries, etc.)

The end result is a "directory" of entities. 

### Knowledge Graph
The example Knowledge Graph ([sandbox account ID 2992605](https://sandbox.yext.com/s/2992605/entities2#)) contains 7 entities:
- 5 in the United States
- 1 in the U.K.
- 1 in Germany

As such, the resulting Knowledge Graph directory consists of the following entity types - and appropriately, you will find a Handlebars template in this repository dedicated to each one:
- ce_root (index.html)
- ce_country (country.hbs)
- ce_state (state.hbs)
- ce_city (city.hbs)
- location (location.hbs)

### Directory Features
#### Breadcrumbs
If you navigate to [partials/breadcrumbs.hbs](https://github.com/lymarrie/directory-demo/blob/main/partials/breadcrumbs.hbs), you can see my implementation of breadcrumbs. Each entity profile from Streams will include a "parents" array that contains the full ancestry of that entity. Therefore, all that needs to be done is iterate through the array - start to finish - to generate breadcrumbs.  

#### Skip-Level Links
There are existing implementations configured to "skip a level" if a link on a page only has a single child entity. One example is [Taco Bell](https://locations.tacobell.com/ct.html); upon clicking "Stamford", you are directed straight to the location entity in Stamford (rather than to a Stamford city page - which can still be accessed via URL).

This type of functionality is easily configurable in Sites as well - and can be seen on [line 59 of my state.hbs file](https://github.com/lymarrie/directory-demo/blob/main/templates/state.hbs#L59).

To show all possible implementation styles, you can observe the same behavior from the [countries page](https://github.com/lymarrie/directory-demo/blob/main/templates/country.hbs) as well:
- Clicking on a city in the UK kicks you straight to the leaf page
- Clicking on a city in Germany takes you to a city page insteaad 

### One Caveat
- The Knowledge Graph in the account was created manually by me, not by the Directory Manager. I have structured the graph more or less identically to the expected output of the Directory Manager 
