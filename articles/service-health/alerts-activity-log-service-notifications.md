---
title: Ta emot aktivitetsloggaviseringar för Azure-tjänstmeddelanden
description: Få ett meddelande via SMS, e-post eller webhook när Azure-tjänsten inträffar.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d318adc76959ac24f4be9946167965a83053f632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749319"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Skapa aktivitetsloggsaviseringar i tjänstmeddelanden
## <a name="overview"></a>Översikt

Den här artikeln visar hur du ställer in aktivitetsloggaviseringar för tjänsthälsomeddelanden med hjälp av Azure-portalen.  

Tjänsthälsomeddelanden lagras i [Azure-aktivitetsloggen](../azure-monitor/platform/platform-logs-overview.md) Med tanke på den eventuellt stora mängden information som lagras i aktivitetsloggen finns det ett separat användargränssnitt för att göra det enklare att visa och ställa in aviseringar om tjänsthälsomeddelanden. 

Du kan få en avisering när Azure skickar tjänsthälsomeddelanden till din Azure-prenumeration. Du kan konfigurera aviseringen baserat på:

- Klassen av tjänsten hälsa anmälan (Service frågor, planerat underhåll, Hälso-rådgivning).
- Prenumerationen påverkades.
- Tjänsten/tjänsterna påverkas.
- Regionen/regionen påverkas.

> [!NOTE]
> Hälsomeddelanden för tjänsten skickar ingen avisering om resurshälsohändelser.

Du kan också konfigurera vem aviseringen ska skickas till:

- Välj en befintlig åtgärdsgrupp.
- Skapa en ny åtgärdsgrupp (som kan användas för framtida aviseringar).

Mer information om åtgärdsgrupper finns i [Skapa och hantera åtgärdsgrupper](../azure-monitor/platform/action-groups.md).

Information om hur du konfigurerar aviseringar om tjänsthälsomeddelanden med hjälp av Azure Resource Manager-mallar finns i [Resource Manager-mallar](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Titta på en video om hur du konfigurerar din första Azure Service Health-avisering

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Avisering och ny åtgärdsgrupp med Azure-portalen
1. Välj **Servicehälsa**i [portalen](https://portal.azure.com).

    ![Tjänsten "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. I avsnittet **Aviseringar** väljer du **Hälsovarningar**.

    ![Fliken "Hälsovarningar"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Välj **Skapa hälsoavisering för tjänsten** och fyll i fälten.

    ![Kommandot "Skapa hälsovarning för tjänsten"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Välj den **prenumeration,** **de tjänster**och **regioner som** du vill bli aviserad för.

    ![Dialogrutan "Lägg till aktivitetslogg"](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Den här prenumerationen används för att spara aktivitetsloggaviseringen. Aviseringsresursen distribueras till den här prenumerationen och övervakar händelser i aktivitetsloggen för den.

1. Välj de **händelsetyper** som du vill bli aviserad för: *Serviceproblem,* *Planerat underhåll*och *Hälsorekommendationer* 

1. Definiera dina aviseringsuppgifter genom att ange ett **varningsregelnamn** och **beskrivning**.

1. Välj den **resursgrupp** där du vill att aviseringen ska sparas.

1. Skapa en ny åtgärdsgrupp genom att välja **Ny åtgärdsgrupp**. Ange ett namn i rutan **Namn på åtgärdsgrupp** och ange ett namn i rutan **Kortnamn.** Kortnamnet refereras i de meddelanden som skickas när den här aviseringen utlöses.

    ![Skapa en ny åtgärdsgrupp](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Definiera en lista över mottagare genom att ange mottagarens:

    a. **Namn**: Ange mottagarens namn, alias eller identifierare.

    b. **Åtgärdstyp:** Välj SMS, e-post, webhook, Azure-app med mera.

    c. **Detaljer:** Baserat på den valda åtgärdstypen anger du ett telefonnummer, e-postadress, webhook URI, etc.

1. Välj **OK** om du vill skapa åtgärdsgruppen och skapa sedan **aviseringsregeln** för att slutföra aviseringen.

Inom några minuter är aviseringen aktiv och börjar utlösas baserat på de villkor som du angav när du skapades.

Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problemhanteringssystem](service-health-alert-webhook-guide.md). Information om webhook-schemat för aktivitetsloggaviseringar finns i [Webhooks för Azure-aktivitetsloggaviseringar](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Åtgärdsgruppen som definieras i dessa steg kan återanvändas som en befintlig åtgärdsgrupp för alla framtida varningsdefinitioner.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Avisera med befintlig åtgärdsgrupp med Azure Portal

1. Följ steg 1 till och med 6 i föregående avsnitt för att skapa hälsomeddelanden för tjänsten. 

1. Klicka på knappen **Välj åtgärdsgrupp** under Definiera **åtgärdsgrupp.** Välj lämplig åtgärdsgrupp.

1. Välj **Lägg till** om du vill lägga till åtgärdsgruppen och skapa sedan **aviseringsregeln** för att slutföra aviseringen.

Inom några minuter är aviseringen aktiv och börjar utlösas baserat på de villkor som du angav när du skapades.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Avisering och ny åtgärdsgrupp med azure Resource Manager-mallarna

Följande är ett exempel som skapar en åtgärdsgrupp med ett e-postmål och aktiverar alla servicehälsomeddelanden för målprenumerationen.

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

När du har skapat en avisering visas den i avsnittet **Aviseringar** i **Monitor**. Välj den avisering som du vill hantera:

* Redigera den.
* Ta bort den.
* Inaktivera eller aktivera den om du tillfälligt vill stoppa eller återuppta mottagningsmeddelanden för aviseringen.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer [om metodtips för att konfigurera Azure Service Health-aviseringar](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Läs om hur du [konfigurerar mobila push-meddelanden för Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problemhanteringssystem](service-health-alert-webhook-guide.md).
- Läs mer om [hälsomeddelanden](service-notifications.md)för tjänster .
- Läs mer om [begränsning av meddelandehastighet](../azure-monitor/platform/alerts-rate-limiting.md).
- Granska [webbkroksschemat för aktivitetsloggen.](../azure-monitor/platform/activity-log-alerts-webhook.md)
- Få en översikt över aviseringar för [aktivitetsloggar](../azure-monitor/platform/alerts-overview.md)och lär dig hur du tar emot aviseringar.
- Läs mer om [åtgärdsgrupper](../azure-monitor/platform/action-groups.md).
