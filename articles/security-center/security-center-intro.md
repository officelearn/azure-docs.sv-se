---
title: Introduktion till Azure Security Center | Microsoft Docs
description: Här får du lära dig om Azure Security Center, de viktigaste funktionerna och hur Security Center fungerar.
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
# Introduktion till Azure Security Center
Här får du lära dig om Azure Security Center, de viktigaste funktionerna och hur Security Center fungerar.

> [!NOTE]
> Informationen i det här dokumentet rör förhandsversionen av Azure Security Center. I det här dokumentet beskrivs tjänsten genom en exempeldistribution.
> 
> 

## Vad är Azure Security Center?
 Med hjälp av Security Center kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla Azureprenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

## De viktigaste funktionerna
 Security Center innehåller lättanvända och effektiva funktioner som finns inbyggda i Azure och som kan användas för att förebygga, upptäcka och åtgärda hot. Här är de viktigaste funktionerna:

|  |  |
| --- | --- |
| Förebygga |Säkerhetsstatusen för resurserna i Azure övervakas. |
| Principer för prenumerationerna och resursgrupperna i Azure utarbetas utifrån företagets säkerhetskrav, vilken typ av program som används och hur känsliga företagets data är. | |
| Utifrån principerna ges säkerhetsrekommendationer som hjälper de ansvariga för en tjänst att utföra de åtgärder som behövs. | |
| Tjänster och redskap från Microsoft och partnerföretag kan snabbt distribueras. | |
| Upptäcka |Säkerhetsdata från resurserna i Azure, nätverket och partnerlösningarna, till exempel program mot skadlig kod och brandväggar, samlas in och analyseras automatiskt. |
| Global hotinformation från Microsofts produkter, Microsofts Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) och externa flöden används. | |
| Avancerad analysteknik, till exempel machine learning och beteendeanalys används. | |
| Åtgärda |Säkerhetsrelaterade incidenter/aviseringar prioritetsklassas och rapporteras. |
| Du får information om var angreppet kommer ifrån och vilka resurser som är drabbade. | |
| Du får förslag på hur du kan stoppa pågående angrepp och förebygga framtida angrepp. | |

## Introduktionsgenomgång
 Security Center öppnas från [Azure Portal](https://azure.microsoft.com/features/azure-portal/). [Logga in på portalen](https://portal.azure.com), klicka på **Browse (Bläddra)** och bläddra sedan ned till alternativet **Security Center** eller klicka på rutan **Security Center** om du tidigare har fäst den på instrumentpanelen i portalen.

![Säkerhetsrutan i Azure Portal][1]

I Security Center kan du ställa in säkerhetsprinciper, övervaka säkerhetskonfigurationer och se säkerhetsaviseringar.

### Säkerhetsprinciper
Du kan ställa in principer för dina prenumerationer och resursgrupper i Azure enligt ditt företags säkerhetskrav. Du kan även anpassa principerna efter de typer av program som används i företaget eller efter hur känsliga datauppgifterna är i de olika prenumerationerna. De resurser som används för utveckling eller tester behöver kanske en annan typ av säkerhetsinställningar än de som används för program i produktion. På samma sätt behövs det kanske en högre säkerhetsnivå för sådant som det finns lagar om, till exempel personuppgifter.

> [!NOTE]
> För att kunna ändra en säkerhetsprincip på prenumerations- eller resursgruppsnivå måste du ha rollen som ägare till prenumerationen eller som deltagare i den.
> 
> 

På bladet **Security Center** väljer du **Policy (Princip)**. Då får du upp en lista med dina prenumerationer och resursgrupper.   

![Security Center-bladet][2]

Om du klickar på en prenumeration på bladet **Security policy (Säkerhetsprincip)** får du upp information om principen som gäller för prenumerationen.

![Blad med säkerhetsprincip för prenumeration][3]

Genom funktionen **datainsamling** (se ovan) samlas data för säkerhetsprincipen in. Om du aktiverar den här funktionen händer följande:

* Alla kompatibla virtuella datorer genomsöks för säkerhetsövervakning och rekommendationer.
* Säkerhetshändelser samlas in för analys och hotidentifiering.

Under alternativet **Choose a storage account per region (Välj ett lagringskonto per region)** (se ovan) väljer du ett lagringskonto för var och en av de regioner där du har virtuella datorer, där alla data från de här datorerna ska lagras. Om du inte väljer något lagringskonto för en region skapas ett åt dig. Data som samlas in är logiskt isolerade från andra kunders data av säkerhetsskäl.

> [!NOTE]
> Datainsamling och valet av lagringskonto per region ställs in på prenumerationsnivå.
> 
> 

Under **Show recommendations for (Visa rekommendationer för)** (se ovan) väljer du de säkerhetskontroller som du vill ska övervakas och rekommenderas utifrån säkerhetsbehoven hos resurserna i prenumerationen.

Markera sedan en resursgrupp. Principinformationen visas då till höger.

![Blad med säkerhetsprincip för resursgrupp][4]

Under **Inheritance (Arv)** (se ovan) kan du ställa in resursgruppen som

* ärvd (standardinställning), vilket innebär att alla säkerhetsprinciper för den här resursgruppen ärvs från prenumerationsnivån
* unik, vilket innebär att säkerhetsprincipen är specifikt inställd för resursgruppen Då måste du göra inställningar under **Show recommendations for (Visa rekommendationer för)**.

> [!NOTE]
> Om en princip på prenumerationsnivå motsäger en princip på resursgruppsnivå är det principen på resursgruppsnivå som gäller i första hand.
> 
> 

### Säkerhetsrekommendationer
 Säkerhetstillståndet på resurserna i Azure analyseras i Security Center för upptäckt av eventuella säkerhetsrisker. Genom en lista med rekommendationer får du hjälp att ställa in de kontrollfunktioner som behövs. Exempel på rekommendationer:

* att installera program som kan hitta och ta bort skadlig kod
* att skapa nätverkssäkerhetsgrupper och regler för trafiken till virtuella datorer
* att installera brandväggar för webbaserade program för skydd mot angrepp riktade mot webbaserade program
* genomföra alla systemuppdateringar som fattas
* se till att operativsystemen är konfigurerade enligt rekommenderade baslinjer

Klicka på rutan **Recommendations (Rekommendationer)**. Då öppnas en lista med rekommendationer. Markera en rekommendation om du vill se mer information om den och/eller vidta åtgärder för att lösa problemet.

![Säkerhetsrekommendationer i Azure Security Center][5]

### Resurshälsa
Rutan **Resource security health (Resurssäkerhetshälsa)** ger en övergripande bild över säkerhetsläget i miljön uppdelat efter resurstyp, till exempel virtuella datorer, webbprogram med flera.   

Om du markerar en resurstyp i rutan **Resource security health (Resurssäkerhetshälsa)** visas mer information, bland annat en lista med eventuella säkerhetsrisker som har hittats. (I exemplet nedan har **Virtual machines (Virtuella datorer)** valts.)

![Rutan med resurshälsa][6]

### Säkerhetsaviseringar
 Loggdata från resurserna i Azure, nätverket och partnerlösningarna, till exempel program mot skadlig kod och brandväggar, samlas in, analyseras och integreras automatiskt i Security Center. Om hot upptäcks skapas en säkerhetsavisering. Exempel på hot:

* angripna virtuella datorer som kommunicerar med IP-adresser som man vet är skadliga
* avancerad skadlig kod upptäckt genom felrapporteringen i Windows
* nyckelsökningsangrepp mot virtuella datorer
* säkerhetsaviseringar från integrerade brandväggar och program mot skadlig kod

Om du klickar på rutan **Security alerts (Säkerhetsaviseringar)** visas en lista med prioritetsklassade aviseringar.

![Säkerhetsaviseringar][7]

Om du klickar på en avisering får du mer information om den och förslag på hur du kan lösa det aktuella problemet.

![Utförlig information om säkerhetsaviseringar][8]

### Partnerlösningar
I panelen **Partner solutions (Partnerlösningar)** kan du snabbt se hälsostatusen på de partnerlösningar som är integrerade i din Azure-prenumeration. Aviseringar från lösningarna visas i Security Center.

Klicka på rutan **Partner solutions (Partnerlösningar)**. Då öppnas ett blad med en lista över alla anslutna partnerlösningar.

![Partnerlösningar][9]

## Komma igång
För att komma igång med Security Center måste du ha en prenumeration på Microsoft Azure. Security Center aktiveras genom Azure-prenumerationen. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

 Security Center öppnas från [Azure Portal](https://azure.microsoft.com/features/azure-portal/). [Portaldokumentationen](https://azure.microsoft.com/documentation/services/azure-portal/) innehåller mer information.

I artikeln [Komma igång med Azure Security Center](security-center-get-started.md) beskrivs säkerhetsövervaknings- och principhanteringsfunktionerna i Security Center.

## Nästa steg
I det här dokumentet har du fått lära dig grunderna om Security Center, de viktigaste funktionerna och hur du kommer igång. Läs mer i:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i  Azure Security Center](security-center-recommendations.md): Här får du lära dig hur du kan skydda resurserna i Azure med hjälp av rekommendationer.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här kan du hitta de senaste nyheterna och aktuell information om säkerheten i Azure .

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png



<!--HONumber=Jun16_HO2-->
