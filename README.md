# betable-ios

Betable iOS SDK

## Betable Object

This is the object that serves as a wrapper for all calls to the Betable API.

### Initializing

    - (Betable*)initWithClientID:(NSString*)clientID clientSecret:(NSString*)clientSecret redirectURI:(NSString*)redirectURI;

To create a betable object simply initilize it with your client ID, client Secret and redirectURI. All of these can be set at the [Developer Dashboard](http://developers.betable.com) when you create your game. We suggest that your redirect uri be `betable+<GAME_ID>://authorize`.  See Authorization below for more details on this.

### Authorization

    - (void)authorize;

This method should be called when no access token exists for the current user. It will initiate the OAuth flow. It will bounce the user to the Safari app that is native on the device. After the person authorizes your app at betable.com, betable will redirect them to your redirect URI which can be registered [here](http://developers.betable.com)

The redirect uri should have a protocol that opens your app. See this [Reference](http://developer.apple.com/library/ios/#documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html#//apple_ref/doc/uid/TP40007072-CH7-SW50) for more info. It is suggested that your URL Scheme be `betable+<GAME_ID>` and that your redirect uri be `betable+<GAME_ID>://authorize`. After login your in your UIApplicationDelegate's method application:handleOpenURL: you can handle the request, which will be formed as `betable+<GAME_ID>://authorize?code=<YOUR ACCESS CODE>&state=<RANDOM UNIQUE STRING>`.

### Getting the Access Token
    
    - (void)token:(NSString*)code
       onComplete:(BetableAccessTokenHandler)onComplete
        onFailure:(BetableFailureHandler)onFailure;

Once you have your access code from the application:handleOpenURL: of your UIApplicationDelegate after betable redirects to your app uri you can pass the access code to the token:onComplete:onFailure method of your betable object.

This is the final step of oauth.  In the onComplete handler you will recieve your access token for the user associated with this Betable object.  You will want to store this with the user so you can make future calls on be half of said user.

### Betting

    - (void)betForGame:(NSString*)gameID
              withData:(NSDictionary*)data
            onComplete:(BetableCompletionHandler)onComplete
             onFailure:(BetableFailureHandler)onFailure;

This method is used to place a bet for the user associated with this Betable object.

+  `gameID`: This is your gameID which is registered and can be checked at 
   http://developers.betable.com
+  `data`: This is a dictionary that will converted to JSON and added
   request as the body. It contains all the important information about
   the bet being made. For documentation on the format of this
   dictionary see https://developers.betable.com/docs/api/reference/
+  `onComplete`: This is a block that will be called if the server returns
   the request with a successful response code. It will be passed a
   dictionary that contains all of the JSON data returned from the
   betable server.
+  `onFailure`: This is a block that will be called if the server returns
   with an error. It gets passed the NSURLResponse object, the string
   reresentation of the body, and the NSError that was raised.
   betable server.

### Getting User's Account

    - (void)userAccountOnComplete:(BetableCompletionHandler)onComplete
                        onFailure:(BetableFailureHandler)onFailure;

This method is used to retrieve information about the account of the user associated with this betable object.

+  `onComplete`: This is a block that will be called if the server returns
   the request with a successful response code. It will be passed a
   dictionary that contains all of the JSON data returned from the
   betable server.
+  `onFailure`: This is a block that will be called if the server returns
   with an error. It gets passed the NSURLResponse object, the string
   reresentation of the body, and the NSError that was raised.
   betable server.

### Getting User's Wallet

    - (void)userWalletOnComplete:(BetableCompletionHandler)onComplete
                       onFailure:(BetableFailureHandler)onFailure;

This method is used to retrieve information about the wallet of the user associated with this betable object.

+  `onComplete`: This is a block that will be called if the server returns
   the request with a successful response code. It will be passed a
   dictionary that contains all of the JSON data returned from the
   betable server.
+  `onFailure`: This is a block that will be called if the server returns
   with an error. It gets passed the NSURLResponse object, the string
   reresentation of the body, and the NSError that was raised.
   betable server.

### Completion and Failure handlers

#### BetableAccessTokenHandler:

    typedef void (^BetableAccessTokenHandler)(NSString *accessToken);

This is called when token:onCompletion:onFailure successfully retrieves the access token.

#### BetableCompletionHandler:

    typedef void (^BetableCompletionHandler)(NSDictionary *data);

This is called when any of the APIs successfully return from the server.  `data` is a nested NSDictionary object that represents the JSON returned

#### BetableCompletionHandler:

    typedef void (^BetableFailureHandler)(NSURLResponse *response, NSString *responseBody, NSError *error);

This is called when something goes wrong in the request.  `error` will have details about the nature of the error and `responseBody` will be a string representation of the body of the response.
    

### Accessing the API URLs
    + (NSString*)getTokenURL;
    + (NSString*)getBetURL:(NSString*)gameID;
    + (NSString*)getWalletURL;
    + (NSString*)getAccountURL;

