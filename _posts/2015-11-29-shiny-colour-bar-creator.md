---
layout: post
title: A Shiny app for creating custom color bars
---

There has been a few odd times that I've needed to create a custom color bar for my figures. 
A really simple solution in R can be found [here](http://www.colbyimaging.com/wiki/statistics/color-bars). Although running this in R is easy enough,
one idea I had was to turn this task into an interactive [Shiny](http://shiny.rstudio.com/) app. So here it is... introducing
[Colour Bar Builder](https://github.com/kcha/colour_bar_builder), a Shiny app for creating custom colour bars interactively.
A demo is available on [shinyapps.io](https://kcha.shinyapps.io/colour_bar_creator).

Putting together a functional app was quick and easy, as all I had to do was to create the Shiny interface.
In addition to the existing R function, another key component that made this app possible 
was the [`colourInput()`](http://deanattali.com/2015/06/28/introducing-shinyjs-colourinput/) input control provided by 
[shinyjs](https://github.com/daattali/shinyjs). This provides the user an
interactive control for choosing colours in just a few lines of code:

{% highlight r %}
colourInput("hi", "Select high colour:", value = "blue"),
colourInput("mid", "Select middle colour:", value = "white"),
colourInput("lo", "Select low colour:", value = "red"),
{% endhighlight %}

To create a colour bar, three colours that define the gradient are required, as 
shown in above code snippet. A colour ramp palette using `colorRampPalette()` is then created based on these selected colours.
The amount of colours in the gradient can be controlled using the slider.
I also added the option to choose from any of the colourblind friendly 
[RColorBrewer](https://cran.r-project.org/web/packages/RColorBrewer/index.html) 
sequential and diverging palettes. Other optional parameters can also be customized, such as the minimum and maximum
values for the y-axis, title, and number of ticks. Taken together, the plot code in `server.R` looks like the following: 

{% highlight r %}
color.bar(
	colorRampPalette(c(input$lo, input$mid, input$hi))(input$numColours),
	min = input$min, max=input$max,
	nticks = input$nticks,
	title = input$title)  
{% endhighlight %}

![Screenshot](http://individual.utoronto.ca/hakevin/images/colour_bar_shiny_1.png)

## Saving the colour bar
The generated colour bar can then
be saved by right-clicking on the image, or saving it as a PDF. The latter was made possible by adding a [download handler](http://shiny.rstudio.com/articles/download.html). In `ui.R`, I added a download button called `"savepdf"`:

{% highlight r %}
downloadButton("savepdf", label = "Download PDF")
{% endhighlight %}

Next, in `server.R`, I just had to program the action of the download button:

{% highlight r linenos %}
output$savepdf <- downloadHandler(
	filename = "colorbar.pdf",
	content = function(file) {
	  pdf(file, height=4, width=2)
	  color.bar(
		colorRampPalette(c(input$lo, input$mid, input$hi))(input$numColours),
		min = input$min, max=input$max,
		nticks = input$nticks,
		title = input$title)  
	  dev.off()
	}
)
{% endhighlight %}

Here, the arguments `filename` and `content` handle the default filename to use 
when saving the PDF and what to save, respectively.
