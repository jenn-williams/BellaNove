# Bella Nove
Work from July 16 to August 22.

## Contents
- [Code](README.md#code)
   - [The Gem Child Theme](README.md#The-Gem-Child-Theme)
   - [Google Fonts](README.md#Google-Fonts)
   - [Refresh CSS](README.md#Refresh-CSS)
   - [ReferralCandy Code](README.md#ReferralCandy-Code)
   - [Add Product Images to Confirmation Email](README.md#Add-Product-Images-to-Confirmation-Email)
   - ["Add to Wishlist" within Cart Page](README.md#"Add-to-Wishlist"-within-Cart-Page)
   - [User Roles Issue](README.md#User-Roles-Issue)
   - [3 Items per Row on "All" Page](README.md#3-Items-per-Row-on-"All"-Page)
- [Design/UX](README.md#design/ux)
   - [Top Menu Consolidation/UX Improvements](README.md#Top-Menu-Consolidation/UX-Improvements)
   - [Consolidating Dashboard and Edit Account Pages](README.md#Consolidating-Dashboard-and-Edit-Account-Pages)
   - [Add a Footer Menu](README.md#Add-a-Footer-Menu)
   - [Format Contact and Gift Form Pages](README.md#Format-Contact-and-Gift-Form-Pages)
   - [Add Size Guide](README.md#Add-Size-Guide)
   - [Remove Buttons on Product Cards](README.md#Remove-Buttons-on-Product-Cards)
   - [Mini-Cart Scroll](README.md#Mini-Cart-Scroll)
   - [Gifting UX](README.md#Gifting-UX)
   - ["Registration" Page](README.md#"Registration"-Page)
   - [Uniform Fonts / Alignment / Other Added CSS](README.md#Uniform-Fonts-/-Alignment-/-Other-Added-CSS)
- [Plugins](README.md#plugins)
   - [Product Reviews](README.md#Product-Reviews)
   - [Sitemap/SEO](README.md#Sitemap/SEO)
   - [Quickview](README.md#Quickview)
- [Other](README.md#other)
   - [Staging Site](README.md#Staging-Site)
   - [Databases](README.md#Databases)
   - [Cloudflare](README.md#Cloudflare)
   - [Site Optimization](README.md#Site-Optimization)
   - [FB Pixel](README.md#FB-Pixel)


## Code
### The Gem Child Theme
- **Problem**: The Gem Child Theme existed but changes made in these files did not show up on the website.
- **Solution**: Deleted the old child theme files, recopied the required files from TheGem theme, and found that the `functions.php` file had a closing tag (`?>`) at the end of the file. Removing the closing tag solved the problem. 

Required files for a child theme are: `functions.php` and `style.css` that are placed within the child theme directory (a folder inside themes called `thegem-child-new`. The `style.css` file must have a snippet like this at the top of the file:
```
/*
 Theme Name:   The Gem Child
 Theme URI:    http://codex-themes.com/thegem/
 Description:  The Gem Child Theme
 Author:       Jennifer Williams
 Template:     thegem
 Version:      1.0.0
 License:      GNU General Public License v2 or later
 License URI:  http://www.gnu.org/licenses/gpl-2.0.html
 Tags:         
 Text Domain:  thegem-child
*/
```

Within our child theme folder we also have a `header.php` file, `footer.php` file, a `Woocommerce` folder, and a `custom.css` file. All of the CSS that I have added is within `custom.css`. Any other additional `php` files would be placed in this folder as well.

For more information about child theme set up see this link: [WP Child Themes](https://codex.wordpress.org/Child_Themes)


### Google Fonts
- **Problem**: One of our fonts, `Montserrat`, was not found by all browers.
- **Solution**: Added a code snippet from Google Fonts to The Gem Child Theme `header.php` file inside the `<head>` tag (see code snippet)
```
<link href="https://fonts.googleapis.com/css?family=Montserrat|Source+Sans+Pro" rel="stylesheet">  
```

### Refresh CSS
- **Problem**: Edits to the CSS in the child theme folder do not show up immediately.
- **Solution**: Add code in `functions.php` so that the CSS is refreshed immediately:
```
function thegemchild_enqueue_styles() {
    
    wp_enqueue_style( 'thegem-child-style', get_stylesheet_directory_uri() . '/style.css', array( $parent_style ), wp_get_theme()->get('Version'));
    wp_enqueue_style('thegem-child-custom', get_stylesheet_directory_uri().'/custom.css', array( 'thegem-child-style' ), filemtime( get_stylesheet_directory() . '/custom.css' ));
}
add_action('wp_enqueue_scripts', 'thegemchild_enqueue_styles',1000);
``` 

### ReferralCandy Code
- **Implementation**: Add required code to the end of each page's `<body>` section using the "Head, Footer and Post Injections" plugin. In the section "Before the </Body> Closing Tag (Footer)" add the following code:
```
<div id="refcandy-poprocks" data-id="i69t3r7kq8mabouco6lz80on9" data-location="right" data-minimized="no" data-version="2"></div>
<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0];if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src="//portal.referralcandy.com/assets/widgets/refcandy-poprocks.js";fjs.parentNode.insertBefore(js,fjs);}}(document,"script","refcandy-poprocks-js");</script>
```

### Add Product Images to Confirmation Email
- **Problem**: We would like product images to be included in the confimation email sent after a user makes an order.
- **Solution**: I made a copy of the Woocommerce template file `email-order-details.php`, and added it to the child theme folder (Woocommerce > Templates > Emails > Plain), then configured the following settings:
```
echo "\n" . wc_get_email_order_items( $order, array( // WPCS: XSS ok.
	'show_sku'      => $sent_to_admin,
	'show_image'    => true,
	'image_size'    => array( 60, 80 ),
	'plain_text'    => true,
	'sent_to_admin' => $sent_to_admin,
) );
```
I also added the following code into the child theme `functions.php` file to make sure the arguments are sent correctly.

```
add_filter( 'woocommerce_email_order_items_args', 'iconic_email_order_items_args', 10, 1 );

function iconic_email_order_items_args( $args ) {

    $args['show_image'] = true;
    $args['image_size'] = array( 60, 80 );

    return $args;

}
```
See these links for more info about the code snippets: [1](https://www.cloudways.com/blog/add-product-images-skus-to-woocommerce-order-emails/) [2](https://wordpress.stackexchange.com/questions/274960/woocommerce-3-1-add-product-image-to-order-confirmation-email-not-working)

### "Add to Wishlist" within Cart Page
- **Problem**: We would like to add an "Add to Wishlist" button for each product in a user's cart.
- **Solution**: Add code to `functions.php`:
```
if(!function_exists('yith_woocommerce_add_wishlist_button_name')) {
    function yith_woocommerce_add_wishlist_button_name($product_name, $cart_item, $cart_item_key)
    {

        return $product_name .' '. do_shortcode( "[yith_wcwl_add_to_wishlist product_id=".$cart_item['product_id']."]");
    }

    add_filter('woocommerce_cart_item_name', 'yith_woocommerce_add_wishlist_button_name', 10, 3);
}
```

### User Roles Issue
- **Problem**: When a user signs up for membership and makes the payment, their role is "Subscriber" instead of "Starter Closet", "Enhanced Closet", and "Ultimate Closet" and this affects the min/max cart rule.
- **Solution**: This code was previously in the file `changeroles.php` in the `mu-plugins` folder within `wp-content`:
```
add_action( 'ms_model_relationship_create_ms_relationship_before', 'ms_controller_member_assign_memberships_done_cb', 99, 4 );
function ms_controller_member_assign_memberships_done_cb( $membership_id, $user_id, $gateway_id, $move_from_id ) {
	$user = new WP_User( $user_id );
	switch( $membership_id ){
		case 3702:
			$user->set_role( 'starter_closet' );
			break;
		
		case 3722:
			$user->set_role( 'enhanced_closet' );
			break;
		
		case 3723:
			$user->set_role( 'ultimate_closet' );
			break;
	}
}


add_action( 'ms_model_event', 'my_event_handler', 10, 2 );
function my_event_handler( $event, $data ) {
	$member = false;
	$subscription = false;
	$membership = false;
	
	switch ( $event->type ) {
		case MS_Model_Event::TYPE_MS_CANCELED:
			// A membership was cancelled - either by Admin or by the member.
			// No more payments will be made but member has access until current period ends.
			$subscription = $data;
			$membership = $data->get_membership();
			$member = $subscription->get_member();
			assign_default_role( $member->id );
			break;
		case MS_Model_Event::TYPE_MS_DEACTIVATED:
			// A membership was permanently deactivated. Member has no access anymore.
			$subscription = $data;
			$membership = $data->get_membership();
			$member = $subscription->get_member();
			assign_default_role( $member->id );
			break;
	}
	
}
function assign_default_role( $user_id = 0 ){
	$user = new WP_User( $user_id );
	$user->set_role( 'subscriber' );
}
```
I tried removing `changeroles.php` and adding the first action and corresponding function to `functions.php` in the child folder and the problem was resolved.
It is unclear exactly why, but the second action call seemed to be affecting the user's role after they make a payment. So the following snippet of code is no longer included in the codebase:
```
add_action( 'ms_model_event', 'my_event_handler', 10, 2 );
function my_event_handler( $event, $data ) {
	$member = false;
	$subscription = false;
	$membership = false;
	
	switch ( $event->type ) {
		case MS_Model_Event::TYPE_MS_CANCELED:
			// A membership was cancelled - either by Admin or by the member.
			// No more payments will be made but member has access until current period ends.
			$subscription = $data;
			$membership = $data->get_membership();
			$member = $subscription->get_member();
			assign_default_role( $member->id );
			break;
		case MS_Model_Event::TYPE_MS_DEACTIVATED:
			// A membership was permanently deactivated. Member has no access anymore.
			$subscription = $data;
			$membership = $data->get_membership();
			$member = $subscription->get_member();
			assign_default_role( $member->id );
			break;
	}
	
}
function assign_default_role( $user_id = 0 ){
	$user = new WP_User( $user_id );
	$user->set_role( 'subscriber' );
}
```

### 3 Items per Row on "All" Page
- **Problems**: The "All" product page (Woocommerce designated "Shop" page) has 4 items per row whereas all of the other product pages have 3 items per row. The smaller image size for the "All" page interferes with the quickview button placement.
- **Solution**: Add the following code to the child theme `functions.php` file:
```
function loop_columns() {
    return 3; // 3 products per row
}
add_filter('loop_shop_columns', 'loop_columns', 999);
```

## Design/UX
### Top Menu Consolidation/UX Improvements
- **Problems**: 1. The large amount of links in the menu is potentially confusing to a user. 2. The BellaNove logo sometimes covers the *Browse Closet* tab. 3. There is a link for *Home* and the BellaNove logo links to *Home* as well.
- **Solution**: 
   1) I consolidated the links *Browse Closet* and *Lookbook* into one large "megamenu" tab called *Collections*. This was done by going into Appearance > Menus,selecting the top primary menu, adding a custom link page called *Collections*, and then enabling the "megamenu" style 1 with 3 columns. One column for the *Lookbook* link and 2 columns for all the links within the *Browse Closet* tab (*All*, *Bottoms*, *Dresses*, *Outerwear*, and *Tops*). I have included an image of the link order and will list the rest of the settings.

   ![Image](images/collections_tab_menu.png)

    - *Get Inspired!*: col width = 300px, check "Don't link"
    - *Browse Our Collection*: col width = 600px, check "Don't link"
    - *Lookbook*: col width 300px, click "Make Clickable on Mobile" and "This item should start a new row"
    - *All*: col width 300px, click "Make Clickable on Mobile"
    - *Bottoms*: col width 300px, click "Make Clickable on Mobile"
    -(space): this was added to keep the links inline under the correct label; col width 300px, click "Don't link", "Don't show", and "This item should start a new row"
    - *Dresses*: col width 300px, click "Make Clickable on Mobile"
    - *Outerwear*: col width 300px, click "Make Clickable on Mobile"
    -(space x2): this was added to keep the links inline under the correct label; col width 600px, click "Don't link", "Don't show", and "This item should start a new row"
    - *Tops*: col width 300px, click "Make Clickable on Mobile"

        Design-wise, I wanted to make the labels look different than the links, so I added in some css to achieve this. There is also some css to fix alignment of the *Tops* link. (see code snippet below from `custom.css` in The Gem Child Theme)
        ```
        #primary-menu.no-responsive > li.megamenu-enable > ul > li span.megamenu-column-header a.mega-no-link {
	    border-bottom: 1px solid #dfe5e8;
	    font-family: 'Montserrat', Arial, sans-serif;
	    font-size: 16px;
	    font-weight: 500;
	    padding-bottom: 20px;
	    padding-top: 10px;
	    text-align: center;
        }

        #primary-menu.no-responsive > li.megamenu-enable > ul > li span.megamenu-column-header {
	    border-bottom: none;
        }

        #primary-menu.no-responsive > li.megamenu-enable.megamenu-style-default > ul > li#menu-item-26592 {
	    margin-left: 60px;
        }

        #primary-menu.no-responsive > li.megamenu-enable > ul > li {
	    text-align: center;
	    vertical-align: middle;
        }
        ```

        With this change there are also additional settings for mobile, which include hiding the labels *Get Inspired!* and *Browse Our Collection*. There is also some css to fix alignment of the *Tops* link. (see code snippet below from `custom.css` in The Gem Child Theme)
        ```
        @media only screen and (max-width: 980px) {
	    .mobile-menu-layout-default .primary-navigation.responsive .dl-menu.dl-subview li.dl-subviewopen > .dl-submenu > li#menu-item-28906, .mobile-menu-layout-default .primary-navigation.responsive .dl-menu.dl-subview li.dl-subviewopen > .dl-submenu > li#menu-item-28905, .mobile-menu-layout-default .primary-navigation.responsive .dl-menu.dl-subview li.dl-subviewopen > .dl-submenu > li#menu-item-28916, .mobile-menu-layout-default .primary-navigation.responsive .dl-menu.dl-subview li.dl-subviewopen > .dl-submenu > li#menu-item-28917 {
		display: none;
	    }

	    #primary-menu.no-responsive > li.megamenu-enable.megamenu-style-default > ul > li#menu-item-26592 {
	    margin-left: 0;
	    }
        ```
    2) Move the BellaNove logo to above the top menu
    - This is done within Appearance > Theme Options > Menu and Header (see image for details)
    
	![Image](images/logo_alignment.png)

### Consolidating Dashboard and Edit Account Pages
- **Problem**: Woocommerce and Membership2 both have account pages with important information on them but it is confusing for the user to have these as 2 separate pages.
- **Solution**: Consolidate the account pages by adding shortcode for both the Woocommerce account `[woocommerce_my_account order_count="15"]` and the Membership2 account `[ms-membership-account]` on a single page (My Account) and ensure this page is set to Account/My Account page for both Woocommerce and Membership2. (It is the link *Dashboard* under the *Account* tab in the main menu. I also added additional CSS in the child theme `custom.css`:

### Add a Footer Menu
- **Problem**: We have pages that are important to link to on every page (e.g. Contact), but should not be included on the top primary menu.
- **Solution**: Add a footer menu within Appearance > Menus

### Format Contact and Gift Form Pages
- **Problem**: These form pages were all left aligned but it makes more sense to have them centered.
- **Solution**: Within WPBakery Page Builder, I changed the style to 3 column and added a 15% left padding on the column

### Add Size Guide
- **Problem**: There is no size guide on the website, but we want users to be aware of the more specific measurements (e.g. armpit circumfrence)
- **Solution**: The size guide was added to all product pages via Appearance > General > Woocommerce settings (see image)

![Image](images/size_guide.png)

Additionally, a link to the size guide was added to the "Profile" page where the user inputs their measurements. This can be added within Users > Profile Fields (see image)

![Image](images/size_guide_2.png)

### Remove Buttons on Product Cards
- **Problem**: There were 3 buttons on the bottom of the product cards: 1 for add to wishlist (heart), and 2 that linked to the product page.
- **Solution**: Remove all but the heart button using the following CSS in `custom.css` in the child theme folder
```
.products .product-bottom .bottom-product-link, .products .product-bottom .add_to_cart_button {
	display: none;
}
```
### Mini-Cart Scroll
- **Problem**: When the cart has a lot of items, the "checkout" and "view cart" button are no longer visible and users cannot scroll down the list.
- **Solution**: Enable scrolling by adding this CSS to `custom.css` in the child theme folder.
```
#primary-menu.no-responsive > li.menu-item-cart .widget_shopping_cart_content ul.cart_list {
	max-height: 500px;
	overflow-y: scroll;
}
```

### Gifting UX
- **Problem**: The current process for gifting a closet subscription is confusing and not very user friendly.
- **Solution**: Given that the subscription purchase process and checkout process each rely on 2 different plugins, Woocommerce and Membership2, it is impossible to allow the user to purchase a subscription through the regular checkout. However, I figured out a way to make the process a bit smoother. First, I activated the Yith Gift Card plugin and then created the 3 different "gift card" products with the following settings: 

![Image](images/gift_card_product_data.png)

Next, I created a new "Gift" page to hold all of the gift card products. For each of the individual product pages, I added some shortcode to the product short description: 

![Image](images/gift_card_description.png)

This enables a button that says "Buy Now" and links to the same membership purchasing tract that it did before. Because there is no price associated with the gift card product, it shows some text "This product cannot be purchased" I also added some CSS to hide this text in the child theme `custom.css`:
```
.gift-cards_form p {
	visibility: hidden;
}
```
Screenshot of gift page:

![Image](images/gift_page.png)

Screenshot of gift card product page:

![Image](images/gift_product_page.png)

### "Registration" Page
- **Problem**: The Membership2 defined "Registration" page does not work the way we want it to. Specifically, we want to make sure users have the option to cancel their membership here. This page is where users are taken when they click "Change" in their dashboard page, like below:

![Image](images/change_membership.png)

- **Solution**: I created a new page "Registration" that contains the shortcode: `[ms-membership-signup]`, then edited membership descriptions within Membership2 > Memberships and then clicking edit for each membership. I added the following CSS in the child theme `custom.css` file to make the page look a bit nicer:
```
.ms-top-bar h4 span.ms-title {
	font-family: 'Montserrat', Arial, sans-serif;
}

.ms-signup .ms-top-bar h4 {
	border-bottom: 1px solid #ddd;
}

.ms-signup .ms-top-bar, .ms-signup .ms-bottom-bar{
	background: none;
}

.ms-signup {
	border: none;
}

.ms-membership-form-wrapper legend {
	font-size: 2em;
	padding-bottom: 10px;
}

.ms-membership-form-wrapper .ms-signup-button {
	float: none;
}

.ms-membership-details-wrapper {
	padding-bottom: 40px;
}
```
Before:

![Image](images/reg_before.png)

After:

![Image](images/reg_after.png)

Further editing to make this page look even nicer would require editing the template files, because all that is displayed on this page is just a shortcode.

### Uniform Fonts / Alignment / Other Added CSS
- **Problem**: Some elements are misaligned, including the main page titles; we want to use the same 2 fonts for everything; miscellaneous
- **Solution**: Add CSS to child theme `custom.css`:
```
/* main page titles */
.page-title-title h1 {
	text-align: center;
}
.page-title-block {
	position: unset;
}
.block-content {
	padding-top: 0;
}

/* home page */
.wpb_wrapper h2 {
	font-family: 'Montserrat', Arial, sans-serif;
}

/*main menu*/
#primary-menu.no-responsive > li > a {
	font-family: 'Montserrat', Arial, sans-serif;
}

/*drop down menus*/
#primary-menu.no-responsive > li.megamenu-enable > ul > li span.megamenu-column-header a, 
#primary-menu.no-responsive > li li > a {
	color: #5f727f;
	font-family: 'Montserrat', Arial, sans-serif;
	font-weight: 400;
}

/* profile and account pages */
#buddypress div.profile h2 {
	font-family: 'Montserrat', Arial, sans-serif;
}
.woocommerce-account h2, .ms-account-wrapper h2 a {
	font-family: 'Montserrat', Arial, sans-serif;
}

/* reviews */
h3.woocommerce-Reviews-title {
	font-family: 'Montserrat', Arial, sans-serif;
}
h3.comment-reply-title {
	font-family: 'Montserrat', Arial, sans-serif;
}
.gem-button {
	font-family: 'Montserrat', Arial, sans-serif;
}
```

## Plugins
### Product Reviews
- **Problem**: We would like to enable users to write reviews and upload pictures for products
- **Solution**: Enable the Woocommerce Photo Reviews plugin with the following settings:
   - General: enable "Enable" and "Mobile"
   - Reviews: enable "Include photos", Max photo size = 2000kb, disable Photo required, Front-end style = "Masonry",  Review display order = newest
   - Rating Counts: disable "Ratings count", enable "Overall rating"
   - Filters: disable
   - Coupons: disable
   - Review Reminders: disable
- **Issue**: The only issue with this plugin is that user's cannot write reviews while logged in; the entire "Write a Review" section only appears on a product page when user is not logged in.

### Sitemap/SEO
- **Problem**: We would like make a sitemap to help search engines crawl our site the way we would like.
- **Solution**: First, I privated all of the pages that are not currently being used, so the sitemap will not link to them. Then I downloaded and enabled the "Google XML Sitemaps" plugin. Settings for this plugin are found within Settings > XML-Sitemap. All of the default settings can be kept except for the "Sitemap Content" section which should be enabled like this: 

![Image](images/xml_sitemap_config.png)

I also excluded some of the pages by their post IDs. These are the Account, Checkout, Legal, Disclaimer, Members,Profile, Thank you, Cart, Size Guide, Wishlist, Login,and Membership pages:

![Image](images/excluded_posts.png)

The post ID number can be seen by editing a given page and looking at the URL (post= ___):

![Image](images/post_id.png)

Additionally, any time that these settings are updated you need to resend the updated sitemap to search engines. Via this link at the top of the settings page:

![Image](images/xml_send_sitemap.png)


### Quickview
- **Problem**: We would like to have a quick view option for each product
- **Solution**: In Appearance > Theme Options > General > Woocommerce settings, I disabled Quick View. This quick view that is included with TheGem does not work correctly for all of our product pages (only worked for the "All" page which is the Woocommerce designated "Shop" page). Instead, I downloaded and enabled a plugin called "Quick View WooCommerce". I configured the following settings from the WordPress backend:

![Image](images/qv_1.png)

![Image](images/qv_2.png)

![Image](images/qv_3.png)


I also added some CSS to the child theme `custom.css` to make the quick view button and pop up look nicer (similar to the way TheGem quickview looked).

Here is styling for the quick view button that appears when you hover over a product:
```
/* quick view button*/
a.xoo-qv-button {
	font-family: 'Montserrat', Arial, sans-serif;
	line-height: 25px;
	text-transform: uppercase;
	top: 80% !important;
	width: 100%;
}
```

Here is styling for the product image and thumbnail images:
```
.xoo-qv-images .woocommerce-main-image img {
	height: 100%;
	width: 100%;
	padding-left: 30px;
	padding-top: 30px;
}
.xoo-qv-images .thumbnails {
	padding-left: 30px;
}
.xoo-qv-images  .attachment-shop_thumbnail {
	height: auto;
	padding-right: 10px;
	padding-top: 15px;
}
```

Here is sizing CSS for the popup box itself:
```
div.xoo-qv-container {
	height: 775px;
	max-height: 775px;
}
```

Here is styling for all of the buttons (including hiding the unnecessary "Browse Wishlist" buttons):
```
div.xoo-qv-summary .single_add_to_cart_button {
	margin-top: 0px;
}
div.xoo-qv-summary table.variations td.label label {
	line-height: 25px;
}
div.xoo-qv-summary table.variations {
	margin-bottom: 0px;
}
.xoo-qv-plink {
	background-color: rgb(185, 118, 167);
	border-radius: 3px;
	font-family: 'Montserrat', Arial, sans-serif;
}
div.yith-wcwl-wishlistexistsbrowse, div.yith-wcwl-wishlistaddedbrowse {
	display: none;
}
div.yith-wcwl-add-to-wishlist {
	display: inline;
}
div.yith-wcwl-add-button a.add_to_wishlist {
	margin-top: 0px;
}
```

Finally, here is CSS to hide the product metadata, which is also unnecessary for quickview:
```
div.xoo-qv-summary  div.product-meta {
	display: none;
}

```

Quickview Before:

![Image](images/qv_before_1.png)

![Image](images/qv_before_2.png)

Quickview After:

![Image](images/qv_after.png)


## Other

### Staging Site
- Done within GoDaddy account
- Created a subdomain "staging.bellanove.com"
- From the cPanel, go to Installatron
- Click the main site, and then click "Clone"
- Enter "staging.bellanove.com" for "Domain" and make sure the "Directory" input is empty; the rest of the settings can stay at default
- When cloning staging back to the main site, you need to delete the main site first then follow the previous steps, with "Domain" = www.bellanove.com

### Databases
- When deleting and recloning the sites, additional databased will be created
- You can easily delete databases that are currently unused by going to Databases > MySQL databases

### Cloudflare
- We set up a cloudflare account to improve website performance and security.

### Site Optimization
- Image re-sizing was successful for our images, but there are still some large images coming from plugins
- Page caching was successful for our own pages, but most of the pages slowing our site down are not our own so we have no control over them
- Attempted defering some JavaScript but the home page relies on a lot of JS and would not load correctly when I tried to defer it
- Overall, most of the assets slowing us down are coming from plugins, not our own content, so it is beyond our control (unless we want to deactivate the plugins)

### FB Pixel
- The FB pixel still does not work correctly, we've tried a number of different approaches including creating a new pixel, adding additional microtags, deleting and reinstalling the Woocommerce for FB app, and deactivating plugins to see if there was interference there.
