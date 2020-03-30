---
title: Så här integrerar du det gemensamma varningsschemat med Logic Apps
description: Lär dig hur du skapar en logikapp som utnyttjar det gemensamma varningsschemat för att hantera alla dina aviseringar.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668238"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Så här integrerar du det gemensamma varningsschemat med Logic Apps

Den här artikeln visar hur du skapar en logikapp som utnyttjar det gemensamma varningsschemat för att hantera alla dina aviseringar.

## <a name="overview"></a>Översikt

Det [gemensamma varningsschemat](https://aka.ms/commonAlertSchemaDocs) innehåller ett standardiserat och utökningsbart JSON-schema för alla dina olika varningstyper. Det gemensamma varningsschemat är mest användbart när det används programmässigt – via webhooks, runbooks och logic apps. I den här artikeln visar vi hur en enda logikapp kan skapas för att hantera alla dina aviseringar. Samma principer kan tillämpas på andra programmatiska metoder. Logikappen som beskrivs i den här artikeln skapar väldefinierade variabler för de ["väsentliga" fälten](alerts-common-schema-definitions.md#essentials)och beskriver också hur du kan hantera [varningstypspecifik](alerts-common-schema-definitions.md#alert-context) logik.


## <a name="prerequisites"></a>Krav 

Den här artikeln förutsätter att läsaren är bekant med 
* Ställa in varningsregler ([mått,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) [logg](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [aktivitetslogg)](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)
* Ställa in [åtgärdsgrupper](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Aktivera det [gemensamma varningsschemat](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) inifrån åtgärdsgrupper

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Skapa en logikapp som utnyttjar det gemensamma varningsschemat

1. Följ stegen [som beskrivs för att skapa logikappen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Välj utlösaren: **När en HTTP-begäran tas emot**.

    ![Utlösare för logikappar](media/action-groups-logic-app/logic-app-triggers.png "Utlösare för logikappar")

1.  Välj **Redigera** om du vill ändra HTTP-begärandens utlösare.

    ![HTTP-begäran triggers](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-begäran triggers")


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

1. Välj **+** **Nytt steg** och välj sedan Lägg till en **åtgärd**.

    ![Lägga till en åtgärd](media/action-groups-logic-app/add-action.png "Lägga till en åtgärd")

1. I det här skedet kan du lägga till en mängd olika kopplingar (Microsoft Teams, Slack, Salesforce, etc.) baserat på dina specifika affärskrav. Du kan använda "väsentliga fält" out-of-the-box. 

    ![Viktiga fält](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Viktiga fält")
    
    Du kan också skapa villkorsstyrd logik baserat på aviseringstypen med alternativet "Uttryck".

    ![Uttryck för logikapp](media/alerts-common-schema-integrations/logic-app-expressions.png "Uttryck för logikapp")
    
     Med [fältet "monitoringService"](alerts-common-schema-definitions.md#alert-context) kan du unikt identifiera aviseringstypen, baserat på vilken du kan skapa den villkorliga logiken.

    
    Under kodavsnittet kontrolleras till exempel om aviseringen är en Application Insights-baserad loggavisering och skrivs i så fall ut sökresultaten. Annars skriver den ut "NA".

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Läs mer om [att skriva logikapputtryck](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Nästa steg

* [Läs mer om åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).
* [Läs mer om det gemensamma varningsschemat](https://aka.ms/commonAlertSchemaDocs).

