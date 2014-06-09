<article class="post-131190 post type-post status-publish format-standard hentry
category-css-popular category-javascript tag-css tag-javascript tag-responsive-
web-design tag-svg tag-techniques post
">
If you haven’t seen [Joe Harrison][1]’s [responsive icons][2] technique yet,
you’ll most probably be impressed as much as I was when I first discovered it. 
In this article, I’d like to explore what we can do with SVG beyond “traditional
” scalable vector graphics that are used to replace bitmap PNGs. In fact, we can
see**SVG as an independent module** that encapsulates CSS for the customization
of views as well as the responsive behavior that also encapsulates JavaScript 
for the interaction logic.

Now, let’s dig a bit deeper into this technique.

### Responsive SVG: The Hobo’s Method

Harrison’s Responsive Icons website is implemented pretty simply. It follows
a well-known technique, image sprites. If you’re not familiar with sprites, let 
me explain. Image spriting is a technique that was previously used only for 
raster images to combat poor network performance. The idea is to**combine a lot
of small images into a single file**, so that the client has to download only a
single image from the server.

You would also use CSS to shift the image around and display only the part that
you need for a particular element, saving the user the overhead of having to 
download every image individually. (Read more about[sprites on CSS-Tricks][3]

Harrison’s technique does the same thing, except with SVG instead of PNGs.
This is what all of his icons combined into a single file would look like:

![All Icons combined in a single SVG file.][4]  
*All Icons combined in a single SVG file. [Large View][5].*

This file, then, would be set as the background of a container in which one of
these icon would need to be displayed:

    .icon {
    	width: 300px;
    	height: 300px;
    	background: url(../images/home_sprite.svg);
    	background-position: center top;
    }

The example above is simple enough but has some problems. The solution is not
portable enough. In fact, two parts are needed to make the method work: external
CSS and an SVG sprite.

### Responsive SVG: The Poor Man’s Method

Because CSS can be defined within SVG itself, let’s revise the example above
to encapsulate the icon and to make it portable.

First, let’s remove all of the spatial shifts of the icons in the sprite. Of
course, that leaves us with a layered mess of icons:

See the Pen [inxym][6] by Ilya Pukhalski ([@pukhalski][7]) on [CodePen][8].

Then, let’s rearrange all of the shapes and group them by icon, adding an 
`.icon` class to each group, as well as numbers to be able to identify each one
we want (so,`#home_icon_0`, `#home_icon_1` and up to `#home_icon_8`):

    <svg>
    	<g id="home_icon_0" class="icon">
    		<!-- paths and shapes -->
    	</g>
     
    	<!-- ... -->
     
    	<g id="home_icon_8" class="icon">
    		<!-- paths and shapes -->
    	</g>
    </svg>

Now, we’re ready to add media queries, so that we can select the icon in the
SVG file that we want to display. For this purpose, writing the CSS directly in 
the`<svg>` tag is possible using `<defs>` tags.

    <svg>
    	<defs>
    		<style>
    		/* Hide all of the icons first. */
    		.icon {
    			display: none;
    		}
    
    		/* Display the first one. */
    		#home_icon_0 {
    			display: block;
    		}
    
    		/* Display the desired icon and hide the others according to the viewport's size. */
    		@media screen and (min-width: 25em) {
    
    			#home_icon_0 {
    				display: none;
    			}
    
    			#home_icon_1 {
    				display: block;
    			}
    		}
    
    		@media screen and (min-width: 30em) {
    			#home_icon_1 {
    				display: none;
    			}
    
    			#home_icon_2 {
    				display: block;
    			}
    		}
    
    		/* And so on */
    
    		</style>
    	</defs>
    
    <!-- Icon groups go here -->
    
    </svg>

As a result, the same icon now adapts to the viewport’s size — except now,
the CSS rules, media queries and SVG shapes are all encapsulated in the SVG file
itself. Resize your browser to see how the example below works:

See the Pen [uxIKB][9] by Ilya Pukhalski ([@pukhalski][7]) on [CodePen][8].

### Responsive SVG: The Man With A Gun’s Method

The example above looks better than the first one, but questions remain:

*   Could the responsive SVG be delivered in a better way?
*   Is following a responsive approach for laying out the icons and customizing
    elements possible, rather than just hiding and showing parts of the file?
   

Looking to the [content choreography][10] and layout restructuring tricks that
we rely on for responsive Web design on a daily basis, we can improve our 
prototype even still. We’ll use responsive design, shape restructuring and 
transformations to adapt the icons to different viewport sizes.

First, let’s redraw the biggest and most detailed house icon in our SVG
sprite file, splitting all of the paths and joined shapes into elemental shapes.
The result is much more readable, and applying any transformations to any part 
of the icon is now possible:

See the Pen [Azqyn][11] by Ilya Pukhalski ([@pukhalski][7]) on [CodePen][8].

Our redrawn icon looks the same as the biggest one from the sprite but contains
many more shapes and takes up a bit more space. The magic is that we’ll add 
media queries and transformations to the new variant, transforming the shapes of
the icon itself to get the same result as the SVG sprite:

    <svg>
    	<defs>
    		<style>
    		@media screen and (max-width: 65em) {
    
    			#door-shadow, #tube-shadow, .backyard {
    				display: none;
    			}
    
    			#door-body {
    				fill: white;
    			}
    
    			#door-handle {
    				fill: #E55C3C;
    			}
    
    			#door-body, #door-handle {
    				-ms-transform: translate(0,0);
    				-webkit-transform: translate(0,0);
    				transform: translate(0,0);
    			}
    
    			#window {
    				-ms-transform: translate(0,0) scale(1);
    				-webkit-transform: translate(0,0) scale(1);
    				transform: translate(0,0) scale(1);
    			}
    
    			#house-body {
    				-ms-transform: scaleX(1) translate(0, 0);    
    				-webkit-transform: scaleX(1) translate(0, 0);    
    				transform: scaleX(1) translate(0, 0);    
    			}
    
    			#tube-body {
    				-ms-transform: translate(0, 0);
    				-webkit-transform: translate(0, 0);
    				transform: translate(0, 0);
    			}
    
    			#tube-upper {
    				-ms-transform: translate(0, 0);
    				-webkit-transform: translate(0, 0);
    				transform: translate(0, 0);
    			}
    		}
    
    		/* And so on */
    
    		</style>
    	</defs>
    
    <!-- Icon groups go here -->
    
    </svg>

Once we’ve added a bit of transformation magic, the icon will behave just
like Joe Harrison’s SVG sprite but will contain all of the logic within itself. 
Open the example below in a new window and resize it to see all of the icon 
variants.

See the Pen [hFLDG][12] by Ilya Pukhalski ([@pukhalski][7]) on [CodePen][8].

#### Adapting the Icon to the Parent Container’s Size

One more thing. Making the icon respond to changes in its parent container (at
least the width and height) is possible, too.

To do this, first, I tried to load the SVG file in an `img` element, wrapped in
a`div`. But not even one media query in the SVG file seemed to work.

My second attempt was to load the icon in the flexible `object` element,
wrapped in a`div`. Doing that made all of the media queries work. And making an
object fill the space of its parent is now possible, too. (Don’t forget to set 
the width and height attributes of the`svg` element to 100% or to remove them
completely.
)

    <div style="width: 100%; margin: 0 auto;">
    	<object>
    		<embed src="responsive3.svg" style="width: 100%; height: auto;" />
    	</object>
    </div>
    

As for other ways to embed SVG with media queries and transforms, you could use
SVG as a background image for any block element. Inline SVG is allowed as well, 
but the media queries would respond to the viewport.

The example below demonstrates how the icon responds to different container
sizes. The media queries in the SVG handle how the icon is drawn, according to 
the dimensions that the SVG is to be rendered in. Here are eight blocks with 
different sizes, embedded with**one and the same** SVG file.

See the Pen [hszLl][13] by Ilya Pukhalski ([@pukhalski][7]) on [CodePen][8].

#### Adding JavaScript to SVG

More good news! The SVG file may encapsulate not only CSS, but JavaScript, too
. In essence, we can regard an included SVG file as an independent module to 
which any old markup language may be applied.

JavaScript in SVG will work perfectly when embedded inline in the 
`<object>` element. Such a wonderful world, huh?

#### Browser Support

This last and most complex method of using SVG with media queries and
transformations behaves perfectly in the following browser versions:

*   Internet Explorer 9+
*   Firefox 17+
*   Chrome 17+
*   Opera 15+
*   Safari 6.0+
*   Safari on iOS 6.0+
*   Android browser on Android 3.0+

The technique might work in the old versions of browsers, but some
transformations, such as scaling, wouldn’t be applied.

### Conclusion

Responsive SVG icons can be used in a lot of ways, including the following:

*   responsive advertisements (the ad content would occupy the space given to
    it by the document, and considering that CSS and JavaScript are allowed, most of
    the action would be contained to a single SVG file per ad
    );
*   logos;
*   application icons.

I should state that nothing is wrong with the sprites technique proposed by Joe
Harrison. It works, and it’s necessary for some purposes!

Thoughts? Feedback? I look forward to your input in the comments section below
.

Cheers, and have fun!

*(al, ml, il)*</article>

 [1]: http://www.joeharrison.co.uk
 [2]: http://responsiveicons.co.uk
 [3]: http://css-tricks.com/css-sprites/
 [4]: img/1-giant-500.png "All Icons combined in a single SVG file."
 [5]: http://media.smashingmagazine.com/wp-content/uploads/2014/03/1-giant.png
 [6]: http://codepen.io/pukhalski/pen/inxym
 [7]: http://codepen.io/pukhalski
 [8]: http://codepen.io
 [9]: http://codepen.io/pukhalski/pen/uxIKB

 [10]: http://www.smashingmagazine.com/2013/04/25/maintain-hierarchy-content-choreography/
 [11]: http://codepen.io/pukhalski/pen/Azqyn
 [12]: http://codepen.io/pukhalski/pen/hFLDG
 [13]: http://codepen.io/pukhalski/pen/hszLl