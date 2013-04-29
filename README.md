IOSLinkedInAPI
==============
A small non intrucive library that makes it easy to authenticate and authorize against LinkedIn using OAuth2.
The API uses a UIWebView to authenticate against LinkedIn.
<p align="center" >
  <img src="https://raw.github.com/jeyben/IOSLinkedInAPI/master/gh-pages/authenticate-example.png" alt="Authentication" title="Authentication">
</p>

If the enduser is authenticated you end up with an accesstoken that is nessesary to retrieve data from the LinkedIn [API](https://developer.linkedin.com/apis)

Example
-------

A LinkedIn client is created using a LIALinkedInApplication.
A LIALinkedInApplication defines the application which is granted access to the users linkedin data.
``` objective-c
LIALinkedInApplication *application = [LIALinkedInApplication applicationWithRedirectURL:@"http://www.ancientprogramming.com"
                                                                                    clientId:@"clientId"
                                                                                clientSecret:@"clientSecret"
                                                                                       state:@"DCEEFWF45453sdffef424"
                                                                               grantedAccess:@[@"r_fullprofile", @"r_network"]];
LIALinkedInHttpClient *client = [LIALinkedInHttpClient clientForApplication:application];
```
* redirectURL: has to be a valid http or https url, but other than that, the endpoint doesn't have to respond anything.
* clientId: The id which is provided by LinkedIn, when registering an application.
* clientSecret: The secret which is provided by LinkedIn, when registering an application.
* grantedAccess: An array telling which access the application would like to be granted by the enduser. See full list here: http://developer.linkedin.com/documents/authentication

Afterwards the client can be used to retrieve an accesstoken:
``` objective-c
client getAuthorizationCode:^(NSString * code) {
    [self.client getAccessToken:code success:^(NSDictionary *accessTokenData) {
        NSString *accessToken = [accessTokenData objectForKey:@"access_token"];
        [self.client getPath:[NSString stringWithFormat:@"https://api.linkedin.com/v1/people/~?oauth2_access_token=%@&format=json", accessToken] parameters:nil success:^(AFHTTPRequestOperation * operation, NSDictionary *result) {
            NSLog(@"current user %@", result);
        } failure:^(AFHTTPRequestOperation * operation, NSError *error) {
            NSLog(@"failed to fetch current user %@", error);
        }];
    } failure:^(NSError *error) {
        NSLog(@"Quering accessToken failed %@", error);
    }];
} cancel:^{
    NSLog(@"Authorization was cancelled by user");
} failure:^(NSError *error) {
    NSLog(@"Authorization failed %@", error);
}];

```

Next step
--------------------
The library is currently only handling the authentication and authorization.
I would like to improve the libary to also make it easy to do the actually API calls.
My current thought is to let the client remember the accessToken after it is retrieved and afterwards automatically append it to futher calls along with the format=json GET parameter.

If you have other good ideas of how that could be implemented let me know.

http://www.ancientprogramming.com