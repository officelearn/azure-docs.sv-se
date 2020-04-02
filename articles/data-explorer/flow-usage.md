---
title: Exempel på användning av Microsoft Azure Data Explorer Flow Connector (Preview)
description: Lär dig några vanliga exempel på vanliga Microsoft Flow-anslutningsanvändning.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 1b9d593b0f0895e2ba75fae7ab7e78ea883c8907
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521712"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Exempel på användning av Microsoft Flow connector (förhandsversion)

Azure Data Explorer-flödesappen gör att Azure Data Explorer kan använda [Microsoft Power Automates flödesfunktioner](https://flow.microsoft.com/) för att köra Kusto-frågor och kommandon automatiskt som en del av en schemalagd eller utlöst aktivitet. Det här dokumentet innehåller flera vanliga exempel på användning av Microsoft Flow-anslutning.

Mer information finns i [Microsoft Flow connector (Preview)](flow.md).

* [Microsoft Flow-anslutning och SQL](#microsoft-flow-connector-and-sql)
* [Skicka data till Power BI-datauppsättning](#push-data-to-power-bi-dataset)
* [Villkorsstyrda frågor](#conditional-queries)
* [Skicka flödesscheman för flera Azure Data Explorer-data](#email-multiple-azure-data-explorer-flow-charts)

## <a name="microsoft-flow-connector-and-sql"></a>Microsoft Flow-anslutning och SQL

Använd Microsoft Flow-kopplingen för att fråga dina data och aggregera dem i en SQL-databas.

> [!Note]
> SQL-infogning görs separat för varje rad. Använd endast Microsoft Flow-kopplingen för små mängder utdata. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> Ange kluster-URL:en i fältet *Klusternamn.*

## <a name="push-data-to-power-bi-dataset"></a>Skicka data till Power BI-datauppsättning

Microsoft Flow-anslutningen kan användas tillsammans med Power BI-anslutningen för att skicka data från Kusto-frågor till Power BI-strömmande datauppsättningar.

1. Skapa en ny körningsfråga och listresultat.
1. Välj **Nytt steg**.
1. Välj **Lägg till en åtgärd** och sök efter Power BI.
1. Välj **Power BI**.
1. Välj **Lägg till rader i en datauppsättning**. 

    ![Power BI-kontakt med flödesström](./media/flow-usage/flow-powerbiconnector.png)
1. Ange **arbetsytan,** **datauppsättningen**och **tabell till** vilken data ska skickas.
1. Lägg till en nyttolast som innehåller ditt datamängdsschema och relevanta Kusto-frågeresultat i dialogrutan för dynamiskt innehåll.

    ![Flödeskraft BI-fält](./media/flow-usage/flow-powerbifields.png)

Flow tillämpar automatiskt Power BI-åtgärden för varje rad i kusto-frågeresultattabellen. 

![Flöde Power BI-åtgärd för varje rad](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Villkorsstyrda frågor

Resultaten av Kusto-frågor kan användas som indata eller villkor för nästa flödesåtgärder.

I följande exempel frågar vi Kusto efter incidenter som inträffade under den sista dagen. För varje löst incident publiceras ett slackmeddelande och ett push-meddelande skapas.
För varje incident som fortfarande är aktiv efterfrågas Kusto för mer information om liknande incidenter. Den skickar den informationen som ett e-postmeddelande och öppnar en relaterad TFS-uppgift.

Följ dessa instruktioner för att skapa ett liknande flöde:

1. Skapa en ny körningsfråga och listresultat.
1. Välj **Nytt steg**.
1. Välj **villkorskontroll**.
1. Välj den parameter som du vill använda som villkor för nästa åtgärd i fönstret dynamiskt innehåll.
1. Välj den typ av *relation* och *värde som* ska anges för ett visst villkor för den angivna parametern.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Flow tillämpar det här villkoret på varje rad i frågeresultattabellen.
1. Lägg till åtgärder för när villkoret är sant och falskt.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

Du kan använda resultatvärdena från Kusto-frågan som indata för nästa åtgärder. Välj resultatvärdena i fönstret dynamiskt innehåll.
I exemplet nedan lades en Slack - Inläggsmeddelandeåtgärd och Visual Studio - Skapa en ny arbetsobjektåtgärd som innehåller data från Kusto-frågan till.

![Slack - Åtgärden Inläggsmeddelande](./media/flow-usage/flow-slack.png)

![Visual Studio-åtgärd](./media/flow-usage/flow-visualstudio.png)

I det här exemplet, om en incident fortfarande är aktiv, frågar du Kusto igen för att få information om hur incidenter från samma källa har löstes tidigare.

![Fråga om flödesvillkor](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> Ange kluster-URL:en i fältet *Klusternamn.*

Visualisera den här informationen som ett cirkeldiagram och skicka den till teamet via e-post.

![E-post med flödestillstånd](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Skicka flödesscheman för flera Azure Data Explorer-data

1. Skapa ett nytt flöde med upprepningsutlösaren och definiera intervallet för flödet och frekvensen. 
1. Lägg till ett nytt steg med en eller flera Kusto - Kör fråga och visualisera resultatåtgärder. 

    ![Köra flera frågor i ett flöde](./media/flow-usage/flow-severalqueries.png)
1. För varje Kusto - Kör fråga och visualisera resultatet definierar du följande fält:
    * Kluster-URL
    * Databasnamn
    * Fråga och diagramtyp (HTML-tabell, cirkeldiagram, tidsdiagram, stapeldiagram eller ange ett anpassat värde).

    ![Visualisera resultat med flera bifogade filer](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. Lägga till en skicka ett e-postmeddelande (v2) åtgärd: 
    1. Välj kodvyikonen i brödtextavsnittet.
    1. I fältet **Brödtext** infogar du den nödvändiga BodyHtml så att det visualiserade resultatet av frågan inkluderas i brödtexten i e-postmeddelandet.
    1. Om du vill lägga till en bifogad fil i e-postmeddelandet lägger du till Namn och innehåll för bifogade filer.
    
    ![E-post flera bilagor](./media/flow-usage/flow-email-multiple-attachments.png)

    Fullständiga instruktioner om hur du skapar en e-poståtgärd finns i [E-postutfrågeresultatet för Kusto.](flow.md#email-kusto-query-results) 

Resultat:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Microsoft Azure Explorer Logic App-kopplingen](https://docs.microsoft.com/azure/kusto/tools/logicapps) som är ett annat sätt att köra Kusto-frågor och kommandon automatiskt som en del av en schemalagd eller utlöst aktivitet.
