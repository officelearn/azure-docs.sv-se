---
title: Skapa Azure Advisor-aviseringar för nya rekommendationer
description: Skapa Azure Advisor-aviseringar för ny rekommendation
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443159"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Skapa Azure Advisor-aviseringar om nya rekommendationer 

Den här artikeln visar hur du konfigurerar en avisering för nya rekommendationer från Azure Advisor med azure-portalen och Azure Resource Manager-mallarna. 

När Azure Advisor identifierar en ny rekommendation för en av dina resurser lagras en händelse i [Azure Activity log](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview). Du kan ställa in aviseringar för dessa händelser från Azure Advisor med hjälp av en rekommendationsspecifik upplevelse för att skapa aviseringar. Du kan välja en prenumeration och eventuellt en resursgrupp för att ange vilka resurser som du vill ta emot aviseringar på. 

Du kan också bestämma vilka typer av rekommendationer som ska med hjälp av följande egenskaper:

* Kategori
* Islagsnivå
* Typ av rekommendation

Du kan också konfigurera den åtgärd som ska vidtas när en avisering utlöses av:  

* Välja en befintlig åtgärdsgrupp
* Skapa en ny åtgärdsgrupp

Mer information om åtgärdsgrupper finns i [Skapa och hantera åtgärdsgrupper](../azure-monitor/platform/action-groups.md).

> [!NOTE] 
> Advisor-aviseringar är för närvarande endast tillgängliga för rekommendationer för hög tillgänglighet, prestanda och kostnad. Säkerhetsrekommendationer stöds inte. 

## <a name="in-the-azure-portal"></a>I Azure-portalen
1. Välj **Azure Advisor**i **portalen**.

    ![Azure Advisor i portalen](./media/advisor-alerts/create1.png)

2. I avsnittet **Övervakning** på den vänstra menyn väljer du **Aviseringar**. 

    ![Varningar i Advisor](./media/advisor-alerts/create2.png)

3. Välj **Ny advisoravisering**.

    ![Ny advisor-varning](./media/advisor-alerts/create3.png)

4. I avsnittet **Omfattning** väljer du prenumerationen och eventuellt den resursgrupp som du vill bli aviserad på. 

    ![Scope för advisor-varning](./media/advisor-alerts/create4.png)

5. I avsnittet **Villkor** väljer du den metod som du vill använda för att konfigurera aviseringen. Om du vill avisera alla rekommendationer för en viss kategori och/eller effektnivå väljer du **Kategori- och effektnivå**. Om du vill avisera alla rekommendationer av en viss typ väljer du **Rekommendationstyp**.

    ![Varningsvillkor för Azure Advisor](./media/advisor-alerts/create5.png)

6. Beroende på alternativet Konfigurera efter väljer du kan du ange villkoren. Om du vill ha alla rekommendationer lämnar du bara de återstående fälten tomma. 

    ![Åtgärdsgrupp för rådgivarevarning](./media/advisor-alerts/create6.png)

7. I avsnittet **Åtgärdsgrupper** väljer du **Lägg till befintlig** om du vill använda en åtgärdsgrupp som du redan har skapat eller väljer Skapa **ny** för att skapa en ny [åtgärdsgrupp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups). 

    ![Advisor alert lägga till befintliga](./media/advisor-alerts/create7.png)

8. I avsnittet Aviseringsinformation ger du din avisering ett namn och en kort beskrivning. Om du vill att aviseringen ska aktiveras lämnar du aktivera regeln när markeringen **skapas** inställd på **Ja**. Välj sedan resursgruppen som du vill spara aviseringen till. Detta kommer inte att påverka rekommendationens inriktnings omfattning. 

    ![Banderollen Azure Advisor](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Med en Azure Resource Manager-mall

Med den här Resource Manager-mallen skapas en rekommendationsavisering och en ny åtgärdsgrupp.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
  ```

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Konfigurera rekommendationsaviseringar för att använda en webhook
I det här avsnittet visas hur du konfigurerar Azure Advisor-aviseringar för att skicka rekommendationsdata via webhooks till dina befintliga system. 

Du kan ställa in aviseringar som ska meddelas när du har en ny Advisor-rekommendation på en av dina resurser. Dessa varningar kan meddela dig via e-post eller sms, men de kan också användas för att integrera med dina befintliga system via en webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Använda payloaden för advisorrekommendation
Om du vill integrera Advisor-aviseringar i dina egna system med hjälp av en webhook måste du tolka JSON-nyttolasten som skickas från meddelandet. 

När du konfigurerar åtgärdsgruppen för den här aviseringen väljer du om du vill använda det gemensamma varningsschemat. Om du väljer det gemensamma varningsschemat ser nyttolasten ut så här: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Om du inte använder det gemensamma schemat ser nyttolasten ut så här: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

I båda schemana kan du identifiera rekommendationshändelser `Recommendation` för Rådgivare genom att leta efter **eventSource** is och **operationName** är `Microsoft.Advisor/recommendations/available/action`.

Några av de andra viktiga fält som du kanske vill använda är: 

* *alertTargetIDs* (i det gemensamma schemat) eller *resourceId* (äldre schema)
* *rekommendationTyp*
* *rekommendationNamn*
* *rekommendationKategori*
* *rekommendationImpact*
* *rekommendationResourceLink*


## <a name="manage-your-alerts"></a>Hantera aviseringar 

Från Azure Advisor kan du redigera, ta bort eller inaktivera och aktivera dina rekommendationer. 

1. Välj **Azure Advisor**i **portalen**.

    ![Banderollen Azure Advisor](./media/advisor-alerts/create1.png)

2. I avsnittet **Övervakning** på den vänstra menyn väljer du **Aviseringar**.

    ![Banderollen Azure Advisor](./media/advisor-alerts/create2.png)

3. Om du vill redigera en avisering klickar du på aviseringens namn för att öppna aviseringen och redigera de fält som du vill redigera.

4. Om du vill ta bort, aktivera eller inaktivera en avisering klickar du på ellipsen i slutet av raden och väljer sedan den åtgärd du vill vidta.
 

