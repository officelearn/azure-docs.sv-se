---
title: "Hanteraren för filserverresurser REST API: er | Microsoft Docs"
description: "En översikt över hanteraren för filserverresurser REST API: er autentiserings- och exempel"
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: e8d7a1d2-1e82-4212-8288-8697341408c5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: navale;tomfitz;
ms.openlocfilehash: 2f7ba23775545637de865f9ef63680ae22c62164
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="resource-manager-rest-apis"></a>REST API:er för Resurshanteraren
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [Azure CLI](xplat-cli-azure-resource-manager.md)
> * [Portal](resource-group-portal.md) 
> * [REST API](resource-manager-rest-api.md)
> 
> 

Bakom varje anrop till Azure Resource Manager bakom alla distribuerade mallen bakom alla konfigurerade storage-konto finns en eller flera anrop till Azure Resource Manager RESTful-API. Det här avsnittet är avsett för dessa API: er och hur du kan anropa dem utan att använda alla SDK alls. Den här metoden är användbar om du vill ha fullständig kontroll över begäranden till Azure eller om SDK för ditt språk är inte tillgänglig eller inte stöder åtgärderna som du behöver.

Den här artikeln går inte via varje API som exponeras i Azure, men i stället använder vissa åtgärder som exempel på hur du ansluter till dem. När du förstår grunderna kan du läsa den [Azure Resource Manager REST API Reference](https://docs.microsoft.com/rest/api/resources/) att hitta detaljerad information om hur du använder resten av API: erna.

## <a name="authentication"></a>Autentisering
Autentisering för Resource Manager hanteras av Azure Active Directory (AD). Om du vill ansluta till API: er, måste du först autentisera med Azure AD tar emot en token för autentisering som du kan vidarebefordra till varje begäran. Vi förutsätter att du inte vill autentisera med blir ombedd att ange ett användarnamn och lösenord som vi beskriver ett rent anrop direkt till REST-API: er. Vi förutsätter också att du inte använder två faktor autentiseringsmekanismer. Därför kan skapa vi en så kallad en Azure AD-program och en tjänst huvudnamn som används för att logga in. Men kom ihåg att Azure AD stöder flera autentiseringsprocedurer och alla kan användas för att hämta den autentiseringstoken som behövs för efterföljande API-begäranden.
Följ [skapa Azure AD-program och tjänstens huvudnamn](resource-group-create-service-principal-portal.md) steg-för-steg-instruktioner.

### <a name="generating-an-access-token"></a>Generera en åtkomst-Token
Autentisering mot Azure AD görs genom att anropa till Azure AD finns i login.microsoftonline.com. För att autentisera, måste du ha följande information:

* Azure AD-klient-ID (namnet på den Azure AD som du har inte nödvändiga men använder för att logga in ofta samma som ditt företag)
* Program-ID (tas under steget Skapa Azure AD-program)
* Lösenord (som du valde när du skapar Azure AD-program)

Se till att ersätta ”Azure AD-klient-ID”, ”program-ID” och ”Password” med de korrekta värdena i de följande HTTP-begäranden.

**Allmän HTTP-begäran:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... kommer (om autentiseringen lyckas) ger ett svar som liknar följande svar:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(Access_token i föregående svaret har minskats för att öka läsbarhet)

**Generera åtkomsttoken med hjälp av Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net/&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Generera åtkomsttoken med hjälp av PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

Svaret innehåller ett åtkomsttoken, information om hur länge den token är giltig och information om vilken resurs som du kan använda den token för.
Åtkomst-token som du fick i det föregående HTTP-anropet måste skickas för alla begäranden till Resource Manager-API. Du skickar det som ett värde med ett huvud med namnet ”tillstånd” med värdet ”ägar YOUR_ACCESS_TOKEN”. Observera avståndet mellan ”ägar” och åtkomst-token.

Som du ser ovan HTTP resultatet är token giltig för en specifik tidsperiod under vilken du bör cachelagra och återanvända samma säkerhetstoken. Även om det är möjligt att autentisera mot Azure AD för varje API-anrop, skulle det vara mycket ineffektiv.

## <a name="calling-resource-manager-rest-apis"></a>Anropar Resource Manager REST API: er
Det här avsnittet använder endast några API: er som förklarar grundläggande användning av REST-åtgärder. Information om alla åtgärder finns [Azure Resource Manager REST API: er](https://docs.microsoft.com/rest/api/resources/).

### <a name="list-all-subscriptions"></a>Visa en lista över alla prenumerationer
Ett av de enklaste åtgärder som du kan göra är att visa en lista över tillgängliga prenumerationer som du har åtkomst till. I följande request se du hur den åtkomst-token som skickas som en rubrik:

(Ersätt YOUR_ACCESS_TOKEN med ditt faktiska åtkomst-Token.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

... och därför kan du hämta en lista över prenumerationer som ska kunna använda den här tjänstens huvudnamn

(Prenumerations-ID: N har minskats för läsbarhet)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Visa en lista över alla resursgrupper i en viss prenumeration
Alla resurser som är tillgängliga med Resource Manager-API: er är kapslad i en resursgrupp. Du kan fråga Resource Manager för befintliga resursgrupper i din prenumeration med hjälp av följande HTTP GET-begäran. Observera hur prenumerations-ID skickas som en del av URL: en nu.

(Ersätt YOUR_ACCESS_TOKEN och PRENUMERATIONSID med ditt faktiska åtkomst-token och prenumerations-ID)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

Det svar du får beror på om du har alla resursgrupper som har definierats och i så fall, hur många.

(Prenumerations-ID: N har minskats för läsbarhet)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Hittills har har vi endast frågar Resource Manager-API: er för information. Det är dags vi skapa vissa resurser, och låt oss börja med den enklaste av allihop, en resursgrupp. Följande HTTP-begäran skapar en resursgrupp i en region/plats och lägger till en tagg.

(Ersätta YOUR_ACCESS_TOKEN, PRENUMERATIONSID, RESOURCE_GROUP_NAME med faktiska åtkomst-Token, prenumerations-ID och namnet på resursgruppen som du vill skapa)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Om det lyckas, får du ett svar som liknar följande svar:

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Du har skapat en resursgrupp i Azure. Grattis!

### <a name="deploy-resources-to-a-resource-group-using-a-resource-manager-template"></a>Distribuera resurser i en resursgrupp med en Resource Manager-mall
Med Resource Manager kan du distribuera dina resurser med hjälp av mallar. En mall definierar flera resurser och deras beroenden. För det här avsnittet förutsätter vi att du är bekant med Resource Manager-mallar och vi bara att visa hur du gör API-anrop för att starta distributionen. Mer information om hur du skapar mallar finns [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).

Distribution av en mall inte skilja sig mycket att hur du anropar andra API: er. En viktig aspekt är att distributionen av en mall kan ta lång tid. API-anrop returnerar bara och det är du som utvecklare kan ställa frågor om status för distributionen för att ta reda på när distributionen är klar. Mer information finns i [spåra asynkrona åtgärder i Azure](resource-manager-async-operations.md).

I det här exemplet använder vi ett offentligt exponerade mallen som finns på [GitHub](https://github.com/Azure/azure-quickstart-templates). Den mall som vi använder distribuerar en Linux VM till regionen västra USA. Även om det här exemplet används en mall som är tillgängliga i en offentlig databas som GitHub, kan du i stället skickar den fullständiga mallen som en del av begäran. Observera att vi parametervärden i begäran som används i den distribuerade mallen.

(Ersätta PRENUMERATIONSID, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, lösenord och DNS_NAME_FOR_PUBLIC_IP till värden som är lämpliga för din begäran)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

Lång JSON-svar för denna begäran har utelämnats att förbättra läsbarhet i den här dokumentationen. Svaret innehåller information om mallbaserat distributionen som du skapade.

## <a name="next-steps"></a>Nästa steg

- Läs om hur du hanterar asynkrona REST-åtgärder i [spåra asynkrona åtgärder i Azure](resource-manager-async-operations.md).
