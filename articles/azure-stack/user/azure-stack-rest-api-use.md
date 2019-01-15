---
title: Använd Azure Stack API | Microsoft Docs
description: Lär dig hur du hämtar en autentisering från Azure för att göra API-begäranden till Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: fe516d1d34496d190ae45e00893deb646fc08408
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306564"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Använd Azure Stack API

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan använda Application Programming Interface (API) för att automatisera åtgärder som att lägga till en virtuell dator i Azure Stack-molnet.

API: et kräver klienten att autentisera till slutpunkten för Microsoft Azure-inloggning. Slutpunkten som returnerar en token som ska användas i huvudet i varje begäran som skickas till API: et för Azure Stack. Microsoft Azure använder Oauth 2.0.

Den här artikeln innehåller exempel som använder den **cURL** verktyget för att skapa Azure Stack-begäranden. Programmet cURL, är ett kommandoradsverktyg med ett bibliotek för att överföra data. De här exemplen igenom processen för att hämta en token för att få åtkomst till Azure Stack-API. De flesta programmeringsspråk tillhandahåller Oauth 2.0-bibliotek som har robust token uppgifter för och hantera sådana uppdateringen av token.

Gå igenom hela processen med hjälp av Azure Stack REST-API med en allmän REST-klient som **cURL**, för att hjälpa dig att förstå den underliggande begär och visar vad du kan förvänta dig att ta emot i en svarsnyttolasten.

Den här artikeln utforska inte alla tillgängliga alternativ för att hämta token, till exempel interaktiv inloggning eller skapa dedikerade App-ID: N. För att få information om de här ämnena finns i [Azure REST API-referens](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Hämta en token från Azure

Skapa en begärantext som formaterats med den innehållstyp x--www-form-urlencoded för att hämta en åtkomsttoken. PUBLICERA din begäran till slutpunkten Azure REST-autentisering och logga in.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**Klient-ID** är antingen:

 - Din Klientdomän, till exempel `fabrikam.onmicrosoft.com`
 - Din klient-ID: T, som `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - Standardvärde för klient-oberoende nycklar: `common`

### <a name="post-body"></a>Publicera brödtext

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

För varje värde:

 - **_typ av beviljande**  
    Typ av schema för autentiseringsmetoder som du ska använda. I det här exemplet är värdet `password`

 - **resource**  
    Resursen har åtkomst till att token. Du kan hitta resursen genom att fråga metadata för Azure Stack hanteringsslutpunkten. Titta på den **målgrupper** avsnittet

 - **Azure Stack-hanteringsslutpunkt**  
    ```
    https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
    ```

  > [!NOTE]  
  > Om du är administratör försöker få åtkomst till klientorganisations-API måste sedan du se till att använda klientorganisationsslutpunkt, till exempel: `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  Till exempel med Azure Stack Development Kit som en slutpunkt:

    ```bash
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

  Det här värdet är hårdkodad ett standardvärde:

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Alternativ är tillgängliga för specifika scenarier:

  
  | Program | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **användarnamn**

  Till exempel Azure Stack AAD-konto:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **Lösenord**

  Azure Stack AAD administratörslösenordet.

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

När du får din åtkomsttoken, måste du lägga till den som en rubrik i var och en av dina API-begäranden. För att kunna göra det måste du skapa en rubrik **auktorisering** med värde: `Bearer <access token>`. Exempel:

Begäran:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Svar:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>URL-struktur och frågesyntax

Allmän begäran URI, består av: {URI-schema} :// {URI-värden} / {resurssökväg}? {query-string}

- **URI-schema**:  
URI: N anger det protokoll som används för att skicka begäran. Till exempel `http` eller `https`.
- **URI-värd**:  
Värden anger domännamnet eller IP-adressen för servern där REST-tjänstslutpunkten finns, till exempel `graph.microsoft.com` eller `adminmanagement.local.azurestack.external`.
- **Resurssökväg**:  
Sökvägen anger den resurs eller resurssamling som kan innehålla flera segment som används av tjänsten fastställa valet av dessa resurser. Till exempel: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` kan användas för att fråga efter listan över ägare för ett visst program inom samlingen program.
- **Frågesträng**:  
Strängen som ger ytterligare enkla parametrar, till exempel API-versionen eller resursurvalskriterier.

## <a name="azure-stack-request-uri-construct"></a>Azure Stack-begäran-URI-konstruktion

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>URI-syntax

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Exempel på sökfråga URI

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du använder Azure RESTful-slutpunkter, [Azure REST API-referens](https://docs.microsoft.com/rest/api/).
