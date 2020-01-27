---
title: Fakturor för Azure Enterprise-registrering
description: Den här artikeln förklarar hur du hanterar och använder din Azure Enterprise-faktura.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 7cb2966c94485d0a05febf3085ab367fcba97434
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514365"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Fakturor för Azure Enterprise-registrering

Den här artikeln förklarar hur du hanterar och använder din Azure Enterprise-faktura. Din faktura är en representation av din räkning, så du bör granska den för att se om den är korrekt. Du bör även bekanta dig med andra uppgifter som kan behövas för hantering av din faktura.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Ändra ett IO-nummer för en överförbrukningsfaktura

Azure EA-portalen genererar automatiskt ett standardmässigt inköpsordernummer (IO) såvida inte EA-administratören anger ett före fakturadatumet. EA-administratörer kan uppdatera inköpsordernumret upp till sju dagar från och med att det automatiska fakturameddelandet tagits emot via e-post.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Så här uppdaterar du inköpsordernumret för Azure-tjänster:

1. I Azures EA-portalen klickar du på **Rapport** och sedan på **Sammanfattning av användning**.
1. Välj **Redigera IO-nummer** i det övre högra hörnet.
1. Välj alternativknappen **Azure-tjänster**.
1. Välj en **fakturaperiod** i listrutan med datumintervall. IO-nummer kan redigeras i upp till sju dagar från och med att fakturameddelandet mottagits eller fakturan har betalats, beroende på vad som kommer först.
1. Ange ett nytt IO-nummer i fältet  **IO-nummer** .
1. Klicka på  **Spara**  för att spara ändringarna.

### <a name="to-update-the-marketplace-purchase-order-number"></a>Så här uppdaterar du inköpsordernumret för Marketplace:

1. I Azures EA-portalen klickar du på **Rapport** och sedan på **Sammanfattning av användning**.
1. Välj **Redigera IO-nummer** i det övre högra hörnet
1. Välj alternativknappen **Marketplace**
1. Välj en **fakturaperiod** i listrutan med datumintervall. IO-nummer kan redigeras i upp till sju dagar från och med att fakturameddelandet mottagits eller fakturan har betalats, beroende på vad som kommer först.
1. Ange ett nytt IO-nummer i fältet  **IO-nummer** .
1. Klicka på  **Spara**  för att spara ändringarna.

## <a name="cadence-of-azure-ea-billing"></a>Faktureringsintervall för Azure EA

### <a name="billing-intervals"></a>Faktureringsintervall

Microsoft fakturerar årligen på registreringsdatumet köp som gjorts inom kundens åtagande för Microsoft Azure-tjänsterna och i efterskott för all användning som överstiger beloppen för åtagandet. Utfästelseavgifterna baseras på en månatlig avgift och faktureras per år i förväg. Avgifter för överförbrukning beräknas varje månad och faktureras i efterskott i slutet av faktureringsperioden.

Beroende på hur du väljer att göra dina åtagandeinköp sammanfaller ditt årliga åtagande antingen med årsdagen för registreringen eller giltighetsdatumet för din ettåriga tilläggsprenumeration.

Du får fakturan för överförbrukning beroende på startdatumet för registreringen och konfigurationen.

**Direkt registreringar med start datum den 1 maj 2018**: direkta Enterprise Azure-kunder (EA) ställs in som en årlig fakturerings period för Azure-tjänster (exklusive Marketplace-tjänster). Din faktureringsperiod baseras på årsdagen. Årsdagen är det datum då avtalet började gälla. För att få den första fakturan för överförbrukning av Azure-tjänsten måste du överskrida 150 procent av ditt tröskelvärde för betalningsåtagande.  När den totala tjänstförbrukningen överskrider 150 procent av tröskelvärdet för betalningsåtagande sker en automatisk omvandling till kvartalsvis fakturering baserat på årsdagen.  Om du inte överskrider 150 procent av tröskelvärdet kvarstår den årliga faktureringen och överförbrukningsfakturan erhålls i slutet av året för åtagandet.

**Direkt registreringar med start datum den 1 maj 2018**: kundens Azure-förbrukning och avgifter faktureras separat faktureras enligt en månatlig fakturerings period.  Alla avgifter som inte omfattas av Azure-betalningsåtagandet betalas som överförbrukning.  

**Indirekta registreringar med en registrering som påbörjats före den 1 maj 2018**: indirekta Enterprise Azure-kunder (EA) har kon figurer ATS som en kvartals fakturerings period.  Kanalpartner fakturerar kunden direkt.  

**Indirekta registreringar med start datum den 1 maj 2018**: eventuella indirekta avtal med ett start datum som är större än eller lika med den 1 maj 2018 faktureras varje månad.  

### <a name="increasing-commitment"></a>Öka åtagandet

Åtagandet kan när som helst ökas, och det faktureras för det antal månader som kvarstår för det årets åtagandeperiod. Om du till exempel registrerar dig för en ettårig tilläggsprenumeration och ökar åtagandet under månad sex, så faktureras du för de återstående sex månaderna i den perioden. Ditt åtagande uppdateras sedan för de sista sex månaderna i din åtagandeperiod för att fastställa eventuella överförbrukningsavgifter.

### <a name="overage"></a>Överanvändning

Du debiteras för överförbrukning för den användning eller de reservationer som överskrider ditt åtagande under faktureringsperioden. Om du vill veta hur överförbrukningen har beräknats för enskilda poster kan du se rapporten med en sammanfattning av användningen eller kontakta din kanalpartner.

För varje post på fakturan visas de totala avgifterna (beräknat belopp), mängden åtagande som använts för att täcka kostnaderna (utnyttjat åtagande) och beloppet för avgifter i överskridet åtagande (nettobelopp).  Tillämpliga skatter beräknas bara på det nettobelopp som överstiger åtagandet.

Faktureringen av överförbrukning sker automatiskt.  Tiden för meddelanden och fakturor beror på slutdatumet för kundens faktureringsperiod.  Ett meddelande om överförbrukning skickas vanligtvis sju dagar efter slutdatumet för kundens faktureringsperiod. Under den tiden kan kunder gå till portalen och granska kostnaderna och uppdatera systemgenererade inköpsordernummer (detta kan också uppdateras när som helst innan överförbrukningen släpps).  Fakturor för överförbrukning skickas sju till nio dagar efteråt.

### <a name="azure-marketplace"></a>Azure Marketplace

Från och med faktureringsperioden april 2019 kommer kunder att börja få en enskild Azure-faktura eftersom vi har slagit ihop Azure- och Azure Marketplace-debiteringarna i en enda faktura i stället för två separata fakturor. (Den här ändringen påverkar inte kunder i Australien, Japan eller Singapore.) Under över gången till en konsol IDE rad faktura får du en delvis marknads plats faktura. Den här slutliga separata fakturan visar Marketplace-avgifterna före datumet för din faktureringsuppdatering. Marketplace-avgifter efter detta datum kommer att visas på din Azure-faktura. Efter övergångsperioden visas alla Azure-och Marketplace-avgifter på den konsoliderade fakturan.  

### <a name="purchase-order-numbers"></a>Inköpsordernummer

Inköpsordernumren genereras som standard av systemet. Användare kan uppdatera sina inköpsordernummer i EA-portalen genom att logga in som företagsadministratör och navigera till avsnittet Rapporter. Det finns en ruta för inköpsordernummer i det övre högra hörnet av fönstret där företagsadministratörer kan redigera inköpsordernumret genom att klicka på pennikonen.

## <a name="adjust-billing-frequency"></a>Justera faktureringsfrekvensen

Faktureringsfrekvensen för en kund är årlig, kvartalsvis eller månatlig. Faktureringsperioden fastställs när en kund signerar sitt avtal. Månatlig fakturering är det kortaste faktureringsintervallet.

Godkännande från en företagsadministratör krävs för att ändra en faktureringscykel från årlig till kvartalsvis för direkta registreringar. Godkännande från en partneradministratör krävs för indirekta registreringar. Ändringen träder i kraft i slutet av det aktuella faktureringskvartalet.

För ändring av en faktureringsperiod från årlig eller kvartalsvis till månatlig krävs en ändring i avtalet.  Eventuella ändringar av den befintliga faktureringscykeln för registrering kräver godkännande från en företagsadministratör eller från den person som identifierats som _Faktureringskontaktperson_ i ditt avtal. Du kan skicka in ditt godkännande i [supporten för Azure EA-portalen](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) och sedan välja problemkategorin **Fakturering**.  Ändringen träder i kraft i slutet av det aktuella faktureringskvartalet.

Om en Amendment M503 (ändring) signeras kan du flytta valfritt avtal från valfri frekvens till månatlig fakturering. 

## <a name="credits-and-adjustments"></a>Krediter och justeringar

Alla krediter eller justeringar som tillämpas på din registrering visas på [https://ea.azure.com](https://ea.azure.com) i avsnittet **Rapporter**.

Så här visar du krediterna:

1. Välj avsnittet **Rapporter**.
1. Klicka på **Sammanfattning av användning**.
1. Ändra vyn _M_ till _C_ i det övre högra hörnet.
1. Utöka justeringsfältet i tabellen med det ekonomiska åtagandet för Azure-tjänsten.
1. På den här raden ser du de krediter som du har använt för registreringen samt en kort förklaring till exempel: Serviceavtal kredit.

## <a name="request-an-invoice-copy"></a>Begära en fakturakopia

Kontakta din partner om du vill begära en kopia av din faktura.

## <a name="overage-offset-by-customers"></a>Överförbrukningsförskjutning av kunder

Om kunden har överförbrukning som den vill använda med ekonomiskt åtagande måste följande kriterier uppfyllas:

- Kunden ska ha överförbrukningsavgifter som kunden har ådragit sig men inte har betalat, och som gäller inom ett år efter den fakturerade tjänstens slutdatum.
- Det tillgängliga ekonomiska åtagandet ska omfatta det fullständiga antalet gällande avgifter, inklusive alla tidigare ej betalda Azure-fakturor.
- Den faktureringsperiod som slutförande begärs för måste vara helt avslutad. Fakturering avslutas helt efter den femte dagen varje månad.
- Den faktureringsperiod som förskjutning begärs för måste vara helt avslutad.
- ACD-rabatt baseras på det faktiska nya åtagandet minus eventuella belopp som planeras för den tidigare förbrukningen. Detta krav gäller endast för aktuella överförbrukningsavgifter. Detta fungerar endast för tjänster som förbrukar ekonomiskt åtagande, och därför kan du inte täcka Marketplace-avgifter. Marketplace-avgifter faktureras separat.
- Om en kund vill slutföra en överförbrukningsförskjutning kan kunden skapa en supportbegäran. Eller så kan kontoteamet skapa supportbegäran. Slutförande av processen kräver godkännande via e-post från kundens EA-administratör eller faktureringskontaktpersonen.

## <a name="view-price-sheet-information"></a>Visa information om prisdokument

Företagsadministratörer kan visa den prislista som är associerad med deras registrering för Azure-tjänster.

Så här visar du aktuellt prisdokument:

1. I Azure EA-portalen klickar du på **Rapporter** och sedan på **Prisdokument**.
2. Visa prisdokumentet eller klicka på **Ladda ned**.

![Exempel som visar information om prisdokument](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Så här laddar du ned en historisk prislista:

1. I Azure EA-portalen klickar du på **Rapporter** och sedan på **Ladda ned användning**.
2. Ladda ned prisdokumentet.

![Exempel som visar var du laddar ned ett äldre pris dokument](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Om du har frågor om varför det finns en avvikelse i prissättningen kan det bero på följande:

Prissättningen kan ha ändrats mellan den tidigare registreringen och den nya registreringen. Prisändringar sker eftersom priser är avtalsenliga för en specifik registrering från startdatumet till slutdatumet för ett avtal. När en registrering överförs till en ny registrering följer den prissättningen för ett nytt avtal. Prissättningen definieras av kundens prisdokument. Priserna kan därför vara högre i den nya registreringen.

Om en registrering övergår till en längre period ändras även prissättningen. Priserna ändras till Betala per användning-priser.

## <a name="request-detailed-usage-information"></a>Begära detaljerad användningsinformation

Företagsadministratörer kan visa en sammanfattning av sina användningsdata, förbrukat ekonomiskt åtagande samt avgifter som är kopplade till ytterligare användning i Azure EA-portalen. Avgifterna presenteras på sammanfattningsnivå för alla konton och prenumerationer.

Om du vill visa detaljerad användning i specifika konton kan du ladda ned rapporten med användningsinformation genom att gå till **Rapporter** > **Ladda ned användning**. Rapporten inkluderar inga tillämpliga skatter. Det kan finnas en svarstid på upp till åtta timmar från den tidpunkt då användningen började gälla till dess att den återspeglas i rapporten.

För indirekta registreringar behöver din partner aktivera markeringsfunktionen innan du kan se kostnadsrelaterad information.

## <a name="reports"></a>Rapporter

Företagsadministratörer kan visa en sammanfattning av användningsdata, förbrukat betalningsåtagande samt avgifter som är kopplade till ytterligare användning i EA-portalen. Avgifterna presenteras på sammanfattningsnivå för alla konton och prenumerationer.

**EA-rapporter**

- Användningssammanfattning och diagram
- Rapport för tjänstanvändning
- Rapport för saldo och avgifter
- Rapport för användningsinformation
- Rapport för Marketplace-debiteringar
- Prisdokument
- Nedladdning av avancerad rapport
- Formatering av CSV-rapport

**Så här visar du rapporter och diagram för användningssammanfattning**

1. I Azure EA-portalen klickar du på **Rapporter** i det vänstra navigeringsfältet och visar fliken **Användningssammanfattning**.
1. Välj önskad åtagandeperiod i listrutan med datumintervall längst upp till vänster.
1. Välj önskad period eller månad i diagrammet om du vill visa mer information.
1. Visa diagram över användning månad för månad med en analys av utnyttjad användning, tjänstöverförbrukning, separat debiterade avgifter samt Marketplace-debiteringar.
1. För den valda månaden filtrerar du efter avdelningar, konton och prenumerationer nedanför diagrammet.
1. Växla uppdelning mellan Avgifter efter tjänster och Avgifter efter hierarki.
1. Visa Azure-tjänster, separat debiterade avgifter och Azure Marketplace-debiteringar i detalj.

## <a name="service-usage-report"></a>Rapport för tjänstanvändning

På sidan Rapport för tjänstanvändning kan företagsadministratörer visa en sammanfattning av tjänstanvändningsdata. Användningen visas på sammanfattningsnivå för alla konton och prenumerationer, men du kan också filtrera rapporten efter konto eller prenumeration om du vill visa användningen i detalj.

Observera att det också kan vara en fördröjning på upp till fem dagar från användningsdatumet tills användningen visas i rapporten.

### <a name="to-view-the-report"></a>Så här visar du rapporten:

1. Logga in på EA-portalen.
1. Klicka på **Rapporter** i det vänstra navigeringsfältet.
1. Klicka på fliken **Användningssammanfattning**.
1. Klicka på önskat datumintervall.
1. Välj vilka konton eller prenumerationer du vill visa.
1. Ändra vy från Avgifter efter tjänster eller Avgifter efter hierarki för att visa olika uppdelningar.
1. Visa information, till exempel tjänstnamn, måttenhet, förbrukade enheter, pris och utökad kostnad.

## <a name="download-csv-reports"></a>Ladda ned CSV-rapporter

På sidan för nedladdning av månadsrapport kan företagsadministratörer ladda ned flera rapporter som CSV-filer, till exempel en rapport för saldo och avgifter, en rapport för användningsinformation, en rapport för Marketplace-debiteringar och ett prisdokument.

## <a name="to-download-reports"></a>Så här laddar du ned rapporter:

1. Klicka på **Rapport** i Azure EA-portalen.
1. Välj **Usage Download** (Ladda ned användning) från det översta menyfliksområdet.
1. Välj **Hämta** intill månadens rapport.



## <a name="csv-report-formatting"></a>Formatering av CSV-rapport

Kunder som visar CSV-rapporter i Azure EA-portalen i euro kan få formateringsproblem med kommatecken och punkter.

Du kan till exempel visa:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Timmar | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Du bör se:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Timmar | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

### <a name="root-cause"></a>Rotorsak

Excel importerar alla fält som "allmän" text och förutsätter att ett tal skiljs åt i den matematiska standarden: "1 000,00".  En europeisk valuta som använder en punkt (.) som tusentalsavgränsare och ett kommatecken som decimaltecken (,), till exempel "1.000,00", visas därmed felaktigt. Detta kan variera beroende på dina nationella inställningar.

### <a name="when-importing-the-csv-use-the-following-steps"></a>Använd följande steg när du importerar CSV-filen:

1.    Öppna Excel och gå till Arkiv > Öppna.
1.    Guiden för att importera text visas.
1.    Välj _Avgränsade_ under Ursprunglig datatyp.  Standardvärdet är _Fast bredd_.
1.    Välj **Nästa**.
1.    Under Avgränsare markerar du kryssrutan för kommatecken. Standardvärdet är Tabb (avmarkera).
1.    Välj **Nästa**.
1.    Bläddra till kolumnerna ResourceRate och ExtendedCost.
1.    Välj kolumnen ResourceRate (den visas markerad i svart).
9.    Under avsnittet Kolumndataformat väljer du Text i stället för Allmänt.  Kolumnrubriken ändras från Allmänt till Text.
10. Upprepa steg 8 och 9 för kolumnen ExtendedCost. Välj **Slutför**.

Om du har kopplat Excel så att \*CSV-filer öppnas automatiskt måste du använda funktionen Öppna i Excel i stället. Öppna Excel och gå till Arkiv > Öppna.

## <a name="balance-and-charge-report"></a>Rapport för saldo och avgifter

Rapporten för saldo och avgifter tillhandahåller en månatlig informationssammanfattning om saldon, nya inköp, Azure Marketplace-tjänstavgifter, justeringar och överförbrukningsavgifter. Alla rader i sammanfattningstabellen för tjänståtagande i Azure visas statiskt månad för månad medan information om alla köp och justeringar visas i avsnitten New Purchase Details (Ny inköpsinformation) och Adjustement Details (Justeringsinformation).

### <a name="download-the-balance-and-charge-report"></a>Ladda ned rapporten för saldo och avgifter

1. Logga in i Azure EA-portalen som företagsadministratör.
1. Klicka på **Rapporter** i det vänstra navigeringsfältet.
1. Klicka på fliken **Report Download** (Ladda ned rapport).
1. Välj lämplig månad under kolumnen _Balance and Charge_ (Saldo och avgifter) och klicka för att ladda ned rapporten.

## <a name="usage-detail-report"></a>Rapport för användningsinformation

I rapporten med användningsinformation visas en månads en månadssammanfattning av de tjänster och kvantiteter som har förbrukats av en registrering, inklusive information om mätarnamn, mätartyper och förbrukade kvantiteter.

### <a name="download-the-usage-detail-report"></a>Ladda ned rapporten med användningsinformation

1. Logga in i Azure EA-portalen som företagsadministratör.
1. Klicka på **Rapporter** i det vänstra navigeringsfältet.
1. Klicka på fliken **Download Usage** (Ladda ned användning).
1. Välj lämplig månad under kolumnen _Usage Detail_ (Användningsinformation) och klicka för att ladda ned rapporten.

## <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Marketplace-debiteringar i rapporter på Azure EA-portalen

Ytterligare information om Marketplace-debiteringar hittar du [här](https://azure.microsoft.com/marketplace/faq/).

Det finns två typer av Marketplace-debiteringar:

- **Användning-baserad:** Produkter som mäts i transaktions enheter.  Till exempel debiteras virtuella datorer per timme och Bing API-sökningar debiteras per antal sökningar.
- **Månads avgift:** Faktureras per månad baserat på användning/åtkomst.

Så här visar du olika debiteringar i EA-portalen:

1. **Sammanfattningsrapporten för användning –** visar **både** användningsbaserad debitering och månadsavgift.
1. **Rapport för Marketplace-debiteringar –** visar **endast** användningsbaserade Marketplace-debiteringar.  Engångsavgifter visas inte.

Observera att Azure Marketplace inte är tillgängligt för MPSA-registreringar.

## <a name="advanced-report-download"></a>Nedladdning av avancerad rapport

Om du vill skapa rapporter för specifika datumintervall eller konton kan du välja nedladdning av avancerad rapport. Från och med 30 augusti 2016 ändras formatet på utdatafilen från .xlsx till .csv för att hantera större uppsättningar av poster.

1. Välj **Advanced Report Download** (Nedladdning av avancerad rapport).
1. Välj **Appropriate Date Range** (Lämpligt datumintervall).
1. Välj **Appropriate Accounts** (Lämpliga konton).
1. Välj **Request Usage Data** (Begär användningsdata).
1. Välj **uppdateringsknappen** tills rapportens status uppdateras till Ladda ned.
1. Ladda ned rapporten.

## <a name="reporting-for-non-enterprise-administrators"></a>Rapportering för icke-företagsadministratörer

Företagsadministratörer kan aktivera åtkomst för att visa kostnader för avdelningsadministratörer (DA) och kontoägare (AO) för en registrering. När det har aktiverat kan en kontoägare ladda ned CSV-rapporter som är specifika för dennes konto och prenumerationer och visa information under rapportavsnittet i EA-portalen.

### <a name="to-enable-access"></a>Så här aktiverar du åtkomst:

 1. Logga in som företagsadministratör.
 1. Klicka på **Hantera** i det vänstra navigeringsfältet.
 1. Klicka på fliken **Registrering**.
 1. Under avsnittet med registreringsinformation väljer du pennikonen bredvid:
    - Visa avgifter för DA
    - Visa avgifter för AO
 1. Välj **Aktiverad**.
 1. Klicka på **Spara**.

### <a name="to-view-reports"></a>Så här visar du rapporter:

1. Logga in på Azure EA-portalen som avdelningsadministratör eller kontoägare.
1. Klicka på **Rapporter** i det vänstra navigeringsfältet.
1. Klicka på fliken **Användningssammanfattning** för att visa information om kontot och prenumerationer.
1. Klicka på **Usage Download** (Ladda ned användning) för att visa CSV-rapporter.

Avdelningsadministratörer och kontoägare kan inte visa avgifter när du använder funktionen _Advanced Report Download_ (Nedladdning av avancerad rapport). Kostnaderna visas som 0 USD.

Visa avgifter för AO utökas för alla kontoägare och alla användare som har behörighet på associerade prenumerationer. Om du är en indirekt kund måste din kanalpartner aktivera kostnadsfunktioner.

## <a name="move-usage-data-to-another-enrollment"></a>Flytta användningsdata till en annan registrering

Användningsdata flyttas bara när en överföring antedateras. Det finns två alternativ för att flytta användningsdata från en registrering till en annan:

- Kontoöverföringar från en registrering till en annan registrering
- Registreringsöverföringar från en registrering till en annan registrering

För endera alternativet måste du skicka en [supportbegäran](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) till EA-supportteamet för att få hjälp. 

## <a name="azure-ea-pricing-overview"></a>Prisöversikt för Azure EA

Det här dokumentet innehåller information om hur användningen beräknas och ger svar på många vanliga frågor om priser för olika Azure-tjänster i Enterprise-program där beräkningarna är mer komplexa.

### <a name="price-protection"></a>Prisskydd

Kunder och kanalpartner (om våra indirekta kanaler används) är garanterade priserna som anges i kundprislistan (CPS) eller lägre eller det pris som gäller vid datumet för Azure-köpet. Det här priset kallas för baspriset. För tjänster som har införts efter köpet är det priset som gäller för rabatten på den aktuella nivån när tjänsten först introducerades. Det här prisskyddet gäller för åtagandeperioden, som kan vara ett eller tre år beroende på Enterprise-programmet.

### <a name="price-changes"></a>Prisändringar

Microsoft kan sänka det aktuella priset för Enterprise-programmet för enskilda Azure-tjänster under en registreringsperiod.  Vi utökar dessa reducerade priser till befintliga kunder medan det lägre priset gäller.  Om dessa priser ökas senare ökar inte registreringens pris för en tjänst över kundens prisskyddstak enligt definitionen ovan, men priset kan öka i förhållande till det tidigare sänkta priset.  I båda fallen informerar Microsoft kunder och indirekta kanalpartner om kommande prisändringar via e-post till de företags- och partneradministratörer som är kopplade till registreringen.

### <a name="current-effective-pricing"></a>Aktuella gällande priser

Kunder och kanalpartner kan visa det aktuella priset för en registrering genom att logga in på [Azure EA-portalen](https://ea.azure.com/) och gå till prisdokumentsidan för registreringen.  Om du köper Azure indirekt via någon av våra kanalpartner måste du få dina prisuppdateringar från kanalpartnern, såvida de inte har aktiverat visning av påläggpris för din registrering.

### <a name="introduction-of-new-azure-services"></a>Introduktion till nya Azure-tjänster

Vi förbättrar ständigt Azure och lägger regelbundet till nya tjänster som prissätts separat från befintliga tjänster.  Vissa förhandsversioner av tjänster är tillgängliga automatiskt medan andra kräver kundåtgärder via [Azure-kontoportalen](https://account.windowsazure.com/PreviewFeatures). Observera också att när tjänster flyttas från förhandsversionen till den allmänt tillgängliga versionen kan priserna öka när SLA för full prestanda och tillförlitlighet tillämpas. Slutligen tillhandahålls vissa tjänster till kampanjpris när de först lanseras och det priset kan ökas vid ett senare datum. De ökningar som uppstår vid en övergång från förhandsversionspriser eller kampanjpriser till priserna för allmän tillgänglighet är inte begränsade av det vanliga basprisskyddet och gäller fortsättningsvis för användningen av dessa tjänster. Kunder kan undvika sådana kostnader för dessa tjänster genom att välja att inte använda den nya tjänsten.

### <a name="introduction-of-regional-pricing"></a>Introduktion till regional prissättning

Utöver införandet av nya tjänster ändras även tjänster regelbundet från en enda global grund till en mer regional modell när det regionala stödet ökar för dessa tjänster. När regionaliseringen för en tjänst först införs gäller prisskyddet för dessa nya regioner enligt den tidigare globala priset som gäller för registreringen. Men när nya regioner införs vid en senare tidpunkt för samma tjänst anses dessa tjänster som nya tjänster och erbjuds till egna priser som är oberoende av basprisskyddet.

### <a name="enterprise-msdn-devtest"></a>Utveckling och testning i MSDN för Enterprise

Företagsadministratörer kan låta kontoägare skapa prenumerationer baserat på erbjudandet för utveckling och testning i EA MSDN. För att detta ska fungera korrekt måste kontoägaren konfigurera de prenumerationer för utveckling och testning av EA MSDN som krävs för de underliggande MSDN-prenumeranterna. Detta gör att aktiva MSDN-prenumeranter kan köra arbetsbelastningar för utveckling och testning i Azure till specialpris. Mer information finns i [Utveckling och testning i EA MSDN](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="enterprise-program-usage-calculation-guidelines"></a>Riktlinjer för beräkning av användning av Enterprise-program

Grundläggande information om offentliga priser, måttenheter, vanliga frågor och svar och riktlinjer för användningsrapportering för varje enskild tjänst finns i [Azure-tjänster](https://azure.microsoft.com/services/) och [Azure-priser](https://azure.microsoft.com/pricing/). Vid beräkning av tjänstanvändning bör följande riktlinjer för Enterprise-program användas.

### <a name="enterprise-program-units-of-measure"></a>Måttenhet för Enterprise-program

Enheten för Enterprise-program skiljer sig ofta från andra program som prenumerationsavtalet för Microsoft Online (MOSA). Det innebär att för ett antal tjänster aggregeras måttenheten för att ge det normaliserade priset. Den måttenhet som visas i användningssammanfattningsvyn i EA-portalen är alltid Enterprise-måttet. En fullständig lista över aktuella måttenheter och omvandlingar för varje tjänst finns i [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Avrundningsregler

Vid avrundning i EA-portalen används IEEE-standard med avrundning till närmaste jämna tal (så kallad ”Banker’s Rounding” eller ”Gaussian Rounding”). Avrundningen sker till närmsta jämna tal för halvtal medan vanligare avrundningsfunktioner alltid avrundar halvtal uppåt till nästa högsta jämna tal. Den här metoden ger faktiskt en mer exakt totalsumma över gruppen i jämförelse med standardlogiken i Excel.

När det första decimaltalet är 5 och det inte finns några efterföljande tal eller om de efterföljande talen är nollor blir den framförvarande siffran jämn (d.v.s. avrundning till det närmaste jämna talet). Exempel: Både 2,315 och 2,325 blir 2,32 när de avrundas till närmaste hundradel.

När du använder Excel för att modellera avrundnings- och konverteringsregler som används i EA-portalen bör du använda de MROUND-formler som visas nedan.

| Scenario | Formellogik (banker) |
| --- | --- |
| Användning av avrundning | =MROUND({_källa_}, 0.0002) |
| Avrundning av priser (2 decimaler) | =MROUND({_källa_}, 0.02) |
| Avrundning av priser (0 decimaler) | =MROUND({_källa_}, 2) |

**Tabell** **2** **– konvertering av timmar för molntjänst och virtuell dator**

### <a name="conversion-between-download-usage-file-and-usage-summary-portal-view"></a>Konvertering mellan den nedladdningsbara filen för användning och vyn som visar en sammanfattning av användningen i portalen

Rådata för resursanvändning rapporteras upp till maximalt sex decimaler i den nedladdningsbara rapporten för användningsdata. Men Azure Enterprise Portal avrundar användningen till fyra decimaler för åtagandeenheter och trunkerar till noll decimaler för överförbrukningsenheter. Den råa användningen avrundas först till fyra decimaler före konverteringen till Enterprise-måttenhet och resulterande Enterprise-enheter avrundas igen till fyra decimaler. De faktiska förbrukade timmarna före konverteringen visas endast i den nedladdningsbara rapporten för användning och inte i själva användargränssnittet.

Anta att 694.533404 är det faktiska antalet SQL Server-timmar som rapporteras i den nedladdningsbara rapporten för användning. Dessa enheter konverteras sedan till 6.94533404 enheter för 100 beräkningstimmar, som sedan avrundas till 6.9453 som visas i EA-portalen.

Dessa enheter multipliceras sedan med enhetspriset för åtagandet och resultatet trunkeras till två decimaler för att fastställa det beräknade faktureringsbeloppet. För japanska yen (JPY) och koreanska won (Won) avrundas det beräknade beloppet till noll decimaler.

Om vi använder samma exempel för överförbrukning skulle de fakturerbara enheterna trunkeras till sex och sedan multipliceras med enhetspriset för överförbrukning för att fastställa det beräknade faktureringsbeloppet. För fakturering av hanterad tjänstleverantör (MSP) trunkeras all användning som är kopplad till en avdelning som är markerad som MSP till noll decimaler efter konvertering till EA-måttenheten som för rapportering av överförbrukning. Detta innebär att summan av den här användningen kan bli lägre än totalsumman av all användning som rapporteras i användargränssnittet beroende på om MSP fortfarande är inom saldot för betalningsåtagande eller redan räknas som överförbrukning.

### <a name="graduated-pricing"></a>Graderat pris

Priset för Enterprise-program har för närvarande inte graderat pris (där priset per enhet minskar när användningen ökar). När du byter från ett MOSA-program med graderat pris till ett Enterprise-program sätts priset i proportion till basnivån för den tjänsten, efter justering för rabatter för Enterprise-programmet (om sådana finns).

### <a name="partial-hour-billing"></a>Debitering per deltimme

All användning debiteras baserat på minuter som konverteras till deltimmar i stället för att räknas upp till heltimmar.  Enterprise-priser som anges som timpris motsvarar det totala antalet timmar inklusive eventuella deltimmar.

### <a name="average-daily-consumption"></a>Genomsnittlig daglig förbrukning

När en tjänst prissätts månadsvis men användningen rapporteras dagligen beräknas användningen en gång per dag dividerat med 31 och räknas samman för antalet dagar i den aktuella faktureringsmånaden. Detta resulterar i ett pris som aldrig är högre än förväntat under en månad och som är något lägre för de månader som har mindre än 31 dagar.

### <a name="compute-hours-conversion"></a>Konvertering av beräkningstimmar

Tidigare angavs all användning för virtuella A0-, A2-, A3- och A4- Standard-och Basic-datorer och molntjänster som bråkdelar (för A0) eller multipler (för A2, A3 och A4) av mätarminuter för virtuella A1-datorer. Detta ledde till viss förvirring för våra kunder, så vi har infört en ändring för att tilldela användning per minut till dedikerade A0-, A2-, A3- och A4-mätare.

Den nya mätningen började gälla mellan 27 och 28 januari 2016. I. csv-nedladdning för distributionen under den här över gångs perioden kommer du att märka två rad objekt: en för användning som har utsatts på a1-mätaren, och en annan för användning som släpps på den nya dedikerade mätaren som motsvarar din distributions storlek.

| **Distributionsstorlek** | **Användning anges som en multipel av A1 till och med 26 januari 2016** | **Användning anges på dedikerad mätare från och med 27 januari 2016** |
| --- | --- | --- |
| A0 | 0,25 av A1-timme | 1 av A0-timme |
| A2 | 2 av A1-timme | 1 av A2-timme |
| A3 | 4 av A1-timme | 1 av A3-timme |
| A4 | 8 av A1-timme | 1 av A4-timme |

### <a name="regions"></a>Regioner

För de tjänster där zon och region påverkar prissättningen visar följande tabell mappningen för geografiska områden och regioner:

| Geografi | Dataöverföringsregioner | CDN-regioner |
| --- | --- | --- |
| Zon 1 | Nordeuropa <br> Västeuropa <br> USA, västra <br> USA, östra <br> USA, norra centrala <br> USA, södra centrala <br> USA, östra 2 <br> USA, centrala | Nordamerika <br> Europa |
| Zon 2 | Östra Asien och stillahavsområdet <br> Sydöstra Asien och stillahavsområdet <br> Japan, östra <br> Japan, västra <br> Australien, östra <br> Australien, sydöstra | Asien och stillahavsområdet <br> Japan <br> Latinamerika <br> Mellanöstern/Afrika <br> Australien, östra <br> Australien, sydöstra |
| Zon 3 | Brasilien, södra |   |

**Tabell** **4** **– dataöverföringsregioner**

Det finns inga kostnader för utgående data mellan tjänster (till exempel O365 och Azure) i samma datacenter.

### <a name="monetary-commitment-and-unbilled-usage"></a>Ekonomiskt åtagande och ej fakturerad användning

Ekonomiskt åtagande för Azure är ett belopp som förskottsbetalats för Azure-tjänster. Det ekonomiska åtagandet förbrukas allt eftersom tjänster används. Azure-tjänster från första part använder det ekonomiska åtagandet. Det finns dock undantag för avgifter som debiteras separat samt Marketplace-tjänster.

### <a name="charges-billed-separately"></a>Avgifter som debiteras separat

Vissa produkter och tjänster från tredjepartskällor använder inte Azures betalningsåtagande. Dessa artiklar faktureras i stället separat som en del av fakturan för överförbrukning i standardfaktureringsperioden.

Vi har slagit samman alla avgifter för Azure och Marketplace till en enda faktura som sammanfaller med registreringens faktureringsperiod. (Detta gäller inte kunder i Australien, Japan eller Singapore.)

På den konsoliderade fakturan visas först Azure-användning, följt av eventuella Marketplace-avgifter. Kunder i Australien, Japan eller Singapore kommer att fortsätta att få Marketplace-avgifterna på en separat faktura.

Om det inte finns någon överförbrukning i slutet av standardfaktureringsperioden görs en separat fakturering av de avgifter som debiteras separat. (Gäller kunder i Australien, Japan och Singapore.)

**Tjänster som debiteras separat:**

- Canonical
- Citrix XenApp Essentials
- Registrerad användare för Citrix XenDesktop
- Openlogic
- Registrerad användare för Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (månatligen)
- Visual Studio Enterprise (årsvis)
- Visual Studio Professional (månatligen)
- Visual Studio Professional (årsvis)

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace för EA-kunder

För direktkunder visas Marketplace-avgifter i Azures EA-portalen. Marketplace-köp och -förbrukning debiteras utanför betalningsåtagandet och debiteras kvartalsvis/månadsvis och i efterskott.

Indirekta kunder kan se sina Azure Marketplace-prenumerationer på sidan Hantera prenumerationer i Azure EA-portalen, men priserna är dolda. Kunderna bör kontakta sin LSP för information om Marketplace-avgifter.

Nya månatliga eller årligen återkommande Marketplace-köp debiteras hela beloppet inom den period då Marketplace-objekten köptes. Dessa objekt förnyas automatiskt i nästa period på samma dag då det ursprungliga köpet gjordes.

Detta påverkar inte befintliga återkommande Marketplace-avgifter. Återkommande månadsavgifter fortsätter att förnyas den första i varje kalendermånad, och årsavgifter förnyas på årsdagen för inköpet.

Vissa tjänster från tredjepartsåterförsäljare på Azure Marketplace ingår nu i EA-betalningsåtagandet och påverkar saldot. Tidigare fakturerades dessa tjänster separat och ingick inte i EA-betalningsåtagandet. EA-betalningsåtagande för dessa tjänster på Marketplace förenklar hanteringen av köp och betalningar från kunder. En fullständig lista över tjänster som ingår i betalningsåtagandet finns på [Azure-webbplatsen](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/?WT.mc_id=azurebg_email_Trans_33771_1695_Release_Direct_Tier2_March14).

### <a name="partners"></a>Partner

LSP:er kan ladda ned en Marketplace-specifik prislista genom att öppna prisdokumentet på Azure EA-portalen och klicka på länken för **Marketplace-prislistan** uppe i det högra hörnet. Marketplace-prislistan visar tillgängliga tjänster och motsvarande priser.

**Så här hämtar du prislistan:**

1. Gå till Rapporter > Prisdokument.
1. Leta reda på länken Azure Marketplace-prislistan under ditt användarnamn i det övre högra hörnet.
1. Högerklicka på länken och välj **Spara mål som**.
1. I fönstret Spara ändrar du dokumentets titel till _AzureMarketplacePricelist.zip_ så att filen ändras från en XLSX-fil till en ZIP-fil.
1. När hämtningen är klar har du en ZIP-fil med landsspecifika prislistor.
1. LSP:er kan se priser för respektive land i motsvarande fil. På fliken Notifications (Meddelanden) kan LSP:er se SKU:er som är nya för Marketplace och SKU:er som tagits bort.
1. Prisändringar är ovanliga, men när de inträffar meddelas LSP:er via e-post 30 dagar i förväg. Det gäller till exempel prisökningar och valutakursändringar.
1. LSP:er får en faktura per registrering, ISV och kvartal.

### <a name="enabling-marketplace-purchases"></a>Aktivera Marketplace-inköp

Företagsadministratörer kan inaktivera eller aktivera Marketplace-inköp för alla Azure-prenumerationer som ingår i registreringen. Om företagsadministratören inaktiverar köp och det finns Azure-prenumerationer som redan har Marketplace-prenumerationer, avbryts eller påverkas inte dessa Marketplace-prenumerationer.

Även om kunderna kan konvertera sina Azure-direktprenumerationer till EA genom att koppla dem till registreringen i Azure EA-portalen konverteras inte de underordnade Marketplace-prenumerationerna automatiskt med den åtgärden.

**Så här aktiverar du Marketplace-inköp:**

1. Logga in i Azure EA-portalen som företagsadministratör.
1. Navigera till _Hantera_.
1. Under _Enrollment Detail_ (Registreringsinformation) klickar du på pennikonen bredvid _Azure Marketplace_.
1. Växla _Aktiverad/inaktiverad_ eller _Endast kostnadsfria/BYOL SKU:er\*_ efter behov.
1. Klicka på **Spara**.

### <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Marketplace-debiteringar i rapporter på Azure EA-portalen

Ytterligare information om Marketplace-debiteringar hittar du [här](https://azure.microsoft.com/marketplace/faq/).

Det finns två typer av Marketplace-debiteringar:

- **Användning baserad:** Produkter mäts i transaktions enheter.  Till exempel debiteras virtuella datorer per timme och Bing API-sökningar debiteras per antal sökningar.
- **Ej användning baserad:** En engångs avgift eller återkommande månads avgift som är oberoende av användningen.

Både användningsbaserade och ej användningsbaserade avgifter samlas in i Marketplace-avgiftsrapporten.

Observera att Azure Marketplace inte är tillgängligt för MPSA-registreringar.

\*BYOL (Bring your own license) och alternativet för endast kostnadsfritt skulle begränsa inköp och förvärv av Azure Marketplace-SKU:er till endast BYOL och kostnadsfria SKU:er.

### <a name="services-billed-hourly-for-ea"></a>Tjänster debiteras per timme för EA

Application Delivery Network och brandväggen för webbaserade program faktureras per timme för EA jämfört med per månad i MOSP.

### <a name="remote-app"></a>Remote App

EA-kunder betalar för Remote App baserat på deras EA-prisnivå och debiteras inte extra. Standardprisnivån innefattar de första 40 timmarna och den obegränsade prisnivån innefattar de första 80 timmarna. Remote App slutar generera användning efter dessa 80 timmar.

## <a name="azure-marketplace-faq"></a>Vanliga frågor och svar om Azure Marketplace

Det här dokumentet med vanliga frågor och svar visar uppdateringar av Azure-betalningsåtagandets tillämpning för vissa tredje parter som har publicerat tjänster på Azure Marketplace.

### <a name="what-are-we-changing-with-respect-to-marketplace-services-and-azure-monetary-commitment"></a>Vad har vi ändrat när det gäller Marketplace-tjänster och Azure-betalningsåtagandet?

Med undantag för Azure reserved VM instances får kunderna en separat faktura för alla tjänster som de köper från Azure Marketplace. Vi utökar användningen av Azure-betalningsåtagandet till att nu omfatta några av våra kunders mest köpta Azure Marketplace-tjänster som publicerats av tredje part.

### <a name="why-are-we-making-this-change"></a>Varför gör vi den här ändringen?

Kunder letar ständigt efter nya sätt att dra nytta av förskottsbetalningen som de har gjort i form av Azure-betalningsåtagandet.  Vi uppfyller en vanlig förfrågan som påverkar en stor del av våra Azure Marketplace-kunder genom att utöka betalningsåtagandet till dessa nya tjänster.

### <a name="what-is-the-customer-benefit"></a>Vad innebär det för fördel för kunderna?

Kunderna får en enklare faktureringsupplevelse och kan se till att de utnyttjar sitt Azure-betalningsåtagande.  Tillägget av den här fördelen i det förbetalda betalningsåtagandet och Azure reserved VM instances skapar ytterligare värde för Azure-betalningsåtagandet.

### <a name="what-services-will-deduct-from-azure-monetary-commitment-and-how-will-my-customer-know"></a>Vilka tjänster dras av från Azure-betalningsåtagandet och hur får mina kunder reda på det?

Under köpupplevelsen på Azure Marketplace särskiljer vi varje tjänst som använder betalningsåtagande med en ansvarsfriskrivning. De utgivare som stöds för närvarande innefattar vissa tjänster som publiceras av Red Hat, SUSE, Autodesk och Oracle. Tjänster som har liknande namngivningskonventioner men som publiceras av andra parter (som inte anges ovan), dras inte av från betalningsåtagandet. En fullständig lista finns i slutet av dessa vanliga frågor och svar.

### <a name="what-if-my-customer-runs-out-of-monetary-commitment"></a>Vad händer om betalningsåtagandet tar slut för min kund?

När kunderna har förbrukat betalningsåtagandet övergår användningen till överförbrukning, och avgifterna för dessa tjänster visas på kundens nästa överförbrukningsfaktura tillsammans med andra förbrukningstjänster.  Detta är en förändring. Tidigare fakturerades dessa avgifter på en egen faktura med andra Azure Marketplace-erbjudanden.

### <a name="why-are-we-not-enabling-azure-monetary-commitment-for-all-marketplace-purchases"></a>Varför används inte Azure-betalningsåtagande för alla Marketplace-köp?

Vi arbetar ständigt med att skapa den bästa kundupplevelsen för Azure-betalningsåtagande. Den här ändringen påverkar ett stort antal kunder och en stor del av de totala köpen i Azure Marketplace. Andra tjänster kan läggas till i framtiden.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Hur påverkar detta indirekt registrering och partner?

Det påverkar inte vår indirekta registrering för kunder eller partner. Dessa tjänster har samma påläggsmöjligheter för partner som andra konsumtionstjänster. Den enda ändringen blir fakturan de visas på och betalningen för avgifter från betalningsåtagande.

### <a name="list-of-services-that-will-deduct-from-azure-monetary-commitment"></a>Lista över tjänster som dras av från Azure-betalningsåtagandet

Specifika Azure Marketplace-erbjudanden kan använda betalningsåtagandemedel. Se [Azure-betalningsåtagande](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) för en fullständig lista över produkter som medverkar i det här programmet.

## <a name="additional-information"></a>Ytterligare information

Mer information finns i dessa ytterligare informationskällor. De här filerna uppdateras två gånger i månaden, den 6:e och 20:e i varje månad. Information om de olika filerna:

| Titel på bilaga | Beskrivning | Namngivningskonvention för URL |
| --- | --- | --- |
| [**Friendly Service Names**](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Innehåller en lista över alla aktiva tjänster med tjänstkategorin, friendly service name, åtagandenamn och artikelnummer, förbrukningsnamn och artikelnummer, måttenheter och konverteringsfaktorer mellan rapporterad användning och visad användning i EA-portalen. | Friendly\_Service\_Names.xlsx |
| [**Service Download Fields**](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Det här kalkylbladet innehåller en lista över alla möjliga kombinationer av tjänsterelaterade fält i den nedladdningsbara användningsrapporten. | Service\_Download\_Fields.xlsx |

**Tabell** **5** **– ytterligare informationskällor**

## <a name="power-bi-reporting"></a>Power BI-rapportering

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro är nu tillgängligt för EA-kunder. Med Power BI Pro kan du skapa och dela rapporter för att effektivt hantera dina kostnadsdata, med ytterligare funktioner för samarbete och uppdatering av data. Power BI Pro erbjuder högre gränser för datakapacitet och dataströmning. Snart kommer nya spännande kostnadshanteringsfunktioner för Azure Enterprise-kunder.

Nuvarande användare av den kostnadsfria versionen av Power BI som använder Microsoft Azure Consumption Insights-innehållspaketet är berättigade till en 60-dagars kostnadsfri utvärderingsversion av Power BI Pro. Om du vill fortsätta använda Power BI Pro efter den kostnadsfria utvärderingen kan du göra det genom att lägga till en licens.

Registrera dig för den kostnadsfria utvärderingsversionen genom att gå till kugghjulsikonen och välja **Hantera personlig lagring**. Välj sedan **Testa Pro gratis** till höger. Se [Självbetjäningsregistrering för Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) för mer information om den kostnadsfria utvärderingsversionen av Power BI Pro.

### <a name="microsoft-azure-ea-power-bi-pro-trial-terms"></a>Microsoft Azure EA – villkor för utvärderingsversionen av Power BI Pro

- **Generell användning**: den utökade Power BI Pro för utvärderings versionen av innehålls paketet "Microsoft Azure Enterprise" ("Erbjudandet") är tillgänglig för befintliga kvalificerade användare under erbjudandet, så att de får åtkomst till vissa insikter som rör sin Microsoft Azure förbrukning genom att använda ett specifikt Power BI innehålls paket.
- **Berättigande**: användare under en Enterprise-avtal (EA) kan delta i erbjudandet om de har en funktion som är relaterad till organisationens Microsoft Azure fakturering, tjänst, tjänst och/eller kostnads hantering.
- **Undantag**:
  - Användare som redan deltar i den utökade utvärderingsversionen av Power BI Pro är fortfarande kvalificerade enligt det tidigare erbjudandet, men kan inte utnyttja Azure EA-erbjudandet för utvärderingsversionen av Power BI Pro.
  - Användare som utnyttjar erbjudandet kan bara använda Power BI Pro med Microsoft Azure Enterprise-innehållspaketet. All annan användning av Power BI Pro är förbjuden.
  - Period: Erbjudandet börjar den 1 juni 2017 och slutar den 31 maj 2018.  Det går att godkänna när som helst under 12-månadersperioden, men erbjudandet upphör den 31 maj 2018 för alla användare oavsett när de godkände Erbjudandet.

### <a name="to-access-the-microsoft-azure-consumption-insights-content-pack"></a>Så här kommer du åt Microsoft Azure Consumption Insights-innehållspaketet:

1. Navigera till [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Klicka på **Get It Now** (Hämta nu).
1. Ange registreringsnummer och antal månader. Klicka på **Next**.
1. Ange din API-åtkomstnyckel för att ansluta. Du hittar nyckeln för registreringen i [EA-portalen](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Klicka på **Logga in**.
1. Importen startar automatiskt. När den är klar visas en ny instrumentpanel, rapport och modell i navigeringsfönstret. Klicka på instrumentpanelen för att visa dina importerade data.

Mer information om hur du skapar API-nyckeln för din registrering finns i hjälpfilen för API-rapporter i [EA-portalen](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Mer information om det nya innehålls paketet finns i [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Så här kommer du åt det tidigare Power BI EA-innehållspaketet:

 1. Navigera till [Power BI-webbplats](https://app.powerbi.com/getdata/services/azure-enterprise).
 1. Logga in med ett giltigt arbets- eller skolkonto.
    - Arbets- eller skolkontot kan vara detsamma eller ett annat än det som används för att komma åt registreringen via Azure EA-portalen.
 1. Välj **Microsoft Azure Enterprise** på instrumentpanelen för tjänster och klicka på **Anslut**.
 1. På sidan ”Connect to Azure Enterprise” (Anslut till Azure Enterprise) väljer du:
    - URL till Azure-miljön: [https://ea.azure.com](https://ea.azure.com/).
    - Antal månader: Välj ett värde mellan 1 och 36.
    - Registreringsnummer: ange registreringsnumret.
    - Klicka på **Next**.
 1. I rutan Autentiseringsnyckel anger du API-nyckeln. Du kan hämta API-nyckeln här i Azure EA-portalen. Klicka på API Access Key (API-åtkomstnyckel) under Download Usage (Ladda ned användning)
    - Kopiera och klistra in nyckeln i rutan för kontonyckel
 1. Det tar cirka 5 till 30 minuter att läsa in data i Power BI beroende på datamängdernas storlek.

Power BI-rapportering är tillgängligt för EA-direktkunder, partner och indirekta kunder som har åtkomst till att via faktureringsinformation.

## <a name="report-faq"></a>Vanliga frågor och svar om rapporter

Det här avsnittet av artikeln svarar på vanliga frågor om tolkning av rapporter.

### <a name="why-is-my-cost-showing-as-0"></a>Varför visas min kostnad som 0 USD?

**Direktregistrering** Om du är kontoägare eller avdelningsadministratör kontaktar du EA-administratören för att aktivera prissättningsfunktionen:

1. Klicka på **Hantera** i det vänstra navigeringsfältet.
1. Klicka på den blå pennan bredvid Visa avgifter för DA (avdelningsadministratör).
1. Välj **Aktiverad** och spara.
1. Klicka på den blå pennan bredvid Visa avgifter för AO (kontoägare).
1. Välj **Aktiverad** och spara.

Den här åtgärden ger kontoägare och avdelningsadministratörer åtkomst till information om kostnader/pris i användningsrapporterna.

**Indirekt registrering** Kontrollera med din partner att de har aktiverat prissättningsfunktionen åt dig. Detta kan bara göras av en partner och när de har aktiverat funktionen kan du visa kostnader och priser för din registrering som en EA-administratör.

Om du vill aktivera funktionen för att visa avgifter för kontoägare och avdelningsadministratörer följer du stegen under **Direktregistrering** ovan.

### <a name="there-is-no-sku-information-on-the-usage-detail-report"></a>Det finns ingen SKU-information i rapporten för användningsinformation

Rapporten för användningsinformation saknar SKU-information, men du kan visa den tjänstinformation som används i rapporten. Och sedan kan du få ta på SKU-informationen genom att hämta prisdokumentrapporten.

### <a name="the-total-amount-on-marketplace-does-not-match-on-usage-summary-and-csv-report"></a>Det totala beloppet på Marketplace stämmer inte överens med användningssammanfattningen och CSV-rapporten

Rapporten för Marketplace-debiteringar visar endast användningsbaserade Marketplace-debiteringar. Engångsavgifter visas inte. Du kan se den senaste användningen för både användningsbaserade avgifter och engångsavgifter på sidan med användningssammanfattningen.

### <a name="there-is-no-information-on-my-api-report"></a>Det finns ingen information i min API-rapport

API-nycklar upphör att gälla var sjätte månad. Skapa en ny API-nyckel om du har problem. Det är också viktigt att be EA-administratören att generera nya API-nycklar och följa stegen i vanliga frågor och svar om API-rapporter.

### <a name="my-power-bi-report-isnt-working"></a>Min Power BI-rapport fungerar inte

Om du har problem med Power BI loggar du en begäran om teknisk support till Power BI-teamet på [https://support.powerbi.com](https://support.powerbi.com)så att de kan hjälpa dig.

### <a name="my-resource-tags-arent-showing-up-on-my-reports"></a>Mina resurstaggar visas inte i mina rapporter

Resurstaggar hanteras i Azure-portalen. Du kan kontakta Azure-prenumerationsteamet på [https://portal.azure.com](https://portal.azure.com). Följ anvisningarna på [den här länken](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) för att skicka en supportbegäran.

### <a name="why-does-my-resource-rate-change-every-day"></a>Varför ändras mitt resurspris varje dag?

Resurspriset i den detaljerade användningsrapporten är ett beräknat värde och representerar det genomsnittliga månadspriset som debiteras för en tjänst. Det här priset beräknas med hjälp av genomsnittet av det månatliga åtagandet och de månatliga överförbrukningsavgifterna för en tjänstenhet. Den del av användningen som debiteras mot åtagande och överförbrukningsavgifter ändras till dagen då månaden slutar. Detta innebär att även resurspriset ändras under månaden. Resurspriser låses den femte dagen efter månadens slut.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Ordlista för processer för beräkning av resurspris

**Totalt antal RAW-enheter:** Förbrukad kvantitet i den detaljerade användnings rapporten.
**MOCP-resurs per enhet:** Det överordnade användnings systemet utsätter användningen för varje tjänst i olika enheter. Svällningsförinställning **Förbrukning per enhet:** EA-mått enhet. Svällningsförinställning **Pris:** Enhets pris från Azure-EA-portalen.
**Total kostnad:** Utökad kostnad från den detaljerade användnings rapporten eller åtagande användning + överförbrukning från Azure-EA-portalen.


### <a name="charges-calculation"></a>Avgiftsberäkning

**Konvertering till MOCP-resurs per enhet** = ROUND(Totalt antal RAW-enheter * MOCP-resurs per enhet,4) **Konvertering till enheter** = enheter efter konvertering till MOCP-resurs per enhet/förbrukning per enhet **Total kostnad** = enheter * pris

### <a name="download-usage-calculation-logic"></a>Hämta logik för beräkning av användning

**Resurspris** = total kostnad/(totalt antal RAW-enheter/MOCP-resurs per enhet)

Resurspriset beräknas utifrån avgifterna och stämmer ofta inte överens det faktiska enhetspriset i prisdokumentet.

När det gäller beräkning av överförbrukningsavgifter rapporteras rådata för resursanvändning med upp till högst sex decimaler i den nedladdningsbara rapporten för användningsdata. Men i Azure EA-portalen avrundas användningen till fyra decimaler för åtagandeenheter och användningen trunkeras till noll decimaler för överförbrukningsenheter. Det innebär att vi endast debiterar för fullständiga enheter för all användning som debiteras som överförbrukning i Azure EA-portalen. Det blir en stor skillnad mellan enhetspriset och resurspriset för den användning som debiteras som överförbrukning eller vid blandade månader.

## <a name="next-steps"></a>Nästa steg
- Information om hur du tolkar din faktura och dina kostnader finns i [Förstå fakturan för ditt Azure Enterprise-avtal](../understand/review-enterprise-agreement-bill.md).
- Om du vill börja använda Azure EA-portalen kan du läsa [Komma igång med Azure EA-portalen](ea-portal-get-started.md).
