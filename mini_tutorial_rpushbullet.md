Mini-Tutorial for RPushbullet
================
Anthony Raborn, Univeristy of Florida

#### Last updated on October 12, 2018.

# R Script for installing/using RPushbullet

Make sure you have the Pushbullet app (phone) and/or the Pushbullet
extension (browser) set up in order to use this. Go to:
www.pushbullet.com and sign up first\!

Here is a quick function I use to make sure that RPushbullet is
installed on a computer before my script is run.

``` r
is.installed <- function(mypkg){
  is.element(mypkg, installed.packages()[,1])
} 
```

Using the function forces the installation of the `RPushbullet` package.

``` r
if (!is.installed("RPushbullet")){
  install.packages("RPushbullet")
}
```

Change the default error options in R to use RPushbullet to forward your
errors to all your connected devices. (This Can be reset by calling
`options(error = NULL)`\!)

``` r
options(error = function() { 
  RPushbullet::pbPost("note", "Error", geterrmessage())
  if(!interactive()) stop(geterrmessage())
})
```

What this does is send you a Pushbullet notification whenever your R
script hits an error. However, it *will not* be able to tell you if a
power outage, inconsiderate student, or terribly-planned update stopped
the simulation without
    warning.

``` r
library(RPushbullet)
```

    ## Warning: package 'RPushbullet' was built under R version 3.5.1

    ## Attaching RPushbullet version 0.3.1.

    ## No file ~/.rpushbullet.json found. Consider placing the Pushbullet
    ## API key and your device id(s) there.

Check for the warning message `"No file ~/.rpushbullet.json found"`. If
this pops up, you need to find the default or home directory of the
computer (the `"~/"` directory) by calling `dirname("~/")`. This is
where your .json file gets placed.

However, that means you need to create the “.rpushbullet.json” file. The
`Rpushbullet` package can help with that:

``` r
pbSetup(apikey = "youraccesstoken")
```

This will create the .json file for you in your home directory (again,
in `"~/"`). The api key can be found by logging into your Pushbullet
account that you created earlier, going to
<https://www.pushbullet.com#settings>, and clicking on “Create Access
Token”. Then, you can paste the access token in place of
“youraccesstoken” and have your .json file created automatically\!

Then, all you need to do (in addition to appending the code above,
**except** the `pbSetup()` function, into your R script) is remember to
move the .rpushbullet.json file into the home directory of the
computer(s) you’re using for your scripts.

If you need any more detailed help, look at the `RPushbullet` help files
and the package author’s page:
<https://github.com/eddelbuettel/rpushbullet>.

# Copy of Code to Include in the Beginning of your R Scripts

``` r
is.installed <- function(mypkg){
  is.element(mypkg, installed.packages()[,1])
} 
if (!is.installed("RPushbullet")){
  install.packages("RPushbullet")
}
options(error = function() { 
  RPushbullet::pbPost("note", "Error", geterrmessage())
  if(!interactive()) stop(geterrmessage())
})
library(RPushbullet)
```
