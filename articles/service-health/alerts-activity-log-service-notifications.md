---
title: Ta emot aktivitets logg aviseringar i Azure Service Notifications
description: Få ett meddelande via SMS, e-post eller webhook när Azure-tjänsten sker.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 40ffe0b377a5cbb21f07c479097958d7c15a2879
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383149"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Skapa aktivitets logg aviseringar för tjänst meddelanden
## <a name="overview"></a>Översikt

Den här artikeln visar hur du konfigurerar aktivitets logg aviseringar för meddelanden om tjänstens hälso tillstånd med hjälp av Azure Portal.  

Meddelanden om tjänstens hälso tillstånd lagras i [Azure aktivitets loggen](../azure-monitor/platform/activity-logs-overview.md) , med tanke på den stora mängden information som lagras i aktivitets loggen, det finns ett separat användar gränssnitt som gör det lättare att visa och konfigurera aviseringar om meddelanden om tjänstens hälsa. 

Du kan få en avisering när Azure skickar meddelanden om tjänst hälsa till din Azure-prenumeration. Du kan konfigurera aviseringen baserat på:

- Hälso tillstånds klassen för tjänsten (tjänste problem, planerat underhåll, hälso rekommendationer).
- Prenumerationen som påverkas.
- Den eller de tjänster som påverkas.
- De regioner som påverkas.

> [!NOTE]
> Meddelanden om tjänstens hälso tillstånd skickar ingen avisering om resurs hälso händelser.

Du kan också konfigurera vem aviseringen ska skickas till:

- Välj en befintlig åtgärds grupp.
- Skapa en ny åtgärds grupp (som kan användas för framtida aviseringar).

Läs mer om åtgärdsgrupper i [Skapa och hantera åtgärdsgrupper](../azure-monitor/platform/action-groups.md).

Information om hur du konfigurerar aviseringar om tjänstens hälso tillstånd med hjälp av Azure Resource Manager mallar finns i [Resource Manager-mallar](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Titta på en video om hur du konfigurerar din första Azure Service Health avisering

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Avisering och ny åtgärds grupp med Azure Portal
1. I [portalen](https://portal.azure.com)väljer du **service Health**.

    ![Tjänsten "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. I avsnittet **aviseringar** väljer du **hälso aviseringar**.

    ![Fliken "hälso aviseringar"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Välj **Skapa service Health-avisering** och fyll i fälten.

    ![Kommandot "skapa tjänstens hälso avisering"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Välj den **prenumeration**, de **tjänster**och **regioner** som du vill bli aviserad om.

    ![Dialog rutan Lägg till aktivitets logg avisering](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Den här prenumerationen används för att spara aktivitets logg aviseringen. Aviserings resursen distribueras till den här prenumerationen och övervakar händelser i aktivitets loggen för den.

1. Välj de **händelse typer** som du vill bli aviserad om: *Service problem*, *planerat underhåll*och *hälso rekommendationer* 

1. Definiera aviserings informationen genom att ange ett namn och en **Beskrivning**för **varnings regeln** .

1. Välj den **resurs grupp** där du vill att aviseringen ska sparas.

1. Skapa en ny åtgärds grupp genom att välja **ny åtgärds grupp**. Ange ett namn i rutan **Åtgärds grupp namn** och ange ett namn i rutan **kort namn** . Det korta namnet refereras till i de meddelanden som skickas när den här aviseringen utlöses.

    ![Skapa en ny åtgärds grupp](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Definiera en lista med mottagare genom att ange mottagarens:

    a. **Namn på**: Ange mottagarens namn, alias eller identifierare.

    b. **Åtgärds typ**: Välj SMS, e-post, webhook, Azure app med mera.

    c. **Information**: Baserat på den valda åtgärds typen anger du ett telefonnummer, en e-postadress, en webhook-URI osv.

1. Välj **OK** för att skapa åtgärds gruppen och skapa sedan en **aviserings regel** för att slutföra aviseringen.

Inom några minuter är aviseringen aktiv och börjar utlösas baserat på de villkor du angav när du skapade.

Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problem hanterings system](service-health-alert-webhook-guide.md). Information om webhook-schemat för aktivitets logg aviseringar finns i [Webhooks för Azure aktivitets logg aviseringar](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Åtgärds gruppen som definieras i dessa steg återanvänds som en befintlig åtgärds grupp för alla framtida aviserings definitioner.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Avisering med en befintlig åtgärds grupp med hjälp av Azure Portal

1. Följ steg 1 till 6 i föregående avsnitt för att skapa ett meddelande om tjänstens hälsa. 

1. Under **definiera åtgärds grupp**klickar du på knappen **Välj åtgärds grupp** . Välj lämplig åtgärds grupp.

1. Välj **Lägg** till för att lägga till åtgärds gruppen och skapa sedan en **aviserings regel** för att slutföra aviseringen.

Inom några minuter är aviseringen aktiv och börjar utlösas baserat på de villkor du angav när du skapade.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Avisering och ny åtgärds grupp med hjälp av Azure Resource Manager mallar

Följande är ett exempel som skapar en åtgärds grupp med ett e-postmål och aktiverar alla meddelanden om tjänst hälsa för mål prenumerationen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
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
            "comments": "Service Health Activity Log Alert",
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
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
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

## <a name="manage-your-alerts"></a>Hantera aviseringar

När du har skapat en avisering visas den i avsnittet **aviseringar** i **övervakaren**. Välj den avisering som du vill hantera för att:

* Redigera den.
* Ta bort den.
* Inaktivera eller aktivera det om du tillfälligt vill stoppa eller återuppta mottagning av aviseringar.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [metod tips för att konfigurera Azure Service Health aviseringar](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Lär dig hur du [ställer in mobila push-meddelanden för Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problem hanterings system](service-health-alert-webhook-guide.md).
- Läs mer om [meddelanden om tjänst hälsa](service-notifications.md).
- Lär dig mer om [begränsning av meddelande frekvens](../azure-monitor/platform/alerts-rate-limiting.md).
- Granska [aktivitets logg aviseringens webhook-schema](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Få en [Översikt över aktivitets logg aviseringar](../azure-monitor/platform/alerts-overview.md)och lär dig hur du tar emot aviseringar.
- Läs mer om [Åtgärds grupper](../azure-monitor/platform/action-groups.md).
