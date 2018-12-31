---
layout: page
title: Site Search
---

Dit is een basis zoekfunctionaliteit gebaseerd op [Lunr.js](https://lunrjs.com/).  
*Het kan even duren voordat het resultaat geladen is!*  
Een alternatief is [google cse]({{ "/search/google.html" | absolute_url }}).

<script src="{{ "/js/lunr.js" | absolute_url }}"></script>

<input class="search" type="text" size="15" id="lunr-search" placeholder="Search..." aria-label="search">
<input class="buttons" type="button" onclick="lunr_search();" value=" Search ">
<p id="count"></p>
<ul id="search-results"></ul>

Tip: zoekargumenten `title:foo` of `text:foo`, of gebruik wildcards `foo*`.

<script>
// create docs
var posts = [
    {% for post in site.posts %}
    {
      "url": {{ post.url | absolute_url | jsonify }},
      "title": {{ post.title | jsonify }},
      "text": {{ post.content | strip_html | jsonify }}
    }{% unless forloop.last %},{% endunless %}
    {% endfor %}
];
// create index
var idx = lunr(function () {
  this.ref('id')
  this.field('title')
  this.field('text')
  for (var item in posts) {
    this.add({
      title: posts[item].title,
      text: posts[item].text,
      id: item
    })
  }
});
// do search
function displayResults(results) {
  var countResults = document.getElementById('count');
  countResults.innerHTML = results.length + ' Result(s) found</p>';
  var searchResults = document.getElementById('search-results');
  if (results.length) {
    var appendString = '';
    for (var item in results) {
      var ref = results[item].ref;
      var searchItem = '<li><a href="' + posts[ref].url + '">' + posts[ref].title + '</a><br>' + posts[ref].text.substring(0,150) + '... </li>';
      appendString += searchItem;
    }
    searchResults.innerHTML = appendString;
  } else {
    searchResults.innerHTML = '<li>No results found</li>';
  }
}
function lunr_search() {
    var query = document.getElementById("lunr-search").value;
    var results = idx.search(query);
    displayResults(results);
}
</script>
