# Elementor Freebie Free Implementation

Free system to implement Freebie in Wordpress Elementor

## Introduction

This is a work around the elementor popup and the dynamic conditions (free plugin) to build a smart system to implement the freebie content to your wordpress website.

### Principal benefit of this solution:

-   Easy to use
-   One popup to customize for all download files
-   Integrate the cookie, to returning users
-   Free of charge

# Requirements

-   **Elementor** ([https://wordpress.org/plugins/elementor/](https://wordpress.org/plugins/elementor/))
-   **Elementor Pro** ([https://elementor.com](https://elementor.com/))

# Workflow

![Freebie Workflow](%5Bhttps://www.nicolapavan.it/wp-content/uploads/2023/11/Freebie_NicolaPavan_-scaled.jpg%5D(https://www.nicolapavan.it/wp-content/uploads/2023/11/Freebie_NicolaPavan_-scaled.jpg))

From a multiple webpages you can recall the **subscription popup** with a “logged-download” attribute, when it opens a function scan the “SubmittedContact” cookie and create if wasn’t or skip if it value is true. When the form present, in the popup, submit succefuly trigger the second popup, with the specific attribute.

The **download popup** that could contain multiple element’s in it, a javascript removes all elements hasn’t the right corrisponding “logged-download” value.

# Installation

## Before start

### Custom code

This implementation need a custom script in elementor>custom code

```bash
<script type="text/javascript">
	var $ = jQuery;
	var subscriptionPopupID = **PUT YOUR ID** ; 
var downloadPopupID = **PUT YOUR ID** ;
var expiryDays = 15 ; /* days to expire the cookie*/
$(".elementor-widget-button[logged-download] a").on( "click" , function (){
	//console.log($(this).parent().parent().parent().parent().find('.elementor-element[logged-download]').attr('logged-download'));
	$("body").attr("logged-download" , $(this).parent().parent().parent().parent().find('.elementor-element[logged-download]').attr('logged-download'));
});
	</script>```  

  
### Create the Subscription Popup  
  
generate the popup with the contact form and attach the newsletter API  
  
**Take note of Popup ID / post ID**  
  
you can find in the WP Dashboard > Templates (Elementor) > Popup in shortcode column, exemple  
  
[elementor-template id="4598"]  
  
  
### Create the Downloading Popup  
  
generate the popup with one continer for every download options  
  
**Take note of Popup ID / post ID**  
  
you can find in the WP Dashboard > Templates (Elementor) > Popup in shortcode column, exemple  
  
[elementor-template id="9832"]  
  
  
  
### Create the attribute  
  
Create the logged-download attribute for every download option  
  
There is two ways to create it  
  
- generate a random identification number or name, like "9765374865558" or "CorporateBook"  
- calculate the hash of file  
  
> calculating the hash anonimize the file and help you to the find the correct file  
  
How to calculate the hash:  
  
**Online**  
  
use: [https://emn178.github.io/online-tools/sha256_checksum.html](https://emn178.github.io/online-tools/sha256_checksum.html)  
  
**Mac os**, in terminal (to optain the path drag an drop the file in the window)  
```bash  
shasum .path  

```

**Windows**, in Powershell

```bash
Get-fileHash .path  

```

# Webpage

From your landing page add the download button and trigger the subscription popup

![content tab](https://www.nicolapavan.it/wp-content/uploads/2023/11/Freebie_NicolaPavan_4.png)

and in advance tab add a attribute

```bash
logged-download | YOUR_IDENTIFICATION_NUMBER

```

![advance tab](https://www.nicolapavan.it/wp-content/uploads/2023/11/Freebie_NicolaPavan_5.png)

# Subscription Popup

The subscription popup must has elementor form and below it a html tag, insert in it the following code

```bash
<script type="text/javascript">

var subscriptionPopupID = **PUT YOUR ID** ; 
var downloadPopupID = **PUT YOUR ID** ;
var expiryDays = 15 ; /* days to expire the cookie*/

    function getCookie(name) {
    var nameEQ = name + "=";
    var ca = document.cookie.split(';');
    for(var i=0;i < ca.length;i++) {
        var c = ca[i];
        while (c.charAt(0)==' ') c = c.substring(1,c.length);
        if (c.indexOf(nameEQ) == 0) return c.substring(nameEQ.length,c.length);
    }
    return null;
}

function createCookie(name, value, days) {
  let expires = "";
  if (days) {
    const date = new Date();
    date.setTime(date.getTime() + (days * 24 * 60 * 60 * 1000));
    expires = "; expires=" + date.toUTCString();
  }
  document.cookie = name + "=" + value + expires + "; path=/";
}

  if (document.cookie.replace(/(?:(?:^|.*;\s*)SubmittedContact\s*\=\s*([^;]*).*$)|^.*$/, "$1") == "") {
    createCookie("SubmittedContact", "false", expiryDays); // Creating the cookie with x days expiry
  }

    jQuery( document ).on( 'elementor/popup/show', ( event, id, instance ) => {
	if ( id === subscriptionPopupID ) {

    if (getCookie('SubmittedContact')=='true'){
        var selectorEle = "elementor-" + subscriptionPopupID + " form";
        $(selectorEle).addClass("hide");
        elementorProFrontend.modules.popup.showPopup( { id: downloadPopupID } );
    }
	}
	jQuery( document ).on( 'elementor/popup/hide', ( event, id, instance ) => {
	if ( id === subscriptionPopupID ) {
		$('body').removeAttr('logged-download');
	}
    
} );


</script>

```

# Download Popup

The Download Popup a container or section with the same class as loggin-download attribute selected before and a second one “hide”

The html tag needs goes at the bottom of the page

```bash
<script type="text/javascript">

var subscriptionPopupID = **PUT YOUR ID** ; 
var downloadPopupID = **PUT YOUR ID** ;
var expiryDays = 15 ; /* days to expire the cookie*/

    jQuery( document ).on( 'elementor/popup/show', ( event, id, instance ) => {
	if ( id === downloadPopupID ) {
		var loggeddownload = $("body").attr("logged-download");
       $("."+loggeddownload).removeClass("hide");
       setCookie('SubmittedContact','true',15);
	}
	jQuery( document ).on( 'elementor/popup/hide', ( event, id, instance ) => {
	if ( id === downloadPopupID ) {
		$('body').removeAttr('logged-download');
	}
} );
    
} );


function setCookie(name,value,days) {
    var expires = "";
    if (days) {
        var date = new Date();
        date.setTime(date.getTime() + (days*24*60*60*1000));
        expires = "; expires=" + date.toUTCString();
    }
    document.cookie = name + "=" + (value || "")  + expires + "; path=/";
}

</script>



```

![advance tab download popup](https://www.nicolapavan.it/wp-content/uploads/2023/11/Freebie_NicolaPavan_6.png)

**to edit, the simple solution is remove the "hide" class and makes your changes**

# Support the project
Pull any request to improve the project or the code
Support me **https://bitly.ws/33Foh**
