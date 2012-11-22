### [Live Demo] (http://netmera.com/netmeraJsTest)

# What is that?

[Netmera] (http://netmera.com) is a cloud platform (PaaS) optimized for mobile applications. Netmera offers a cloud based content & data repository. With simple APIs and mobile SDKs it is easy to store, retrieve, search and query data & content on the cloud.

JS-Netmera is a JavaScript library. With JS-Netmera you can use Netmera Rest API via javascript objects and methods. Thus it is easier and more advantageous to use JS-Netmera library for pure javascript applications, rather than wrapping Netmera Rest API in your application.
One of the intended usage of JS-Netmera is for HTML5 applications running both on mobile devices and desktops.
A demo application is also available. Because of [cross-site scripting] (https://developer.mozilla.org/en/http_access_control) limitations in browsers, you can run the demo in modern mobile browsers (such as iPhone Safari). Developers are free to copy and use the code in their applications.

# Usage

	<script type="text/javascript" src="http://netmera.com/mobimera/files/js/final/js-netmera.js"></script>
	<script type="text/javascript">
		NetmeraClient.init("<your api key>");
	</script>

## NetmeraContent Object

Storing content in Netmera mobile service is done by creating NetmeraContent object which contains key-value pairs of data.

### Create Content
Following code is used to create content. First, it adds data to the NetmeraContent object as key-value pairs and then calls the create() method to insert data into Netmera repository.

	var content = new NetmeraContent("Blog");
	content.add("title", "My First Blog");
	content.add("description", "This is my first blog content.");
	content.create(function () {
	    console.log("Path = " + content.getPath());
	    console.log("Title = " + content.get("title"));
	    console.log("Description = " + content.get("description"));
	}, function (error) {
	    // error is an instance of NetmeraException
	    console.log(error.getMessage());
	});

### Delete Content

Following code can be used to delete content from the Netmera repository. In order to delete content either set path to find and delete content or first call get() or search() methods and then delete the retrieved NetmeraContent object.

	var content = new NetmeraContent("Blog");
	content.setPath("/mobimeracontents/_531913427");
	content.remove(function () {
	    // Content is deleted
	}, function (error) {
	    // error is an instance of NetmeraException
	    console.log(error.getMessage());
	});

## NetmeraService Object

NetmeraService object is used to retrieve content by its search and get methods. Many query options defined to help finding exact object easily. There are also search methods which get content within the given range of geo-locations.

### Get Content

In order to get the data you need to know the path. In Netmera repository each content has a unique path.

	var service = new NetmeraService("Blog");
	service.setPath("/mobimeracontents/_531913427");
	service.get(function(content) {
	    console.log("Path = " + content.getPath());
	    console.log("Title = " + content.get("title"));
	    console.log("Description = " + content.get("description"));
	}, function(error) {
	    // error is an instance of NetmeraException
	    console.log(error.getMessage());
	});

### Search

The following code searches the content and returns the Array of NetmeraContent objects. If you set searchText then it will search the content repository and retrieve the results which contains the searchText. If searchText is not set then it returns all the content that matches with the objectName.

	var service = new NetmeraService("Blog");
	service.setMax(15); // Get 15 results
	service.setPage(0); // Get the first page
	service.addSearchText("first");
	service.search(function (contents) {
	    $.each(contents, function (index, content) {
	        console.log("Path = " + content.getPath());
	        console.log("Title = " + content.get("title"));
	        console.log("Description = " + content.get("description"));
	    });
	}, function (error) {
	    // error is an instance of NetmeraException
	    console.log(error.getMessage());
	});

### Search and Update

In order to update content, first find content and add data to update as key-value pair and then call update() method.

	var service = new NetmeraService("Blog");
	service.setMax(15);
	service.setPage(0);
	service.addSearchText("first");
	service.search(function (contents) {
	    $.each(contents, function (index, content) {
	        content.add("title", "Updated Blog title");
	        content.update(function () {
	            console.log("Path = " + content.getPath());
	            console.log("Title = " + content.get("title"));
	        }, function (error) {
	            console.log("Error while updating " + error.getMessage());
	        });
	    });
	}, function (error) {
	    console.log("Error while searching " + error.getMessage());
	});

### Search Query Options

There are different ways to add options to the NetmeraService object. They are used to filter contents.

It filters the content whose "name" is "John".

	service.whereEqual("name", "John");
	
It filters the content whose "name" is not "Tom".

	service.whereNotEqual("name", "John");

It filters the content whose "age" is greater than 20.

	service.whereGreaterThan("age", 20);

It filters the content whose "name" is greater than or equal to 20.

	service.whereGreaterThanOrEqual("age", 20);

It filters the content whose "age" is less than 30.

	service.whereLessThan("age", 30);

It filters the content whose "name" is less than or equal to 30.

	service.whereLessThanOrEqual("age", 30);

It filters the content which contains "email" key.

	service.whereExists("email", true);

It filters the content which does not contain "email" key.

	service.whereExists("email", false);

It filters the content whose "name" starts with "J".

	service.whereStartsWith("name", "J");

It filters the content whose "name" is ends with "hn".

	service.whereEndsWith("name", "hn");

Say we have a list of names.
It filters the content whose "name" is any name in the list.

	var list = new Array();
	list.push("John");
	list.push("Tom");
	service.whereContainedIn("name", list);

This method filters the content that matches with the query which contains all of the values in the list above.

	var list = new Array();
	list.push("John");
	list.push("Tom");
	service.whereAllContainedIn("name", list);
	
You can set the maximum number of results returning from the search() method. Default value is 10.

	service.setMax(100);
	
This is used for the pagination. It skips page*max results and return the max result. For example, if page = 2, and max = 100 then it skips 2*100=200 results and return the 100 results.

	service.setPage(2);

## Netmera Geolocation

NetmeraGeoLocation is used to create location with the given latitude and longitude values. It is used to set location into the content and then use it on the search queries.

### Adding Geolocation

Following code shows how to add location into the content. Firstly, it creates NetmeraGeoLocation object with the latitude and longitude values and then it adds location into the NetmeraContent object.

	var content = new NetmeraContent("Stadiums");
	content.add("name", "Camp Nou");
	content.add("team", "FC Barcelona");
	var location = new NetmeraGeoLocation(41.380842, 2.122806);
	content.add("location", location);
	content.create(function () {
	    console.log("Content is created. Path: " + content.getPath());
	}, function (error) {
	    console.log(error.getMessage());
	});

### Getting Geolocation

You can get geolocation from a content via getNetmeraGeoLocation method of NetmeraContent object.

	var service = new NetmeraService("Stadiums");
	service.setPath("< path of your content >");
	service.get(function (content) {
	    var geolocation = content.getNetmeraGeoLocation("location");
	    console.log(geolocation.getLatitude() + ", " + geolocation.getLongitude());
	}, function (error) {
	    console.log(error.getMessage());
	});

### Circle Search using Netmera Geolocation

Searches the content by taking given location as a base and retrieves the contents that located given distance far away. Distance unit is kilometer.

	var service = new NetmeraService("Stadiums");
	var geolocation = new NetmeraGeoLocation(41, 28);
	service.circleSearch(geolocation, 10, "location", function (contents) {
	    // 'contents' is an Array of NetmeraContent objects.
	}, function (error) {
	    console.log(error.getMessage());
	});

### Box Search using Netmera Geolocation

Creates box using the given two location (latitude,longitude) data and searches inside that box.

	var service = new NetmeraService("Stadiums");
	var loc1 = new NetmeraGeoLocation(40.7, 28.6);
	var loc2 = new NetmeraGeoLocation(41.2, 29.4);
	service.boxSearch(loc1, loc2, "location", function (contents) {
	    // 'contents' is an Array of NetmeraContent objects.
	}, function (error) {
	    console.log(error.getMessage());
	});

### Sorting Contents In Location Based Searches

By default, in location based searches, we sort your contents by its distance to the center of the box or the circle. In other words, if you don't change the sorting, contents which are nearby the center comes first. If you want, you can reverse that sorting by using NetmeraService.SortBy.NEARBY constant.

	var service = new NetmeraService("Stadiums");
	var center = new NetmeraGeoLocation(40.7, 28.6);
	var distance = 100;
	// Sort by their distance to the center
	service.setSortBy(NetmeraService.SortBy.NEARBY);
	 
	// Contents with minimum distance to the center comes first.
	// This is set by default.
	service.setSortOrder(NetmeraService.SortOrder.ascending);
	// Or
	// Contents with maximum distance to the center comes first.
	service.setSortOrder(NetmeraService.SortOrder.descending);
	 
	service.circleSearch(center, distance, "location", function(contents) {
	    for(var i = 0; i < contents.length; i++) {
	        var location = contents[i].getNetmeraGeoLocation("location");
	        console.log(location.getLatitude() + "," + location.getLongitude());
	    }
	}, function(error) {
	    // TODO Handle Error
	    console.log(error.getMessage());
	});

In addition, you don't have to sort your contents by their distance in location based searches. You can sort your data by any field you want.

	var service = new NetmeraService("Stadiums");
	var center = new NetmeraGeoLocation(40.7, 28.6);
	var distance = 100;
	 
	// Sort by the field "name"
	service.setSortBy("name");
	service.setSortOrder(NetmeraService.SortOrder.ascending);
	// Or
	service.setSortOrder(NetmeraService.SortOrder.descending);
	 
	service.circleSearch(center, distance, "location", function(contents) {
	    for(var i = 0; i < contents.length; i++) {
	        var name = contents[i].getString("name");
	        console.log(name);
	    }
	}, function(error) {
	    // TODO Handle Error
	    console.log(error.getMessage());
	});

## Netmera User

NetmeraUser is used to manage users. It enables application developer to add register/login functionality to the application.

### Register User

Before registering user email, password and nickname fields of NetmeraUser object should be setted. Those are the compulsory fields. There are also optional name and surname fields. We use register method to create new user.

	var user = new NetmeraUser();
	user.setEmail("test@test.com");
	user.setPassword("123456");
	user.setNickname("test");
	user.setName("testName"); // optional
	user.setSurname("testSurname"); // optional
	user.register(function () {
	    // Registering the new user is successful
	}, function (error) {
	    console.log(error.getMessage());
	});

### Update User

User info and password can be updated using the update method of the NetmeraUser. You can update all fields other than email, but email field should be setted in order to find user.

	var user = new NetmeraUser();
	user.setEmail("test@test.com");
	user.setPassword("111222");
	user.setNickname("updatedNick");
	user.setName("updatedName");
	user.setSurname("updatedSurname");
	user.update(function () {
	    // Updating the user is successful
	}, function (error) {
	    console.log(error.getMessage());
	});

### Activate User

When user created it is automatically activated. App developer can also activate/deactivate user(s) using API. This method gets email address as a parameter.

	var user = new NetmeraUser();
	user.activateUser("test@test.com", function () {
	    // Activating the user is successful
	}, function (error) {
	    console.log(error.getMessage());
	});

### Deactivate User

Deactivate user is same as activate method. By sending email address as a parameter user can be deactivated.

	var user = new NetmeraUser();
	user.deactivateUser("test@test.com", function () {
	    // Deactivating the user is successful
	}, function (error) {
	    console.log(error.getMessage());
	});

### User Login

Users can be logged in to the application that they register. This method has email and password parameters. When user logged, currentUser is setted as the logged user, otherwise it is null.

	NetmeraUser.login("test@test.com", "12345", function (currentUser) {
	    // Login is successful
	}, function (error) {
	    console.log(error.getMessage());
	});

### User Logout

Users can be logged out using the following code.

	NetmeraUser.logout();

### Get Current User

When user log in then currentUser is setted as the logged user, otherwise it is null. You can get currentUser using the following method.

	var currentUser = NetmeraUser.getCurrentUser();
	if (currentUser != null) {
	    // do something with this user.
	}

### Create content with logged user

While creating content, if user is setted into the NetmeraContent object via setOwner method, then content owner will be the setted user. This is useful when you need the contents of the specific user. The following code shows how to create content for a user.

	NetmeraUser.login("test@test.com", "123456", function() {
	    var currentUser = NetmeraUser.getCurrentUser();
	    if (currentUser != null) {
	        var content = new NetmeraContent("testContent");
	        content.add("title", "content with user");
	        content.setOwner();
	        content.create(function () {
	            console.log("Creating content is successful. Path: " + content.getPath());
	        }, function (error) {
	            console.log(error.getMessage());
	        });
	    }
	}, function(error) {
	    console.log(error.getMessage());
	});

After creating content for the user, user's content can be retrieved back with the following code. When we set user in the whereOwnerEqual() method it will query the contents of that user, otherwise it will get all public contents.

	var service = new NetmeraService("testContent");
	service.whereOwnerEqual(); // Sets logged user as owner
	service.search(function (contents) {
	    // 'contents' is the Array of NetmeraContent objects returned from search
	}, function (error) {
	    console.log(error.getMessage());
	});

### Search User

You can search over the registered users. It is similar to the content search. The only difference is, you should first get NetmeraUserService then call searchUser method. The sample code to search users is shown below.

	var service = NetmeraService.getNetmeraUserService();
	service.searchUser(function (users) {
	    $.each(users, function (index, user) {
	        console.log(user.getNickname());
	    });
	}, function (error) {
	    console.log(error.getMessage());
	});

## Netmera Media

Netmera Media object is used to upload and store media content into cloud. Currently media object supports image files. Besides storing your media files Netmera creates various sizes of uploaded images, for different app screens. Netmera stores media content in CDN for fast retrieval.

### Create Netmera Media

For now, creating NetmeraMedia object works only with HTML5.

In order to create media content on the cloud, first you should get file object from an input[type=file] tag in your HTML.

	<input type="file" id="netmeraFile" />

You can take file object from input tag when onchange event occurs. When you take file object, then you can create a NetmeraMedia object with it and add that media into a NetmeraContent object.

	var fileElement = document.getElementById("netmeraFile");
	fileElement.onchange = function () {
	    var file = fileElement.files[0];
	     
	    var media = new NetmeraMedia(file);
	     
	    var content = new NetmeraContent("Blog");
	    content.add("image", media);
	    content.create(function () {
	        console.log("Content was created successfully!");
	    }, function (e) {
	        // Handle exception
	        console.log(e.getMessage());
	    });
	};

### Get Netmera Media

In order to get Netmera Media object, firstly you have to get content that contains media object and then get media from content.In order to get content you can use get() or search() methods. If you know path you can use get() method otherwise you should use search().Path can be retrieved from the NetmeraContent.getPath() after running the search() or get() methods or can be accessed from the Content Browser page.You can get the data as the url of the image with different sizes.

	var service = new NetmeraService("Blog");
	// if some of our contents don't contain image field
	service.whereExists("image", true);
	service.search(function(contents) {
	    for(var i = 0; i < contents.length; i++) {
	        var media = contents[i].getNetmeraMedia("image");
	        var url = media.getUrl(NetmeraMedia.PhotoSize.LARGE);
	        console.log(url);
	    }
	}, function(e) {
	    console.log(e.getMessage());
	});

### Netmera Media Photo Size

Different photo size options added to the NetmeraMedia object in order to get url of the uploaded image with different sizes.

It can be accessed from the NetmeraMedia.PhotoSize object

Following photo sizes are defined on the NetmeraMedia

DEFAULT : This is the original image size.If no photo size is setted then original size is used.

THUMBNAIL : Image size is defined as 41x41 px

SMALL : Image size is defined as 82x82 px

MEDIUM : Image size is defined as 180x250 px

LARGE : Image size is defined as 300x350 px

## Netmera Push Notification

Netmera Push Notification is a service that enables developers to send data to their applications. You can send push notifications to Android and IOS devices registered in your application from our Javascript SDK. There are three similar classes with which you can send notifications; NetmeraPush, NetmeraAndroidPush, NetmeraIOSPush. Those are extending abstract class BasePush.

### NetmeraPush: Sending Push to Both Android and IOS

With NetmeraPush object, you can easily determine platforms to which you want to send push notifications by using setSendToAndroid() and setSendToIos() methods.

	// First, you need to initialize your api key
	NetmeraClient.init("your_api_key");
	 
	var push = new NetmeraPush();
	 
	// Send to android devices
	push.setSendToAndroid(true);
	 
	// Send to IOS devices
	push.setSendToIos(true);
	 
	// Set the message you want to send 
	// to registered devices in your app.
	push.setMessage("Hello, World!");
	 
	// Send the notification
	push.sendNotification(function(details) {
	    // your push notification is sent.
	    var androidDetail = details[PushChannel.android];
	    var iosDetail = details[PushChannel.ios];
	    console.log(androidDetail.getStatus());
	    console.log(iosDetail.getStatus());
	}, function(error) {
	    // There is an error. Check error.getMessage();
	});

### NetmeraAndroidPush: Sending Push to Only Android Devices

You can send push notifications to only Android devices via NetmeraAndroidPush object.

	// First, you need to initialize your api key
	NetmeraClient.init("your_api_key");
	 
	var push = new NetmeraAndroidPush();
	 
	// Set the message you want to send 
	// to registered devices in your app.
	push.setMessage("Hello, Android Devices!");
	 
	// Send the notification
	push.sendNotification(function(details) {
	    // your push notification is sent.
	    var androidDetail = details[PushChannel.android];
	    console.log(androidDetail.getStatus()); 
	}, function(error) {
	    // There is an error. Check error.getMessage();
	});

### NetmeraIOSPush: Sending Push to Only IOS Devices

You can send push notifications to only IOS devices via NetmeraIOSPush object.

	// First, you need to initialize your api key
	NetmeraClient.init("your_api_key");
	 
	var push = new NetmeraIOSPush();
	 
	// Set the message you want to send 
	// to registered devices in your app.
	push.setMessage("Hello, IOS Devices!");
	 
	// Send the notification
	push.sendNotification(function(details) {
	    // your push notification is sent.
	    var iosDetail = details[PushChannel.ios];
	    console.log(iosDetail.getStatus()); 
	}, function(error) {
	    // There is an error. Check error.getMessage();
	});

### Sending Push Notifications to Special Device Groups

In some SDKs in which you can register the device to push notifications, such as Android and IOS, you can also register that device in some device groups. Therefore, you can send push notifications to devices which are registered in special device groups. In the two examples below, assume that you divided your app's devices into 4 device groups: Sports, News, Man, and Woman.

	// First, you need to initialize your api key
	NetmeraClient.init("your_api_key");
	 
	var push = new NetmeraPush();
	 
	// Send to android devices
	push.setSendToAndroid(true);
	 
	// Send to IOS devices
	push.setSendToIos(true);
	 
	// Send to devices only registered in 'Woman' group
	push.setDeviceGroups(["Woman"]);
	 
	// Set the message you want to send to 
	// registered devices in 'Woman' group.
	push.setMessage("Happy Women's Day!");
	 
	// Send the notification
	push.sendNotification(function(details) {
	    // your push notification is sent.
	    var iosDetail = details[PushChannel.ios];
	    var androidDetail = details[PushChannel.android];
	    console.log(iosDetail.getStatus());
	    console.log(androidDetail.getStatus());
	}, function(error) {
	    // There is an error. Check error.getMessage();
	});

Or you may want to send push notification to those who are registered in the group 'Man' and plus those who are registered in the group 'Sports', and you may want to send that notification to only IOS devices.

	// First, you need to initialize your api key
	NetmeraClient.init("your_api_key");
	 
	var push = new NetmeraIOSPush();
	 
	// Send push to devices registered in "Sports" or "Man" groups.
	push.setDeviceGroups(["Sports", "Man"]);
	 
	// Set the message you want to send to 
	// registered devices in 'Man' or 'Sports' group.
	push.setMessage("Iniesta scored two goals in tonight's game!");
	 
	// Send the notification
	push.sendNotification(function(details) {
	    // your push notification is sent.
	    var iosDetail = details[PushChannel.ios];
	    console.log(iosDetail.getStatus());
	}, function(error) {
	    // There is an error. Check error.getMessage();
	});

### Listing All Device Groups

You can get all device groups created in your app with the following code:

	NetmeraPushService.getDeviceGroups(function(groups) {
	    // groups is an Array of Strings, which 
	    // are names of the groups in your app
	    for (var i = 0; i < groups.length; i++) {
	        console.log(groups[i]);
	    }
	}, function(error) {
	    // handle exception
	    console.log(error.getMessage());
	});

## Netmera Facebook Login

With Netmera, mobile apps can easily provide a log in with Facebook option. When a user logs in with Facebook, Netmera associates a user’s NetmeraUser entity and Facebook identity so that you can use all NetmeraUser features with a user logging in with Facebook.

### Let's Start

First, if you don’t have a Facebook application, you need to create one from Facebook Developers page. Choose the "Website with Facebook Login" option under "Select how your app integrates with Facebook" and enter your site's URL.

![Alt text](http://netmera.com/mobimera/images/guide/facebookapp1.png)

Then, put the code below into your application’s body tag by setting appId and channelUrl parameters in order to initialize Facebook with your application id.

	<div id="fb-root"></div>
	<script>
	    window.fbAsyncInit = function() {
	        NetmeraFacebookUtils.initialize({
	            appId : "YOUR_APP_ID",              // App ID
	            channelUrl : 'YOUR_CHANNEL_URL',    // Channel File
	            status : true,                      // check login status
	            cookie : true,                      // enable cookies to allow the server to access the session
	            xfbml : true                        // parse XFBML
	        });
	    };
	         
	    // Load the SDK's source Asynchronously
	    (function(d){
	        var js, id = 'facebook-jssdk', ref = d.getElementsByTagName('script')[0];
	        if (d.getElementById(id)) {return;}
	        js = d.createElement('script'); js.id = id; js.async = true;
	        js.src = "//connect.facebook.net/en_US/all.js";
	        ref.parentNode.insertBefore(js, ref);
	    }(document));
	</script>

After initialization happens, you can call NetmeraFacebookUtils.login() method. If the user is new in your app or not logged in Facebook, a permission/login popup will be shown.
When the user gives permissions which your app wants, the following process will be followed:

- If there is a NetmeraUser whose email address is the same as the email address coming from Facebook, that NetmeraUser will be associated with the Facebook id.
- If the email address is not associated with any previous NetmeraUsers in your app, a new NetmeraUser will be created and that NetmeraUser will be associated with the user’s Facebook id.

At the end, a NetmeraUser will be returned by the login method and it’ll be logged in.

You can take logged user from either login method’s callback or NetmeraUser.getCurrentUser() methods.

	// An array of permissions that you want from Facebook.
	var permissions = ["email"];
	NetmeraFacebookUtils.login(permissions, function(user) {
	    // user is logged successfully.
	    // you can use either 'user' parameter in callback or 
	    // NetmeraUser.getCurrentUser() as logged user.
	    var currentUser = NetmeraUser.getCurrentUser();
	    console.log(currentUser.getNickname());
	    console.log(user.getNickname());
	    // Both are the same.
	}, function(error) {
	    console.log("error occurred: " + error.getMessage());
	});

### Logout

In order to logout from Netmera, you should call NetmeraUser's logout() function. This will destroy your Facebook session in Netmera, too.

	NetmeraUser.logout();

## NetmeraTwitterUtils

NetmeraTwitterUtils allows you to integrate Twitter accounts to your application. You can also send requests to Twitter REST API via NetmeraTwitterUtils.

### Step 1 :

Create your Twitter application from [https://dev.twitter.com] (https://dev.twitter.com)

### Step 2 :

While sending requests to Twitter REST API, we need to use other Javascript libraries. Include sha1.js and oauth.js files just before including js-netmera.js

	<script src="http://netmera.com/mobimera/files/js/final/sha1.js" type="text/javascript"></script>
	<script src="http://netmera.com/mobimera/files/js/final/oauth.js" type="text/javascript"></script>
	 
	<script src="http://netmera.com/mobimera/files/js/final/js-netmera.js" type="text/javascript"></script>

### Step 3 :

When you get your Twitter access token(consumerKey) and access token secret(consumerSecret), add the following line to the beginning of your code

	NetmeraClient.init("your_netmera_api_key");
	NetmeraTwitterUtils.initialize("consumerKey", "consumerSecret");

![Alt text](http://netmera.com/mobimera/images/guide/twitterapp.png)

### Login

When users login via Twitter, associated NetmeraUser will be returned and current user will be set.

	NetmeraTwitterUtils.login(function(user) {
	    // user is an instance of NetmeraUser
	}, function(error) {
	    console.log(error.getMessage());
	});

### Sending Request to Twitter REST API

NetmeraTwitterUtils allows you to send Http requests to Twitter API by signing your requests.

Sending POST Requests :

You can send HTTP POST requests to Twitter REST API via requestPost() method

	// URL to send POST request
	var url = "https://api.twitter.com/1/statuses/update.json";
	 
	// Params to be sent to the URL above
	var params = {
	    "status": "Hello Twitter with @netmera"
	};
	 
	// Send the request
	NetmeraTwitterUtils.requestPost(url, params, function(response) {
	    // We sent the request to update.json, so the response type is json
	    var json = JSON.parse(response);
	    // Work with json.
	}, function(error) {
	    console.log(error.getMessage());
	});

Sending GET Requests :

You can send HTTP GET request to Twitter REST API via requestGet() method

	// URL to send GET request
	var url = "https://api.twitter.com/1/account/verify_credentials.json";
	 
	// Params to be sent to the URL above
	var params = {
	    "skip_status": "false"
	};
	 
	// Send the request
	NetmeraTwitterUtils.requestGet(url, params, function(response) {
	    // We sent the request to verify_credentials.json, so the response type is json
	    console.log(JSON.parse(response));
	}, function(error) {
	    console.log(error.getMessage());
	});

### Logout

When you logout via NetmeraUser.logout() method, you destroy your twitter session, too.

	NetmeraUser.logout();