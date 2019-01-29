---
title: Ta emot aviseringar för aktivitetsloggar för Azure tjänstmeddelanden
description: Bli informerad via SMS, e-post eller webhook när Azure-tjänsten inträffar.
author: shawntabrizi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: shtabriz
ms.openlocfilehash: 6b617ff99b9183c6b5008292dd3e5d11f2e69da9
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55092242"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Skapa aviseringar för aktivitetsloggen för tjänstmeddelanden
## <a name="overview"></a>Översikt
Den här artikeln visar hur du ställer in varningar för aktivitetsloggar för meddelanden om hälsostatus för tjänsten med hjälp av Azure portal.  

Du kan få en avisering när Azure skickar meddelanden om hälsostatus för tjänsten på Azure-prenumerationen. Du kan konfigurera aviseringen baserat på:

- Klassen i aviseringen om tjänstens hälsa (tjänstproblem, planerat underhåll hälsoråd).
- Den prenumeration som påverkas.
- Tjänster som påverkas.
- Region(er) som påverkas.

> [!NOTE]
> Meddelanden om hälsostatus för tjänsten skickar inte en avisering om resource health-händelser.

Du kan också konfigurera som aviseringen ska skickas till:

- Välj en befintlig åtgärdsgrupp.
- Skapa en ny åtgärdsgrupp (som kan användas för framtida aviseringar).

Läs mer om åtgärdsgrupper i [Skapa och hantera åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).

Information om hur du konfigurerar tjänsten hälsovarningar för meddelande med hjälp av Azure Resource Manager-mallar finns i [Resource Manager-mallar](alerts-activity-log.md).

## <a name="alert-and-new-action-group-using-azure-portal"></a>Aviseringar och ny åtgärdsgrupp med hjälp av Azure portal
1. I den [portal](https://portal.azure.com)väljer **Tjänstehälsa**.

    ![Tjänsten ”Tjänstehälsa”](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. I den **aviseringar** väljer **hälsovarningar**.

    ![Fliken ”hälsovarningar”](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Välj **skapa hälsoavisering för tjänst** och Fyll i fälten.

    ![Kommandot ”Skapa avisering för tjänsthälsa”](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Välj den **prenumeration**, **Services**, och **regioner** du vill få aviseringar.

    ![Dialogrutan ”Lägg till aktivitetsloggavisering”](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Den här prenumerationen används för att spara aktivitetsloggaviseringen. Aviseringen resursen har distribuerats till den här prenumerationen och övervakar händelser i aktivitetsloggen för den.

1. Välj den **händelsetyper** du vill få aviseringar: *Tjänsten problemet*, *planerat underhåll*, och *hälsorådgivning* 

1. Definiera din Aviseringsinformationen genom att ange en **varningsregelns namn** och **beskrivning**.

1. Välj den **resursgrupp** där du vill att aviseringen ska sparas.

1. Skapa en ny åtgärdsgrupp genom att välja **ny åtgärdsgrupp**. Ange ett namn i den **namn på åtgärdsgrupp** och anger ett namn i den **kortnamnet** box. Det korta namnet refereras i meddelanden som skickas när den här varningen utlöses.

    ![Skapa en ny åtgärdsgrupp](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Definiera en lista över mottagare genom att tillhandahålla mottagarens:

    a. **Namn**: Ange mottagarens namn, alias eller identifierare.

    b. **Åtgärdstyp**: Välj SMS, e-post, webhook, Azure-app med mera.

    c. **Information om**: Baserat på typen av valt, ange ett telefonnummer, e-postadress, webhook URI osv.

1. Välj **OK** att skapa åtgärdsgruppen och sedan **skapa varningsregel** att slutföra aviseringen.

Inom några minuter, aviseringen är aktiv och börjar att utlösa baserat på de villkor som du angav när du skapar.

Lär dig hur du [konfigurera webhook-aviseringar för befintliga problem system](../../service-health/service-health-alert-webhook-guide.md). Information om webhook-schema för aktivitetsloggaviseringar finns [Webhooks för Azure-aktivitetsloggar loggaviseringar](../../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Åtgärdsgrupp som definierats i de här stegen är återanvändbara som en befintlig åtgärdsgrupp för alla framtida varningsdefinitioner.
>
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Avisering med befintlig åtgärdsgrupp med hjälp av Azure portal

1. Följ steg 1 till och med 7 i föregående avsnitt för att skapa aviseringen om tjänstens hälsa. 

1. Under **definiera åtgärdsgruppen**, klickar du på den **väljer åtgärdsgrupp** knappen. Välj lämplig åtgärdsgruppen.

1. Välj **Lägg till** att lägga till åtgärdsgrupp och sedan **skapa varningsregel** att slutföra aviseringen.

Inom några minuter, aviseringen är aktiv och börjar att utlösa baserat på de villkor som du angav när du skapar.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Aviseringar och ny åtgärdsgrupp med hjälp av Azure Resource Manager-mallar

Följande är ett exempel som skapar en grupp med ett mål för e-post och aktiverar alla service health-aviseringar för målprenumerationen.

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

När du skapar en avisering, är det visas i den **aviseringar** delen av **övervakaren**. Välj den avisering du vill hantera till:

* Redigera den.
* Ta bort den.
* Inaktivera eller aktivera den, om du vill att tillfälligt stoppa eller återuppta tar emot meddelanden om aviseringen.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurera webhook-aviseringar för befintliga problem system](../../service-health/service-health-alert-webhook-guide.md).
- Lär dig mer om [service health meddelanden](../../azure-monitor/platform/service-notifications.md).
- Lär dig mer om [meddelande hastighetsbegränsning](../../azure-monitor/platform/alerts-rate-limiting.md).
- Granska den [avisering webhook för aktivitetslogg](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Hämta en [översikt över aktivitetsloggaviseringar](../../azure-monitor/platform/alerts-overview.md), och lär dig hur du får aviseringar. 
- Läs mer om [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).
