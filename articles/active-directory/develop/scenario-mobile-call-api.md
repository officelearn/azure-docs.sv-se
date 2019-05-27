---
title: 'Att anrop webb-API: er – anropa ett webb-API-mobilappen | Microsoft identity-plattformen'
description: 'Lär dig att skapa en mobilapp som anropar webb-API: er (anropa en webb-API)'
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44b6c203583a082228c2ba1f4c5f6fdb04d059be
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962380"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Mobila appar som anropar webb-API: er – anropa ett webb-API

När din app har loggat in en användare och emot token, visar MSAL flera olika typer av information om användare, användarens miljö och de token som utfärdas. Din app kan använda dessa värden för att anropa en webb-API eller visa ett välkomstmeddelande till användaren.

Först ska vi titta på MSAL resultatet. Sedan ska vi titta på hur du använder en åtkomsttoken från den `AuthenticationResult` eller `result` att anropa ett skyddat webb-API.

## <a name="msal-result"></a>MSAL resultat
MSAL innehåller följande värden: 

- `AccessToken`: Används för att anropa skyddade webb-API: er i en HTTP-ägar-begäran.
- `IdToken`: Innehåller användbar information om den inloggade användaren, som användarnamn, hem-klient och en unik identifierare för lagring.
- `ExpiresOn`: Förfallotid för token. MSAL hanterar automatisk uppdatering för appar.
- `TenantId`: Identifierare för den klient som användaren har loggat in med. Det här värdet kommer för gästanvändare (Azure Active Directory B2B) identifiera den klient som användaren har loggat in med inte användarens startklientorganisation.  
- `Scopes`: Scope som har beviljats med din token. Beviljade scope kan vara en delmängd av omfång som du har begärt.

MSAL innehåller också en abstraktion för en `Account`. En `Account` representerar det aktuella inloggade användarkontot.

- `HomeAccountIdentifier`: Identifierare för användarens startklientorganisation.
- `UserName`: Användarens primära användarnamn. Det kan vara tom för Azure Active Directory B2C-användare.
- `AccountIdentifier`: Identifierare för den inloggade användaren. Det här värdet ska vara samma som den `HomeAccountIdentifier` värdet i de flesta fall, om inte användaren är gäst i en annan klient.

## <a name="call-an-api"></a>Anropa ett API

När du har den åtkomst-token, är det enkelt att anropa ett webb-API. Din app använder token för att skapa en HTTP-begäran och kör sedan begäran.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put access token in HTTP request.
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph.
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put access token in HTTP request.
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Flera API-förfrågningar

Om du vill anropa flera gånger för samma API eller om du vill anropa flera API: er, ta hänsyn till följande när du skapar din app:

- **Inkrementell medgivande**: Microsoft identity-plattformen kan appar för att hämta användarens medgivande som krävs i stället alla i början. Varje gång som din app är redo att anropa ett API, bör det begära de omfång som måste användas.
- **Villkorlig åtkomst**: I vissa fall, kan du få ytterligare villkorlig åtkomstkrav när du gör flera API-begäranden. Detta kan inträffa om den första begäran har inga principer för villkorlig åtkomst tillämpas och din app försöker komma åt tyst ett nytt API som kräver villkorlig åtkomst. För att hantera det här scenariot måste du fånga upp fel från tyst begäranden och vara beredd på att göra en interaktiv begäran.  Mer information finns i [vägledning för villkorlig åtkomst](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-mobile-production.md)
