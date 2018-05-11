---
title: Använda Azure API-stacken | Microsoft Docs
description: Lär dig hur du hämtar en autentisering från Azure för att se API-begäranden till Azure-stacken.
services: azure-stack
documentationcenter: ''
author: cblackuk
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 2bbfe4f829ad5c42a5742fdf08f2d185af627f42
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
<!--  cblackuk and charliejllewellyn -->

# <a name="use-the-azure-stack-api"></a>Använda Azure API-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan använda Azure Stack-API för att automatisera åtgärder, till exempel publicerar marketplace-objekt.

Med API kräver klienten att autentisera mot slutpunkten för Microsoft Azure-inloggning. Slutpunkten returnerar en token för i rubriken för varje begäran som skickades till API: et för Azure-stacken. (Microsoft Azure använder Oauth 2.0).

Den här artikeln innehåller exempel som använder du curl för att skapa Azure Stack-begäranden. De här exemplen går igenom processen att hämta en token för att komma åt Azure Stack-API. De flesta programmeringsspråk tillhandahålla Oauth 2.0-bibliotek som har robust token uppgifter för och hantera dessa uppdatera token.

Tittar på hela processen i Azure-stacken REST API med en generisk REST-klient som curl kan hjälpa dig att förstå de underliggande begäranden och visar vad du kan förvänta dig att ta emot i nyttolast för ett svar.

Den här artikeln utforska inte alla alternativ som är tillgängliga för hämtning av token som interaktiv inloggning eller skapa dedikerade App-ID: N. Mer information finns i [Azure REST API-referens](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Hämta en token från Azure

Skapa en förfrågan *brödtext* formaterats med den content-type x-www-formuläret-urlencoded för att hämta en åtkomst-token. SKICKA din begäran till slutpunkten Azure REST autentisering och inloggning.

```
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**Klient-ID** är antingen:

* Din klient-domän, till exempel fabrikam.onmicrosoft.com
* Klient-ID, till exempel 8eaed023-2b34-4da1-9baa-8bc8c9d6a491
* Standardvärdet för klient-oberoende nycklar: vanliga

### <a name="post-body"></a>Bokför brödtext

```
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

För varje värde:

  **grant_type**

  Typ av autentiseringsschema som ska använda. I det här exemplet är värdet:

  ```
  password
  ```

  **Resursen**

  Resursen har åtkomst till att token. Du kan hitta resursen genom att fråga Azure Stack management metadataslutpunkten. Titta på den **målgrupper** avsnitt

  Azure-stacken Management-slutpunkten:

  ```
  https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
  ```

 > [!NOTE]
 > Om du är administratör försöker få åtkomst till klientorganisations-API och sedan måste du se till att använda klient-slutpunkt, till exempel: 'https://adminmanagement.{region}.{Azure Stack domän} / metadata/slutpunkter? api-version 2015-01-011 =

  Till exempel med Azure-stacken Development Kit som en slutpunkt:

  ```
  curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
  ```

  Svar:

  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
      "loginEndpoint":"https://login.windows.net/",
      "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
      }
  }
  ```

### <a name="example"></a>Exempel

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  Det här värdet är hårdkodad till ett standardvärde:

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Alternativ är tillgängliga för specifika scenarier:

  
  | Program | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-C000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **Användarnamn**

  Till exempel Azure Stack AAD-konto:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **Lösenord**

  Azure-stacken AAD administratörslösenord.

### <a name="example"></a>Exempel

Begäran:

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Svar:

```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>API-frågor

När du har fått åtkomst-token, måste du lägga till den som en rubrik i var och en av dina API-begäranden. För att kunna göra det, måste du skapa ett huvud **auktorisering** med värde: `Bearer <access token>`. Exempel:

Begäran:

```
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Svar:

```
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>URL: en struktur och frågesyntax

Allmän begäran URI, som består av: {URI-schema} :// {URI-värden} / {resurssökvägen}? {frågesträngen}

- **URI-schema**:  
URI: N anger det protokoll som används för att skicka begäran. Till exempel `http` eller `https`.
- **URI-värden**:  
Värden anger domännamnet eller IP-adressen för servern där REST tjänstslutpunkten finns som `graph.microsoft.com` eller `adminmanagement.local.azurestack.external`.
- **Resurssökvägen**:  
Sökvägen anger resurs eller resurssamling som kan innehålla flera segment som används av tjänsten för att fastställa valet av dessa resurser. Till exempel: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` kan användas för att fråga efter listan ägare för ett visst program inom samlingen program.
- **Frågesträng**:  
Strängen innehåller ytterligare enkla parametrar, till exempel API-version eller resurs urvalskriterier.

## <a name="azure-stack-request-uri-construct"></a>Azure Stack begäran URI konstruktion

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>URI-syntax

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Frågan URI-exempel

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure RESTful slutpunkter finns [Azure REST API-referens](https://docs.microsoft.com/rest/api/).
