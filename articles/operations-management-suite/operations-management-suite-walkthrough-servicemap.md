---
title: "Tjänstkarta – en demo du går igenom i egen takt | Microsoft Docs"
description: "Tjänstkarta är en lösning i Operations Management Suite (OMS) och som automatiskt identifierar programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster.  Det här är en demo du går igenom i egen takt där Tjänstkarta används till att identifiera och diagnostisera ett simulerat problem i en webbapp."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: c3548d24c74f8ad865b22d6af3490d0b5cc77a84
ms.lasthandoff: 04/13/2017


---

# <a name="operations-management-suite-oms-self-paced-demo---service-map"></a>En demo för Operations Management Suite (OMS) – Tjänstkarta
Det här är en demo du går igenom i egen takt där [lösningen Tjänstkarta](operations-management-suite-service-map.md) i Operations Management Suite (OMS) används till att identifiera och diagnostisera ett simulerat problem i en webbapp.  Tjänstkarta identifierar automatiskt programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster.  Dessutom sammanställs data som samlas in från andra OMS-tjänster som beslutsstöd när du ska analysera prestanda och identifiera problem.  Du använder också [loggsökningar i Log Analytics](../log-analytics/log-analytics-log-searches.md) till att öka detaljnivån på insamlade data så att du kan identifiera rotorsaken till problemet.


## <a name="scenario-description"></a>Scenariobeskrivning
Du har precis fått ett meddelande om att programmet ACME Customer Portal har prestandaproblem.  Den enda information du har är att problemen startade kring 04.00 PST idag.  Du är inte helt säker på vilka komponenter som portalen är beroende av förutom en uppsättning webbservrar.  

## <a name="components-and-features-used"></a>Komponenter och funktioner som används
- [Lösningen Tjänstkarta](operations-management-suite-service-map.md)
- [Loggsökningar i Log Analytics](../log-analytics/log-analytics-log-searches.md)


## <a name="walk-through"></a>Genomgång

### <a name="1-connect-to-the-oms-experience-center"></a>1. Anslut till OMS Experience Center
I den här genomgången används [Operations Management Suite Experience Center](https://experience.mms.microsoft.com/) som innehåller en fullständig OMS-miljö med exempeldata. Börja med att följa länken, ange din information och välj sedan scenariot **Insikter och analys**.


### <a name="2-start-service-map"></a>2. Starta Tjänstkarta
Starta lösningen Tjänstkarta genom att klicka på panelen **Tjänstkarta**.

![Panelen Tjänstkarta](media/operations-management-suite-walkthrough-servicemap/tile.png)

Konsolen för Tjänstkarta öppnas.  I den vänstra rutan visas en lista med datorer i din miljö där Tjänstkarta-agenten är installerad.  Välj den dator du vill visa i den här listan.

![Lista med datorer](media/operations-management-suite-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3. Visa datorn
Vi vet att webbservrarna heter AcmeWFE001 och AcmeWFE002, så att det ser ut som ett rimligt ställe att börja på.  Klicka på **AcmeWFE001**.  Då visas kartan för AcmeWFE001 och alla dess beroenden.  Du kan se vilka processer som körs på den valda datorn och vilka externa tjänster de kommunicerar med.

![Webbserver](media/operations-management-suite-walkthrough-servicemap/web-server.png)

Vi är bekymrade över prestandan hos våra webbappar, så klicka på processen **AcmeAppPool (IIS-appool)**.  Då visas information om processen och dess beroenden framhävs.  

![Programpool](media/operations-management-suite-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4. Ändra tidsfönstret

Vi fick veta att problemen började kring 04.00, så vi tar en titt på vad som hände då. Klicka på **Tidsintervall** och ändra tiden till 04.00 PST (behåll aktuellt datum och anpassa tiden till din lokala tidszon) och använd varaktigheten 20 minuter.

![Tidväljaren](./media/operations-management-suite-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5. Visa aviseringar

Vi ser nu att det finns en avisering för beroende **acmetomcat**, så det här kan vara orsaken till vårt problem.  Klicka på aviseringsikonen i **acmetomcat** så att du ser detaljerna om aviseringen.  Vi kan se att processoranvändningen var kritiskt hög och kan expandera den här informationen.  Det här har förmodligen orsakat våra prestandaproblem. 

![Varning](./media/operations-management-suite-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6. Visa prestanda

Låt oss titta närmare på **acmetomcat**.  Klicka uppe till höger i **acmetomcat** och välj **Läs in servermappning** så att du kan se detaljerad information och beroenden för den här datorn. Vi kan sedan titta lite närmare på de här prestandaräknarna och verifiera vår misstanke.  Välj fliken **Prestanda** där du kan se de [prestandaräknare som samlats in av Log Analytics](../log-analytics/log-analytics-data-sources-performance-counters.md) under tidsintervallet.  Vi kan se att det förekommer periodiska toppar för processor- och minnesanvändningen.

![Prestanda](./media/operations-management-suite-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7. Visa spårning av ändringar
Nu ska vi se om vi kan ta reda på vad som har orsakat den här höga användningsnivån.  Klicka på fliken **Sammanfattning**.  Här ser vi information som OMS har samlat in från datorn, som misslyckade anslutningar, kritiska aviseringar och ändringar i programvaran.  Du bör alltid expandera avsnitt med intressant och aktuell information, och du kan expandera andra avsnitt om du vill kontrollera informationen i dem.


Om **Spårning av ändringar** inte redan är öppet expanderar du det här avsnittet.  Här visas information som samlats in av [lösningen Spårning av ändringar](../log-analytics/log-analytics-change-tracking.md).  Det verkar som om att en ändring i programvaran har utförts under den här tidsperioden.  Visa mer information genom att klicka på **Programvara**.  En säkerhetskopiering lades till i datorn precis efter 04.00, så det här verkar vara orsaken till att så mycket resurser har används.

![Spårning av ändringar](./media/operations-management-suite-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8. Visa information i loggsökningar
Vi kan kontrollera det här noggrannare genom att granska den detaljerade prestandainformationen i Log Analytics-databasen.  Klicka på fliken **Aviseringar** igen och klicka sedan på någon av aviseringarna om **hög CPU-användning**.  Klicka på **Visa i loggsökning**.  Då öppnas fönstret Loggsökning där du kan göra [loggsökningar](../log-analytics/log-analytics-log-searches.md) mot data som lagras i databasen.  Tjänstmappning har redan fyllt i en fråga så att vi kan hämta de aviseringar vi är intresserade av.  

![Loggsökning](./media/operations-management-suite-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9. Öppna sparad sökning
Låt oss se om vi kan hämta mer information om prestandasamlingen som genererade aviseringen och kontrollera vår misstanke om att problemen beror på säkerhetskopieringen.  Ändra tidsintervallet till **6 timmar**.  Klicka sedan på **Favoriter** och rulla ned till de sparade sökningarna för **Tjänstkarta**.  Det här är frågor som vi har skapat särskilt för den här analysen.  Klicka på **Top 5 Processes by CPU for acmetomcat** (De 5 processer som har högst processoranvändning för acmetomcat).

![Sparad sökning](./media/operations-management-suite-walkthrough-servicemap/saved-search.png)


Den här frågan returnerar en lista med de 5 processer som förbrukar mest processorkraft på **acmetomcat**.  Du kan granska frågan om du vill se ett exempel på det frågespråk som används för loggsökningar.  Om du är intresserad av processerna på andra datorer kan du ändra frågan och hämta den informationen.

I det här fallet kan vi se att säkerhetskopieringen konstant förbrukar ungefär 60 % av programserverns processorkraft.  Det är ganska uppenbart att den här nya processen orsakar våra prestandaproblem.  Lösningen är naturligtvis att flytta den här nya säkerhetskopieringen från programservern.  Vi kan faktiskt använda DSC (Desired State Configuration) som hanteras av Azure Automation till att definiera principer som ser till att den här processen aldrig körs på de här kritiska systemen.


## <a name="summary-points"></a>Sammanfattning
- Med [Tjänstkarta](operations-management-suite-service-map.md) får du en överblick över hela programmet även om du inte känner till alla servrar och beroenden.
- I Tjänstkarta ser du data som samlats in av andra OMS-lösningar så att du kan identifiera problem med program och den underliggande infrastrukturen.
- Med [loggsökningar](../log-analytics/log-analytics-log-searches.md) kan du öka detaljnivån för specifika data som samlats in i Log Analytics-databasen.    

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Tjänstkarta](operations-management-suite-service-map.md).
- [Distribuera och konfigurera](operations-management-suite-service-map-configure.md) Tjänstkarta.
- Läs mer om [Log Analytics](../log-analytics/log-analytics-overview.md) som krävs för Tjänstkarta och som lagrar driftdata från agenter.
