---
title: Hantera partnerlösningar i Azure Security Center | Microsoft Docs
description: I det här dokumentet går vi igenom hur du i Azure Security Center  enkelt kan övervaka hälsostatusen på de partnerlösningar som är integrerade i Azureprenumerationen.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/22/2016
ms.author: terrylan

---
# Övervaka partnerlösningar med Azure Security Center
I det här dokumentet går vi igenom hur du övervakar partnerlösningar med Azure Security Center.

> [!NOTE]
> Informationen i det här dokumentet rör förhandsversionen av Azure Security Center. I det här dokumentet beskrivs tjänsten genom en exempeldistribution. Det är alltså inte en steg-för-steg-guide.
> 
> 

## Vad är Security Center?
 Med hjälp av Security Center kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

## Övervaka partnerlösningar
På bladet **Partner solutions (Partnerlösningar)** i **Security Center** kan du snabbt se hälsostatusen på de partnerlösningar som är integrerade i Azureprenumerationen.
![Partnerlösningsrutan][1]

I rutan **Partner solutions (Partnerlösningar)** visas antalet partnerlösningar och en statussammanfattning för lösningarna.

**STATUS** för partnerlösningarna kan vara:

* Väl skyddad (grön): Det finns inga problem med säkerheten.
* Inte väl skyddad (röd): Det finns säkerhetsproblem som måste åtgärdas omedelbart.
* Har slutat rapportera (orange): Det kommer inga säkerhetsrapporter från lösningen.
* Okänd skyddsstatus (orange): Lösningens hälsa är okänd för tillfället på grund av ett misslyckat försök att lägga till en ny resurs i den befintliga lösningen.
* Inga rapporter (grå): Ingen rapport har kommit från lösningen än. Status för en lösning kan vara orapporterad om lösningen just anslutits och distributionen av den fortfarande pågår.

Om det inte finns några lösningar integrerade i prenumerationen syns det i rutan. Om du klickar på rutan **Partner solutions (Partnerlösningar)** öppnas bladet **Recommendations (Rekommendationer)**, varifrån du kan distribuera säkerhetslösningar från partnerföretag.
![Inga partnerlösningar][2]

Om du vill se partnerlösningarnas hälsa gör du så här:

1. Klicka på rutan **Partner solutions (Partnerlösningar)**. Då öppnas ett blad med en lista över alla partnerlösningar som är anslutna till Security Center.
   ![Partnerlösningar][3]
2. Klicka på en partnerlösning. I det här exemplet väljer vi lösningen **F5-WAF2**.  Då öppnas ett blad som visar status för partnerlösningen och resurserna som är kopplade till lösningen. Om du klickar på **Solution console (Lösningskonsol)** öppnas partnerhanteringsfunktionen för lösningen i fråga.
   ![Partnerlösningsinformation][4]
3. Gå tillbaka till bladet för **F5-WAF2** och klicka på **Link app (Koppla program)**. Bladet **Link app (Koppla program)** öppnas. Här kan du ansluta resurser till partnerlösningen.
   ![Koppla resurser till partnerlösning][5]

## Nästa steg
I det här dokumentet berättade vi om rutan **Partner Solutions (Partnerlösningar)** i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md): Här får du lära dig hur du kan skydda resurserna i Azure med hjälp av rekommendationer.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här kan du hitta de senaste nyheterna och aktuell information om säkerheten i Azure .

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png



<!--HONumber=Jun16_HO2-->


