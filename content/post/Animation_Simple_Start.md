---
title: "Visualization: Animation (Simple Start)"
date: 2017-12-31
categories:
- Notebook
tags:
- SAS
- R
- Visualization
thumbnailImagePosition: left
thumbnailImage: //https://s3-eu-west-1.amazonaws.com/static.gapminder.org/GapminderMedia/wp-uploads/20161019161829/screenshot2016.jpg
---

A brief introduction to animation visualization. Simple start with SAS 9.4, R/shiny, Plotly.
<!--more-->


### 1 Overview 

#### Famous Animation Examples

> Visualization is a powerful and influential approach for presenting all types of data, big and small. While even static data visualizations, particularly unique ones, are more persuasive, memorable, and effective as decision aids than tables and text, animation can boost these advantages even further. When paired with a compelling topic and high-quality dataset, well-crafted animated visualizations can be extremely effective at explaining complex concepts and deeply engaging viewers— especially for data showing change across multiple groups or time periods.

* [**Use Animation to Supercharge Data Visuaalization**](https://medium.com/@EvanSinar/use-animation-to-supercharge-data-visualization-cd905a882ad4)

#### How to tell story

> 1. Know when to use animation as a medium
> 2. Structure the story
> 3. Try starting static and animate layers to build the explanation
> 4. Use storyboarding to understand context and develop focus
> 5. Give the Project Constraints

* [How to Turn a Static Visualization Into a Successful Animation](https://visual.ly/blog/how-to-turn-a-static-visualization-into-a-successful-animation/)

### 2 How to build

#### SAS 9.4

* SAS help documentation

* [Creating Animated GIF Images and SVG Documents](http://documentation.sas.com/?docsetId=lrcon&docsetTarget=p04zpqk21h6sczn1gpdcy30s6xjf.htm&docsetVersion=9.4&locale=ja)
* [Examples: Generating Animated Images](http://documentation.sas.com/?docsetId=graphref&docsetTarget=p0so6ik5s8nu4fn12nzdh96nzw2a.htm&docsetVersion=9.4&locale=en)


#### R/Shiny

* Using **sliders** [link](https://shiny.rstudio.com/articles/sliders.html)

> webplot [demo unavailable yet] 

#### Plotly

> plotly R [link](https://plot.ly/r/#animations)
>
> plotly Python [link](https://plot.ly/python/#animations)

### Ways to Make Your Own Animated GIFs
* **The Photoshop Route** [tutorial](https://github.com/lenagroeger/gifs)
* **FFMPEG** [official site](http://ffmpeg.org/)
   * images->video (https://stackoverflow.com/questions/24961127/ffmpeg-create-video-from-images)
   * video->images (https://stackoverflow.com/questions/7468274/ffmpeg-images-to-video-script-anyone)


### My Experience
* how to tell the story is the most important
* if you just want a very simple animation gif, looping with a timeline or other axis, you can build a series of static graphics and turn them into gif using PS
* Excel + PS seems also quite good
* R has powerful ggplot2, plotly for graphics, but neither could output .gif directly. Once you use Shiny, you need to consider the available server
* **FFMPEG is 1 powerful free tool to deal with photoes -> video, video -> photoes or video -> video. ** 