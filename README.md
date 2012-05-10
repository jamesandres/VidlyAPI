# VidlyAPI
VidlyAPI is a PHP library for communicating with the [http://vid.ly/api](http://vid.ly/api/) REST web-service.


## Functionality

* Uploading videos to Vid.ly:
  * [AddMedia](http://api.vid.ly/#AddMedia)
  * [AddMediaLite](http://api.vid.ly/#AddMediaLite)
* Altering existing Vid.ly videos:
  * [updateMedia](http://api.vid.ly/#UpdateMedia)
  * [deleteMedia](http://api.vid.ly/#DeleteMedia)
* Fetching information and stats:
  * [getStatus](http://api.vid.ly/#GetStatus)
  * [getStatistics](http://api.vid.ly/#GetStatistics)

Note, this library is an ongoing effort and some areas are not heavily tested.

## Example

**Example #1**: Sending a video to Vid.ly for transcoding and hosting.

    <?php

    require 'VidlyAPI/VidlyAPI.php';

    // Create a new VidlyAPI object:
    // 1) Pass it your Vid.ly user token and key
    // 2) Optionally, but recommended, pass it a URL where Vid.ly will send
    //    notification responses (eg: when a video finishes transcoding)
    $api = new VidlyAPI('API-USER', 'API-KEY', 'http://www.example.com/vidly-notifications.php');

    // Inform Vid.ly that a new file is ready for transcoding.
    //
    // 1) Vid.ly will download the video using the 'SourceFile' URL you provide
    //   1a) If the download fails, Vid.ly will try several times to re-download.
    //       Pro tip: If your site is behind HTTP authentication you will need to
    //                include the user/password inside the URL.
    //
    // 2) If Vid.ly is able to download the video AND successfully transcode it a
    //    notification will be posted to the notification URL you provided.
    $newMedia = (object) array('SourceFile' => 'http://www.example.com/files/user_uploaded_video.mov');
    $api->addMedia(array($newMedia));

**Example #2**: Receiving notifications

	<?php
	// vidly-notifications.php
	
	if (!isset($_POST['xml'])) {
	  // ERROR: Vid.ly (or some malicious entity) sent an invalid request
	  return;
	}
	
	$api = new VidlyAPI('API-USER', 'API-KEY', 'http://www.example.com/vidly-notifications.php');
	$api->_parseResponse($_POST['xml']);
	
	// Now the following variables will be available, see: http://api.vid.ly/
	//
	// 1) $api->errors                  Either: A list of errors, sent from Vid.ly
	//                                      or: A list of internal VidlyAPI errors,
	//                                          usually a list of videos that failed
	//                                          to be transcoded
	// 2) $api->message_code            The MessageCode from the last request
	// 3) $api->message                 The Message from the last request
	// 4) $api->batch_id                The batch this notification relates to
	// 5) $api->successes               A list of videos that were successfully
	//                                  transcoded and hosted. Yay!
	// 6) $api->result                  The catch-all place where Vid.ly puts
	//                                  other notifications.  Yes, it's annoying.

## Tips
* Append a unique token to the notification URL you pass to the VidlyAPI constructor (eg: 'http://www.example.com?token=202cb962').  Store that token in your database.  When Vid.ly sends a notification response you will be able to match the response to the original request more easily.


## TODOs
* Improve Vid.ly webservice response parsing and handling.  Ideally the Vid.ly webservice warts should be gracefully hidden.
* Unit tests


## Credits

Many thanks to [Meedan](http://news.meedan.net) and [QFI](http://www.qfi.org) for support to create this project.