---
title: Utgående autentisering – Azure Scheduler
description: Lär dig hur du ställer in eller ta bort utgående autentisering för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 42d6ec93a3382f494b49fb574c4aee5e8eec142a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344356"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Utgående autentisering för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras tillbaka. Om du vill schemalägga jobb kan du [testa Azure Logic Apps istället](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Azure Scheduler-jobb kan behöva anropa tjänster som kräver autentisering, till exempel andra Azure-tjänster, Salesforce.com, Facebook och säker anpassade webbplatser. Tjänsten kallas kan fastställa om Scheduler-jobb kan komma åt de begärda resurserna. 

Scheduler har stöd för dessa autentiseringsmodeller: 

* *Klientcertifikatet* autentisering när du använder SSL/TLS-klientcertifikat
* *Grundläggande* autentisering
* *Active Directory-OAuth* autentisering

## <a name="add-or-remove-authentication"></a>Lägg till eller ta bort autentisering

* Lägg till autentisering i Scheduler-jobb när du skapar eller uppdaterar jobbet genom att lägga till den `authentication` JavaScript Object Notation (JSON) underordnat element till den `request` element. 

  Svar aldrig returneras hemligheter som skickas till tjänsten Scheduler via en PUT, PATCH eller POST-begäran i den `authentication` objekt. 
  Svar ange hemlig information till null eller använda en token för offentlig som representerar den autentiserade enheten. 

* Om du vill ta bort autentisering från ett Scheduler-jobb, uttryckligen kör en PUT eller PATCH-begäran på jobbet, och ange den `authentication` objektet till null. Svaret innehåller inte egenskaper för autentisering.

## <a name="client-certificate"></a>Klientcertifikat

### <a name="request-body---client-certificate"></a>Begärandetexten - klientcertifikat

När du lägger till autentisering med den `ClientCertificate` modellera, ange dessa ytterligare objekt i begärandetexten.  

| Element | Obligatoriskt | Beskrivning |
|---------|----------|-------------|
| **autentisering** (överordnade element) | Autentiseringsobjektet för att använda ett SSL-klientcertifikat |
| **typ** | Ja | Autentiseringstypen. Värdet är för SSL-klientcertifikat `ClientCertificate`. |
| **pfx** | Ja | Base64-kodad innehållet i PFX-filen |
| **Lösenord** | Ja | Lösenord för åtkomst till PFX-filen |
||| 

### <a name="response-body---client-certificate"></a>Svarstexten - klientcertifikat 

När en begäran skickas med autentiseringsinformation, innehåller svaret de här elementen för autentisering.

| Element | Beskrivning | 
|---------|-------------| 
| **autentisering** (överordnade element) | Autentiseringsobjektet för att använda ett SSL-klientcertifikat |
| **typ** | Autentiseringstypen. Värdet är för SSL-klientcertifikat `ClientCertificate`. |
| **certificateThumbprint** |Certifikatets tumavtryck |
| **certificateSubjectName** |Det unika ämnesnamnet för certifikatet |
| **certificateExpiration** | Certifikatets förfallodatum |
||| 

### <a name="sample-rest-request---client-certificate"></a>Exempel på REST-begäran - klientcertifikat

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

### <a name="sample-rest-response---client-certificate"></a>Exempelsvaret för REST - klientcertifikat

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

### <a name="request-body---basic"></a>Begärandetext – Basic

När du lägger till autentisering med den `Basic` modellera, ange dessa ytterligare objekt i begärandetexten.

| Element | Obligatoriskt | Beskrivning |
|---------|----------|-------------|
| **autentisering** (överordnade element) | Autentiseringsobjektet för att använda grundläggande autentisering | 
| **typ** | Ja | Autentiseringstypen. För grundläggande autentisering, är värdet `Basic`. | 
| **användarnamn** | Ja | Användarnamnet för att autentisera | 
| **Lösenord** | Ja | Lösenordet för autentisering |
|||| 

### <a name="response-body---basic"></a>Svarstexten – Basic

När en begäran skickas med autentiseringsinformation, innehåller svaret de här elementen för autentisering.

| Element | Beskrivning | 
|---------|-------------|
| **autentisering** (överordnade element) | Autentiseringsobjektet för att använda grundläggande autentisering |
| **typ** | Autentiseringstypen. För grundläggande autentisering, är värdet `Basic`. |
| **användarnamn** | Det autentiserade användarnamnet |
||| 

### <a name="sample-rest-request---basic"></a>REST exempelförfrågan – Basic

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

### <a name="sample-rest-response---basic"></a>Exempel på REST-svar – Basic

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

### <a name="request-body---active-directory-oauth"></a>Begärandetext - Active Directory-OAuth 

När du lägger till autentisering med den `ActiveDirectoryOAuth` modellera, ange dessa ytterligare objekt i begärandetexten.

| Element | Obligatoriskt | Beskrivning |
|---------|----------|-------------|
| **autentisering** (överordnade element) | Ja | Autentiseringsobjektet för att använda ActiveDirectoryOAuth autentisering |
| **typ** | Ja | Autentiseringstypen. Värdet är för ActiveDirectoryOAuth autentisering, `ActiveDirectoryOAuth`. |
| **klient** | Ja | Klient-ID för Azure AD-klient. Du hittar klient-ID för Azure AD-klienten genom att köra `Get-AzureAccount` i Azure PowerShell. |
| **Målgrupp** | Ja | Det här värdet anges till `https://management.core.windows.net/`. | 
| **clientId** | Ja | Klient-ID för Azure AD-programmet | 
| **secret** | Ja | Hemligheten för den klient som begär token | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Svarstexten - Active Directory-OAuth

När en begäran skickas med autentiseringsinformation, innehåller svaret de här elementen för autentisering.

| Element | Beskrivning |
|---------|-------------|
| **autentisering** (överordnade element) | Autentiseringsobjektet för att använda ActiveDirectoryOAuth autentisering |
| **typ** | Autentiseringstypen. Värdet är för ActiveDirectoryOAuth autentisering, `ActiveDirectoryOAuth`. | 
| **klient** | Klient-ID för Azure AD-klient |
| **Målgrupp** | Det här värdet anges till `https://management.core.windows.net/`. |
| **clientId** | Klient-ID för Azure AD-programmet |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Exempel på REST-begäran - Active Directory-OAuth

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

### <a name="sample-rest-response---active-directory-oauth"></a>Exempel på REST-svar – Active Directory-OAuth

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

## <a name="see-also"></a>Se också

* [Vad är Azure Scheduler?](scheduler-intro.md)
* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)
* [Azure Scheduler REST API](https://msdn.microsoft.com/library/mt629143)
* [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)
