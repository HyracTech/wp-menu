Things i indentified

When trying to create a js file in /library/www/html/iiab-menu/menu-files/js#  called hyracbox-iiab-menu.js,
i kept getting an error

 [ Error reading lock file ./.hyracbox-iiab-menu.js.swp: Not enough data read ]
 
 but when i reduced the name length to hyracbox.js i din't get the error any more. 
 
 My Pi resources at that point were
               total        used        free      shared  buff/cache   available
Mem:           927M        383M         42M         35M        501M        447M
Swap:          499M          0B        499M


Knowing the Disk Space, Memory Use, and CPU Load: du, df, free, and w
https://learn.adafruit.com/an-illustrated-shell-command-primer/checking-file-space-usage-du-and-df



Easy way to link to external JS library to Wordpress and use it easily in project:
register:

wp_register_script( 'someScript-js', 'https://domain.com/someScript.js' , '', '', true );
this will put the someScript.js at the bottom of our HTML file before 
call:

wp_enqueue_script( 'someScript-js' );


iiab-menu.js was dependent on jquery 2 to run functions like $.Deferred();

But since wordpress uses jquery 1, i got the error 
Cannot read property 'Deferred' of undefined

I solved this by enquing the jquery file that comes with iiab as in the following example.

	$parts = parse_url(site_url());
	$domain_url = $parts['scheme'] . '://' . $parts['host'];
	
		wp_register_script('iiab_menu-jquery', $domain_url . '/common/js/jquery.min.js', array( ), '1.0', true);
	
	wp_register_script('iiab_menu', $domain_url . '/iiab-menu/menu-files/js/iiab-menu.js', array( 'iiab_menu-jquery'), '1.0', true);
	
	wp_enqueue_script( 'iiab_menu-jquery' );
	wp_enqueue_script( 'iiab_menu' );
 
 
 classes that i had to update
 
 class="content-item"
 
 in iiab_menu.js in 'function calcLink(href,module)'
 
 
class="content-icon"
class="content-cell"

id="' + module.menu_id
class="toggleExtraHtml"

The function produced content like the one below

<div style="display: table;">
	<div style="display: table-row;">
		<div class="content-icon">
			<a href="/usb"><img src="/iiab-menu/menu-files/images/content.jpg" alt="USB"> 
		</div>
		<div class="content-cell">
			<h2><a href="/usb">USB</a></h2>
			<p>Insert a USB stick/drive with content in a folder called 'usb' to publish [http://HOST/usb live content here].</p>
			
			<div id="0-en-usb-htmlf" class="toggleExtraHtml"></div>
		</div>
	</div>
</div>

to me it seems not to be fully sanitized..


var menuDefs ={};    //just an empty array

procMenu()  is the one that is actually called to list out the elements of the menuItems array

getMenuDef()  is then called on each menu item

**The name of the menu item has to match the name of the menu defination json file. like en-usb for  en-usb.json **


This async code below was a little confusing at first

----


var scaffold = $.Deferred();
var i;

if (dynamicHtml){
  //$.when(scaffold, getZimVersions, getConfigJson).then(procMenu);
  $.when(scaffold, getZimVersions, getConfigJson).always(procMenu); // ignore errors like kiwix not installed
  // create scaffolding for menu items
  var html = "";
  for (i = 0; i < menuItems.length; i++) {
  	var menu_item_name = menuItems[i];
  	menuDefs[menu_item_name] = {}
  	menuItemDivId = i.toString() + "-" + menu_item_name;
  	menuDefs[menu_item_name]['menu_id'] = menuItemDivId;

  	html += '<div id="' + menuItemDivId + '" class="content-item" dir="auto">&emsp;Attempting to load ' + menu_item_name + ' </div>';
 //
console.log(menu_item_name);
//

  }
  $("#content").html(html);
  $(".toggleExtraHtml").toggle(showFullDisplay);
  scaffold.resolve();
}
else {
  $.when(getConfigJson).then(procStatic);
}
 
 ----
 
 from this line ' when(scaffold, getZimVersions, getConfigJson).always(procMenu); '
 The procMenu function will only run after the scaffold is resolved. Meaning the for loop actually runs before the function is run.  --do more research on this
 
 Also
 menuDefs[menu_item_name]['menu_id'] = menuItemDivId;
 
 since menuDefs is an object, the square brackets is a way to navigate through the object just like you would use menuDefs.menu_item_name.menu_id.   The object holds single menu items/definitions as objects.
 
 in this example we are adding a new property menu_id to each of the menu definitions that are represented in our menuItems array
 
 
 
 

 
 WordPress Theme Development Basics: Required Files & Template Hierarchy
 https://ithemes.com/2017/07/14/wordpress-theme-development-basics/
 
 
Using conditionals in wordpress php files
you can use page ids or slug names
<?php if (is_page('portfolio')) {?>
	//do something
<?php }?>

 
 
Applying specific styling to specific pages /Template file with matching name (slug or ID)
name the file page-'slugname/id'.php   e.g page-portfolio.php

*We can tell users to make a page with a particular name.
e.g page-hyracbox.php

Have several pages share a common layout
*Use templates
You can name the templates whatever you want

Custom page templates
Custom page templates are php files similar to page.php in structure 
What identifies a template is the comment block at the start of the file.
 <?php /* Template Name: CustomPageT1 */ ?>
 
Very useful if you want to include a particular feature or component in many pages in your site.
 
