---
title: Automatisera Azure Monitor logg processer med Microsoft Flow
description: Lär dig hur du kan använda Microsoft Flow för att snabbt automatisera upprepade processer med hjälp av Azure Log Analytics-anslutningen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/29/2017
ms.openlocfilehash: 92f0d2916b0f28760f7d028ee3e6dc0be37c32d2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672318"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Automatisera Azure Monitor logg processer med anslutnings programmet för Microsoft Flow
Med [Microsoft Flow](https://ms.flow.microsoft.com) kan du skapa automatiserade arbets flöden med hundratals åtgärder för olika tjänster. Utdata från en åtgärd kan användas som indata till en annan som gör det möjligt att skapa integration mellan olika tjänster.  Med Azure Log Analytics-anslutaren för Microsoft Flow kan du bygga arbets flöden som innehåller data som hämtats av logg frågor från en Log Analytics arbets yta i Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Du kan till exempel använda Microsoft Flow för att använda Azure Monitor loggdata i ett e-postmeddelande från Office 365, skapa en bugg i Azure DevOps eller publicera ett slack-meddelande.  Du kan utlösa ett arbets flöde med ett enkelt schema eller från en åtgärd i en ansluten tjänst, till exempel när ett e-postmeddelande eller en tweet tas emot.  

Självstudien i den här artikeln visar hur du skapar ett flöde som automatiskt skickar resultatet av en Azure Monitor logg fråga per e-post, bara ett exempel på hur du kan använda Log Analytics-anslutningen i Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Steg 1: skapa ett flöde
1. Logga in på [Microsoft Flow](https://flow.microsoft.com)och välj **mina flöden**.
2. Klicka på **+ skapa från Tom**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Steg 2: skapa en utlösare för ditt flöde
1. Klicka på **Sök hundratals kopplingar och utlösare**.
2. Skriv **schema** i sökrutan.
3. Välj **schema**och välj sedan **schema-upprepning**.
4. I rutan **frekvens** väljer du **dag** och i rutan **intervall** anger du **1**.<br><br>dialog rutan ![Microsoft Flow utlösare](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Steg 3: Lägg till en Log Analytics-åtgärd
1. Klicka på **+ nytt steg**och klicka sedan på **Lägg till en åtgärd**.
2. Sök efter **Log Analytics**.
3. Klicka på **Azure Log Analytics – kör fråga och visualisera resultat**.<br><br>![Log Analytics köra Query-fönstret](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Steg 4: Konfigurera åtgärden Log Analytics

1. Ange information om arbets ytan, inklusive prenumerations-ID, resurs grupp och arbets ytans namn.
2. Lägg till följande logg fråga i **frågefönstret** .  Detta är endast en exempel fråga och du kan ersätta med andra som returnerar data.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. Välj **HTML-tabell** för **diagram typen**.<br><br>![Log Analytics åtgärd](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Steg 5: konfigurera flödet för att skicka e-post

1. Klicka på **nytt steg**och klicka sedan på **+ Lägg till en åtgärd**.
2. Sök efter **Office 365 Outlook**.
3. Klicka på **Office 365 Outlook – skicka ett e-postmeddelande**.<br><br>![Office 365 Outlook Selection-fönster](media/flow-tutorial/flow04.png)

4. Ange e-postadressen för en mottagare i fönstret **till** och ett ämne för e-postmeddelandet i **ämne**.
5. Klicka var som helst i rutan **brödtext** .  Ett **dynamiskt innehålls** fönster öppnas med värden från föregående åtgärder.  
6. Välj **brödtext**.  Detta är resultatet av frågan i Log Analytics åtgärden.
6. Klicka på **Visa avancerade alternativ**.
7. I rutan **är HTML** väljer du **Ja**.<br><br>![Office 365-fönstret för e-postkonfiguration](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Steg 6: Spara och testa ditt flöde
1. Lägg till ett namn för ditt flöde i rutan **flödes namn** och klicka sedan på **skapa flöde**.<br><br>![Spara flöde](media/flow-tutorial/flow06.png)
2. Flödet skapas nu och körs efter en dag som är det schema du angav. 
3. Om du vill testa flödet omedelbart klickar du på **Kör nu** och **kör sedan flöde**.<br><br>![kör flöde](media/flow-tutorial/flow07.png)
3. När flödet har slutförts, kontrol lera e-postmeddelandets mottagare som du har angett.  Du bör ha fått ett e-postmeddelande med en brödtext som liknar följande:<br><br>![Exempel på e-post](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [logg frågor i Azure Monitor](../log-query/log-query-overview.md).
- Läs mer om [Microsoft Flow](https://ms.flow.microsoft.com).


