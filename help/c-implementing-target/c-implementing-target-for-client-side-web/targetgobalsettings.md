---
description: Information about the targetGlobalSettings() function for at.js. 
keywords: targetGlobalSettings;targetglobalsettings;globalSettings;globalsettings;global settings;at.js;functions;function;clientCode;clientcode;serverDomain;serverdomain;cookieDomain;cookiedomain;crossDomain;crossdomain;timeout;globalMboxAutoCreate;visitorApiTimeout;defaultContentHiddenStyle;defaultContentVisibleStyle;bodyHiddenStyle;bodyHidingEnabled;imsOrgId;secureOnly;overrideMboxEdgeServer;overrideMboxEdgeServerTimeout;optoutEnabled;optout;opt out;selectorsPollingTimeout;dataProviders
seo-description: Information about the targetGlobalSettings() function for the Adobe Target at.js JavaScript library.
seo-title: Information about the targetGlobalSettings() function for the Adobe Target at.js JavaScript library.
solution: Target
subtopic: Getting Started
title: targetGlobalSettings()
topic: Standard
---

# targetGlobalSettings()

You can override settings in the at.js library using `targetGlobalSettings()`, rather than configuring the settings in the [!DNL Target] Standard/Premium UI or by using REST APIs.

There are use cases, especially when at.js is delivered via [!DNL Dynamic Tag Management] (DTM) when you would like to override some of the settings.

## Settings {#section_42C759AE9B524A43B8659018677224B8}

You can override the following settings:

| Settings | Type | Default Value | Description |
|--- |--- |--- |--- |
|clientCode|String|Value set via UI|Represents client code|
|serverDomain|String|Value set via UI|Represents Target edge server|
|cookieDomain|String|If possible set to top level domain|Represents the domain used when saving cookies|
|crossDomain|String|Value set via UI|Indicates whether cross-domain tracking is enabled or not.<br>The allowed values are:<ul><li>disabled</li><li>enabled</li><li>x-only</li></ul>|
|timeout|Number|Value set via UI|Represents Target edge request timeout|
|globalMboxAutoCreate|Boolean|Value set via UI|Indicates whether the global mbox request should be fired or not|
|visitorApiTimeout|Number|2000 ms = 2 s|Represents the Visitor API request timeout|
|enabled|Boolean|true|Indicates whether at.js as library is enabled, meaning if it should execute anything or not. The main use case for this setting being opt-out cookies or other custom decisions that would disable  at.js  functionality|
|defaultContentHiddenStyle|String|visibility: hidden|Used only for wrapping mboxes that use DIV with class name "mboxDefault" and are executed via `mboxCreate()`, `mboxUpdate()`, or `mboxDefine()` to hide default content|
|defaultContentVisibleStyle|String|visibility: visible|Used only for wrapping mboxes that use DIV with class name "mboxDefault" and are executed via `mboxCreate()`, `mboxUpdate()`, or `mboxDefine()` to reveal applied offer if any or default content|
|bodyHiddenStyle|String|body { opacity: 0 }|Used only when `globalMboxAutocreate === true` to minimize the chance of flicker.<br>For more information, see [How at.js Manages Flicker](/help/c-implementing-target/c-implementing-target-for-client-side-web/c-how-atjs-works/manage-flicker-with-atjs.md).|
|bodyHidingEnabled|Boolean|true|Used to control flicker when `target-global-mbox` is used to deliver offers created in the  Visual Experience Composer , also known as visual offers|
|imsOrgId|String|IMS ORG ID|Represents the IMS ORG ID|
|secureOnly|Boolean|false|Indicates whether  at.js  should use HTTPS only or be allowed to switch between HTTP and HTTPS based on the page protocol.|
|overrideMboxEdgeServer|Boolean|true (true beginning with at.js version 1.6.2)|Indicates if we should use `<clientCode>.tt.omtrdc.net` domain or `mboxedge<clusterNumber>.tt.omtrdc.net` domain.<br>If this value is true, `mboxedge<clusterNumber>.tt.omtrdc.net` domain will be saved to a cookie|
|overrideMboxEdgeServerTimeout|Number|1860000 => 31 minutes|Indicates the cookie lifetime that contains the `mboxedge<clusterNumber>.tt.omtrdc.net` value.|
|optoutEnabled|Boolean|false|Indicates whether Target should call the Visitor API `isOptedOut()` function. This is part of Device Graph enablement.|
|selectorsPollingTimeout|Number|5000 ms = 5 s|In at.js 0.9.6, Target introduced this new setting that can be overridden via `targetGlobalSettings`.<br>`selectorsPollingTimeout` represents how long the client is willing to wait for all the elements identified by selectors to appear on the page.<br>Activities created via the Visual Experience Composer (VEC) have offers that contain selectors.|
|dataProviders|See "Data Providers" below.|See "Data Providers" below.|See "Data Providers" below.|

## Usage {#section_9AD6FA3690364F7480C872CB55567FB0}

This function can be defined before at.js is loaded or in **[!UICONTROL Setup]** > **[!UICONTROL Implementation]** > **[!UICONTROL Edit at.js Settings]** > **[!UICONTROL Code Settings]** > **[!UICONTROL Library Header]**.

The Library Header field allows you to enter free-form JavaScript. The customization code should look something similar to the following example:

```
window.targetGlobalSettings = {  
   timeout: 200, // using custom timeout  
   visitorApiTimeout: 500, // using custom API timeout  
   enabled: document.location.href.indexOf('https://www.adobe.com') >= 0 // enabled ONLY on adobe.com  
};
```

## Data Providers {#data-providers}

This setting lets customers gather data from third-party data providers, such as Demandbase, BlueKai, and custom services, and pass the data to Target as mbox parameters in the global mbox request. It supports the collection of data from multiple providers via async and sync requests. Using this approach makes it easy to manage flicker of default page content, while including independent timeouts for each provider to limit the impact on page performance

>[!NOTE]
>
>Data Providers requires [!DNL at.js] 1.3 or later.

The following videos contain more information:

| Video | Description |
|--- |--- |
|[Using Data Providers in Adobe Target](https://helpx.adobe.com/target/kt/using/dataProviders-atjs-feature-video-use.html)|Data Providers is a capability that allows you to easily pass data from third parties to Target. A third party could be a weather service, a DMP, or even your own web service. You can then use this data to build audiences, target content, and enrich the visitor profile.|
|[Implement Data Providers in Adobe Target](https://helpx.adobe.com/target/kt/using/dataProviders-atjs-technical-video-implement.html)|Implementation details and examples of how to use Adobe Target's dataProviders feature to retrieve data from third-party data providers and pass it in the Target request.|

The `window.targetGlobalSettings.dataProviders` setting is an array of data providers.

Each data provider has the following structure:

| Key | Type | Description |
|--- |--- |--- |
|name|String|Name of provider.|
|version|String|Provider version. This key will be used for provider evolution.|
|timeout|Number|Represents the provider timeout if this is a network request.  This key is optional.|
|provider|Function|The function that contains the provider data fetching logic.<br>The function has a single required parameter: `callback`. The callback parameter is a function that should be invoked only when the data has been successfully fetched or there is an error.<br>The callback expects two parameters:<ul><li>error: Indicates if an error occurred. If everything is OK, then this parameter should be set to null.</li><li>params: A JSON object, representing the parameters that will be sent in a Target request.</li></ul>|

The following example shows where the data provider is using sync execution:

```
var syncDataProvider = { 
  name: "simpleDataProvider", 
  version: "1.0.0", 
  provider: function(callback) { 
    callback(null, {t1: 1}); 
  } 
}; 
  
window.targetGlobalSettings = { 
  dataProviders: [ 
    syncDataProvider 
  ] 
};
```

After at.js processes `window.targetGlobalSettings.dataProviders`, the Target request will contain a new parameter: `t1=1`.

The following is an example if the parameters that you want to add to the Target request are fetched from a third-party service, such as Bluekai, Demandbase, and so forth:

```
var blueKaiDataProvider = { 
   name: "blueKai", 
   version: "1.0.0", 
   provider: function(callback) { 
      // simulating network request 
     setTimeout(function() { 
       callback(null, {t1: 1, t2: 2, t3: 3}); 
     }, 1000); 
   } 
} 
  
window.targetGlobalSettings = { 
   dataProviders: [ 
      blueKaiDataProvider 
   ] 
};
```

After at.js processes `window.targetGlobalSettings.dataProviders`, the Target request will contain additional parameters: `t1=1`, `t2=2` and `t3=3`.

The following example uses data providers to collect weather API data and send it as parameters in a Target request. The Target request will have additional params, such as `country` and `weatherCondition`.

```
var weatherProvider = { 
      name: "weather-api", 
      version: "1.0.0", 
      timeout: 2000, 
      provider: function(callback) { 
        var API_KEY = "caa84fc6f5dc77b6372d2570458b8699"; 
        var lat = 44.426767399999996; 
        var lon = 26.1025384; 
        var url = "//api.openweathermap.org/data/2.5/weather?lang=en"; 
        var data = { 
          lat: lat, 
          lon: lon, 
          appId: API_KEY 
        } 
 
        $.ajax({ 
          type: "GET", 
                url: url, 
          dataType: "json", 
          data: data, 
          success: function(data) { 
            console.log("Weather data", data); 
            callback(null, { 
              country: data.sys.country, 
              weatherCondition: data.weather[0].main 
            }); 
          }, 
          error: function(err) { 
            console.log("Error", err); 
            callback(err); 
          } 
        });         
      } 
    }; 
 
    window.targetGlobalSettings = { 
      dataProviders: [weatherProvider] 
    };
```

Consider the following when working with the `dataProviders` setting:

* If the data providers added to `window.targetGlobalSettings.dataProviders` are async, they will be executed in parallel. The Visitor API request will be executed in parallel with functions added to `window.targetGlobalSettings.dataProviders` to allow a minimum wait time. 
* at.js won't try to cache the data. If the data provider fetches data only once, the data provider should make sure that data is cached and, when the provider function is invoked, serve the cache data for the second invocation.
