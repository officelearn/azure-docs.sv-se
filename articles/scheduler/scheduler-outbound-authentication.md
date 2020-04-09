---
title: Utgående autentisering
description: Lär dig hur du konfigurerar eller tar bort utgående autentisering för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 0a8d79af9f45731971cb1be1f39fc193f9d0f0d9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878977"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Utgående autentisering för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som [dras tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du har konfigurerat i Scheduler [migrerar](../scheduler/migrate-from-scheduler-to-logic-apps.md) du till Azure Logic Apps så snart som möjligt. 
>
> Scheduler är inte längre tillgängligt i Azure-portalen, men [REST API-](/rest/api/scheduler) och [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) förblir tillgängliga just nu så att du kan hantera dina jobb och jobbsamlingar.

Azure Scheduler-jobb kan behöva anropa tjänster som kräver autentisering, till exempel andra Azure-tjänster, Salesforce.com, Facebook och säkra anpassade webbplatser. Den anropade tjänsten kan avgöra om Scheduler-jobbet kan komma åt de begärda resurserna. 

Scheduler stöder dessa autentiseringsmodeller: 

* *Autentisering av klientcertifikat* vid användning av SSL/TLS-klientcertifikat
* *Grundläggande* autentisering
* *OAuth-autentisering i Active Directory*

## <a name="add-or-remove-authentication"></a>Lägga till eller ta bort autentisering

* Om du vill lägga till autentisering i ett Scheduler-jobb lägger du till det underordnade elementet `authentication` JavaScript Object Notation (JSON) i elementet `request` när du skapar eller uppdaterar jobbet. 

  Svar returnerar aldrig hemligheter som skickas till Scheduler-tjänsten via en `authentication` PUT-, PATCH- eller POST-begäran i objektet. 
  Svar anger hemlig information till null eller kan använda en offentlig token som representerar den autentiserade entiteten. 

* Om du vill ta bort autentisering från ett Scheduler-jobb kör `authentication` du uttryckligen en PUT- eller PATCH-begäran på jobbet och anger att objektet ska null. Svaret innehåller inga autentiseringsegenskaper.

## <a name="client-certificate"></a>Klientcertifikat

### <a name="request-body---client-certificate"></a>Handtext för begäran - Klientcertifikat

När du lägger till autentisering med `ClientCertificate` hjälp av modellen anger du dessa ytterligare element i begärandetexten.  

| Element | Krävs | Beskrivning |
|---------|----------|-------------|
| **autentisering** (överordnat element) | Autentiseringsobjektet för att använda ett SSL/TLS-klientcertifikat |
| **Typ** | Ja | Autentiseringstypen. För SSL/TLS-klientcertifikat är `ClientCertificate`värdet . |
| **Pfx** | Ja | Det base64-kodade innehållet i PFX-filen |
| **lösenord** | Ja | Lösenordet för åtkomst till PFX-filen |
||| 

### <a name="response-body---client-certificate"></a>Svarstext - Klientcertifikat 

När en begäran skickas med autentiseringsinformation innehåller svaret dessa autentiseringselement.

| Element | Beskrivning | 
|---------|-------------| 
| **autentisering** (överordnat element) | Autentiseringsobjektet för att använda ett SSL/TLS-klientcertifikat |
| **Typ** | Autentiseringstypen. För SSL/TLS-klientcertifikat är `ClientCertificate`värdet . |
| **certificateThumbprint** |Certifikatets tumavtryck |
| **certifikatSugnamn** |Certifikatet ämne framstående namn |
| **certifikatExpiration** | Certifikatets utgångsdatum |
||| 

### <a name="sample-rest-request---client-certificate"></a>Exempel på REST-begäran - Klientcertifikat

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>Exempel på REST-svar - Klientcertifikat

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Basic

### <a name="request-body---basic"></a>Begäran kropp - Grundläggande

När du lägger till autentisering med `Basic` hjälp av modellen anger du dessa ytterligare element i begärandetexten.

| Element | Krävs | Beskrivning |
|---------|----------|-------------|
| **autentisering** (överordnat element) | Autentiseringsobjektet för att använda grundläggande autentisering | 
| **Typ** | Ja | Autentiseringstypen. För grundläggande autentisering `Basic`är värdet . | 
| **Användarnamn** | Ja | Användarnamnet för att autentisera | 
| **lösenord** | Ja | Lösenordet för att autentisera |
|||| 

### <a name="response-body---basic"></a>Svarsorgan - Grundläggande

När en begäran skickas med autentiseringsinformation innehåller svaret dessa autentiseringselement.

| Element | Beskrivning | 
|---------|-------------|
| **autentisering** (överordnat element) | Autentiseringsobjektet för att använda grundläggande autentisering |
| **Typ** | Autentiseringstypen. För grundläggande autentisering `Basic`är värdet . |
| **Användarnamn** | Det autentiserade användarnamnet |
||| 

### <a name="sample-rest-request---basic"></a>Exempel på REST-begäran - Grundläggande

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>Exempel på REST-svar - Grundläggande

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>Brödtext för begäran – Active Directory OAuth 

När du lägger till autentisering med `ActiveDirectoryOAuth` hjälp av modellen anger du dessa ytterligare element i begärandetexten.

| Element | Krävs | Beskrivning |
|---------|----------|-------------|
| **autentisering** (överordnat element) | Ja | Autentiseringsobjektet för användning av ActiveDirectoryOAuth-autentisering |
| **Typ** | Ja | Autentiseringstypen. För ActiveDirectoryOAuth-autentisering `ActiveDirectoryOAuth`är värdet . |
| **Hyresgästen** | Ja | Klientidentifieraren för Azure AD-klienten. Om du vill hitta klientidentifieraren `Get-AzureAccount` för Azure AD-klienten körs du i Azure PowerShell. |
| **Publik** | Ja | Det här värdet `https://management.core.windows.net/`är inställt på . | 
| **klientId** | Ja | Klientidentifieraren för Azure AD-programmet | 
| **Hemliga** | Ja | Hemligheten för klienten som begär token | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Svarstext - Active Directory OAuth

När en begäran skickas med autentiseringsinformation innehåller svaret dessa autentiseringselement.

| Element | Beskrivning |
|---------|-------------|
| **autentisering** (överordnat element) | Autentiseringsobjektet för användning av ActiveDirectoryOAuth-autentisering |
| **Typ** | Autentiseringstypen. För ActiveDirectoryOAuth-autentisering `ActiveDirectoryOAuth`är värdet . | 
| **Hyresgästen** | Klientidentifieraren för Azure AD-klienten |
| **Publik** | Det här värdet `https://management.core.windows.net/`är inställt på . |
| **klientId** | Klientidentifieraren för Azure AD-programmet |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Exempel på REST-begäran - Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>Exempel på REST-svar - Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="next-steps"></a>Nästa steg

* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)
* [Referens för REST-API:et för Azure Scheduler](/rest/api/scheduler)
* [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)