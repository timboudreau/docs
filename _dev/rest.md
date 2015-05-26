---
title: REST API
weight: 0
---

Syncthing exposes a REST interface over HTTP on the GUI port. This is used by
the GUI code (Javascript) and can be used by other processes wishing to
control syncthing. In most cases both the input and output data is in JSON
format. The interface is subject to change.

## API Key

To use the POST methods, or *any* method when authentication is enabled, an API
key must be set and used. The API key can be generated in the GUI, or set in
the `configuration/gui/apikey` element in the configuration file. To use an
API key, set the request header `X-API-Key` to the API key value.

## Endpoint Index

<ol>
{% assign cats = site.rest | group_by: 'category' | sort: 'name' %}
{% for cat in cats %}
<li>Category "{{cat.name}}"</li>
<ol>{% assign locs = cat.items | group_by: 'loc' | sort: 'name' %}
{% for loc in locs %}<li><a href="#{{loc.name | slugify}}">{{loc.name}}</a></li>{% endfor %}
</ol>{% endfor %}
</ol>

---

## Endpoints

{% assign cats = site.rest | group_by: 'category' | sort: 'name' %}
{% for cat in cats %}
{% assign n1 = forloop.index %}
<h3 id="{{cat.name | slugify}}">Category "{{cat.name}}"</h3>
{% assign locs = cat.items | group_by: 'loc' | sort: 'name' %}
{% for loc in locs %}
{% assign n2 = forloop.index %}
<h4 id="{{loc.name | slugify}}">{{n1}}.{{n2}} &emsp; {{loc.name}}</h4>
{% for node in loc.items %}
{% assign n3 = forloop.index %}
<h5>{{n1}}.{{n2}}.{{n3}} &emsp; {{node.method}}</h5>
<div>
{{node.content | markdownify}}
</div>
{% endfor %}
{% endfor %}
---
{% endfor %}
