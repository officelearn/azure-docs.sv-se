---
title: 'Webbapp som anropar webb-API: er (hämtar en token för appen) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en webbapp som anropar webb-API: er (hämtar en token för appen)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fd66dcd6e3845aad79ebffb3cad656d0a14c1a6
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720223"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webbapp som anropar webb-API: er – hämta en token för appen

Nu när du har skapat ett klient program objekt använder du det för att hämta en token för att anropa ett webb-API. Anropa ett webb-API i ASP.NET eller ASP.NET Core och sedan utföras i kontrollanten. Det är ungefär:

- Hämta en token för webb-API: et med hjälp av token cache. Om du vill hämta den här token anropar `AcquireTokenSilent`du.
- Anropar det skyddade API: t med åtkomsttoken.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Styrenhets metoderna skyddas av ett `[Authorize]` attribut som tvingar användare att autentiseras att använda webbappen. Här är den kod som anropar Microsoft Graph.

```CSharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

Tjänsten `ITokenAcquisition` injiceras av ASP.NET via beroende inmatning.


Här är en förenklad kod för åtgärden för HomeController, som hämtar en token för att anropa Microsoft Graph.

```CSharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

// use the access token to call a protected web API
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Add("Authorization", result.CreateAuthorizationHeader());
string json = await client.GetStringAsync(url);
}
```

Information om den kod som krävs för det här scenariot finns i steg 2 ([2-1 – webapps-anrop Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) i själv studie kursen [MS-Identity-aspnetcore-webapp – självstudie](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Det finns många ytterligare komplicerade faktorer, till exempel:

- Anropa flera API: er,
- bearbetning av stegvist godkännande och villkorlig åtkomst.

Dessa avancerade steg bearbetas i kapitel 3 i självstudien [3-webapp-multi-API: er](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Saker liknar varandra i ASP.NET:

- En kontroll enhets åtgärd som skyddas av ett [auktorisera]-attribut extraherar klient-ID: t och användar-ID: t för kontrollantens `ClaimsPrincipal`-medlem. (ASP.NET använder `HttpContext.User`.)
- Därifrån skapar den en MSAL.NET `IConfidentialClientApplication`.
- Slutligen anropar `AcquireTokenSilent` den metoden för det konfidentiella klient programmet.

Koden liknar den kod som visas för ASP.NET Core.

# <a name="javatabjava"></a>[Java](#tab/java)

I Java-exemplet finns den kod som anropar ett API i getUsersFromGraph-metoden [AuthPageController. java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Det försöker anropa `getAuthResultBySilentFlow`. Om användaren behöver samtycka till fler omfattningar bearbetar koden `MsalInteractionRequiredException` för att utmana användaren.

```java
@RequestMapping("/msal4jsample/graph/users")
    public ModelAndView getUsersFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
            throws Throwable {

        IAuthenticationResult result;
        ModelAndView mav;
        try {
            result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
        } catch (ExecutionException e) {
            if (e.getCause() instanceof MsalInteractionRequiredException) {

                // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
                // so user can consent to new scopes
                String state = UUID.randomUUID().toString();
                String nonce = UUID.randomUUID().toString();

                SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

                String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                        httpRequest.getParameter("claims"),
                        "User.ReadBasic.all",
                        authHelper.getRedirectUriGraphUsers(),
                        state,
                        nonce);

                return new ModelAndView("redirect:" + authorizationCodeUrl);
            } else {

                mav = new ModelAndView("error");
                mav.addObject("error", e);
                return mav;
            }
        }
    // Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

I python-exemplet finns koden som anropar Microsoft Graph i [app. py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Det försöker hämta en token från token-cachen och anropar sedan EB-API: et när du har angett Authorization-huvudet. Om den inte kan det, så signeras den igen i användaren.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa en webb-API](scenario-web-app-call-api-call-api.md)
