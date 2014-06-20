TwitterOAuth
------------

كلاس php للتعمل مع تويتر

وضائف الكلاس
=============

1. الحصول على صلاحيات .
2. ارسال التغريدات الى تويتر.
3. البحث في محرك تويتر.





شرح الاستخدام
================================

To use TwitterOAuth with the Twitter API you need *TwitterOAuth.php*, *OAuth.php* and
client credentials. You can get client credentials by registering your application at
[dev.twitter.com/apps](https://dev.twitter.com/apps).

Users start out on connect.php which displays the "Sign in with Twitter" image hyperlinked
to redirect.php. This button should be displayed on your homepage in your login section. The
client credentials are saved in config.php as `CONSUMER_KEY` and `CONSUMER_SECRET`. You can
save a static callback URL in the app settings page, in the config file or use a dynamic
callback URL later in step 2. In example use https://example.com/callback.php.

1) When a user lands on redirect.php we build a new TwitterOAuth object using the client credentials.
If you have your own configuration method feel free to use it instead of config.php.

    $connection = new TwitterOAuth(CONSUMER_KEY, CONSUMER_SECRET); // Use config.php client credentials
    $connection = new TwitterOAuth('abc890', '123xyz');

2) Using the built $connection object you will ask Twitter for temporary credentials. The `oauth_callback` value is required.

    $temporary_credentials = $connection->getRequestToken(OAUTH_CALLBACK); // Use config.php callback URL.

3) Now that we have temporary credentials the user has to go to Twitter and authorize the app
to access and updates their data. You can also pass a second parameter of FALSE to not use [Sign
in with Twitter](https://dev.twitter.com/docs/auth/sign-twitter).

    $redirect_url = $connection->getAuthorizeURL($temporary_credentials); // Use Sign in with Twitter
    $redirect_url = $connection->getAuthorizeURL($temporary_credentials, FALSE);

4) You will now have a Twitter URL that you must send the user to.

    https://api.twitter.com/oauth/authenticate?oauth_token=xyz123

5) The user is now on twitter.com and may have to login. Once authenticated with Twitter they will
will either have to click on allow/deny, or will be automatically redirected back to the callback.

6) Now that the user has returned to callback.php and allowed access we need to build a new
TwitterOAuth object using the temporary credentials.

    $connection = new TwitterOAuth(CONSUMER_KEY, CONSUMER_SECRET, $_SESSION['oauth_token'],
    $_SESSION['oauth_token_secret']);

7) Now we ask Twitter for long lasting token credentials. These are specific to the application
and user and will act like password to make future requests. Normally the token credentials would
get saved in your database but for this example we are just using sessions.

    $token_credentials = $connection->getAccessToken($_REQUEST['oauth_verifier']);

8) With the token credentials we build a new TwitterOAuth object.

    $connection = new TwitterOAuth(CONSUMER_KEY, CONSUMER_SECRET, $token_credentials['oauth_token'],
    $token_credentials['oauth_token_secret']);

9) And finally we can make requests authenticated as the user. You can GET, POST, and DELETE API
methods. Directly copy the path from the API documentation and add an array of any parameter
you wish to include for the API method such as curser or in_reply_to_status_id.

    $account = $connection->get('account/verify_credentials');
    $status = $connection->post('statuses/update', array('status' => 'Text of status here', 'in_reply_to_status_id' => 123456));
    $status = $connection->delete('statuses/destroy/12345');

Contributors
============

* [Abraham Williams](https://twitter.com/abraham) - Main developer, current maintainer.
