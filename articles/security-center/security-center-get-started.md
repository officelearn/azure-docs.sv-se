<properties
   pageTitle="Snabbstartsguide för Azure Security Center | Microsoft Azure"
   description="Det här dokumentet hjälper dig att komma igång snabbt med Azure Security Center och guidar dig genom komponenterna för säkerhetsövervakning och principhantering och länkar till nästa steg."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/26/2016"
   ms.author="terrylan"/>

# Snabbstartsguide för Azure Security Center

Det här dokumentet hjälper dig att komma igång snabbt med Azure Security Center och guidar dig genom komponenterna för säkerhetsövervakning och principhantering och länkar till nästa steg.

> [AZURE.NOTE] Informationen i det här dokumentet rör förhandsversionen av Azure Security Center. I det här dokumentet presenteras tjänsten genom en exempeldistribution. Det är inte en steg-för-steg-guide.

## Vad är Azure Security Center?
 Med hjälp av Security Center kan du förebygga, upptäcka och åtgärda hot med ökad överblick i, och kontroll över, säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

## Nödvändiga komponenter

Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Security Center aktiveras med din prenumeration. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/pricing/free-trial/).

 Security Center öppnas från den [Azure Portal](https://azure.microsoft.com/features/azure-portal/). [Portaldokumentationen](https://azure.microsoft.com/documentation/services/azure-portal/) innehåller mer information.


## Öppna Security Center

Följ anvisningarna för att öppna Security Center i portalen:

1. Välj **Bläddra** och rulla sedan till alternativet **Security Center**.
![Öppna Azure  Security Center i portalen][1]

2. Välj **Security Center**. Då öppnas bladet **Security Center**.
3. Om du enkelt vill komma åt bladet **Security Center** framöver markerar du alternativet **Fäst bladet på instrumentpanelen** (längst upp till höger).
![Alternativet Fäst bladet på instrumentpanelen][2]

## Använda Security Center

Du kan konfigurera säkerhetsprinciper för dina Azure-prenumerationer och -resursgrupper. Vi ska konfigurera en säkerhets**princip** för din prenumeration:

1. Välj ikonen **Princip** i bladet **Security Center**.
![Security Center][3]

2. I bladet **Säkerhetsprincip – Definiera princip per prenumeration eller resursgrupp** väljer du en prenumeration.
![Bladet med säkerhetsprinciper i Azure Security Center][4]

3. I bladet **Säkerhetsprincip** aktiverar du **Datainsamling** så att loggar samlas in automatiskt. När du aktiverar **datainsamling** etableras även övervakningstillägget på alla aktuella och nya virtuella datorer i prenumerationen.
4. Markera **Välj ett lagringskonto per region**. För var och en av de regioner där du har virtuella datorer som körs, väljer du det lagringskonto där data som samlas in från dessa datorer ska lagras. Om du inte väljer ett lagringskonto för varje region skapas ett åt dig. Data som samlas in blir logiskt isolerade från andra kunders data av säkerhetsskäl.

     > [AZURE.NOTE] Vi rekommenderar att du aktiverar datainsamling och väljer ett lagringskonto på prenumerationsnivån först.  Säkerhetsprinciper kan ställas in på prenumerations- och resursgruppsnivå i Azure, men konfigurationen för datainsamling och lagringskontot kan bara ställas in på prenumerationsnivå.

5. Aktivera de **rekommendationer** du vill ha som en del av din säkerhetsprincip. Exempel:

 - Om du aktiverar **Systemuppdateringar** genomsöks alla virtuella datorer som stöds efter saknade OS-uppdateringar.
 - Om du aktiverar **Baslinjeregler** genomsöks alla virtuella datorer  som stöds för att identifiera OS-konfigurationer som kan göra en virtuell dator känsligare för angrepp.

Hantera **Rekommendationer**:

1. Gå tillbaka till bladet **Security Center** och välj panelen **Rekommendationer**. Security Center analyserar regelbundet säkerhetstillståndet hos dina Azure-resurser. När eventuella säkerhetsproblem identifieras visas en rekommendation här.
2.  Markera varje rekommendation om du vill se mer information och/eller vidta åtgärder för att lösa problemet.
![Rekommendationer i Azure Security Center][5]

Visa hälso- och säkerhetsstatus för dina resurser via **resursens säkerhetshälsa**:

1.  Gå tillbaka till bladet **Security Center**.
2.  Panelen för **resursens säkerhetshälsa** innehåller indikatorer gällande säkerhetsstatus för **virtuella datorer**, **nätverk**, **SQL**, och **program**.
3.  Välj **Virtuella datorer** för mer information.
4.  Bladet **Virtuella datorer** visar en statussammanfattning som anger status för program mot skadlig kod, systemuppdateringar, omstarter och baslinjeregler för de virtuella datorerna.
5.  Välj ett objekt under **REKOMMENDATIONER FÖR VIRTUELLA DATORER** om du vill visa mer information och/eller vidta åtgärder för att konfigurera nödvändiga kontroller.
6.  Sök för att visa ytterligare information för specifika virtuella datorer.
![Panelen för resursernas hälsotillstånd i Azure Security Center][6]

Hantera **säkerhetsaviseringar**:

1.  Gå tillbaka till bladet **Security Center** och välj panelen **Säkerhetsaviseringar**. En lista över aviseringar visas i bladet **Säkerhetsaviseringar** . Aviseringarna genereras av Security Centers analys av dina säkerhetsloggar och din nätverksaktivitet. Aviseringar från integrerade partnerlösningar ingår också.
![Säkerhetsaviseringar i Azure Security Center][7]

2.  Välj en avisering om du vill visa ytterligare information.
![Information om säkerhetsaviseringar i Azure Security Center][8]

Visa hälsotillstånd för dina **partnerlösningar**:

1. Gå tillbaka till bladet **Security Center**. I panelen **Partnerlösningar** kan du snabbt se hälsostatusen på de partnerlösningar som är integrerade med din Azure-prenumeration.
2. Klicka på panelen **Partnerlösningar**. Då öppnas ett blad med en lista över alla partnerlösningar som är anslutna till Security Center.
![Partnerlösningar][9]

3. Markera en partnerlösning. I det här exemplet väljer vi lösningen **F5-WAF2**.  Då öppnas ett blad som visar statusen för partnerlösningen och resurserna som är kopplade till lösningen. Om du klickar på **Lösningskonsol** öppnas partnerhanteringsfunktionen för lösningen i fråga.
![Information om partnerlösning][10]

## Nästa steg
I det här dokumentet har du introducerats för komponenterna säkerhetsövervakning och principhantering i Security Center. Läs mer i:

- [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper.
- [Hantera säkerhetsrekommendationer i  Azure Security Center](security-center-recommendations.md) – Här får du lära dig hur du kan skydda resurserna i Azure med hjälp av rekommendationer.
- [Säkerhetshälsoövervakning i Azure Security Center](security-center-monitoring.md) – Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
- [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
- [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Här får du lära dig hur övervakar dina partnerlösningars hälsostatus.
- [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här finns vanliga frågor om tjänsten.
- [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här kan du hitta de senaste nyheterna och aktuell information om säkerheten i Azure .

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-center.png
[4]: ./media/security-center-get-started/security-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/partner-solutions-detail.png



<!--HONumber=Jun16_HO2-->


