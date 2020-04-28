---
title: 'Hämta en token i en webbapp som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du hämtar en token för en webbapp som anropar webb-API: er'
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
ms.openlocfilehash: 40e788099a159e1f60c0af02deccd7e3bef82744
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181740"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>En webbapp som anropar webb-API: er: Hämta en token för appen

Du har skapat ditt klient program objekt. Nu ska du använda den för att hämta en token för att anropa ett webb-API. Anropa ett webb-API i ASP.NET eller ASP.NET Core på kontrollanten:

- Hämta en token för webb-API: et med hjälp av token-cachen. För att hämta denna token anropar du `AcquireTokenSilent` MSAL-metoden (eller motsvarande i Microsoft. Identity. Web).
- Anropa det skyddade API: et och skicka åtkomsttoken till den som en parameter.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Styrenhets metoderna skyddas av ett `[Authorize]` attribut som tvingar användare att autentiseras att använda webbappen. Här är koden som anropar Microsoft Graph:

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

`ITokenAcquisition` Tjänsten injiceras av ASP.net med hjälp av beroende inmatning.

Här är den `HomeController`förenklade koden för åtgärden, som hämtar en token för att anropa Microsoft Graph:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

För att bättre förstå koden som krävs för det här scenariot, se steg 2 ([2-1 – webapps-anrop Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) i självstudien [MS-Identity-aspnetcore-webapp – självstudie](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Det finns andra komplexa variationer, till exempel:

- Anropar flera API: er.
- Bearbetning av stegvist godkännande och villkorlig åtkomst.

Dessa avancerade steg beskrivs i kapitel 3 i själv studie kursen [3-webapp-multi-API: er](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Koden för ASP.NET liknar koden som visas för ASP.NET Core:

- En kontroll enhets åtgärd, som skyddas av ett [auktorisera]-attribut, extraherar klient-ID `ClaimsPrincipal` och användar-ID för kontrollantens medlem. (ASP.NET använder `HttpContext.User`.)
- Därifrån skapar den ett MSAL.NET `IConfidentialClientApplication` -objekt.
- Slutligen anropar den `AcquireTokenSilent` metoden för det konfidentiella klient programmet.

# <a name="java"></a>[Java](#tab/java)

I Java-exemplet finns den kod som anropar ett API i getUsersFromGraph-metoden i [AuthPageController. java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Metoden försöker anropa `getAuthResultBySilentFlow`. Om användaren behöver samtycka till fler omfattningar, bearbetar koden `MsalInteractionRequiredException` objektet för att utmana användaren.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

I python-exemplet finns den kod som anropar Microsoft Graph i [app. py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Koden försöker hämta en token från token-cachen. När du har angett Authorization-huvudet anropas webb-API: et. Om det inte går att hämta en token loggas användaren in igen.

```python
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
> [Anropa en webb-API](scenario-web-app-call-api-call-api.md)
