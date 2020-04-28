---
title: Anropa ett webb-API från en webbapp – Microsoft Identity Platform | Azure
description: 'Lär dig hur du skapar en webbapp som anropar webb-API: er (som anropar ett skyddat webb-API)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 84df33137566445015848655cfecb87ba67ef123
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181689"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>En webbapp som anropar webb-API: er: anropa ett webb-API

Nu när du har en token kan du anropa ett skyddat webb-API.

## <a name="call-a-protected-web-api"></a>Anropa ett skyddat webb-API

Att anropa ett skyddat webb-API beror på vilket språk och ramverk du väljer:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Här är den `HomeController`förenklade koden för åtgärden. Den här koden hämtar en token för att anropa Microsoft Graph. Koden har lagts till för att visa hur du anropar Microsoft Graph som ett REST API. URL: en för Microsoft Graph-API: n finns i filen appSettings. JSON och läses i en variabel med `webOptions`namnet:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 
  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   return me;
  }

  ViewData["Me"] = me;
  return View();
}
```

> [!NOTE]
> Du kan använda samma princip för att anropa alla webb-API: er.
>
> De flesta Azure Web API: er tillhandahåller en SDK som fören klar anropet till API: et. Detta gäller även för Microsoft Graph. I nästa artikel lär du dig var du hittar en själv studie kurs som illustrerar API-användning.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-web-app-call-api-production.md)
