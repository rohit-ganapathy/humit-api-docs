---


---

<h1 id="humit-backend-api-docs">humit backend API docs</h1>
<p>The following pages contain documentation for various endpoints in the backend API of humit</p>
<h2 id="login">Login</h2>
<p>The app starts on the intro login screen, on pressing login, OAuth with spotify begins which will return a access token and refresh token from Spotify’s side.</p>
<p>Post successful OAuth with Spotify, we hit the following endpoint with the spotify tokens so that an entry can be created on the backend for the user with signup_stage = 0</p>
<h3 id="humit-auth-flow">humit auth flow</h3>
<pre><code>ENDPOINT:
https://www.humit.xyz/api/users/auth/signup/

METHOD: 
POST

HEADERS
{ 'content-type':  'application/json' }

BODY
{
	access_token:  spotify_access_token,
	refresh_token:  spotify_refresh_token,
	signup_method:  "SPOTIFY",//could be apple music in the future
	notification_id:  null //this field can be null
}

RESPONSE
{
 "data": 
	{"humit_access_token":&lt;string&gt; , 
	 "humit_refresh_token": &lt;string&gt;,
	 "signup_stage": 1,// 0 if registration not done otherwise 1
	 "spotify_access_token": &lt;string&gt;, 
	 "spotify_refresh_token": &lt;string&gt;
	 }, 
"success": true
 }
</code></pre>
<p>After signup is successful the user is taken to the registration page where he fills in details like full name, username, age, profile pic and an optional invite code. There are also a bunch of endpoints that are used to check if username and invite code are taken.</p>
<h3 id="refresh-expired-humit-token">refresh expired humit token</h3>
<p>humit access token expires in 3600 seconds</p>
<pre><code>ENDPOINT:
http://www.humit.xyz/api/users/auth/token-refresh/

METHOD: 
POST

HEADERS
{ 'content-type':  'application/json',
'Authorization':  `humit ${humit_refresh_token}`
 }

RESPONSE
{
   "data":{
     "humit_access_token":&lt;string&gt;
   },
   "success":true
}
</code></pre>
<h2 id="registration-form">Registration Form</h2>
<h3 id="check-username">check username</h3>
<pre><code>ENDPOINT:
http://www.humit.xyz/api/users/username/?username=Rohit

METHOD: 
GET

AUTHORIZATION
{
'Authorization':  `humit ${humit_access_token}`
}

RESPONSE

IF USERNAME IS NOT TAKEN
{
 "data": {},
 "success": true
 }
 
IF USERNAME IS TAKEN
{
"display_error": {
	"code": "DISPLAY_USERNAME_NOT_UNIQUE",
	"message": "Your User handle is not unique. Please choose another 					  handle",
	 "status": "error"
 }, 
 "errors": [{
	 "code": "DISPLAY_USERNAME_NOT_UNIQUE",
	 "message": "Your 	  User handle is not unique. Please choose another handle", 
	 "status": "error"}],
"extra_data": {}, 
"success": false}
</code></pre>
<h3 id="check-invite-code">check invite code</h3>
<pre><code>ENDPOINT:
https://www.humit.xyz/api/users/referral_code/?code=598631

METHOD: 
GET

HEADERS:
{
'Authorization':  `humit ${humit_access_token}`
}

RESPONSE

IF INVITE CODE EXISTS
{
   "data":{
      "bio":null,
      "country_name":"IN",
      "favourite_artists":{
         
      },
      "first_name":"Breakfree",
      "followed":false,
      "last_name":" ",
      "muted":null,
      "notification_id":"",
      "number_of_followers":0,
      "number_of_following":0,
      "number_of_hums":0,
      "profile_pic_url":"",
      "referral_code":"255539",
      "tokens":0,
      "user_handle":"tmk3tmfmf2mmag48cph6bntbl",
      "user_id":"2751"
   },
   "success":true
}
 
IF INVITE CODE DOES NOT EXIST

{
   "display_error":{
      "code":"USER_DOES_NOT_EXIST",
      "message":"User Does Not Exist",
      "status":"error"
   },
   "errors":[
      {
         "code":"USER_DOES_NOT_EXIST",
         "message":"User Does Not Exist",
         "status":"error"
      }
   ],
   "extra_data":{
      
   },
   "success":false
}
</code></pre>
<h3 id="upload-profile-picture---get-signed-url-for-s3-upload">upload profile picture - get signed URL for S3 upload</h3>
<pre><code>ENDPOINT:
http://www.humit.xyz/api/users/files/upload/

METHOD: 
POST

HEADERS
{
'Authorization':  `humit ${humit_access_token}
}

BODY`
{ file_type:  "profile" }

RESPONSE
{
   "data":{
      "id":"5f0ee15e-4449-4751-a21a",//save image as &lt;id&gt;.jpg
      "upload_url":"https://rohit-test.s3.amazonaws.com/profile_images/65de7794-a6c6-11eb-9d57-0e654f312391.jpg?AWSAccessKeyId=AKIARX5TAUMQRCAUEFFB&amp;Signature=PwUVwHAq2GGPTiNa0PIp46oKC0g%3D&amp;content-type=image%2Fjpeg&amp;Expires=1619469260"
   },
   "success":true
}

</code></pre>
<h3 id="final-submit-registration-form">final submit registration form</h3>
<pre><code>ENDPOINT:
http://www.humit.xyz/api/users/profile/

METHOD: 
POST

HEADERS:
{
'content-type':  'application/json',
'Authorization':  `humit ${humit_token}`
}

BODY`
{
   "date_of_birth":"1994-4-26",//age needs to converted into DOB
   "display_username":"Ftujklloo",
   "favourite_artists":"{\"data\":[]}",//retrieved from SPOTIFY REST API
   "full_name":"Breakfree",
   "notification_id":null,
   "referral_code":598631, //if no code then null
}


RESPONSE
{
   "data":{
      "bio":null,
      "country_name":"IN",
      "date_of_birth":"1994-04-26",
      "favourite_artists":{
         "data":[
            "Array"
         ]
      },
      "first_name":"Breakfree",
      "internal_username":"breakfree453280",
      "last_name":" ",
      "notification_id":"",
      "number_of_followers":0,
      "number_of_following":0,
      "number_of_hums":0,
      "playlist_id":"07iff1phure10snDAETDOz",
      "profile_pic_url":"https://rohit-test.s3.amazonaws.com/profile_images/65de7794-a6c6-11eb-9d57-0e654f312391.jpg",
      "referral_code":"255539",
      "signup_done":true,
      "signup_stage":1,
      "tokens":0,
      "user_handle":"Ftujklloo",
      "user_id":"2751"
   },
   "success":true
}

</code></pre>
<h2 id="recommendations">Recommendations</h2>
<p>After the user submits the registration form successfully, He is taken to one final screen where he can follow some stations and people. If his invite code is valid then there is different endpoint for people recommendations. For station recommendations, there is an endpoint that returns a feed of stations arranged in order of popularity. It is compulsory for the user to follow 3 stations before he can proceed.</p>
<h3 id="get-station-feed-for-recommendations">get station feed for recommendations</h3>
<pre><code>ENDPOINT:
http://www.humit.xyz/api/stations/discover/?limit=20&amp;offset=0

METHOD: 
GET

HEADERS:
{
'content-type':  'application/json',
'Authorization':  `humit ${humit_token}`
}

RESPONSE
{
   "data":{
      "count":304,
      "next":"http://www.humit.xyz/api/stations/discover/?limit=20&amp;offset=20",
      "previous":null,
      "results":[&lt;object&gt;,&lt;object&gt;,&lt;object&gt;,.... ]
   },
   "success":true
}
</code></pre>
<h3 id="people-reccos-with--invite-codenull">people reccos with  invite code==null</h3>
<pre><code>ENDPOINT:
https://www.humit.xyz/api/users/discover/?limit=50&amp;offset=0/

METHOD: 
GET

HEADERS:
{
'content-type':  'application/json',
'Authorization':  `humit ${humit_token}`
}

RESPONSE
{"data": {"count": 100, "next": "https://www.humit.xyz/api/users/discover/?limit=50&amp;offset=50", "previous": null, "results":[&lt;object&gt;,&lt;object&gt;,&lt;object&gt;,.... ]}, "success": true}
</code></pre>
<h3 id="people-reccos-with--invite-codenull-1">people reccos with  invite code!=null</h3>
<pre><code>ENDPOINT:
https://www.humit.xyz/api/users/onboard/?code=598631

METHOD: 
GET

HEADERS:
{
'content-type':  'application/json',
'Authorization':  `humit ${humit_token}`
}

RESPONSE
{
   "data":[
      {
         "bio":null,
         "country_name":"IN",
         "favourite_artists":[
            "Object"
         ],
         "first_name":"Harshita",
         "last_name":"Prabha",
         "member_since":"2020-02-09T16:02:05.931031Z",
         "muted":null,
         "number_of_hums":9,
         "profile_pic_url":"https://rohit-test.s3.amazonaws.com/profile_images/f70b5714-4b55-11ea-a73e-5e1faab9a014.jpg",
         "user_handle":"harshitaprabha",
         "user_id":"13"
      },
      "&lt;object&gt;",
      "&lt;object&gt;",
      "...",
      "......"
   ],
   "success":true
}"``""`"


</code></pre>

