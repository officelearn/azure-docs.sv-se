---
title: Kommunicerar med valfri slutpunkt över HTTP - Azure Logic Apps | Microsoft Docs
description: Skapa logikappar som kan kommunicera med valfri slutpunkt över HTTP
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
tags: connectors
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 355d1e02ef8195bcee469b77976f1a5834f192d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-the-http-action"></a>Kom igång med HTTP-åtgärd

Med HTTP-åtgärd kan du utöka arbetsflöden för din organisation, och kommunicera med valfri slutpunkt via HTTP.

Du kan:

* Skapa logik app arbetsflöden som aktiverar (utlösare) när en webbplats som du hanterar kraschar.
* Valfri slutpunkt att kommunicera via HTTP utökar dina arbetsflöden till andra tjänster.

Om du vill komma igång med HTTP-åtgärden i en logikapp, se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-trigger"></a>Använda HTTP-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definieras i en logikapp. [Mer information om utlösare](connectors-overview.md).

Här är en Exempelsekvens av hur du ställer in HTTP-utlösaren i logik App Designer.

1. Lägga till HTTP-utlösaren i din logikapp.
2. Fyll i parametrarna för den HTTP-slutpunkt som du vill söka.
3. Ändra upprepningsintervallet på hur ofta den ska avsöka.

   Logikappen utlöses nu med allt innehåll som returneras vid varje kontroll.

   ![HTTP-utlösare](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Så här fungerar HTTP-utlösare

HTTP-utlösaren skickar ett anrop till HTTP-slutpunkten med återkommande intervall. Som standard gör HTTP-svarskoden som är lägre än 300 en logikapp ska köras. Du kan redigera logikappen i kodvy och Lägg till ett villkor som utvärderar efter HTTP-anrop för att ange om logikappen ska utlösas. Här är ett exempel på en HTTP-utlösare som utlöses när den returnerade statuskoden är större än eller lika med `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

Fullständig information om HTTP-utlösaren parametrar finns på [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Använd HTTP-åtgärd

En åtgärd är en åtgärd som utförs av arbetsflödet som definieras i en logikapp. 
[Mer information om åtgärder](connectors-overview.md).

1. Välj **nytt steg** > **lägga till en åtgärd**.
3. Skriv i sökrutan åtgärd **http** att lista HTTP-åtgärder.
   
    ![Välj den HTTP-åtgärden](./media/connectors-native-http/using-action-1.png)

4. Lägg till eventuella parametrar för HTTP-anropet.
   
    ![Slutföra HTTP-åtgärd](./media/connectors-native-http/using-action-2.png)

5. Klicka på verktygsfältet designer **spara**. Din logikapp sparas och publiceras (aktiverad) på samma gång.

## <a name="http-trigger"></a>HTTP-utlösare
Här följer information om utlösaren som har stöd för den här anslutningen. HTTP-anslutningen har en utlösare.

| Utlösare | Beskrivning |
| --- | --- |
| HTTP |Gör ett HTTP-anrop och returnerar svaret innehållet. |

## <a name="http-action"></a>HTTP-åtgärd
Här följer information om vad som har stöd för den här anslutningen. HTTP-anslutningen har en möjlig åtgärd.

| Åtgärd | Beskrivning |
| --- | --- |
| HTTP |Gör ett HTTP-anrop och returnerar svaret innehållet. |

## <a name="http-details"></a>HTTP-information
I följande tabeller beskrivs de obligatoriska och valfria indatafält för åtgärden och detaljer om motsvarande utdata som är associerade med hjälp av åtgärden.

#### <a name="http-request"></a>HTTP-begäran
Följande är inmatningsfält för åtgärden, vilket gör en utgående HTTP-begäran.
A * innebär att det är ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| Metoden * |metod |HTTP-verbet som ska användas |
| URI * |URI |URI för HTTP-begäran |
| Sidhuvuden |rubriker |HTTP-huvuden med ett JSON-objekt |
| Innehåll |brödtext |Texten på HTTP-begäran |
| Autentisering |autentisering |Information i den [autentisering](#authentication) avsnitt |

<br>

#### <a name="output-details"></a>Utdatainformation
Nedan visas utdata information för HTTP-svaret.

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Sidhuvuden |objekt |Svarshuvud |
| Innehåll |objekt |Objektet Response |
| Statuskod |int |HTTP-statuskod |

## <a name="authentication"></a>Autentisering
Funktionen Logic Apps kan du använda olika typer av autentisering mot HTTP-slutpunkter. Du kan använda den här autentiseringen med den **HTTP**,  **[HTTP + Swagger](connectors-native-http-swagger.md)**, och **[HTTP Webhook](connectors-native-webhook.md)** kopplingar. Följande typer av autentisering kan konfigureras:

* [Grundläggande autentisering](#basic-authentication)
* [Autentisering av klientcertifikat](#client-certificate-authentication)
* [Azure Active Directory (AD Azure) OAuth-autentisering](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Grundläggande autentisering

Följande autentiseringsobjekt behövs för grundläggande autentisering.
A * innebär att det är ett obligatoriskt fält.

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Typen * |typ |Typ av autentisering (måste vara `Basic` för grundläggande autentisering) |
| Användarnamn * |användarnamn |Användarnamn för autentisering |
| Lösenord * |lösenord |Lösenord för att autentisera |

> [!TIP]
> Om du vill använda ett lösenord som inte kan hämtas från definition, Använd en `securestring` parameter och `@parameters()`  
>  [definition arbetsflödesfunktion](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow).

Exempel:

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Autentisering med klientcertifikat

Följande autentiseringsobjekt krävs för autentisering av klientcertifikat. A * innebär att det är ett obligatoriskt fält.

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Typen * |typ |Typ av autentisering (måste vara `ClientCertificate` för SSL-klientcertifikat) |
| PFX * |Pfx |Base64-kodad innehållet i filen Personal Information Exchange (PFX) |
| Lösenord * |lösenord |Lösenord för åtkomst till PFX-filen |

> [!TIP]
> Om du vill använda en parameter som inte läsas i definitionen när du har sparat logikappen som du kan använda en `securestring` parameter och `@parameters()`  
>  [definition arbetsflödesfunktion](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow).

Exempel:

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Azure AD OAuth-autentisering
Följande autentiseringsobjekt krävs för Azure AD OAuth-autentisering. A * innebär att det är ett obligatoriskt fält.

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Typen * |typ |Typ av autentisering (måste vara `ActiveDirectoryOAuth` för Azure AD OAuth) |
| Klient * |klient |Klient-ID för Azure AD-klient |
| Målgruppen * |målgrupp |Den resurs som du begär tillstånd att använda. Exempel: `https://management.core.windows.net/` |
| Klient -ID * |clientId |Klient-ID för Azure AD-program |
| Hemligt * |hemlighet |Hemligheten för klienten som begär token |

> [!TIP]
> Du kan använda en `securestring` parameter och `@parameters()` [definition arbetsflödesfunktion](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow) att använda en parameter som inte läsas i definitionen när du har sparat.
> 
> 

Exempel:

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Nästa steg
Prova nu, plattform och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Du kan utforska andra tillgängliga kopplingar i Logic Apps genom att titta på vår [API: er listan](apis-list.md).

