-   [Last updated on October
    15, 2018.](#last-updated-on-october-15-2018.)
-   [R Script for installing/using
    RPushbullet](#r-script-for-installingusing-rpushbullet)
    -   [Ensuring RPushbullet is
        installed](#ensuring-rpushbullet-is-installed)
    -   [Creating an error catch function with
        RPushbullet](#creating-an-error-catch-function-with-rpushbullet)
    -   [Setting checkpoints with
        RPushbullet](#setting-checkpoints-with-rpushbullet)
    -   [Conclusion](#conclusion)
-   [Copy of Code to Include in the Beginning of your R
    Scripts](#copy-of-code-to-include-in-the-beginning-of-your-r-scripts)

#### Last updated on October 15, 2018.

R Script for installing/using RPushbullet
=========================================

Pushbullet is an application designed to share notifications between
your work environments. There are many potential uses for Pushbullet,
but one of the more useful ones for useRs is as a way to keep track of
the progress on your R scripts.

Using my own experiences as an example, a lot of my work involves
running simulation studies. The easiest way to finish simulations in a
reasonable amount of time is to split the conditions across multiple
computers<sup>1</sup>. This generally requires a lot of manual labor:
creating multiple files for the different simulation conditions, saving
the output of the simulation, then moving everything back to a central
location for the final analysis.

R, however, is a little fickle at times. If it encounters an `Error` in
the script, it will completely stop running the script. If you’re
watching all of the computers running your scripts in real time, this
probably isn’t too much of a problem. I tend to try getting my
simulation scripts running over the weekend so that they would be
complete (hopefully!) by the time I came back on Monday. This works well
when you’re using well-written code that is robust within your
simulation, but when you write your own novel functions regularly
mistakes can happen (and happen quite often if you’re like me).

In addition, other users or processes may come in at any time and
somehow turn off your script–particularly when you’re using Windows
computers and they decide they *need* to update **RIGHT NOW** regardless
of what else is going on. Unless you’ve been particularly careful with
your simulation, this can cause you to lose out on a lot of processing
time from something entirely outside of your R code.

My solution? Use the [Pushbullet app](www.pushbullet.com) in conjunction
with the [RPushbullet
package](https://github.com/eddelbuettel/rpushbullet) to let my scripts
“ping” me on all my devices for certain milestones in my scripts–errors,
“checkpoints,” and script completion in particular!

Before you begin using this tutorial, make sure you have the Pushbullet
app (phone) and/or the Pushbullet extension (browser) set up. Go to:
www.pushbullet.com and sign up first!

Ensuring RPushbullet is installed
---------------------------------

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

Creating an error catch function with RPushbullet
-------------------------------------------------

Now that you are certain that your scripts will make sure `RPushbullet`
is available, we then change the default error options in R to use
RPushbullet to forward your errors to all your connected devices. (This
can be reset by calling `options(error = NULL)`!)

``` r
options(error = function() { 
  RPushbullet::pbPost("note", "Error", geterrmessage())
  if(!interactive()) stop(geterrmessage())
})
```

What this does is send you a Pushbullet notification whenever your R
script hits an error. However, it *will not* be able to tell you if a
power outage, inconsiderate student, or terribly-planned update stopped
the simulation without warning.

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
“youraccesstoken” and have your .json file created automatically!

Then, all you need to do (in addition to appending the code above,
**except** the `pbSetup()` function, into your R script) is remember to
move the .rpushbullet.json file into the home directory of the
computer(s) you’re using for your scripts.

Setting checkpoints with RPushbullet
------------------------------------

This can be expanded even further. The `pbPost()` function was
originally used to construct the error catch function before. The
notification from this function can be customized even further. You can
specify the post type, title, and body, include a url and/or filetype,
and specify the specific device recipients or specific emails to notice
(see `help(pbPost)` for more details on the specifics). What I do with
this function is something like this:

``` r
pbPost('note', title='Computer 1', body='Simulation part 1 complete.')
```

Adding lines like these in your simulation code will let you be notified
when specific computers reach specific simulation checkpoints. Even
further, the lack of a checkpoint notification can give you a hint that
something happened to the computer itself (rather than the R code); if
you have a idea of how long each checkpoint should take, and it’s been a
while since the last checkpoint was reached, you may need to rush down
to the lab and see what’s going on!

Here’s a further example you can try. We can use the `Sys.sleep()`
function to put R “on hold” for a specified number of seconds; using a
few of these in a row provides an easy test of your RPushbullet setup!

``` r
library(RPushbullet)
pbPost('note', title='Example 1', body='Starting pbPost trial run.')
Sys.sleep(60) # wait for one minute
pbPost('note', title='Example 1', body='First checkpoint reached.')
Sys.sleep(60) # wait for another minute
pbPost('note', title='Example 1', body='Second checkpoint reached.')
Sys.sleep(45) # wait for 45sec this time
pbPost('note', title='Example 1', body='Third and final checkpoint reached. Trial run over!')
```

If everything is working appropriately, you should receive three
notifications over 2 minutes and 45 seconds from Pushbullet to all of
your connected devices. You should be able to see how helpful this can
be when you’re trying to monitor multiple computers and simulation
conditions simultaneously!

Conclusion
----------

If you need any more detailed help, look at the `RPushbullet` help files
and the package author’s page:
<https://github.com/eddelbuettel/rpushbullet>. You can also email me or
post a message on the issues page of this Github repository!

Copy of Code to Include in the Beginning of your R Scripts
==========================================================

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

1: Now, if you’re a computer wiz, you could parallelize your code across
multiple computer cores or distributed it across computers in a cluster
automatically, but learning that takes a lot of time and may not be a
feasible option.
