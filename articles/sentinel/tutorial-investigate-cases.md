---
title: Undersöka incidenter med Azure Sentinel| Microsoft-dokument
description: Använd den här självstudien om du vill lära dig hur du undersöker incidenter med Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: ecd8c508d05bfeb541a6cb5efbcdf2fffd3c78d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587200"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Självstudiekurs: Undersöka incidenter med Azure Sentinel

> [!IMPORTANT]
> Undersökningsdiagrammet är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Den här självstudien hjälper dig att undersöka incidenter med Azure Sentinel. När du har anslutit dina datakällor till Azure Sentinel vill du få ett meddelande när något misstänkt händer. För att du ska kunna göra detta kan du med Azure Sentinel skapa avancerade aviseringsregler som genererar incidenter som du kan tilldela och undersöka.

Denna artikel innehåller:
> [!div class="checklist"]
> * Undersöka incidenter
> * Använd undersökningsdiagrammet
> * Reagera på hot

En incident kan innehålla flera aviseringar. Det är en sammanställning av alla relevanta bevis för en specifik undersökning. En incident skapas baserat på analytiska regler **Analytics** som du har skapat på Analytics-sidan. Egenskaperna som är relaterade till aviseringarna, till exempel allvarlighetsgrad och status, anges på incidentnivå. När du har låtit Azure Sentinel veta vilka typer av hot du letar efter och hur du hittar dem kan du övervaka upptäckta hot genom att undersöka incidenter.

## <a name="prerequisites"></a>Krav
Du kan bara undersöka incidenten om du använde entitetsmappningsfälten när du ställer in den analytiska regeln. Undersökningsdiagrammet kräver att den ursprungliga incidenten innehåller entiteter.

## <a name="how-to-investigate-incidents"></a>Så här undersöker du incidenter

1. Välj **Incidenter**. På sidan **Incidenter** kan du veta hur många incidenter du har, hur många som är öppna, hur många du har angett på **Pågår**och hur många som är stängda. För varje incident kan du se den tid det inträffade och statusen för incidenten. Titta på svårighetsgraden för att avgöra vilka incidenter som ska hanteras först.

    ![Visa allvarlighetsgrad för incidenter](media/tutorial-investigate-cases/incident-severity.png)

1. Du kan filtrera incidenter efter behov, till exempel efter status eller allvarlighetsgrad.

1. Om du vill starta en undersökning väljer du en specifik incident. Till höger kan du se detaljerad information för incidenten, inklusive dess allvarlighetsgrad, sammanfattning av antalet inblandade entiteter, de råa händelser som utlöste den här incidenten och incidentens unika ID.

1. Om du vill visa mer information om aviseringar och entiteter i incidenten väljer du **Visa fullständig information** på incidentsidan och granskar relevanta flikar som sammanfattar incidentinformationen. Granska själva aviseringen på fliken **Aviseringar.** Du kan se all relevant information om aviseringen – frågan som utlöste aviseringen, antalet returnerade resultat per fråga och möjligheten att köra spelböcker på aviseringarna. Om du vill öka detaljnivån ännu längre in i incidenten väljer du antalet **händelser**. Detta öppnar frågan som genererade resultaten och de händelser som utlöste aviseringen i Log Analytics. På fliken **Entiteter** kan du se alla entiteter som du har mappat som en del av aviseringsregeldefinitionen.

    ![Visa varningsinformation](media/tutorial-investigate-cases/alert-details.png)

1. Om du aktivt undersöker en incident är det en bra idé att ange incidentens status till **Pågår** tills du stänger den.

1. Incidenter kan tilldelas en viss användare. För varje incident kan du tilldela en ägare genom att ange fältet **Incident-ägare.** Alla incidenter börjar som otilldelade. Du kan också lägga till kommentarer så att andra analytiker kommer att kunna förstå vad du undersökt och vad dina farhågor är kring händelsen.

    ![Tilldela incident till användaren](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Välj **Undersök om** du vill visa undersökningskartan.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Använd undersökningsdiagrammet för att djupdyka

Undersökningsdiagrammet gör det möjligt för analytiker att ställa rätt frågor för varje undersökning. Undersökningsdiagrammet hjälper dig att förstå omfattningen och identifiera grundorsaken för ett potentiellt säkerhetshot genom att korrelera relevanta data med någon inblandad entitet. Du kan dyka djupare och undersöka alla entiteter som presenteras i diagrammet genom att välja den och välja mellan olika expansionsalternativ.  
  
Undersökningsdiagrammet ger dig:

- **Visuell kontext från rådata**: Det aktiva visuella diagrammet visar entitetsrelationer som extraheras automatiskt från rådata. På så sätt kan du enkelt se anslutningar mellan olika datakällor.

- Fullständig identifiering av **undersökningsomfattning:** Utöka undersökningsomfattningen med hjälp av inbyggda utforskningsfrågor för att visa hela omfattningen av ett intrång.

- **Inbyggda undersökningssteg:** Använd fördefinierade utforskningsalternativ för att se till att du ställer rätt frågor inför ett hot.

Så här använder du undersökningsdiagrammet:

1. Välj en incident och välj sedan **Undersök**. Detta tar dig till undersökningsdiagrammet. Diagrammet innehåller en belysande karta över de entiteter som är direkt kopplade till aviseringen och varje resurs som är ansluten ytterligare.

   > [!IMPORTANT] 
   > Du kan bara undersöka incidenten om du använde entitetsmappningsfälten när du ställer in den analytiska regeln. Undersökningsdiagrammet kräver att den ursprungliga incidenten innehåller entiteter.

   ![Visa karta](media/tutorial-investigate-cases/map1.png)

1. Välj en entitet för att öppna fönstret **Entiteter** så att du kan granska information om den entiteten.

    ![Visa entiteter på kartan](media/tutorial-investigate-cases/map-entities.png)
  
1. Utöka din undersökning genom att hovra över varje enhet för att avslöja en lista med frågor som har utformats av våra säkerhetsexperter och analytiker per enhetstyp för att fördjupa din undersökning. Vi kallar dessa alternativ **prospektering frågor**.

    ![Utforska mer information](media/tutorial-investigate-cases/exploration-cases.png)

   På en dator kan du till exempel begära relaterade aviseringar. Om du väljer en utforskningsfråga läggs de resulterande berättiganden tillbaka till diagrammet. I det här exemplet returnerade valet **av relaterade aviseringar** följande aviseringar till diagrammet:

    ![Visa relaterade aviseringar](media/tutorial-investigate-cases/related-alerts.png)

1. För varje utforskningsfråga kan du välja alternativet för att öppna råhändelseresultaten och frågan som används i Log Analytics genom att välja **Händelser\>**.

1. För att förstå händelsen ger diagrammet dig en parallell tidslinje.

    ![Visa tidslinje på karta](media/tutorial-investigate-cases/map-timeline.png)

1. Håll muspekaren över tidslinjen för att se vilka saker i diagrammet som inträffade vid vilken tidpunkt.

    ![Använd tidslinjen på kartan för att undersöka aviseringar](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig hur du kommer igång med att undersöka incidenter med Azure Sentinel. Fortsätt till handledningen för [hur man ska reagera på hot med hjälp av automatiserade spelböcker](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Svara på hot](tutorial-respond-threats-playbook.md) för att automatisera dina svar på hot.

