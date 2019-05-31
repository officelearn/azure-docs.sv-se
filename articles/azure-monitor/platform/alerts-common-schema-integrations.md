---
title: Hur du integrerar det gemensamma schemat för aviseringar med Logic Apps
description: Lär dig hur du skapar en logikapp som använder det gemensamma schemat som aviseringen för att hantera alla aviseringar.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: b51b9f08819a4c496e051d375f6d52aaa985c8e6
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394138"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Hur du integrerar det gemensamma schemat för aviseringar med Logic Apps

Den här artikeln visar hur du skapar en logikapp som använder det gemensamma schemat som aviseringen för att hantera alla aviseringar.

## <a name="overview"></a>Översikt

Den [gemensamma avisering schemat](https://aka.ms/commonAlertSchemaDocs) ger en standardiserad och utökningsbar JSON-schema i alla olika aviseringstyper. Det gemensamma schemat för aviseringen är mest användbara när utnyttjas programmässigt – via webhooks, runbooks och logic apps. I den här artikeln visar hur en enkel logikapp kan skapas för att hantera alla aviseringar. Samma principer kan tillämpas på andra programmeringsmetoder. Logikappen som beskrivs i den här artikeln skapar väldefinierade variablerna för den [”grundläggande” fält](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields), och beskriver också hur du kan hantera [aviseringstyp]('https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields') specifik logik.


## <a name="prerequisites"></a>Nödvändiga komponenter 

Den här artikeln förutsätter att läsaren är bekant med 
* Ställa in aviseringsregler ([mått](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [log](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [aktivitetsloggen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Konfigurera [åtgärdsgrupper](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Aktivera den [gemensamma avisering schemat](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) från inom åtgärdsgrupper

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Skapa en logikapp som utnyttjar det gemensamma schemat för avisering

1. Följ den [stegen som beskrivs i att skapa din logikapp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Välj utlösaren: **När en HTTP-begäran tas emot**.

    ![Logikapputlösare](media/action-groups-logic-app/logic-app-triggers.png "utlösare för Logikappar")

1.  Välj **redigera** ändra HTTP-begäran-utlösaren.

    ![HTTP-begäran-utlösare](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-begäran-utlösare")


1.  Kopiera och klistra in följande schema:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Välj **+** **nytt steg** och välj sedan **Lägg till en åtgärd**.

    ![Lägg till en åtgärd](media/action-groups-logic-app/add-action.png "Lägg till en åtgärd")

1. I det här skedet kan du lägga till ett stort antal anslutningar (Microsoft Teams, Slack, Salesforce osv.) baserat på dina specifika affärsbehov. Du kan använda den ”viktiga fält' out-of-the-box. 

    ![Viktiga fält](media/alerts-common-schema-integrations/logic-app-essential-fields.png "viktiga fält")
    
    Du kan också skapa villkorslogik baserat på typ av avisering med alternativet 'Uttryck'.

    ![Logic app uttryck](media/alerts-common-schema-integrations/logic-app-expressions.png "Logic app-uttryck")
    
     Den ['monitoringService' fältet]('https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields') kan du identifiera typ av avisering baserat på vilket du kan skapa villkorslogik.

    
    Till exempel den kontrollerar om aviseringen är en avisering om Application Insights baserat loggen och fall i så skriver du ut sökresultaten nedan fragment. Annars den skriver ut ”NA”.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Läs mer om [skriva logic app uttryck](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Nästa steg

* [Läs mer om åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).
* [Mer information om det gemensamma schemat för avisering](https://aka.ms/commonAlertSchemaDocs).

