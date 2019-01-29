---
title: Undantagshantering & fel loggning scenario – Azure Logic Apps | Microsoft Docs
description: Här är en verklig användningsfall om av avancerade undantag och fel vid inloggning i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: hedidin
ms.author: b-hoedid
ms.reviewer: estfan, LADocs
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: 58e59e4faa135e24124f494d90437b49caa30129
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098671"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scenario: Undantagshantering och felloggning för logic apps

Det här scenariot beskriver hur du kan utöka en logikapp för att bättre stödja undantagshantering. Vi har använt ett verkliga användningsfall för att besvara frågan: ”Azure Logic Apps stöder undantag och felhantering”?

> [!NOTE]
> Det aktuella schemat för Azure Logic Apps tillhandahåller en standardmall för åtgärden svar. Den här mallen innehåller både interna verifiering och felsvar som returneras från en API-app.

## <a name="scenario-and-use-case-overview"></a>Scenariot och Använd case-översikt

Här är budskapet som användningsfall för det här scenariot: 

Välkända inom organisationen ägnar åt att utveckla en Azure-lösning som ger en patientportal med hjälp av Microsoft Dynamics CRM Online. De krävs för att skicka avtalade tider mellan Dynamics CRM Online patientportal och Salesforce. Vi har ombedd att använda den [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) standard för alla patientjournaler.

Projektet hade två viktiga krav:  

* En metod för att logga poster som skickas från Dynamics CRM Online-portalen
* Ett sätt att visa alla fel som uppstått i arbetsflödet

> [!TIP]
> Se en övergripande video om det här projektet [Integration User Group](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Integration User Group").

## <a name="how-we-solved-the-problem"></a>Hur vi löste problemet

Vi valde [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") som datalager för och -poster (Cosmos DB refererar till poster som dokument). Eftersom Azure Logic Apps har en standardmall för alla svar, behöver vi inte att skapa ett anpassat schema. Vi kan skapa en API-app till **infoga** och **fråga** för både fel och poster. Vi kan också definiera ett schema för API-App.  

Ett annat krav var att rensa poster efter ett visst datum. Cosmos DB har en egenskap som kallas [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Time to Live") (TTL) som tillåts att vi kan ange en **Time to Live** värde för varje post eller en samling. Den här funktionen elimineras behovet av att manuellt ta bort poster i Cosmos DB.

> [!IMPORTANT]
> Den här kursen måste du skapa en Cosmos DB-databas och två samlingar (loggning och fel).

## <a name="create-the-logic-app"></a>Skapa logic app

Det första steget är att skapa logikappen och öppna appen i Logic App Designer. I det här exemplet använder vi överordnad-underordnad logikappar. Anta att vi redan har skapat överordnat och kommer att skapa en logikapp för underordnade.

Eftersom vi ska logga posten kommer ut från Dynamics CRM Online, låt oss börja längst upp. Vi måste använda en **begära** utlösare, eftersom den överordnade logikappen utlöser det här barnet.

### <a name="logic-app-trigger"></a>Logikapputlösare

Vi använder en **begära** utlösa som visas i följande exempel:

``` json
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

```


## <a name="steps"></a>Steg

Vi måste logga källan (begäran) till patientens posten från Dynamics CRM Online-portalen.

1. Vi måste få en ny post för avtalad tid från Dynamics CRM Online.

   Utlösaren som kommer från CRM ger oss med den **CRM PatentId**, **posttyp**, **ny eller uppdaterad post** (ny eller uppdatera booleskt värde), och  **SalesforceId**. Den **SalesforceId** kan vara null eftersom den används endast för en uppdatering.
   Vi hämta CRM-post med hjälp av CRM **PatientID** och **posttypen**.

2. Därefter behöver vi lägga till vår Azure Cosmos DB SQL API-app **InsertLogEntry** åtgärden som visas här i Logic App Designer.

   **Infoga loggpost**

   ![Infoga loggpost](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Infoga fel post**

   ![Infoga loggpost](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Sök efter skapa poster fel**

   ![Tillstånd](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Källkoden för Logic app

> [!NOTE]
> I följande exempel är bara exempel. Eftersom den här självstudien är baserad på en implementering nu i produktion, värdet för en **Källnoden** kanske inte visar egenskaper som är relaterade till schemaläggning av en avtalad tid. > 

### <a name="logging"></a>Loggning

Följande logic app kodexempel visar hur du hanterar loggning.

#### <a name="log-entry"></a>Loggpost

Här är källkoden för logic app för att infoga en loggpost.

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

Här är log begärandemeddelandet som publicerats till API-appen.

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

Här är loggmeddelande för svaret från API-app.

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

Nu ska vi titta på stegen för felhantering.

### <a name="error-handling"></a>Felhantering

Följande logic app kodexempel visar hur du kan implementera felhantering.

#### <a name="create-error-record"></a>Skapa Felpost

Här är källkoden för att skapa en Felpost logic app.

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

#### <a name="insert-error-into-cosmos-db--request"></a>Infoga fel till Cosmos DB – begäran

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

#### <a name="insert-error-into-cosmos-db--response"></a>Infoga fel i Cosmos DB – svar

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

#### <a name="salesforce-error-response"></a>Salesforce-felsvar

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

### <a name="return-the-response-back-to-parent-logic-app"></a>Returnera svaret tillbaka till överordnad logikapp

När du får svaret kan skicka du svaret tillbaka till den överordnade logikappen.

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

#### <a name="return-error-response-to-parent-logic-app"></a>Returnera felsvar till överordnade logikapp

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

Vår lösning tillagda funktioner med [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Fel-hanteringsportalen

Du kan skapa en MVC-webbapp för att visa felposter från Cosmos DB om du vill visa felen. Den **lista**, **information**, **redigera**, och **ta bort** åtgärder som ingår i den aktuella versionen.

> [!NOTE]
> Redigera åtgärd: Cosmos DB ersätter hela dokumentet. Poster som visas i den **lista** och **detalj** vyer är bara exempel. De är inte faktiska patientens avtalade tider.

Här följer exempel på vår MVC-appinformation som skapats med den tidigare beskrivna metoden.

#### <a name="error-management-list"></a>Fel vid hantering av lista
![Lista över fel](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Fel vid hantering av detaljvy
![Information om fel](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Log management portal

Om du vill visa loggfilerna kan skapat vi också en MVC-webbapp. Här följer exempel på vår MVC-appinformation som skapats med den tidigare beskrivna metoden.

#### <a name="sample-log-detail-view"></a>Exemplet log detaljvy
![Log detaljvy](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Information om API-appar

#### <a name="logic-apps-exception-management-api"></a>Logic Apps undantag hanterings-API

Appen undantag hanterade API: er för öppen källkod Azure Logic Apps innehåller funktioner som beskrivs här – det finns två domänkontrollanter:

* **ErrorController** infogar en Felpost (dokument) i en Azure Cosmos DB-samling.
* **LogController** infogar en loggpost (dokument) i en Azure Cosmos DB-samling.

> [!TIP]
> Båda styrenheterna använda `async Task<dynamic>` åtgärder, så att åtgärder att lösa vid körning, så att vi kan skapa Azure Cosmos DB-schemat i brödtexten i åtgärden. 
> 

Alla dokument i Azure Cosmos DB måste ha ett unikt ID. Vi använder `PatientId` och lägga till en tidsstämpel som konverteras till ett tidsstämpelvärde Unix (double). Vi trunkera värdet för att ta bort bråkvärdet.

Du kan visa källkoden för styrningen fel API från [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Vi kan anropa API från en logikapp med hjälp av följande syntax:

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

* Du kan enkelt implementera loggning och felhantering i en logikapp.
* Du kan använda Azure Cosmos DB som lagringsplats för och -poster (dokument).
* Du kan använda MVC för att skapa en portal för att visa och poster.

### <a name="source-code"></a>Källkod

Källkoden för undantagshantering Logic Apps-API-program är tillgängligt i den här [GitHub-lagringsplatsen](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Logic App undantag Management API").

## <a name="next-steps"></a>Nästa steg

* [Visa mer logic app-exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Läs om hur du övervakar logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Skapa mallar för automatisk distribution för logic apps](../logic-apps/logic-apps-create-deploy-template.md)
