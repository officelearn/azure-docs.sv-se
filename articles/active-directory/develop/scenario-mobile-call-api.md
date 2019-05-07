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
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd65b9f97c373c55a3486e06e83fca7cf824cad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075122"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Mobila appar som anropar webb-API: er – anropa ett webb-API

När din app har loggat in en användare och emot token, visar MSAL flera olika typer av information om användare, deras miljö och de token som utfärdas. Din app kan använda dessa värden för att anropa en webb-API eller visa ett välkomstmeddelande till en användare.

Först ska vi utforska MSAL resultatet och sedan hur du använder en åtkomsttoken från den `AuthenticationResult` eller `result` att anropa ett skyddat webb-API.

## <a name="msal-result"></a>MSAL resultat

- `AccessToken`: Används för att anropa skyddade webb-API: er i en HTTP-ägar-begäran.
- `IdToken`: Innehåller användbar anspråk om den inloggade användaren som deras namn, startklientorganisation och unik identifierare för lagring.
- `ExpiresOn`: förfallotid för token. MSAL hanterar automatisk uppdatering för appar.
- `TenantId`: Identifierare för användarens klient används för att logga in. Detta är den klient som användaren har loggat in med inte sina startklientorganisation för gästanvändare (Azure AD B2B).  
- `Scopes`: de omfång som har beviljats med din token. Detta kan vara en delmängd av vad du har begärt.

Dessutom MSAL också tillhandahåller en abstraktion för en `Account`. Ett konto representerar den aktuella användaren loggat in konto.

- `HomeAccountIdentifier`: Identifierare för användarens startklientorganisation.
- `UserName`: Användarens primära användarnamn. Detta kan vara tom för Azure AD B2C-användare.
- `AccountIdentifier`: Identifierare för den inloggade användaren. Det här är samma som den `HomeAccountIdentifier` i de flesta fall om inte användaren är gäst i en annan klient.

## <a name="calling-an-api"></a>Anropar en API

När du har den åtkomst-token som är klara är det enkelt att anropa ett webb-API. Din app tar detta token, skapa en HTTP-begäran och köra den.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called graph, process data and send to UI 
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put Access Token in HTTP request 
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

        // Put Access token in HTTP Request
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

            // Successfully got data from Graph
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put Access token in HTTP request 
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Flera API-förfrågningar

Om du behöver att anropa flera gånger för samma API eller flera API: er, det finns fler överväganden när du skapar din app:

- ***Inkrementell medgivande***: Microsoft identity-plattformen kan appar för att hämta användarens medgivande som behörighet är obligatoriska, i stället för alla startkostnader. Varje gång som din app är redo att anropa ett API som den ska begära de omfång som den har för avsikt att använda.
- ***Villkorlig åtkomst***: I vissa fall, kan du få ytterligare krav för villkorlig åtkomst vid flera API-begäranden. För att hantera det här scenariot måste du fånga upp fel från tyst begäranden och vara beredd på att göra en interaktiv begäran. Detta kan inträffa om den första begäran har inga principer för villkorlig åtkomst tillämpas och din app försöker komma åt tyst ett nytt API som kräver villkorlig åtkomst. Mer information finns i [vägledning för villkorlig åtkomst](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-mobile-production.md)
