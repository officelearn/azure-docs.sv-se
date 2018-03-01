---
title: "Svara på händelser med Azure Log Analytics-aviseringar | Microsoft Docs"
description: "Den här självstudien hjälper dig att förstå aviseringar i Log Analytics som visar viktig information i OMS-lagringsplatsen. Du kan få tidiga meddelanden om problem eller anropa åtgärder om du vill försöka åtgärda dem."
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
ms.openlocfilehash: fcfaa849f67ffcfa69672d116837e96d318c2124
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>Svara på händelser med Log Analytics-aviseringar
Med aviseringar i Log Analytics kan du identifiera viktig information på Log Analytics-lagringsplatsen. De skapas från varningsregler som automatiskt kör regelbundna loggsökningar. Om resultatet från loggsökningen matchar särskilda kriterier, skapas en aviseringspost som kan konfigureras till en automatisk åtgärd.  Den här självstudien är en fortsättning på självstudien [Skapa och dela instrumentpaneler med Log Analytics-data](log-analytics-tutorial-dashboards.md).   

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en varningsregel
> * Konfigurera en varningsregel som skickar ett e-postmeddelande

Du måste ha en befintlig virtuell dator [som är ansluten till Log Analytics-arbetsytan](log-analytics-quick-collect-azurevm.md) för att kunna utföra exemplet i självstudien.  

## <a name="log-in-to-azure-portal"></a>Logga in på Azure Portal
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Skapa aviseringar

Aviseringar skapas från varningsregler som automatiskt kör regelbundna loggsökningar.  Du kan skapa aviseringar baserat på specifika prestandamått, när vissa händelser skapas, om en händelse saknas, eller om flera händelser skapas inom ett visst tidsintervall.  Aviseringar kan exempelvis användas för att meddela dig när den genomsnittliga CPU-användningen överskrider ett visst tröskelvärde, eller om en händelse genereras när en specifik Windows-tjänst eller Linux-daemon inte körs.   Om resultatet av loggsökningen matchar särskilda villkor, skapas en aviseringspost. Regeln kan sedan automatiskt köra en eller flera åtgärder för att proaktivt meddela dig om aviseringen eller anropa en annan process. 

I följande exempel skapar du en varningsregel för metriska måttenheter som skapar en avisering för varje datorobjekt i frågan med ett värde som överskrider tröskelvärdet 90 %.

1. Klicka på **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. Starta OMS-portalen genom att välja OMS-portalen. På sidan **Översikt** väljer du **Loggsökning**.  
3. Välj **Favoriter** överst i portalen och i fönstret **Sparade sökningar** till höger väljer du frågan *Virtuella Azure-datorer – processoranvändning*.  
4. Klicka på **Avisering** längst upp på sidan för att öppna skärmen **Lägg till varningsregel**.  
5. Konfigurera varningsregeln med följande information:  
   a. Ange en **Namn** på varningen, exempelvis *Processoranvändning för virtuell dator överskrider > 90*  
   b. I **Tidsperiod** anger du ett tidsintervall för frågan, till exempel *30*.  Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden.  
   c. I **Aviseringsfrekvens** väljer du hur ofta frågan ska köras.  I det här exemplet anger vi *5* minuter, vilket inträffar inom vår angivna tidsperiod.  
   d. Välj **Metrisk måttenhet** och ange *90* i **Aggregated Value** (Mängdvärde) och *3* för **Utlös aviseringen baserat på**   
   e. Under **Åtgärder** inaktiverar du e-postaviseringarna.
6. Klicka på **Spara** för att slutföra varningsregeln. Den börjar köras omedelbart.<br><br> ![Exempel på varningsregel](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

Aviseringsposter som skapats av varningsregler i Log Analytics har en typ av **avisering** och SourceSystem **OMS**.<br><br> ![Exempel på genererade aviseringshändelser](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>Aviseringsåtgärder
Du kan utföra avancerade åtgärder med aviseringar som exempelvis att skapa en e-postavisering, starta en [Automation Runbook](../automation/automation-runbook-types.md), använda en webhook för att skapa en incidentpost i händelsehanteringssystemet ITSM, eller använda [Anslutningsprogram för hantering av IT-tjänster (ITSM)](log-analytics-itsmc-overview.md) som åtgärd när aviseringsvillkoren uppfylls.   

E-poståtgärderna skickar ett e-postmeddelande med information om aviseringen till en eller flera mottagare. Du kan ange ämnet för e-postmeddelandet, men innehållet är ett standardformat som Log Analytics sammanställer.  Låt oss uppdatera den varningsregel som vi skapade tidigare och konfigurera den för att skicka e-post till dig, i stället för att aktivt leta efter aviseringsposten med en loggsökning.     

1. I OMS-portalen på den översta menyn väljer du **Inställningar** och sedan **Aviseringar**.
2. Klicka på pennikonen bredvid den avisering som vi skapade tidigare i listan med varningsregler.
3. I avsnittet **Åtgärder** aktiverar du e-postmeddelanden.
4. Ange ett **Ämne** för e-postmeddelandet, exempelvis *Processoranvändningen överskrider tröskelvärdet > 90*.
5. Lägg till adresser till en eller flera e-postmottagare i fältet **Mottagare**.  Om du anger mer än en adress kan du avgränsa dem med semikolon (;).
6. Klicka på **Spara** för att slutföra varningsregeln. Den börjar köras omedelbart.<br><br> ![Varningsregel med e-postavisering](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur varningsregler proaktivt kan identifiera och åtgärda problem när de kör loggsökningar i schemalagda intervall och matchar ett särskilt villkor.  

Följ den här länken om du vill se inbyggda skriptexempel för Log Analytics.  

> [!div class="nextstepaction"]
> [Skriptexempel för Log Analytics](powershell-samples.md)