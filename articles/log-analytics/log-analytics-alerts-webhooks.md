---
title: Webhook Aviseringsåtgärd provet i OMS Log Analytics | Microsoft Docs
description: En av de åtgärder som du kan köra som svar på en avisering om Log Analytics är en * webhook *, där du kan anropa en extern process via en HTTP-begäran. Den här artikeln beskriver hur ett exempel på hur du skapar en webhook-åtgärd i en Log Analytics-avisering med Slack.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 13c39f0f-fd3c-472d-8324-ddf7538be45e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: bwren
ms.openlocfilehash: 55b66132f7ec5c26c0a7cac1ec0a5c403dbd1082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855175"
---
# <a name="create-an-alert-webhook-action-in-oms-log-analytics-to-send-message-to-slack"></a>Skapa en avisering webhook-åtgärd i OMS Log Analytics för att skicka meddelanden till Slack
En av åtgärderna som du kan köra som svar på en [logganalys avisering](log-analytics-alerts.md) är en *webhook*, där du kan anropa en extern process via en HTTP-begäran.  Du kan läsa om information om aviseringar och webhooks i [aviseringar i logganalys](log-analytics-alerts.md)

I den här artikeln går vi igenom ett exempel på hur du skapar en webhook-åtgärd i en Log Analytics-avisering med Slack som är en meddelandetjänst.

> [!NOTE]
> Du måste ha en Slack-konto för att slutföra det här exemplet.  Du kan registrera dig för ett kostnadsfritt konto på [slack.com](http://slack.com).
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>Steg 1 – Aktivera webhooks i Slack
1. Logga in till Slack på [slack.com](http://slack.com).
2. Välj en kanal i den **kanaler** avsnitt i den vänstra rutan.  Det här är den kanal som meddelandet skickas till.  Du kan välja en standard-kanaler som **allmänna** eller **slumpmässiga**.  I ett produktionsscenario, skulle du förmodligen skapa en särskild kanal som **criticalservicealerts**. <br>
   
   ![Slack-kanaler](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. Klicka på **lägga till en app eller en anpassad integrering** att öppna programkatalogen.
4. Typen *webhooks* i sökrutan och väljer sedan **inkommande WebHooks**. <br>
   
   ![Slack-kanaler](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. Klicka på **installera** bredvid ditt Teamnamn.
6. Klicka på **lägga till konfigurationen**.
7. Välj den kanal som du ska använda för det här exemplet och klicka sedan på **Lägg till inkommande WebHooks integrering**.  
8. Kopiera den **Webhooksadressen**.  Du kommer att klistra in det i aviseringskonfigurationen. <br>
   
    ![Slack-kanaler](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>Steg 2 – Skapa varningsregeln i logganalys
1. [Skapa en aviseringsregel](log-analytics-alerts.md) med följande inställningar.
   * Fråga:```    Type=Event EventLevelName=error ```
   * Sök efter den här aviseringen var: 5 minuter
   * Antalet resultat är: större än 10
   * Under den här tidsperioden: 60 minuter
   * Välj **Ja** för **Webhook** och **nr** för andra åtgärder.
2. Klistra in Webbadressen till Slack den **Webhooksadressen** fältet.
3. Välj alternativet för att **omfattar en anpassad JSON-nyttolast**.
4. Slack förväntar sig en nyttolast som har formaterats i JSON med en parameter med namnet *text*.  Detta är den text som visas i meddelandet som skapas.  Du kan använda en eller flera av aviseringen parametrar med den  *#*  symbol exempelvis som i följande exempel.
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![exempel JSON-nyttolast](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. Klicka på **spara** att spara varningsregeln.
6. Vänta tillräckligt med tid för en avisering ska skapas och sedan kontrollera Slack för ett meddelande som liknar följande.
   
   ![exempel webhook i Slack](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Avancerade webhook nyttolasten för Slack
Stor utsträckning kan du anpassa inkommande meddelanden med Slack. Mer information finns i [inkommande Webhooks](https://api.slack.com/incoming-webhooks) på Slack-webbplatsen. Följande är en mer komplex nyttolast för att skapa ett omfattande meddelande med formatering:

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


Detta skulle generera ett meddelande i Slack liknar följande.

![exempel på meddelande i Slack](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Sammanfattning
Med den här varningsregeln på plats har du ett meddelande som skickas till Slack varje gång villkoret är uppfyllt.  

Detta är endast ett exempel på en åtgärd som du kan skapa som svar på en avisering.  Du kan skapa en webhook-åtgärd som anropar en annan extern tjänst, en runbook-åtgärden för att starta en runbook i Azure Automation eller en e-åtgärd för att skicka ett e-postmeddelande till dig själv eller andra mottagare.   

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om andra [Varna åtgärder i logganalys](log-analytics-alerts-actions.md) inklusive andra åtgärder.


