---
title: Lägg till API-kopplingar till självbetjänings registrerings flöden – Azure AD
description: Konfigurera ett webb-API som ska användas i ett användar flöde.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6238e89b3941668f831f3128bb0e723a4097e48
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027520"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Lägga till en API-anslutning till ett användar flöde

Om du vill använda en [API-anslutning](api-connectors-overview.md)skapar du först API-anslutningen och aktiverar den sedan i ett användar flöde.

## <a name="create-an-api-connector"></a>Skapa en API-anslutning

1. Logga in till [Azure-portalen](https://portal.azure.com/) som Azure AD-administratör.
2. Under **Azure-tjänster**väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Välj **alla API-kopplingar (förhands granskning)** och välj sedan **ny API-anslutning**.

   ![Lägg till en ny API-anslutning](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Ange ett visnings namn för anropet. Du kan till exempel **kontrol lera godkännande statusen**.
6. Ange **slut punkts-URL** för API-anropet.
7. Ange autentiseringsinformation för API: et.

   - Det finns för närvarande endast stöd för grundläggande autentisering. Om du vill använda ett API utan grundläggande autentisering i utvecklings syfte anger du bara ett **användar namn** och **lösen ord** som ditt API kan ignorera. För användning med en Azure-funktion med en API-nyckel kan du inkludera koden som en frågeparameter i **slut punkts-URL: en** (till exempel https []() ://contoso.azurewebsites.NET/API/Endpoint<b>? Code = 0123456789</b>).

   ![Lägg till en ny API-anslutning](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Välj de anspråk som du vill skicka till API: et.
9. Välj eventuella anspråk som du planerar att ta emot tillbaka från API: et.

   ![Ange anspråk för API-koppling](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. Välj **Spara**.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Aktivera API-anslutningen i ett användar flöde

Följ dessa steg om du vill lägga till en API-anslutning till ett självbetjänings registrerings användar flöde.

1. Logga in till [Azure-portalen](https://portal.azure.com/) som Azure AD-administratör.
2. Under **Azure-tjänster**väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Välj **användar flöden (förhands granskning)** och välj sedan det användar flöde som du vill lägga till API-kopplingen till.
5. Välj **API-kopplingar**och välj sedan de API-slutpunkter som du vill anropa i följande steg i användar flödet:

   - **När du har loggat in med en identitets leverantör**
   - **Innan du skapar användaren**

   ![Lägga till API: er i användar flödet](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Välj **Spara**.

Lär dig mer om [var du kan aktivera en API-koppling i ett användar flöde](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow).

## <a name="request-sent-to-the-api"></a>Begäran skickades till API: et

En API-anslutning materialiseras som en HTTP POST-begäran, vilket skickar valda anspråk som nyckel/värde-par i en JSON-text. Svaret bör även ha HTTP-huvudet `Content-Type: application/json` . Attributen serialiseras på samma sätt som Microsoft Graph användarattribut. <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>Exempelbegäran

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Det **lokala användar gränssnittet (ui_locales)** skickas som standard i alla begär Anden. Den innehåller en användares nationella inställningar och kan användas av API: et för att returnera internationella svar. Den visas inte i fönstret API-konfiguration.

Om ett anspråk att skicka inte har något värde när API-slutpunkten anropas skickas inte anspråket till API: et.

Du kan skapa anpassade attribut för användaren med hjälp av **extension_ \<extensions-app-id> _AttributeName** format. Ditt API bör förvänta sig att ta emot anspråk i samma serialiserade format. Ditt API kan returnera anspråk med eller utan `<extensions-app-id>` . Mer information om anpassade attribut finns i [definiera anpassade attribut för](user-flow-add-custom-attributes.md)självbetjänings registrerings flöden.

> [!TIP] 
> [**identiteter (' Identities ')**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) och e- **postadressen (e-post)** kan användas för att identifiera en användare innan de har ett konto i din klient organisation. "Identities"-anspråk skickas när en användare autentiseras med ett Google eller Facebook och "e-post" alltid skickas.

## <a name="expected-response-types-from-the-web-api"></a>Förväntade svars typer från webb-API: et

När webb-API: et tar emot en HTTP-förfrågan från Azure AD under ett användar flöde kan det returnera följande svar:

- [Fortsättnings svar](#continuation-response)
- [Blockerar svar](#blocking-response)
- [Verifiering-fel svar](#validation-error-response)

### <a name="continuation-response"></a>Fortsättnings svar

Ett fortsättnings svar visar att användar flödet bör fortsätta till nästa steg. I ett fortsättnings svar kan API: t returnera anspråk.

Om ett anspråk returneras av API: et och väljs som ett **anspråk att ta emot**gör kravet följande:

- Fyller i startfält på sidan för attribut samling om API-kopplingen anropas innan sidan visas.
- Åsidosätter alla värden som redan har tilldelats till anspråket.
- Tilldelar ett värde till anspråket om det tidigare var null.

#### <a name="example-of-a-continuation-response"></a>Exempel på ett fortsättnings svar

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parameter                                          | Typ              | Obligatorisk | Beskrivning                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | Sträng            | Yes      | API-versionen.                                                                                                                                                                                                                                                                |
| åtgärd                                             | Sträng            | Yes      | Värdet måste vara `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | Värden kan lagras i katalogen om de har valts som ett **anspråk att ta emot** i API-anslutningens konfiguration **och användarattribut** för ett användar flöde. Värdena kan returneras i token om de väljs som ett **program anspråk**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | Det returnerade anspråket behöver inte innehålla `_<extensions-app-id>_` . Värdena lagras i katalogen om de valts som ett **anspråk att ta emot** i API-kopplingens konfiguration och **användarattribut** för ett användar flöde. Det går inte att skicka anpassade attribut tillbaka i token. |

### <a name="blocking-response"></a>Blockerar svar

Ett blockerande svar avslutar användar flödet. Det kan användas av API: et för att stoppa användar flödet genom att visa en blockera-sida för användaren. Sidan blockera visar den `userMessage` som tillhandahålls av API: et.

Exempel på spärrnings svar:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Parameter   | Typ   | Obligatorisk | Beskrivning                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | Sträng | Yes      | API-versionen.                                                    |
| åtgärd      | Sträng | Yes      | Värdet måste vara`ShowBlockPage`                                              |
| userMessage | Sträng | Yes      | Meddelande som ska visas för användaren.                                            |
| kod        | Sträng | No       | Felkod. Kan användas för fel söknings syfte. Visas inte för användaren. |

#### <a name="end-user-experience-with-a-blocking-response"></a>Slut användar upplevelse med ett blockerande svar

![Exempel på block sida](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>Verifiering-fel svar

Ett API-anrop som anropas när en attribut samlings sida kan returnera ett verifierings fel svar. När du gör det stannar användar flödet på sidan attribut samling och `userMessage` visas för användaren. Användaren kan sedan redigera och skicka formuläret igen. Den här typen av svar kan användas för verifiering av indatamängden.

#### <a name="example-of-a-validation-error-response"></a>Exempel på ett verifierings fel svar

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Parameter   | Typ    | Obligatorisk | Beskrivning                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | Sträng  | Yes      | API-versionen.                                                    |
| åtgärd      | Sträng  | Yes      | Värdet måste vara `ValidationError` .                                           |
| status      | Heltal | Yes      | Måste vara `400` ett värde för ett ValidationError-svar.                        |
| userMessage | Sträng  | Yes      | Meddelande som ska visas för användaren.                                            |
| kod        | Sträng  | No       | Felkod. Kan användas för fel söknings syfte. Visas inte för användaren. |

#### <a name="end-user-experience-with-a-validation-error-response"></a>Slut användar upplevelse med ett verifierings fel svar

![Exempel på validerings sida](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>Integrering med Azure-funktioner
Du kan använda en HTTP-utlösare i Azure Functions som ett enkelt sätt skapa ett API som ska användas med API-kopplingen. Du kan använda Azure-funktionen till att till [exempel](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)utföra validerings logik och begränsa registreringen till vissa domäner. Du kan också anropa och anropa andra webb-API: er, användar lager och andra moln tjänster.

## <a name="next-steps"></a>Nästa steg

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Lär dig hur du [lägger till ett anpassat godkännande arbets flöde till självbetjänings registrering](self-service-sign-up-add-approvals.md)
- Kom igång med våra [snabb starts exempel för Azure Function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
