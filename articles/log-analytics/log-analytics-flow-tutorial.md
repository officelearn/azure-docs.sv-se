---
title: Automatisera Azure Log Analytics-processer med Microsoft Flow
description: Lär dig hur du kan använda Microsoft Flow för att automatisera snabbt upprepade processer med hjälp av Azure Log Analytics-anslutningen.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
ms.service: log-analytics
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: bwren
ms.component: na
ms.openlocfilehash: d623cd4cb1a62fab7b5f8cc4e9686d88cde94ed8
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303277"
---
# <a name="automate-log-analytics-processes-with-the-connector-for-microsoft-flow"></a>Automatisera processer för Log Analytics med anslutningsappen för Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) kan du skapa automatiserade arbetsflöden med hundratals åtgärder för en mängd olika tjänster. Utdata från en åtgärd kan användas som indata till en annan så att du kan skapa integrering mellan olika tjänster.  Azure Log Analytics-anslutningsappen för Microsoft Flow kan du skapa arbetsflöden som innehåller data som hämtats av loggsökningar i Log Analytics.

Du kan till exempel använda Microsoft Flow för att använda Log Analytics-data i ett e-postmeddelande från Office 365, skapa en bugg i Azure DevOps eller skicka ett Slack-meddelande.  Du kan utlösa ett arbetsflöde med ett enkelt schema eller från en åtgärd i en ansluten tjänst, till exempel när ett e-postmeddelande eller en tweet tas emot.  

Självstudie i den här artikeln visar hur du skapar ett flöde som automatiskt skickar resultaten från en Log Analytics-loggsökning via e-post, bara ett exempel på hur du kan använda Log Analytics i Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Steg 1: Skapa ett flöde
1. Logga in på [Microsoft Flow](http://flow.microsoft.com), och välj **Mina flöden**.
2. Klicka på **+ skapa från tom**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Steg 2: Skapa en utlösare för flödet
1. Klicka på **Sök bland hundratals kopplingar och utlösare**.
2. Typ **schema** i sökrutan.
3. Välj **schema**, och välj sedan **schema – återkommande**.
4. I den **frekvens** , markerar du kryssrutan **dag** och i den **intervall** anger **1**.<br><br>![Dialogrutan för Microsoft Flow-utlösare](media/log-analytics-flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Steg 3: Lägg till en Log Analytics-åtgärd
1. Klicka på **+ nytt steg**, och klicka sedan på **Lägg till en åtgärd**.
2. Sök efter **Log Analytics**.
3. Klicka på **Azure Log Analytics – Kör fråga och visualisera resultat**.<br><br>![Log Analytics kör frågefönster](media/log-analytics-flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Steg 4: Konfigurera Log Analytics-åtgärden

1. Ange information för din arbetsyta inklusive prenumerations-ID, resursgrupp, och namnet på arbetsytan.
2. Lägg till följande Log Analytics-fråga för att den **fråga** fönster.  Detta är endast en exempelfråga och du kan ersätta med alla andra som returnerar data.
```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
```

2. Välj **HTML-tabell** för den **diagramtyp**.<br><br>![Log Analytics-åtgärden](media/log-analytics-flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Steg 5: Konfigurera flödet för att skicka e-post

1. Klicka på **nytt steg**, och klicka sedan på **+ Lägg till en åtgärd**.
2. Sök efter **Office 365 Outlook**.
3. Klicka på **Office 365 Outlook – skicka ett e-postmeddelande**.<br><br>![Office 365 Outlook-urvalsfönstret](media/log-analytics-flow-tutorial/flow04.png)

4. Ange e-postadressen till en mottagare i den **till** och på ett ämne för e-post i **ämne**.
5. Klicka på den **brödtext** box.  En **dynamiskt innehåll** öppnas med värden från tidigare åtgärder.  
6. Välj **brödtext**.  Det här är resultatet av frågan i Log Analytics-åtgärden.
6. Klicka på **visa avancerade alternativ**.
7. I den **är HTML** väljer **Ja**.<br><br>![Konfigurationsfönstret i Office 365 e-post](media/log-analytics-flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Steg 6: Spara och testa ditt flöde
1. I den **flödesnamn** , lägga till ett namn för flödet och sedan på **Skapa flöde**.<br><br>![Spara flödet](media/log-analytics-flow-tutorial/flow06.png)
2. Flödet har nu skapats och körs efter en dag som är det schema som du har angett. 
3. Omedelbart testa flödet genom att klicka på **kör nu** och sedan **köra flödet**.<br><br>![Kör flödet](media/log-analytics-flow-tutorial/flow07.png)
3. När flödet har slutförts bör du kontrollera e-post för den mottagare som du angett.  Du bör ha fått ett e-postmeddelande med en brödtext som liknar följande:<br><br>![E-postexempel](media/log-analytics-flow-tutorial/flow08.png)


## <a name="next-steps"></a>Nästa steg

- Läs mer om [loggsökningar i Log Analytics](log-analytics-log-search-new.md).
- Läs mer om [Microsoft Flow](https://ms.flow.microsoft.com).



