---
title: Skapa anpassade analytiska regler för att upptäcka misstänkta hot med Azure Sentinel| Microsoft-dokument
description: Använd den här självstudien om du vill lära dig hur du skapar anpassade analytiska regler för att upptäcka misstänkta hot med Azure Sentinel.
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
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585115"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Självstudiekurs: Skapa anpassade analytiska regler för att upptäcka misstänkta hot

När du [har anslutit dina datakällor](quickstart-onboard.md) till Azure Sentinel kan du skapa anpassade regler som kan söka efter specifika kriterier i hela din miljö och generera incidenter när kriterierna matchas så att du kan undersöka dem. Den här självstudien hjälper dig att skapa anpassade regler för att identifiera hot med Azure Sentinel.

Den här självstudien hjälper dig att identifiera hot med Azure Sentinel.
> [!div class="checklist"]
> * Skapa analytiska regler
> * Automatisera hotsvar

## <a name="create-custom-analytic-rules"></a>Skapa anpassade analytiska regler

Du kan skapa anpassade analytiska regler som hjälper dig att söka efter de typer av hot och avvikelser som är misstänkta i din miljö. Regeln ser till att du meddelas direkt, så att du kan triage, undersöka och åtgärda hoten.

1. Välj **Analytics**i Azure-portalen under Azure Sentinel .

1. I den övre menyraden väljer du **+Skapa** och väljer **Schemalagd frågeregel**. Då öppnas **guiden Analytics-regel**.

    ![Skapa schemalagd fråga](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Ange ett unikt **namn**och en **beskrivning**på fliken **Allmänt** . I fältet **Taktik** kan du välja mellan olika kategorier av attacker som ska klassificera regeln med. Ställ in **aviseringens allvarlighetsgrad** efter behov. När du skapar regeln **aktiveras** dess **status** som standard, vilket innebär att den körs direkt när du har skapat den. Om du inte vill att den ska köras omedelbart väljer du **Inaktiverad**och regeln läggs till på fliken **Aktiva regler** och du kan aktivera den därifrån när du behöver den.

    ![Börja skapa en anpassad analytisk regel](media/tutorial-detect-threats-custom/general-tab.png)

1. På fliken **Ange regellogik** kan du antingen skriva en fråga direkt i **frågefältet Regel** eller skapa frågan i Logganalys och sedan kopiera och klistra in den där.
 
   ![Skapa fråga i Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Se **förhandsgranskningsområdet resultat** till höger, där Azure Sentinel visar antalet resultat (logghändelser) som frågan genererar, ändrar i farten när du skriver och konfigurerar frågan. Diagrammet visar antalet resultat under den definierade tidsperioden, vilket bestäms av inställningarna i avsnittet **Frågeplanering.**
    - Om du ser att frågan skulle utlösa för många eller för frekventa aviseringar kan du ange en baslinje i avsnittet **Tröskelvärde för avisering.**

      Här är en exempelfråga som skulle varna dig när ett avvikande antal resurser skapas i Azure Activity.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > Frågelängden ska vara mellan 1 och 10 000 \*tecken och \*kan inte innehålla "sökning" eller "union ".

    1. Använd avsnittet **Mappningsentiteter** för att länka parametrar från frågeresultaten till Azure Sentinel-erkända entiteter. Dessa entiteter ligger till grund för ytterligare analys, inklusive gruppering av aviseringar i incidenter på fliken **Incidentinställningar.**
    1. Ange följande parametrar i avsnittet **Frågeplanering:**

       1. Ange **Kör fråga var och** en för att styra hur ofta frågan körs - så ofta som var 5 minuter eller så sällan som en gång om dagen.

       1. Ange **uppslagsdata från den sista** för att bestämma tidsperioden för de data som omfattas av frågan , till exempel kan den fråga de senaste 10 minuterna av data eller de senaste 6 timmarnas data.

       > [!NOTE]
       > Dessa två inställningar är oberoende av varandra, upp till en punkt. Du kan köra en fråga med ett kort intervall som täcker en tidsperiod som är längre än intervallet (i själva verket har överlappande frågor), men du kan inte köra en fråga med ett intervall som överskrider täckningsperioden, annars har du luckor i den övergripande frågetäckningen.

    1. Använd avsnittet Tröskelvärde för **avisering** för att definiera en baslinje. Ange till exempel **Generera avisering när antalet frågeresultat** **är större än** och ange numret 1000 om du vill att regeln bara ska generera en avisering om frågan genererar mer än 1 000 resultat varje gång den körs. Eftersom det här är ett obligatoriskt fält anger du 0 i fältet nummer om du inte vill ange en originalplan.

    1. I avsnittet **Övertryck** kan du aktivera **frågan Stoppa körning efter att aviseringen har genererats** **på** om du, när du får en avisering, vill pausa åtgärden för den här regeln under en tidsperiod som överskrider frågeintervallet. Om du aktiverar detta måste du ställa in **Sluta köra fråga för** hur lång tid frågan ska sluta köras, upp till 24 timmar.

1. På fliken **Incidentinställningar** kan du välja om och hur Azure Sentinel omvandlar aviseringar till åtgärdsbara incidenter. Om den här fliken lämnas ensam skapar Azure Sentinel en enda, separat incident från varje avisering. Du kan välja att inga incidenter ska skapas eller gruppera flera aviseringar i en enda incident genom att ändra inställningarna på den här fliken.

    1. I avsnittet **Incidentinställningar** anges Skapa incidenter från aviseringar som **utlöses av den här analysregeln** som standard **aktiverad**, vilket innebär att Azure Sentinel skapar en enda, separat incident från varje avisering som utlöses av regeln.<br></br>Om du inte vill att den här regeln ska resultera i att några incidenter skapas (till exempel om den här regeln bara ska samla in information för efterföljande analys) anger du detta till **Inaktiverat**.

    1. Om du vill att en enskild incident ska genereras från en grupp med liknande eller återkommande aviseringar i avsnittet **Aviseringsgrupper** anger du **grupprelaterade aviseringar som utlöses av den här analysregeln till incidenter** **till Aktiverade**och anger följande parametrar.

    1. **Begränsa gruppen till aviseringar som skapats inom den valda tidsramen:**<br></br> Bestäm den tidsram inom vilken liknande eller återkommande aviseringar ska grupperas tillsammans. Alla motsvarande aviseringar inom den här tidsramen genererar tillsammans en incident eller en uppsättning incidenter (beroende på gruppinställningarna nedan). Aviseringar utanför den här tidsramen genererar en separat incident eller uppsättning incidenter.

    2. **Gruppaviseringar som utlöses av den här analysregeln till en enda incident av**: Välj den grund på vilken aviseringar ska grupperas tillsammans:

        - **Gruppaviseringar i en enda incident om alla entiteter matchar:** <br></br>Aviseringar grupperas tillsammans om de delar identiska värden för var och en av de mappade entiteterna (definierad på fliken Ange regellogik ovan). Detta är den rekommenderade inställningen.

        - **Gruppera alla aviseringar som utlöses av den här regeln i en enda incident:** <br></br>Alla aviseringar som genereras av den här regeln grupperas tillsammans även om de inte har några identiska värden.

        - **Gruppaviseringar i en enda incident om de valda entiteterna matchar:** <br></br>Aviseringar grupperas tillsammans om de delar identiska värden för vissa av de mappade entiteterna (som du kan välja i listrutan). Du kanske vill använda den här inställningen om du till exempel vill skapa separata incidenter baserat på källan eller mål-IP-adresserna.

    3. **Öppna stängda matchningsincidenter**igen : Om en incident har stängts (vilket innebär att det underliggande problemet har lösts) och därefter genereras en annan avisering som skulle ha grupperats i den incidenten, ställer du in den här inställningen på **Aktiverad** om du vill att den stängda incidenten öppnas igen och lämnar som **inaktiverad** om du vill att aviseringen ska skapa en ny incident.

1. På fliken **Automatiserade svar** väljer du alla spelböcker som du vill köra automatiskt när en avisering genereras av den anpassade regeln. Mer information om hur du skapar och automatiserar spelböcker finns i [Svara på hot](tutorial-respond-threats-playbook.md).

1. Välj **Granska och skapa** om du vill granska alla inställningar för den nya varningsregeln och välj sedan Skapa för att initiera **varningsregeln**.
  
1. När aviseringen har skapats läggs en anpassad regel till i tabellen under **Aktiva regler**. I den här listan kan du aktivera, inaktivera eller ta bort varje regel.

1. Om du vill visa resultatet av de varningsregler som du skapar går du till sidan **Incidenter,** där du kan triage, [undersöka incidenter](tutorial-investigate-cases.md)och åtgärda hoten.


> [!NOTE]
> Aviseringar som genereras i Azure Sentinel är tillgängliga via [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Mer information finns i dokumentationen för [Microsoft Graph Security Alerts](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du kommer igång med att identifiera hot med Azure Sentinel.

Om du vill veta hur du automatiserar dina svar på hot [konfigurerar du automatiska hotsvar i Azure Sentinel](tutorial-respond-threats-playbook.md).

