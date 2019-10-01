---
title: Så här integrerar du det vanliga aviserings schemat med Logic Apps
description: Lär dig hur du skapar en logisk app som använder det gemensamma aviserings schemat för att hantera alla dina aviseringar.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 569b97ecc24306741c3323ce5bc526f88645c1dd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702954"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Så här integrerar du det vanliga aviserings schemat med Logic Apps

Den här artikeln visar hur du skapar en Logi Kap par som utnyttjar det gemensamma aviserings schemat för att hantera alla dina aviseringar.

## <a name="overview"></a>Översikt

Det [vanliga aviserings schemat](https://aka.ms/commonAlertSchemaDocs) innehåller ett standardiserat och utöknings Bart JSON-schema över alla dina olika aviserings typer. Det vanliga aviserings schemat är mest användbart när du använder program mässigt – via Webhooks, Runbooks och Logic Apps. I den här artikeln visar vi hur du kan använda en enda Logic-app för att hantera alla aviseringar. Samma principer kan tillämpas på andra programmerings metoder. Logic-appen som beskrivs i den här artikeln skapar väldefinierade variabler för [fälten "viktigt"](alerts-common-schema-definitions.md#essentials)och beskriver hur du kan hantera en viss logik för [aviserings typen](alerts-common-schema-definitions.md#alert-context) .


## <a name="prerequisites"></a>Förutsättningar 

Den här artikeln förutsätter att läsaren är bekant med 
* Konfigurera aviserings regler ([mått](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [logg](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [aktivitets logg](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Konfigurera [Åtgärds grupper](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Aktivera det [vanliga aviserings schemat](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) i åtgärds grupper

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Skapa en logisk app som utnyttjar det vanliga aviserings schemat

1. Följ [stegen som beskrivs för att skapa din Logic app](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Välj utlösaren: **När en HTTP-begäran tas emot**.

    ![Logic app-utlösare](media/action-groups-logic-app/logic-app-triggers.png "Logic app-utlösare")

1.  Välj **Redigera** för att ändra utlösaren för http-begäran.

    ![Utlösare för http-begäran](media/action-groups-logic-app/http-request-trigger-shape.png "Utlösare för http-begäran")


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

1. I det här skedet kan du lägga till en mängd anslutningar (Microsoft Teams, slack, Salesforce osv.) utifrån dina specifika affärs behov. Du kan använda den "nödvändiga fälten" i-rutan. 

    ![Viktiga fält](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Viktiga fält")
    
    Alternativt kan du redigera villkorlig logik baserat på aviserings typen med hjälp av alternativet "uttryck".

    ![Logic app-uttryck](media/alerts-common-schema-integrations/logic-app-expressions.png "Logic app-uttryck")
    
     Med [fältet monitoringService](alerts-common-schema-definitions.md#alert-context) kan du unikt identifiera aviserings typen, baserat på vilken du kan skapa den villkorliga logiken.

    
    Till exempel kontrollerar kodfragmentet nedan om aviseringen är en Application Insights baserad logg avisering, och om så skrivs ut Sök resultaten. Annars skrivs "NA" ut.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Lär dig mer om att [skriva Logic app-uttryck](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Nästa steg

* [Läs mer om åtgärds grupper](../../azure-monitor/platform/action-groups.md).
* [Läs mer om det vanliga aviserings schemat](https://aka.ms/commonAlertSchemaDocs).

