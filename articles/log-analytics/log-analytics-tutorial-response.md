---
title: "Svara på händelser med Azure Log Analytics aviseringar | Microsoft Docs"
description: "Den här kursen hjälper dig att förstå aviseringar i logganalys att identifiera viktig information i OMS-databasen och att meddela dig om problem eller anropa åtgärder om du vill försöka åtgärda."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3ab8d32eb4b3f2748249f40139de76c8e7f4d971
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>Svara på händelser med Log Analytics aviseringar
Aviseringar i Log Analytics identifiera viktig information i logganalys-databasen.  De skapas med Varningsregler som automatiskt kör loggen söker regelbundet och om loggen sökresultatet matchar särskilda skapas en avisering post och den kan konfigureras för att utföra ett automatiskt svar.  Den här kursen är en förlängning av den den [skapa och dela instrumentpaneler logganalys data](log-analytics-tutorial-dashboards.md) kursen.   

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en aviseringsregel
> * Konfigurera en aviseringsregel om du vill skicka ett e-postmeddelande

Du måste ha en befintlig virtuell dator för att slutföra exemplet i den här självstudiekursen [ansluten till logganalys-arbetsytan](log-analytics-quick-collect-azurevm.md).  

## <a name="log-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Skapa aviseringar

Aviseringar skapas med Varningsregler som automatiskt kör loggen söker regelbundet.  Du kan skapa varningar baserat på specifika prestandamått eller när vissa händelser skapas, skapas frånvaron av en händelse eller ett antal händelser inom ett visst tidsintervall.  Aviseringar kan exempelvis användas för att meddela dig när Genomsnittlig CPU-användning överskrider ett visst tröskelvärde eller en händelse genereras när en specifik tjänst i Windows eller Linux-daemon körs inte.   Om resultatet av loggen sökningen matchar särskilda skapas en avisering post. Regeln kan sedan automatiskt köra en eller flera åtgärder för att proaktivt meddelar dig om aviseringen eller anropa en annan process. 

I följande exempel skapar vi en regel för varning mått mått som skapar en avisering för varje datorobjekt i frågan med ett värde som överskrider ett tröskelvärde för 90%.

1. I Azure-portalen klickar du på **fler tjänster** hittades i det nedre vänstra hörnet. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **logga Analytics**...
2. Starta OMS-portalen genom att välja OMS-portalen och på den **översikt** väljer **loggen Sök**.  
3. Välj **Favoriter** från upp till portalen och i den **sparade sökningar** rutan till höger, Välj frågan som *virtuella datorer i Azure - processorbelastning*.  
4. Klicka på **avisering** längst upp på sidan för att öppna den **lägga till Varningsregeln** skärmen.  
5. Konfigurera varningsregeln med följande information:  
   a. Ange en **namn** om varningen som *VM processoranvändning överskred > 90*  
   b. För **tidsfönstret**, ange ett tidsintervall för frågan, till exempel *30*.  Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden.  
   c. **Varna frekvens** anger hur ofta frågan ska köras.  Det här exemplet anger *5* minuter, som kommer att ske i vår tidsfönster som angetts.  
   d. Välj **mått mätning** och ange *90* för **visar det sammanlagda värdet** och ange *3* för **utlösaren avisering baserat på**   
   e. Under **åtgärder**, inaktiverar du e-postavisering.
6. Klicka på **spara** att slutföra varningsregeln. Den ska börja köras omedelbart.<br><br> ![Varningsregel för exempel](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

Aviseringen poster som skapats av Varningsregler i logganalys har en typ av **avisering** och en SourceSystem av **OMS**.<br><br> ![Exempel på en avisering genereras](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>Aviseringsåtgärder
Du kan utföra avancerade åtgärder med aviseringar så som att skapa en e-postavisering starta en [Automation-runbook](../automation/automation-runbook-types.md), använda en webhook för att skapa en post för incidenten i systemet ITSM incidenthantering eller med den [IT Service Management-anslutningstjänsten lösning](log-analytics-itsmc-overview.md) som en respons när aviseringsvillkoren uppfylls.   

E-post-åtgärder skicka ett e-postmeddelande med information om aviseringen till en eller flera mottagare. Du kan ange ämnet för e-post, men dess innehåll är ett standardformat genom logganalys.  Vi uppdatera varningsregeln skapade tidigare och konfigurera den för att skicka e-post meddelar dig i stället för att aktivt övervakning för avisering posten med en logg-sökning.     

1. I OMS-portalen på den översta menyn väljer **inställningar** och välj sedan **aviseringar**.
2. Klicka på pennikonen bredvid aviseringen skapade tidigare i listan över Varningsregler.
3. Under **åtgärder** aktiverar e-postmeddelanden.
4. Ange en **ämne** för e-post, som *Processor användningen överskrider tröskelvärdet > 90*.
5. Lägg till adresser för en eller flera e-postmottagare i den **mottagare** fältet.  Om du anger fler än en adress sedan Avgränsa adresserna med semikolon (;).
6. Klicka på **spara** att slutföra varningsregeln. Den ska börja köras omedelbart.<br><br> ![Varningsregeln med e-postavisering](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>Nästa steg
I kursen får du lärt dig hur aviseringen regler proaktivt identifiera och svara på ett problem när de kör loggen sökningar på schemalagda intervall och matchar vissa villkor.  

Den här länken om du vill se förskapad logganalys skriptexempel.  

> [!div class="nextstepaction"]
> [Logga Analytics skriptexempel](powershell-samples.md)