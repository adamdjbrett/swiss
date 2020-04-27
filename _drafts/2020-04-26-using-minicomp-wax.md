---
layout: category-post
title:  "Using minicomp/wax"
date:   2020-04-18
excerpt: "Learning how to use minicomp/wax 🐝."
excerpt_separator: <!--more-->
categories: website
tags:
  - jekyll
  - digital-humanities
comments: false
author: adamdjbrett
---
Presently I am working on a project where I am helping a colleague tranistion a set of digital humanities projects from Moveable Type and various CGI and PHP scripts to something more sustainable. We are looking for a system that was fast, responsive, consistent, and relatively easy to use.
We settled on Jekyll and Github Pages to allow for backups, stability, and preservations.

## Challenges
Some of the challenges facing us in this project are:
1. Data recovery of lost and damaged data
2. Updating shockwave flash elements
3. learning to use modern tools

## Getting Starting with minicomp/wax
Using Jekyll and Github Pages provides us with a lot of upsides for the projects longevity. The theme and setup I chose was to go with [/minicomp/wax](https://github.com/minicomp/wax). I have been following along with the [minicomp](https://github.com/minicomp/) for a while now. Philosophically and aesthetically I'm very much on board with what minicomp is doing.
I want to the thank Alex Gil, @elotroalex, [https://github.com/elotroalex](https://github.com/elotroalex) and marii¨, @mnyrop, [https://github.com/mnyrop](https://github.com/mnyrop) and the rest of the folks working on minicomp/wax for all of the awesome work that they are doing. A very special thank you to Marii¨, @mnyrop took the time to look through the repositories, help me troubleshoot issues I was having and to answer all of my questions and I had a ton of questions.

[Minicomp/wax](https://github.com/minicomp/wax) works great for digital humanities projects that are image heavy. It works best with high resolution images and pdfs. It works even better with images and pdfs that are of relatively consistent sizes and shapes.

### Setting up minicomp/wax
[The documentation](https://minicomp.github.io/wiki/wax/) for this template is quite good. Having built several sites in Jekyll now I was able to follow the setup guide and get up and running in an hour. I setup my `csv` and `_data/raw_images` folder as required and that is where I encountered my first issue. by default the image gallery uses `openseadragon` as the viewer and processor of images. My first struggle was figure out how to get the images to display on a page and as a collection. I created an [issue on github](https://github.com/minicomp/wax/issues/78) and Marii @mnyrop responded quickly and pointed out that my images were too small for `IIIF`and instead I should use the simple method. Using simple I was able to complete the setup without issue. The images still weren't displaying right for me and thats when I realized I needed to modify the  `image_viewer`in the YAML Front Matter from: `image_viewer: openseadragon` to `image_viewer: 'simple_image_viewer'`. Once I did this my images displayed on their pages and for the most part displayed perfectly. My two issues with the simple viewer is that panoramas look stretched and simple mode does not seem able to handle folders.

### Using simple derivatives
If you are having issues viewing photos in [minicomp/wax](https://Github.com/minicomp/wax) I recommend following Marii @mnyrops suggesting and using `bundle exec rake wax:derivatives:simple YOUR_COLLECTION_NAME` followed by `bundle exec rake wax:pages YOUR_COLLECTION_NAME` and `bundle exec rake wax:search main`. Simple derivatives worked well with my images of various sizes and as the minicomp/wax documentation notes simple derivatives **does not** handle pdfs and does not allow zooming. In my experience Some images if they are under 300px can look stretched so this is another reminder to make sure your images are high quality.

After running the simple derivatives make sure to update the gallery_item.html layout to use: `image_viewer: 'simple_image_viewer'`


### Add-ons
I used [Jekyll Codex](https://jekyllcodex.org) to do a quick and easy `feed.xml` and `sitemap.xml`. I considered adding [anchor.js](https://github.com/bryanbraun/anchorjs) so that all the headings have a little 🔗 link emoji next to them and you could directly link to the section. This would make moving around in large documents easier and help directly link to sections. I decided ulimately that the last thing this project needed was a more javascript. Plus since Jekyll 4.0 and the Kramdown 2.2.1 the following is more than enough
```
* Do not remove this line (it will not be displayed)
{:toc}
```
