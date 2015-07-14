# iphone-photo-picker #
  * contact: Adrian Graham / Brian Westphal (feedback@nextstop.com)
  * license: MIT license
  * last updated: Dec 14, 2009


## Overview ##

In the process of building an HTML5 application, we realized that mobile Safari does not offer any way to upload photos to a server. We needed this functionality so we built a simple app that may be launched from Safari using a special url. After a user has selected and uploaded a photo, this app has a continue url that is then loaded in Safari.

We thought other people might find this useful as they develop web-based applications on the iPhone so we decided to open source it under the MIT license.


## Requrements ##

  * XCode 3.1.2 or higher (this is an XCode project)
  * A web server that can accept image uploads from an html form: You will need to write code to accept uploaded images. Images are uploaded using standard http multipart/form-data encoding.
  * NOTE: You will need to customize and submit this application to the iPhone AppStore if you would like to distribute it to users.


## Life of an iphone-photo-picker request ##

> CASE 1: iphone-photo-picker already installed
    * User visits your web page in mobile Safari.
    * User clicks link on your web page of the form photopicker://
    * Since this scheme has been registered, the photo picker app will launch.
    * User takes photo or selects photo from photo Library.
    * Photo is uploaded to UPLOAD\_URL. Images are posted using standard http multipart/form-data encoding.
    * Optional: in the response from UPLOAD\_URL, you may pass back a string of data (e.g. the url of the uploaded photo) that will be passed back as a parameter when CONTINUE\_URL is loaded.
    * When upload is complete, mobile Safari is launched and CONTINUE\_URL is loaded. If you passed back the url of the uploaded photo, you may choose to display the newly uploaded photo on this page.


> CASE 2: iphone-photo-picker not installed
    * When a user clicks the link to launch the photo picker, an alert will be displayed saying that Safari does not know how to handle urls of type photopicker://.
    * Behind the alert box, you can load a web page with a link to install your customized photo picker application on the AppStore. That way when the user clicks OK, they will see instructions showing how to install the app. We recommend that you also provide users with a way to return to whatever they were doing before they clicked this in case they decide not to install the application.
    * From this point on, your web application should be able to launch the photo picker as described in CASE 1.


## Configuration ##

There are a variety of things you need to configure in order to integrate this application with your web application.

**Images**
  * icon.png - replace with an iPhone app icon (57x57 pixels)
  * home-background.png - replace with your logo on a background (320x460 pixels), some of which may be covered by buttons at various points.

**Plist**
  * photopicker-Info.plist
    * replace "yourappname" and "yourcompanyname" with appropriate values
    * "Bundle identifier": com.yourcompanyname.photopicker
    * "URL types": "photopicker" (you probably want to customize this -- this is the scheme that launches the app from Safari)
    * "${PRODUCT\_NAME}": the name of your product or company
**XIBs**
  * PhotoPickerViewController.xib - can be customized with fancier graphics or button labels

**Source Code**
  * Configuration.h
    * UPLOAD\_URL: the url the application should post the photo upload to
    * CONTINUE\_URL: the url the application should call after the photo upload is complete (used to notify the user that the upload is complete)


**From your Web App**

Add the following Javascript section to your web application, replacing the URLs as appropriate.

```
<script type="text/javascript">
   window.launchPhotoPicker = function() {
       setTimeout(function() {
           window.location = 'http://www.yourcompanyname.com/instruction-to-install-app.html'
       }, 500);

       window.location = 'photopicker://?source=camera&context=yourdatahere';
   };
</script>
```

  * instruction-to-install-app.html: a web page with a link to your application in the iPhone AppStore (and a link back to whatever the user was doing before they tried to launch the photo picker, in case they decide not to install)
  * GET params for launching the application include:
    * context: (optional) a string value to pass as a POST parameter to the upload URL. This can be used to help maintain state information.
    * source: (optional) Options are: "camera" and "library". If this is specified the camera or library is loaded by default. If left off, a menu of choices may be presented in the application.

  * Hook up a link to call the above Javascript in your web app:

```
<a href="javascript:launchPhotoPicker();">Upload photo</a>
```


**On Your Web Server**

> You will need to write code to accept uploaded images. Images are uploaded using standard http multipart/form-data encoding. The following POST parameters are passed:
  * context - the string value passed when launching the application.
  * image\_file - the uploaded file. This will always be given the filename "photopicker.jpg".

If you'd like to test your server without running the iPhone app, use an HTML form like this:

```
<form action="{ insert UPLOAD_URL }" enctype="multipart/form-data" method="post">
    <input type="file" name="image_file">
    <input type="submit" value="Send">
</form>
```