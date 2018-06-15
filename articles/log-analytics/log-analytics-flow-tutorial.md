---
title: Automatisera Azure logganalys-processer med Microsoft Flow
description: Lär dig hur du kan använda Microsoft Flow för att automatisera snabbt repeterbara processer med hjälp av Azure logganalys-anslutningen.
services: log-analytics
documentationcenter: ''
author: CFreemanwa
manager: carmonm
ms.service: log-analytics
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 9884b97aad8f022e6bc20b2a630ccdd07ebc64db
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2017
ms.locfileid: "23889510"
---
# <a name="automate-log-analytics-processes-with-the-connector-for-microsoft-flow"></a>Automatisera logganalys processer med connector för Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) kan du skapa automatiska arbetsflöden med hundratals åtgärder för en mängd olika tjänster. Utdata från en åtgärd kan användas som indata till en annan så att du kan skapa integration mellan olika tjänster.  Azure logganalys-koppling för Microsoft Flow kan du skapa arbetsflöden som innehåller data som hämtats av loggen söker i logganalys.

Du kan till exempel använda Microsoft Flow för att använda Log Analytics-data i ett e-postmeddelande från Office 365, skapa ett programfel i Visual Studio Team Services eller skicka ett Slack meddelande.  Du kan utlösa ett arbetsflöde med ett enkelt schema eller från en åtgärd i en ansluten tjänst, till exempel när ett e-postmeddelande eller en tweet tas emot.  

Kursen i den här artikeln visar hur du skapar ett flöde som automatiskt skickar resultaten av en logganalys loggen via e-post, bara ett exempel på hur du kan använda logganalys i Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Steg 1: Skapa ett flöde
1. Logga in på [Microsoft Flow](http://flow.microsoft.com), och välj **Mina flödar**.
2. Klicka på **+ skapa från tomt**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Steg 2: Skapa en utlösare för din flöde
1. Klicka på **Sök hundratals kopplingar och utlösare**.
2. Typen **schema** i sökrutan.
3. Välj **schema**, och välj sedan **schema - upprepning**.
4. I den **frekvens** markerar du kryssrutan **dag** och i den **intervall** ange **1**.<br><br>![Dialogrutan för Microsoft Flow utlösare](media/log-analytics-flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Steg 3: Lägg till en Log Analytics-åtgärd
1. Klicka på **+ nytt steg**, och klicka sedan på **lägga till en åtgärd**.
2. Sök efter **logga Analytics**.
3. Klicka på **Azure Log Analytics – Kör fråga och visualisera resultat**.<br><br>![Kör frågefönstret logganalys](media/log-analytics-flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Steg 4: Konfigurera logganalys-åtgärd

1. Ange information för din arbetsyta inklusive prenumerations-ID, resursgrupp, och arbetsytans namn.
2. Lägg till följande Log Analytics-fråga för att den **frågan** fönster.  Detta är en exempelfråga och du kan ersätta med alla andra som returnerar data.
```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
```

2. Välj **HTML-tabell** för den **diagramtypen**.<br><br>![Log Analytics-åtgärd](media/log-analytics-flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Steg 5: Konfigurera flödet för att skicka e-post

1. Klicka på **nytt steg**, och klicka sedan på **+ Lägg till en åtgärd**.
2. Sök efter **Office 365 Outlook**.
3. Klicka på **Office 365 Outlook – skicka ett e-post**.<br><br>![Urvalsfönster för Office 365 Outlook](media/log-analytics-flow-tutorial/flow04.png)

4. Ange e-postadress för en mottagare i den **till** fönster och ett ämne för e-post i **ämne**.
5. Klicka någonstans i den **brödtext** rutan.  En **dynamiskt innehåll** öppnas med värden från tidigare åtgärder.  
6. Välj **brödtext**.  Detta är resultatet av frågan i logganalys-åtgärd.
6. Klicka på **visa avancerade alternativ**.
7. I den **är HTML** väljer **Ja**.<br><br>![Fönster för Office 365 e-konfiguration](media/log-analytics-flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Steg 6: Spara och testa din flöde
1. I den **flöde namnet** , lägga till ett namn för din flöde, och klicka sedan **skapa flödet**.<br><br>![Spara flöde](media/log-analytics-flow-tutorial/flow06.png)
2. Flödet skapas nu och kommer att köras efter en dag som är det schema som du har angett. 
3. Om du vill testa omedelbart flödet, klickar du på **kör nu** och sedan **kör flödet**.<br><br>![Kör flöde](media/log-analytics-flow-tutorial/flow07.png)
3. Kontrollera e-post för mottagare som du angav när flödet har slutförts.  Du bör ha fått ett e-postmeddelande med en text som liknar följande:<br><br>![E-postexempel](media/log-analytics-flow-tutorial/flow08.png)


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [logga sökningar i logganalys](log-analytics-log-search-new.md).
- Lär dig mer om [Microsoft Flow](https://ms.flow.microsoft.com).



