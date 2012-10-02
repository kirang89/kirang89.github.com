---
layout: post
title: "Download a csv in your Play! framework application"
date: 2012-10-03 02:35
comments: true
categories: [Play Framework]
---

In this post, I'll share a simple way to download CSV files in Play Framework. This is especially useful, if you want to export out some reports or analytics data for offline use. So let’s get started.

1. First create a new Play! application (I’m skipping all this for the sake of sticking to the point) 
2. Have a seperate controller/method which handles the generation of CSV files.
3. In that controller add the following code:

{% codeblock CSV download snippet %}
response.contentType = "text/csv";

response.setHeader("Content-Disposition", "attachment;filename="" + <filename> +".csv" + """);

response.writeChunk(<header>); 

response.writeChunk("\n"); 

response.writeChunk(<content>);
{% endcodeblock %}

<strong><u>Note</u>:</strong> Remember to separate your content with a “<strong>,</strong>” so that it will be columnised in the file


