## Research

{% for pub in site.data.publications %}
**{{ pub.title }}**  
{{ pub.authors }}  
*{{ pub.venue }}*  
[pdf]({{ pub.pdf }})

{% endfor %}
