---
title: "Undantagshantering & fel loggning scenario – Azure Logic Apps | Microsoft Docs"
description: "Beskriver en verklig användningsfall om avancerad undantagshantering och felloggning för Logikappar i Azure"
keywords: 
services: logic-apps
author: hedidin
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/29/2016
ms.author: LADocs; b-hoedid
ms.openlocfilehash: 044de27c75da93c95609110d2b73336c42f746fe
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scenario: Undantagshantering och felloggning för logic apps

Det här scenariot beskriver hur du kan utöka en logikapp för att bättre kunna stödja undantagshantering. Vi har använt en verklig användningsfall för att besvara frågan: ”Azure Logikappar stöder undantag och felhantering”?

> [!NOTE]
> Det aktuella schemat för Logikappar i Azure tillhandahåller en standardmall för åtgärden svar. Den här mallen innehåller både interna validering och felsvar som returneras från en API-app.

## <a name="scenario-and-use-case-overview"></a>Scenariot och Använd case-översikt

Här är artikeln användningsfall för det här scenariot: 

Välkända sjukvårdsorganisation arbetar oss att utveckla en lösning för Azure som skulle skapa en patient portal genom att använda Microsoft Dynamics CRM Online. De behövs för att skicka möte poster mellan patient Dynamics CRM Online-portalen och Salesforce. Vi har ombedd att använda den [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) standard för alla patientjournaler.

Projektet har två viktiga krav:  

* En metod för att logga poster som skickas från Dynamics CRM Online-portalen
* Ett sätt att visa alla fel som uppstått i arbetsflödet

> [!TIP]
> Se en övergripande video om det här projektet [integrering användargrupp](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Integration User Group").

## <a name="how-we-solved-the-problem"></a>Hur vi löste problemet

Vi valde [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/ "Azure Cosmos DB") som databas för logg- och fel-poster (Cosmos DB refererar till poster som dokument). Eftersom Azure Logikappar har en standardmall för alla svar kan vi inte att skapa ett anpassat schema. Kan vi skapa en API-app på **infoga** och **frågan** för både fel och loggfiler poster. Vi kan också definiera ett schema för varje API-App.  

Ett annat krav är att rensa poster efter ett visst datum. Cosmos DB har en egenskap som kallas [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Time to Live") (TTL) som tillåts att vi kan ange en **Time to Live** värde för varje post eller en samling. Den här funktionen elimineras behovet av att manuellt ta bort poster i Cosmos-databasen.

> [!IMPORTANT]
> Den här kursen måste du skapa en Cosmos-DB-databas och två samlingar (loggning och -fel).

## <a name="create-the-logic-app"></a>Skapa logikappen

Det första steget är att skapa logikappen och öppna appen i logik App Designer. I det här exemplet använder vi överordnad-underordnad logikappar. Vi förutsätter att det redan har skapat överordnat och kommer att skapa en logikapp för underordnade.

Eftersom vi ska logga posten från Dynamics CRM Online, låt oss börja längst upp. Vi måste använda en **begära** utlösare, eftersom logikappen överordnade utlöser underordnad.

### <a name="logic-app-trigger"></a>Logik apputlösare

Vi använder en **begära** utlösa som visas i följande exempel:

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


## <a name="steps"></a>Steg

Vi måste logga källan (request) för patient posten från Dynamics CRM Online-portalen.

1. Vi måste få en ny post för möte från Dynamics CRM Online.

   Utlösaren kommer från CRM ger oss med den **CRM PatentId**, **posttyp**, **ny eller uppdaterad post** (ny eller uppdatera booleskt värde), och  **SalesforceId**. Den **SalesforceId** kan vara null eftersom den används endast för en uppdatering.
   Vi få CRM-post med hjälp av CRM **PatientID** och **posttyp**.

2. Nu ska vi måste du lägga till våra DocumentDB API-app **InsertLogEntry** åtgärden som visas här i logik App Designer.

   **Infoga loggpost**

   ![Infoga loggpost](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Infoga fel post**

   ![Infoga loggpost](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Kontrollera att skapa poster fel**

   ![Villkor](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Källkoden för logik app

> [!NOTE]
> Följande exempel är bara exempel. Eftersom den här kursen är baserad på en implementering nu i produktion, värdet för en **Källnoden** kan inte visa egenskaper som är relaterade till att schemalägga ett möte. > 

### <a name="logging"></a>Loggning

Följande logik app kodexempel visar hur du hanterar loggning.

#### <a name="log-entry"></a>Loggpost

Här är källkoden logik app för att lägga till en loggpost.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Log-begäran

Här är begäran loggmeddelande anslås API-app.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Loggsvar

Här är svar loggmeddelande från API-app.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Nu ska vi titta på felhantering steg.

### <a name="error-handling"></a>Felhantering

Följande logik app kodexempel visar hur du kan implementera felhantering.

#### <a name="create-error-record"></a>Skapa Felpost

Här är källkoden för att skapa en Felpost logik app.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Infoga fel i Cosmos-DB - begäran

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Infoga fel i Cosmos-DB - svar

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Salesforce felsvaret

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Returnera svar tillbaka till överordnad logikapp

När du får svar kan överföra du svaret tillbaka till överordnad logikappen.

#### <a name="return-success-response-to-parent-logic-app"></a>Returnera lyckat svar till överordnade logikapp

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Returnera felsvar på överordnade logikapp

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB-databasen och -portalen

Vår lösning tillagda funktioner med [Cosmos DB](https://azure.microsoft.com/services/documentdb).

### <a name="error-management-portal"></a>Fel-hanteringsportalen

Du kan skapa en MVC-webbapp för att visa felposter från Cosmos DB om du vill visa felen. Den **lista**, **information**, **redigera**, och **ta bort** operations ingår i den aktuella versionen.

> [!NOTE]
> Redigera åtgärd: Cosmos DB ersätter hela dokumentet. Poster som visas i den **lista** och **detaljer** vyer är bara exempel. De är inte faktiska patient möte poster.

Här följer exempel på vår MVC-appinformation som skapats med metoden som beskrivits tidigare.

#### <a name="error-management-list"></a>Fel vid hantering av lista
![Fel vid lista](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Fel vid hantering av detaljerad vy
![Information om fel](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Log-hanteringsportalen

Om du vill visa loggfilerna kan skapat vi också en MVC-webbapp. Här följer exempel på vår MVC-appinformation som skapats med metoden som beskrivits tidigare.

#### <a name="sample-log-detail-view"></a>Exempel loggen detaljvy
![Loggen detaljvy](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Information om API-app

#### <a name="logic-apps-exception-management-api"></a>Logic Apps undantag hanterings-API

Vår öppen källkod Azure Logikappar undantag hanterings-API-app innehåller funktioner som beskrivs här – det finns två domänkontrollanter:

* **ErrorController** infogar en Felpost (dokument) i en DocumentDB-samling.
* **LogController** infogar en loggpost (dokument) i en DocumentDB-samling.

> [!TIP]
> Både domänkontrollanter använder `async Task<dynamic>` åtgärder, så att åtgärder att lösa vid körning, så att vi kan skapa DocumentDB-schemat i brödtexten för åtgärden. 
> 

Alla dokument i DocumentDB måste ha ett unikt ID. Vi använder `PatientId` och lägga till en tidsstämpel som konverteras till ett tidsstämpelvärde Unix (double). Vi trunkera värdet för att ta bort värdet bråkdelar.

Du kan visa källkoden för fel styrningen API [från GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Vi kallar API: et från en logikapp genom att använda följande syntax:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Uttrycket i föregående kodexemplet söker efter den *Create_NewPatientRecord* status för **misslyckades**.

## <a name="summary"></a>Sammanfattning

* Du kan enkelt implementera loggning och hantera fel i en logikapp.
* Du kan använda DocumentDB som en lagringsplats för logg- och fel-poster (dokument).
* Du kan använda MVC för att skapa en portal för att visa loggen och fel poster.

### <a name="source-code"></a>Källkod

Källkoden för Logic Apps undantag management API-program finns i det här [GitHub-lagringsplatsen](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "logik App undantag Management API").

## <a name="next-steps"></a>Nästa steg

* [Visa mer logik app exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Lär dig mer om hur du övervakar logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Skapa mallar för automatisk distribution för logic apps](../logic-apps/logic-apps-create-deploy-template.md)
