<properties
   pageTitle="Datasäkerhet i Azure Security Center | Microsoft Azure"
   description="Det här dokumentet beskriver hur data hanteras och skyddas i Azure Security Center."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="yurid"/>


# Datasäkerhet i Azure Security Center
Azure Security Center hjälper kunder att förhindra, upptäcka och svara på hot genom att samla in och bearbeta data om dina Azure-resurser, inklusive konfigurationsinformation, metadata, händelseloggar, kraschdumpfiler och mer. Vi arbetar hårt för att skydda sekretessen och säkerheten för dessa data. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst. 

Den här artikeln beskriver hur data hanteras och skyddas i Azure Security Center.

## Datakällor
Azure Security Center analyserar data från följande källor:

- Azures tjänster: Läser information om konfigurationen av Azure-tjänster som du har distribuerat genom att kommunicera med tjänstens resursprovider.
- Nätverkstrafik: Läser in samplade metadata för nätverkstrafiken från Microsofts infrastruktur, till exempel käll- och mål IP-adress/port, paketstorlek och nätverksprotokoll.
- Partnerlösningar: Samlar in säkerhetsvarningar från integrerade partnerlösningar, till exempel brandväggar och lösningar mot skadlig kod. Dessa data lagras i Azure Security Center-lagring, som för närvarande finns i USA.
- Virtual Machines: Azure Security Center kan samla in information om konfigurationer och säkerhetshändelser, t.ex händelser och granskningsloggar i Windows, IIS-loggar, syslog-meddelanden och kraschdumpfiler från dina virtuella datorer med hjälp av datainsamlingsagenter. Mer information finns i avsnittet ”Hantera datainsamling” nedan.  

Dessutom lagras information om säkerhetsvarningar och rekommendationer samt status för hälsotillstånd i Azure Security Center-lagring, som för närvarande finns i USA. Den här informationen kan omfatta relaterad konfigurationsinformation och säkerhetshändelser som samlas in från dina virtuella datorer för att ge dig säkerhetsvarningar, rekommendationer eller säkerhetsrelaterad statusinformation.

## Dataskydd
**Datauppdelning**: Data lagras logiskt och separat på varje komponent i tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten. Data som samlas in från dina virtuella datorer lagras dessutom i dina lagringskonton.

**Dataåtkomst**: För att kunna ge säkerhetsrekommendationer och undersöka möjliga säkerhetshot kan Microsofts personal komma åt information som samlas in eller analyseras av Azure-tjänster, inklusive kraschdumpfiler. Kraschdumpfiler och processgenereringshändelser kan oavsiktligt innehålla kunddata eller personliga data från dina virtuella datorer. Vi följer [villkoren för Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) och tillhörande [sekretesspolicy](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), som garanterar att Microsoft inte använder kunddata eller härleder information från dem för reklamändamål eller i liknande kommersiellt syfte. Vi använder bara kunddata i den mån det är nödvändigt för att tillhandahålla Azure-tjänsterna, samt för därtill relaterade ändamål. Du äger alla rättigheter till dina kunddata.

**Dataanvändning**: Microsoft använder mönster och hotinformation som identifieras från flera klientorganisationer i syfte att förbättra våra skydds- och identifieringsfunktioner. Vi gör detta i enlighet med våra sekretessåtaganden som beskrivs i [sekretesspolicyn](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

**Dataplats**: Ett lagringskonto anges för varje region där virtuella datorer körs. Det gör att du kan lagra data i samma region som den virtuella dator som data samlas in från. Dessa data, inklusive kraschdumpfiler, lagras beständigt i ditt lagringskonto. Tjänsten lagrar även information om säkerhetsvarningar, inklusive aviseringar från integrerade partnerlösningar, rekommendationer och status för säkerhetstillstånd i Azure Security Center-lagring, som för närvarande finns i USA.

## Hantera datainsamling från Virtual Machines

När du väljer att aktivera Azure Security Center är datainsamling aktiverat för var och en av dina prenumerationer. Du kan stänga av datainsamling i avsnittet ”Säkerhetsprincip” på din Azure Security Center-instrumentpanel. När datainsamling är aktiverat etablerar Azure Security Center Azure Monitoring Agent på alla befintliga virtuella datorer som stöds och eventuella nya som skapas. Tillägget Azure Security Monitoring söker efter olika säkerhetsrelaterade konfigurationer och händelser med hjälp av [ETW-spårning (Event Tracing for Windows)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx). Dessutom utlöser operativsystemet händelselogghändelser när datorn körs. Exempel på sådana data är: operativsystemets typ och version, operativsystemloggar (Windows-händelseloggar), processer som körs, datornamn, IP-adresser, inloggad användare och klient-ID. Azure Monitoring Agent läser händelseloggposter och ETW-spårning och kopierar dem till ditt lagringskonto för analys. 

Ett lagringskonto definieras för varje region där du har virtuella datorer som körs, där data som samlas in från virtuella datorer i samma region lagras. På så sätt kan du behålla data inom samma geografiska område, vilket förenklar sekretess- och datalagstiftningshanteringen. Du kan konfigurera lagringskonton för varje region i avsnittet ”Säkerhetsprincip” på din instrumentpanel i Azure Security Center.

Azure Monitoring Agent kopierar också kraschdumpfiler till ditt lagringskonto.  Azure Security Center samlar in tillfälliga kopior av dina kraschdumpfiler och analyserar dem efter tecken på kryphål och säkerhetsintrång.  Azure Security Center utför den här analysen inom samma geografiska region som lagringskontot och tar bort de tillfälliga kopiorna när analysen är klar.

Du kan inaktivera datainsamling från virtuella datorer när som helst. Om du gör det tas alla övervakningsagenter som tidigare installerats av Azure Security Center bort.


## Nästa steg

I det här dokumentet har du lärt dig hur data hanteras och skyddas i Azure Security Center. Mer information om Azure Security Center finns här:

- [Planerings- och användningsguide för Azure Security Center](security-center-planning-and-operations-guide.md) – Lär dig mer om planering och viktiga designaspekter när du ska börja använda Azure Security Center.
- [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar dina Azure-resursers hälsa.
- [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsvarningar.
- [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
- [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten
- [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure



<!--HONumber=Sep16_HO3-->


