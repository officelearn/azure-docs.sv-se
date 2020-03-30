---
title: Undantagshantering & scenario för felloggning
description: Verkligt användningsfall och scenario för avancerad undantagshantering och felloggning i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: 1bb6e28c9dcae01f3233178706d2a24156fa509a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76902700"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scenario: Undantagshantering och felloggning för logikappar

Det här scenariot beskriver hur du kan utöka en logikapp för att bättre stödja undantagshantering. Vi har använt ett verkligt användningsfall för att svara på frågan: "Stöder Azure Logic Apps undantag och felhantering?"

> [!NOTE]
> Det aktuella Azure Logic Apps-schemat innehåller en standardmall för åtgärdssvar. Den här mallen innehåller både intern validering och felsvar som returneras från en API-app.

## <a name="scenario-and-use-case-overview"></a>Översikt över scenario och användningsfall

Här är historien som användningsfall för det här scenariot: 

En välkänd hälso- och sjukvårdsorganisation anlitade oss för att utveckla en Azure-lösning som skulle skapa en patientportal med hjälp av Microsoft Dynamics CRM Online. De behövde skicka avtalade poster mellan Dynamics CRM Online patientportal och Salesforce. Vi ombads att använda [HL7 FHIR-standarden](https://www.hl7.org/implement/standards/fhir/) för alla patientjournaler.

Projektet hade två stora krav:  

* En metod för att logga poster som skickas från Dynamics CRM Online-portalen
* Ett sätt att visa eventuella fel som uppstått i arbetsflödet

> [!TIP]
> En video på hög nivå om det här projektet finns i [Användargruppen för integration](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Användargrupp för integrering").

## <a name="how-we-solved-the-problem"></a>Hur vi löste problemet

Vi valde Azure Cosmos DB som en [lagringsplats](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") för logg- och felposter (Cosmos DB refererar till poster som dokument). Eftersom Azure Logic Apps har en standardmall för alla svar behöver vi inte skapa ett anpassat schema. Vi kan skapa en API-app för att **infoga** och **fråga** efter både fel- och loggposter. Vi kan också definiera ett schema för var och en i API-appen.  

Ett annat krav var att rensa poster efter ett visst datum. Cosmos DB har en egenskap som heter [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Dags att leva") (TTL), vilket gjorde det möjligt för oss att ställa in ett Tid till **Live-värde** för varje post eller samling. Den här funktionen eliminerade behovet av att manuellt ta bort poster i Cosmos DB.

> [!IMPORTANT]
> För att slutföra den här självstudien måste du skapa en Cosmos DB-databas och två samlingar (loggning och fel).

## <a name="create-the-logic-app"></a>Skapa logikappen

Det första steget är att skapa logikappen och öppna appen i Logic App Designer. I det här exemplet använder vi logikappar för överordnad och underordnad. Anta att vi redan har skapat den överordnade och kommer att skapa en underordnad logikapp.

Eftersom vi kommer att logga posten som kommer från Dynamics CRM Online, låt oss börja högst upp. Vi måste använda en **begärandeutlösare** eftersom den överordnade logikappen utlöser det här barnet.

### <a name="logic-app-trigger"></a>Utlösare av logikapp

Vi använder en **begärandeutlösare** som visas i följande exempel:

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

Vi måste logga källan (begäran) för patientjournalen från Dynamics CRM Online-portalen.

1. Vi måste få en ny avtalad tid post från Dynamics CRM Online.

   Den utlösande faktorn som kommer från CRM ger oss **CRM PatentId**, **posttyp**, **Ny eller Uppdaterad Post** (nytt eller uppdatera booleskt värde) och **SalesforceId**. **SalesforceId** kan vara null eftersom den bara används för en uppdatering.
   Vi får CRM-posten med hjälp av CRM **PatientID** och **posttyp**.

2. Därefter måste vi lägga till vår Azure Cosmos DB SQL API-app **InsertLogEntry-åtgärd** som visas här i Logic App Designer.

   **Infoga loggpost**

   ![Infoga loggpost](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Infoga felpost**

   ![Infoga loggpost](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Sök efter fel på en post**

   ![Villkor](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Källkoden för logikapp

> [!NOTE]
> Följande exempel är endast exempel. Eftersom den här självstudien baseras på en implementering som nu är i produktion kanske värdet för en **källnod** inte visar egenskaper som är relaterade till schemaläggning av en avtalad tid.> 

### <a name="logging"></a>Loggning

Följande exempel på logikappkod visar hur du hanterar loggning.

#### <a name="log-entry"></a>Logga in

Här är logikappens källkod för att infoga en loggpost.

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

#### <a name="log-request"></a>Logga begäran

Här är meddelandet om loggbegäran som har publicerats i API-appen.

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


#### <a name="log-response"></a>Logga svar

Här är loggsvarsmeddelandet från API-appen.

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

Nu ska vi titta på felhanteringsstegen.

### <a name="error-handling"></a>Felhantering

Följande exempel på logikappkod visar hur du kan implementera felhantering.

#### <a name="create-error-record"></a>Skapa felpost

Här är logikappens källkod för att skapa en felpost.

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

#### <a name="insert-error-into-cosmos-db--request"></a>Infoga fel i Cosmos DB - begäran

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

#### <a name="insert-error-into-cosmos-db--response"></a>Infoga fel i Cosmos DB - svar

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

### <a name="return-the-response-back-to-parent-logic-app"></a>Returnera svaret tillbaka till den överordnade logikappen

När du har fått svaret kan du skicka tillbaka svaret till den överordnade logikappen.

#### <a name="return-success-response-to-parent-logic-app"></a>Returnera lyckad respons till den överordnade logikappen

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

#### <a name="return-error-response-to-parent-logic-app"></a>Returnera felsvar på den överordnade logikappen

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


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB-databas och portal

Vår lösning la funktioner med [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Portal för felhantering

Om du vill visa felen kan du skapa en MVC-webbapp för att visa felposterna från Cosmos DB. **Åtgärderna Lista**, **Detaljer**, **Redigera**och **Ta bort** ingår i den aktuella versionen.

> [!NOTE]
> Redigering: Cosmos DB ersätter hela dokumentet. Posterna som visas i **vyerna Lista** och **Detalj** är endast exempel. De är inte faktiska patienttidsregister.

Här är exempel på vår MVC app detaljer som skapats med den tidigare beskrivna metoden.

#### <a name="error-management-list"></a>Lista över felhantering
![Fellista](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Detaljvy för felhantering
![Information om fel](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Logghanteringsportal

För att visa loggarna skapade vi också en MVC-webbapp. Här är exempel på vår MVC app detaljer som skapats med den tidigare beskrivna metoden.

#### <a name="sample-log-detail-view"></a>Exempel på logginformationsvy
![Logginformationsvy](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Information om API-appen

#### <a name="logic-apps-exception-management-api"></a>API för undantagshantering för Logic Apps

Vår API-app för undantagshantering för Azure Logic Apps med öppen källkod tillhandahåller funktioner enligt beskrivningen här – det finns två styrenheter:

* **ErrorController** infogar en felpost (dokument) i en Azure Cosmos DB-samling.
* **LogController (loggkontroller)** Infogar en loggpost (dokument) i en Azure Cosmos DB-samling.

> [!TIP]
> Båda styrenheterna använder `async Task<dynamic>` åtgärder, vilket gör att åtgärder kan matchas vid körning, så att vi kan skapa Azure Cosmos DB-schemat i brödtexten för åtgärden. 
> 

Varje dokument i Azure Cosmos DB måste ha ett unikt ID. Vi använder `PatientId` och lägger till en tidsstämpel som konverteras till ett Unix tidsstämpelvärde (dubbel). Vi trunkerar värdet för att ta bort bråkvärdet.

Du kan visa källkoden för vårt API för felkontrollant från [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Vi anropar API:et från en logikapp med hjälp av följande syntax:

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

Uttrycket i föregående kodexempel söker efter *Create_NewPatientRecord* **statusen misslyckades**.

## <a name="summary"></a>Sammanfattning

* Du kan enkelt implementera loggning och felhantering i en logikapp.
* Du kan använda Azure Cosmos DB som databas för logg- och felposter (dokument).
* Du kan använda MVC för att skapa en portal för att visa logg- och felposter.

### <a name="source-code"></a>Källkod

Källkoden för API-programmet för undantagshantering av Logic Apps är tillgänglig i den här [GitHub-databasen](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API för undantagshantering för Logikapp").

## <a name="next-steps"></a>Nästa steg

* [Visa fler exempel och scenarier för logikappar](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Övervaka logikappar](../logic-apps/monitor-logic-apps.md)
* [Automatisera distributionen av logikappar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
