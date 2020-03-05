---
title: Fakturor för Azure Enterprise-registrering
description: Den här artikeln förklarar hur du hanterar och använder din Azure Enterprise-faktura.
author: bandersmsft
ms.author: banders
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: df2d4eb1b28ac9f13511692ca7588414ea4fdff0
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272384"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Fakturor för Azure Enterprise-registrering

Den här artikeln förklarar hur du hanterar och använder fakturan för ditt Azure Enterprise-avtal (Azure EA). Din faktura är en representation av din räkning. Kontrollera att den är korrekt. Du bör även bekanta dig med andra uppgifter som kan behövas för hantering av din faktura.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Ändra ett IO-nummer för en överförbrukningsfaktura

Azure Enterprise-portalen genererar automatiskt ett standardmässigt inköpsordernummer (IO) såvida inte företagsadministratören anger ett före fakturadatumet. Företagsadministratörer kan uppdatera inköpsordernumret upp till sju dagar från och med att det automatiska fakturameddelandet tagits emot via e-post.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Så här uppdaterar du inköpsordernumret för Azure-tjänster:

1. I Azure Enterprise-portalen väljer du **Rapport** > **Användningssammanfattning**.
1. Välj **Redigera IO-nummer** i det övre högra hörnet.
1. Välj alternativknappen **Azure-tjänster**.
1. Välj en **fakturaperiod** i listrutan med datumintervall.

   Du kan redigera ett inköpsordernummer inom sju dagar efter att du fått ett fakturameddelande, men det måste vara innan du betalar fakturan.
1. Ange ett nytt IO-nummer i fältet  **IO-nummer** .
1. Välj **Spara** för att spara ändringen.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Så här uppdaterar du inköpsordernumret för Azure Marketplace:

1. I Azure Enterprise-portalen väljer du **Rapport** > **Användningssammanfattning**.
1. Välj **Redigera IO-nummer** i det övre högra hörnet.
1. Välj alternativknappen **Marketplace**.
1. Välj en **fakturaperiod** i listrutan med datumintervall.

   Du kan redigera ett inköpsordernummer inom sju dagar efter att du fått ett fakturameddelande, men det måste vara innan du betalar fakturan.
1. Ange ett nytt IO-nummer i fältet  **IO-nummer** .
1. Välj **Spara** för att spara ändringen.

## <a name="cadence-of-azure-enterprise-billing"></a>Faktureringsfrekvens för Azure Enterprise

Microsoft fakturerar årligen på registreringsdatumet köp som gjorts inom kundens åtagande för Microsoft Azure-tjänsterna. Microsoft fakturerar i efterskott för all användning som överstiger åtagandebeloppen.

- Utfästelseavgifterna baseras på en månatlig avgift och faktureras per år i förväg.
- Avgifter för överförbrukning beräknas varje månad och faktureras i efterskott i slutet av faktureringsperioden.

### <a name="billing-intervals"></a>Faktureringsintervall

Faktureringsintervallet beror på hur du väljer att göra dina åtagandeinköp. Det årliga åtagandet sammanfaller med antingen:

- årsdagen för registreringen eller
- giltighetsdatumet för din ettåriga tilläggsprenumeration.

Datumet du fick på överförbrukningsfakturan beror på startdatumet och konfigurationen:

- **Direktregistreringar med startdatum före 1 maj 2018**:
  - Om du har ett Enterprise Azure-avtal (EA) med direktregistrering har du en årlig faktureringsperiod för Azure-tjänster, exklusive Azure Marketplace-tjänster. Din faktureringsperiod baseras på årsdagen: det datum då avtalet började gälla.
  - Om du överskrider 150 % av ditt tröskelbelopp för ekonomiskt åtagande (MC) för EA kommer du automatiskt att konverteras till kvartalsvis fakturering som baseras på din årsdag. Du får också en överförbrukningsfaktura för Azure-tjänsten.
  - Om du inte överskrider 150 % av ditt tröskelbelopp för betalningsåtagande kommer registreringen att behålla den årliga faktureringsperioden. Överförbrukningsfakturan erhålls i slutet av året för åtagandet.

- **Direktregistreringar med startdatum efter 1 maj 2018**:
  - Kundens Azure-förbrukning och avgifter faktureras på separata fakturor per månad.
  - Avgifter som inte täcks av ditt betalningsåtagande betalas som överförbrukning.  

- **Indirekta registreringar med en registrering som startat före 1 maj 2018**:

  Om du har ett Enterprise-avtal med indirekt registrering med ett startdatum före den 1 maj 2018 faktureras du kvartalsvis. Kanalpartnern fakturerar dig direkt.  

- **Indirekta registreringar med startdatum efter 1 maj 2018**:

  Du faktureras per månad.  

### <a name="increase-your-monetary-commitment"></a>Öka ditt betalningsåtagande

Du kan när som helst öka ditt åtagande. Du debiteras för det återstående antalet månader för det årets åtagandeperiod. Om du till exempel registrerar dig för en ettårig tilläggsprenumeration och ökar åtagandet under månad sex, faktureras du för de återstående sex månaderna i den perioden. Ditt åtagande uppdateras sedan för de sista sex månaderna i din åtagandeperiod. Dessa nya kvantiteter används för att fastställa eventuella överförbrukningsavgifter.

### <a name="overage"></a>Överanvändning

Du debiteras för överförbrukning för den användning eller de reservationer som överskrider ditt åtagande under faktureringsperioden. Om du vill veta hur överförbrukningen har beräknats för enskilda poster kan du se rapporten med en sammanfattning av användningen eller kontakta din kanalpartner.

För varje post på fakturan visas:

- **Beräknat belopp**: det totala antalet avgifter
- **Utnyttjat åtagande**: beloppet för ditt åtagande som används för att täcka avgifterna
- **Nettobelopp**: de avgifter som överstiger ditt åtagande

Tillämpliga skatter beräknas bara på det nettobelopp som överstiger åtagandet.

Faktureringen av överförbrukning sker automatiskt. Tiden för meddelanden och fakturor beror på slutdatumet för din faktureringsperiod.

- Ett överförbrukningsmeddelande skickas vanligtvis sju dagar efter slutdatumet för faktureringen.
- Överförbrukningsfakturor skickas sju till nio dagar efter meddelandet.
- Du kan granska avgifterna och uppdatera systemgenererade IO-nummer under de sju dagarna mellan överförbrukningsmeddelandet och faktureringen.

### <a name="azure-marketplace"></a>Azure Marketplace

Från och med faktureringsperioden april 2019 får kunder en enskild Azure-faktura eftersom vi har slagit ihop Azure- och Azure Marketplace-debiteringarna i en enda faktura i stället för två separata fakturor. Den här ändringen påverkar inte kunder i Australien, Japan eller Singapore.

Under övergången till en kombinerad faktura får du en delfaktura för Azure Marketplace. Den här slutliga separata fakturan visar Azure Marketplace-avgifterna som uppstått före datumet för din faktureringsuppdatering. Azure Marketplace-avgifter som uppstått efter detta datum kommer att visas på din Azure-faktura. Efter övergångsperioden visas alla Azure-avgifter och Azure Marketplace-avgifter på den kombinerade fakturan.  

## <a name="adjust-billing-frequency"></a>Justera faktureringsfrekvensen

Faktureringsfrekvensen för en kund är årlig, kvartalsvis eller månatlig. Faktureringsperioden fastställs när en kund signerar sitt avtal. Månatlig fakturering är det kortaste faktureringsintervallet.

- **Godkännande** från en företagsadministratör krävs för att ändra en faktureringscykel från årlig till kvartalsvis för direkta registreringar. Godkännande från en partneradministratör krävs för indirekta registreringar. Ändringen träder i kraft i slutet av det aktuella faktureringskvartalet.
- **En ändring** i avtalet krävs för att ändra en faktureringsperiod från årlig eller kvartalsvis till månatlig.  Eventuella ändringar av den befintliga faktureringscykeln för registrering kräver godkännande från en företagsadministratör eller från din kontaktperson för faktureringen.
- **Skicka** ditt godkännande till [supporten via Azure Enterprise-portalen](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Välj problemkategori: **Fakturering**.

Ändringen träder i kraft i slutet av det aktuella faktureringskvartalet.

Om en Amendment M503 (ändring) signeras kan du flytta valfritt avtal från valfri frekvens till månatlig fakturering. 

## <a name="credits-and-adjustments"></a>Krediter och justeringar

Du kan visa alla krediter eller justeringar som tillämpats för din registrering i avsnittet **Rapporter** i [Azure Enterprise-portalen](https://ea.azure.com).

Så här visar du krediter:

1. I [Azure Enterprise-portalen](https://ea.azure.com) väljer du avsnittet **Rapporter**.
1. Välj **Användningssammanfattning**.
1. Ändra vyn **M** till **C** i det övre högra hörnet.
1. Utöka justeringsfältet i tabellen med det ekonomiska åtagandet för Azure-tjänsten.
1. Därmed visas de krediter som används för registreringen och en kort förklaring. Ett exempel: Kredit enligt serviceavtal.

## <a name="request-an-invoice-copy"></a>Begära en fakturakopia

Kontakta din partner om du vill begära en kopia av din faktura.

## <a name="overage-offset"></a>Överförbrukningsförskjutning

Om du vill tillämpa ditt betalningsåtagande för överförbrukning måste du uppfylla följande kriterier:

- Du har ådragit dig överförbrukningsavgifter som inte har betalats, och som gäller inom ett år efter den fakturerade tjänstens slutdatum.
- Ditt tillgängliga ekonomiska åtagande omfattar det fullständiga antalet gällande avgifter, inklusive alla tidigare ej betalda Azure-fakturor.
- Den faktureringsperiod som du vill slutföra för måste vara helt avslutad. Fakturering avslutas helt efter den femte dagen varje månad.
- Den faktureringsperiod som du vill förskjuta för måste vara helt avslutad.
- Din Azure-åtaganderabatt baseras på det faktiska nya åtagandet minus eventuella belopp som planeras för den tidigare förbrukningen. Detta krav gäller endast för aktuella överförbrukningsavgifter. Detta gäller endast för tjänster som förbrukar ekonomiskt åtagande, och gäller därmed inte Azure Marketplace-avgifter. Azure Marketplace-avgifter faktureras separat.

Om du vill slutföra en överförbrukningsförskjutning kan du eller kontoteamet skapa en supportbegäran. Ett e-postmeddelande med godkännande från företagsadministratören eller faktureringskontakten krävs.

## <a name="view-price-sheet-information"></a>Visa information om prisdokument

Företagsadministratörer kan visa den prislista som är associerad med deras registrering för Azure-tjänster.

Så här visar du aktuellt prisdokument:

1. I Azure Enterprise-portalen väljer du **Rapporter** och sedan **Prisdokument**.
2. Visa prisdokumentet eller välj **Ladda ned**.

![Exempel som visar information om prisdokument](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Så här laddar du ned en historisk prislista:

1. I Azure Enterprise-portalen väljer du **Rapporter** och sedan **Ladda ned användning**.
2. Ladda ned prisdokumentet.

![Exempel som visar var du laddar ned ett äldre pris dokument](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Några orsaker till prisskillnader:

- Prissättningen kan ha ändrats mellan den tidigare registreringen och den nya registreringen. Det kan uppstå prisändringar eftersom priser är avtalsenliga för en specifik registrering från startdatumet till slutdatumet för ett avtal.
- När du byter till en ny registrering ändras priset till det nya avtalet. Prissättningen definieras av prisdokumentet, som kan vara högre i den nya registreringen.
- Om en registrering övergår till en längre period ändras även prissättningen. Priserna ändras till Betala per användning-priser.

## <a name="request-detailed-usage-information"></a>Begära detaljerad användningsinformation

Företagsadministratörer kan visa en sammanfattning av användningsdata, förbrukat betalningsåtagande samt avgifter som är kopplade till ytterligare användning i Azure Enterprise-portalen. Avgifterna presenteras på sammanfattningsnivå för alla konton och prenumerationer.

Om du vill visa detaljerad användning i specifika konton laddar du ned rapporten med användningsinformation genom att gå till **Rapporter** > **Ladda ned användning**.

> [!NOTE]
> Rapporten med användningsinformation inkluderar inga tillämpliga skatter. Det kan ta upp till åtta timmar från den tidpunkt då användningen började gälla till dess att den återspeglas i rapporten.

För indirekta registreringar behöver din partner aktivera markeringsfunktionen innan du kan se kostnadsrelaterad information.

## <a name="reports"></a>Rapporter

Företagsadministratörer kan visa en sammanfattning av användningsdata, förbrukat betalningsåtagande samt avgifter som är kopplade till ytterligare användning i Azure Enterprise-portalen. Avgifterna presenteras på sammanfattningsnivå för alla konton och prenumerationer.

### <a name="azure-enterprise-reports"></a>Azure Enterprise-rapporter

- Användningssammanfattning och diagram
- Rapport för tjänstanvändning
- Rapport för saldo och avgifter
- Rapport för användningsinformation
- Rapport för Azure Marketplace-debiteringar
- Prisdokument
- Advanced report download (Nedladdning av avancerad rapport)
- Formatering av CSV-rapport

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>Så här visar du rapporter och diagram för användningssammanfattning:

1. Gå till Azure Enterprise-portalen.
1. Välj **Rapporter** i det vänstra fönstret.
1. Välj fliken **Användningssammanfattning**.
1. Välj åtagandeperiod på menyn för datumintervall längst upp till vänster.
1. Välj en period eller månad i diagrammet om du vill visa mer information.
1. På den här fliken kan du:
   - Visa diagram över användning månad för månad med en analys av utnyttjad användning, tjänstöverförbrukning, separat debiterade avgifter samt Azure Marketplace-debiteringar.
   - Filtrera efter avdelningar, konton och prenumerationer nedanför diagrammet.
   - Växla uppdelning mellan **Avgifter efter tjänster** och **Avgifter efter hierarki**.
   - Visa detaljerad information om Azure-tjänster, separat debiterade avgifter och Azure Marketplace-debiteringar.

## <a name="service-usage-report"></a>Rapport för tjänstanvändning

På sidan med rapporten för tjänstanvändning kan företagsadministratörer visa en sammanfattning av tjänstanvändningsdata. Användningen presenteras på sammanfattningsnivå för alla konton och prenumerationer. Om du vill visa användningen mer i detalj kan du filtrera rapporten efter konton eller prenumerationer.

> [!NOTE]
> Det kan vara en fördröjning på upp till fem dagar från användningsdatumet tills användningen visas i rapporten.

### <a name="to-view-the-report"></a>Så här visar du rapporten:

1. Logga in på Azure Enterprise-portalen.
1. Välj **Rapporter** i det vänstra navigeringsfältet.
1. Välj fliken **Användningssammanfattning**.
1. Välj datumintervall.
1. Välj vilka konton eller prenumerationer du vill visa.
1. Du kan också:
   - Ändra vy mellan **Avgifter efter tjänster** och **Avgifter efter hierarki** för att visa olika uppdelningar.
   - Visa information om tjänstnamn, måttenhet, förbrukade enheter, pris och utökad kostnad.

## <a name="download-csv-reports"></a>Ladda ned CSV-rapporter

På sidan för nedladdning av månadsrapport kan företagsadministratörer ladda ned flera rapporter som CSV-filer. Nedladdningsbara rapporter:

- Rapport för saldo och avgifter
- Rapport för användningsinformation
- Rapport för Azure Marketplace-debiteringar
- Prisdokument

### <a name="to-download-reports"></a>Så här laddar du ned rapporter:

1. Välj **Rapport** i Azure Enterprise-portalen.
1. Välj **Usage Download** (Ladda ned användning) från det översta menyfliksområdet.
1. Välj **Hämta** intill månadens rapport.

### <a name="csv-report-formatting-issues"></a>Formateringsproblem för CSV-rapporter

Kunder som visar CSV-rapporter i Azure Enterprise-portalen i euro kan få formateringsproblem med kommatecken och punkter.

Du kan till exempel visa:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Timmar | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Du bör se:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Timmar | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Det här formateringsproblemet inträffar på grund av standardinställningarna i importfunktionen i Excel. Excel importerar alla fält som ”vanlig” text och antar att tal avgränsas med matematisk standard. Ett exempel: "1,000.00".

Om en europeisk valuta använder en punkt (.) som tusentalsavgränsare och ett kommatecken som decimaltecken (,), till exempel ”1.000,00”, blir formateringen felaktig. Ett exempel: ”1.000,00”. Importresultatet kan variera beroende på dina nationella inställningar.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>Så här importerar du CSV-filen utan formateringsproblem:

1. Gå till **Arkiv** > **Öppna** i Microsoft Excel.
   Guiden för att importera text visas.
1. Välj **Avgränsade** under **Ursprunglig datatyp**.  Standardvärdet är **Fast bredd**.
1. Välj **Nästa**.
1. Under Avgränsare markerar du kryssrutan för**kommatecken**. Avmarkera **Flik** om det är markerat.
1. Välj **Nästa**.
1. Bläddra till kolumnerna **ResourceRate** och **ExtendedCost**.
1. Välj kolumnen **ResourceRate**. Den visas markerad i svart.
1. Under avsnittet **Kolumndataformat** väljer du **Text** i stället för **Allmänt**. Kolumnrubriken ändras från **Allmänt** till **Text.**
1. Upprepa steg 8 och 9 för kolumnen **Extended Cost** och välj sedan **Slutför**.

> [!TIP]
> Om du har kopplat Excel så att CSV-filer öppnas automatiskt måste du använda funktionen **Öppna** i Excel i stället. Gå till **Arkiv** > **Öppna** i Excel.

## <a name="balance-and-charge-report"></a>Rapport för saldo och avgifter

Rapporten för saldo och avgifter ger en månatlig sammanfattning av saldon, nya inköp, Azure Marketplace-tjänstavgifter, justeringar och överförbrukningsavgifter.

Alla rader i översiktstabellen för Azures tjänståtagande är fortfarande statiska och visar månad för månad. Information om alla köp och justeringar finns i avsnitten för **ny information om köp** och **justeringsinformation**.

### <a name="download-the-balance-and-charge-report"></a>Ladda ned rapporten för saldo och avgifter

1. Logga in på Azure Enterprise-portalen som företagsadministratör.
1. Välj **Rapporter** i det vänstra fönstret.
1. Välj fliken **Report Download** (Ladda ned rapport).
1. Välj lämplig månad under kolumnen **Balance and Charge** (Saldo och avgifter) och klicka för att ladda ned rapporten.

## <a name="usage-detail-report"></a>Rapport för användningsinformation

Rapporten för användningsinformation innehåller en månatlig sammanfattning av förbrukningen av tjänster och kvantiteter för en registrering. Den innehåller information om mätarnamn, mätartyper och förbrukade kvantiteter.

### <a name="download-the-usage-detail-report"></a>Ladda ned rapporten med användningsinformation

1. Logga in på Azure Enterprise-portalen som företagsadministratör.
1. Välj **Rapporter** i det vänstra navigeringsfältet.
1. Välj fliken **Ladda ned användning**.
1. Välj lämplig månad under kolumnen **Usage Detail** (Användningsinformation) och välj nedladdning av rapporten.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Azure Marketplace-debiteringar i rapporter på Azure Enterprise-portalen

Det finns två typer av Azure Marketplace-debiteringar:

- **Användningsbaserad:** Produkter mäts i transaktionsenheter.  Till exempel debiteras virtuella datorer per timme och Bing API-sökningar debiteras per antal sökningar.
- **Månadsavgift:** Faktureras månadsvis baserat på användning eller åtkomst.

Mer information om Azure Marketplace-debiteringar finns i [vanliga frågor och svar om Azure Marketplace](https://azure.microsoft.com/marketplace/faq/).

Så här visar du olika debiteringar i Azure Enterprise-portalen:

- **Rapport för användningssammanfattning**: Visar **både** användningsbaserad debitering och månadsavgifter för Azure Marketplace.
- **Rapport för Marketplace-debiteringar**: Visar **endast** användningsbaserade Azure Marketplace-debiteringar.  Engångsavgifter visas inte.

> [!NOTE]
> Azure Marketplace inte är tillgängligt för MPSA-registreringar.

## <a name="advanced-report-download"></a>Nedladdning av avancerad rapport

Om du vill skapa rapporter för specifika datumintervall eller konton kan du välja nedladdning av avancerad rapport. Från och med 30 augusti 2016 är formatet för utdatafilen CSV så att det går att hantera större uppsättningar av poster.

1. I Azure Enterprise-portalen väljer du **Advanced Report Download** (Nedladdning av avancerad rapport).
1. Välj **Appropriate Date Range** (Lämpligt datumintervall).
1. Välj **Appropriate Accounts** (Lämpliga konton).
1. Välj **Request Usage Data** (Begär användningsdata).
1. Välj **uppdateringsknappen** tills rapportens status uppdateras till **Ladda ned**.
1. Ladda ned rapporten.

## <a name="reporting-for-non-enterprise-administrators"></a>Rapportering för icke-företagsadministratörer

Företagsadministratörer kan ge avdelningsadministratörer (DA) och kontoägare (AO) behörighet att visa kostnader för en registrering. Kontoägare med åtkomst kan ladda ned CSV-rapporter som är specifika för deras konto och prenumerationer. De kan också visa informationen visuellt under rapporteringsavsnittet i Azure Enterprise-portalen.

### <a name="to-enable-access"></a>Så här aktiverar du åtkomst:

 1. Logga in på Azure Enterprise-portalen som företagsadministratör.
 1. Välj **Hantera** i det vänstra navigeringsfältet.
 1. Välj fliken **Registrering**.
 1. Under avsnittet med **registreringsinformation** väljer du pennikonen bredvid **Visa avgifter för DA** eller **Visa avgifter för AO**.
 1. Välj **Aktiverad**.
 1. Välj **Spara**.

### <a name="to-view-reports"></a>Så här visar du rapporter:

1. Logga in på Azure Enterprise-portalen som avdelningsadministratör eller kontoägare.
1. Välj **Rapporter** i det vänstra navigeringsfältet.
1. Välj fliken **Användningssammanfattning** för att visa information om kontot och prenumerationer.
1. Välj **Usage Download** (Ladda ned användning) för att visa CSV-rapporter.

Avdelningsadministratörer och kontoägare kan inte visa avgifter när du använder funktionen **Advanced Report Download** (Nedladdning av avancerad rapport). Kostnaderna visas som 0 USD.

Kontoägarnas behörighet att visa avgifter gäller alla kontoägare och alla användare som har behörighet på associerade prenumerationer. Om du är en indirekt kund måste din kanalpartner aktivera kostnadsfunktioner.

## <a name="move-usage-data-to-another-enrollment"></a>Flytta användningsdata till en annan registrering

Användningsdata flyttas bara när en överföring antedateras. Det finns två alternativ för att flytta användningsdata från en registrering till en annan:

- Kontoöverföringar från en registrering till en annan registrering
- Registreringsöverföringar från en registrering till en annan registrering

För endera alternativet måste du skicka en [supportbegäran](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) till EA-supportteamet för att få hjälp. 

## <a name="azure-ea-pricing-overview"></a>Prisöversikt för Azure EA

Det här avsnittet innehåller information om hur användningen beräknas. I avsnittet besvaras många vanliga frågor om avgifter för olika Azure-tjänster i ett Enterprise-avtal med mer komplexa beräkningar.

### <a name="price-protection"></a>Prisskydd

Som kund eller kanalpartner är du garanterad att få priser till, eller under, de priser som visas i din kundprislista (CPS) eller det pris som gäller vid datumet för ditt Azure-köp. Det här priset kallas för baspriset. För tjänster som har införts efter ditt Azure-köp debiteras du priset som gäller för rabatten på den aktuella nivån när tjänsten först introducerades. Det här prisskyddet gäller för åtagandeperioden, som kan vara ett eller tre år beroende på ditt Enterprise-avtal.

### <a name="price-changes"></a>Prisändringar

Microsoft kan sänka det aktuella priset för Enterprise-avtalet för enskilda Azure-tjänster under en registreringsperiod. Vi utökar dessa reducerade priser till befintliga kunder medan det lägre priset gäller. Om dessa priser skulle komma att öka senare kommer ditt registreringspris för en tjänst inte att överstiga pristaket enligt definitionen ovan. Priset kan dock öka i förhållande till det tidigare sänkta priset. I båda fallen informerar vi kunder och indirekta kanalpartner om kommande prisändringar via e-post till de företags- och partneradministratörer som är associerade med registreringen.

### <a name="current-effective-pricing"></a>Aktuella gällande priser

Kunder och kanalpartner kan visa det aktuella priset för en registrering genom att logga in på [Azure Enterprise-portalen](https://ea.azure.com/) och se prisdokumentsidan för registreringen. Om du köper Azure indirekt via någon av våra kanalpartner måste du få dina prisuppdateringar från kanalpartnern, såvida de inte har aktiverat visning av påläggpris för din registrering.

### <a name="introduction-of-new-azure-services"></a>Introduktion till nya Azure-tjänster

Vi förbättrar ständigt Azure och lägger regelbundet till nya tjänster som prissätts separat från befintliga tjänster. Vissa förhandsversioner av tjänster är tillgängliga automatiskt medan andra kräver kundåtgärder i [Azure-kontoportalen](https://account.windowsazure.com/PreviewFeatures).

Slutligen tillhandahålls vissa tjänster till kampanjpris när de först lanseras och det priset kan ökas vid ett senare datum.

När tjänster flyttas från förhandsversionen till den allmänt tillgängliga versionen kan priserna öka när SLA för full prestanda och tillförlitlighet tillämpas. En sådan ökning begränsas inte av det normala basprisskyddet. Användningen av dessa tjänster debiteras till det ökade priset. För att undvika avgifter för dessa nya tjänster måste du välja att inte använda dem.

### <a name="introduction-of-regional-pricing"></a>Introduktion till regional prissättning

Utöver införandet av nya tjänster ändras även tjänster regelbundet från en enda global grund till en mer regional modell när det regionala stödet ökar för dessa tjänster.

När regionaliseringen av en tjänst först införs gäller basprisskyddet för de nya regionerna. Men om nya regioner införs vid en senare tidpunkt för samma tjänst anses dessa tjänster som nya tjänster och erbjuds till separata priser som är oberoende av basprisskyddet.

### <a name="enterprise-devtest"></a>Enterprise Dev/Test

Företagsadministratörer kan låta kontoägare skapa prenumerationer baserat på Enterprise Dev/Test-erbjudandet. Kontoägaren måste konfigurera de Enterprise Dev/Test-prenumerationer som behövs för de underliggande prenumeranterna. Den här konfigurationen gör att aktiva Visual Studio-prenumeranter kan köra arbetsbelastningar i Azure till särskilda Enterprise Dev/Test-priser. Mer information finns i [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="enterprise-agreement-usage-calculations"></a>Användningsberäkningar för Enterprise-avtal

Grundläggande information om offentliga priser, måttenheter, vanliga frågor och svar och riktlinjer för användningsrapportering för varje enskild tjänst finns i [Azure-tjänster](https://azure.microsoft.com/services/) och [Azure-priser](https://azure.microsoft.com/pricing/). Du hittar mer information om EA-beräkningar i följande avsnitt.

### <a name="enterprise-agreement-units-of-measure"></a>Måttenhet för Enterprise-avtal

Enheten för Enterprise-avtal skiljer sig ofta från andra program som prenumerationsavtalet för Microsoft Online (MOSA). Den här skillnaden innebär att för ett antal tjänster aggregeras måttenheten för att ge det normaliserade priset. Den måttenhet som visas i användningssammanfattningsvyn i Azure Enterprise-portalen är alltid Enterprise-måttet. En fullständig lista över aktuella måttenheter och omvandlingar för varje tjänst finns i Excel-filen [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Avrundningsregler

Azure Enterprise-portalen följer IEEE-standard med avrundning till närmaste jämna tal (så kallad ”Banker’s Rounding” eller ”Gaussian Rounding”). Med den här logiken avrundas halva tal till närmaste jämna tal. Det är vanligare att använda logik som avrundar uppåt till nästa heltal vid halva tal. Metoden som används i Azure Enterprise-portalen ger en mer exakt totalsumma över gruppen jämfört med standardlogiken i Excel.

När det första decimaltalet är 5 och det inte finns några efterföljande tal eller om de efterföljande talen är nollor, blir den framförvarande siffran jämn (d.v.s. avrundning till det närmaste jämna talet). Exempel: Både 2,315 och 2,325 blir 2,32 när de avrundas till närmaste hundradel.

I följande tabell visas Excel-formler som du kan använda för att modellera Azure Enterprise Portal-regler för avrundning och konvertering:

| Scenario | Formellogik (banker) |
| --- | --- |
| Användning av avrundning | =MROUND({_källa_}, 0.0002) |
| Avrundning av priser (2 decimaler) | =MROUND({_källa_}, 0.02) |
| Avrundning av priser (0 decimaler) | =MROUND({_källa_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Konvertering mellan rapporten för användningsinformation och sidan med sammanfattning av användning

I den nedladdningsbara rapporten med användningsdata kan du se rådata för resursanvändning med upp till sex decimaler. Men användningsdata som visas Azure Enterprise-portalen avrundas till fyra decimaler för åtagandeenheter och trunkerar till noll decimaler för överförbrukningsenheter. Rådata för användning avrundas först till fyra decimaler före konverteringen till enheter som används i Azure Enterprise-portalen. Sedan avrundas de konverterade Enterprise-enheterna om igen till fyra decimaler. Du kan endast se de faktiska förbrukade timmarna före konverteringen i den nedladdningsbara rapporten för användning och inte i Azure Enterprise-portalen.

Ett exempel: Anta att 694.533404 är det faktiska antalet SQL Server-timmar som rapporteras i rapporten för användningsinformation. Dessa enheter konverteras till 6.94533404 enheter för 100 beräkningstimmar, och avrundas sedan till 6.9453 som visas i Azure Enterprise-portalen.

- För att fastställa det beräknade faktureringsbeloppet multipliceras dessa enheter sedan med enhetspriset för åtagandet och resultatet trunkeras till två decimaler. För japanska yen (JPY) och koreanska won (Won) avrundas det beräknade beloppet till noll decimaler.
- För överförbrukning trunkeras de fakturerbara enheterna till sex decimaler och multipliceras sedan med enhetspriset för överförbrukning för att fastställa det beräknade faktureringsbeloppet.
- För fakturering av hanterad tjänstleverantör (MSP) trunkeras all användning som är kopplad till en avdelning som är markerad som MSP till noll decimaler efter konvertering till EA-måttenheten. Detta innebär att summan av den här användningen kan bli lägre än totalsumman av all användning i Azure Enterprise-portalen. Det beror på om MSP är inom saldot för betalningsåtagande eller om användningen räknas som överförbrukning.

### <a name="graduated-pricing"></a>Graderat pris

Priset för Enterprise-avtal inkluderar för närvarande inte graderat pris där priset per enhet minskar när användningen ökar. När du byter från ett MOSA-program med graderat pris till ett Enterprise-avtal sätts priset i proportion till tjänstens basnivå minus efter justering för eventuella rabatter för Enterprise-avtalet.

### <a name="partial-hour-billing"></a>Debitering per deltimme

All debiterad användning baseras på minuter som konverteras till deltimmar i stället för att räknas upp till heltimmar. De listade Enterprise-timpriserna motsvarar det totala antalet timmar plus deltimmar.

### <a name="average-daily-consumption"></a>Genomsnittlig daglig förbrukning

Vissa tjänster prissätts månadsvis, men användningen rapporteras per dag. I dessa fall utvärderas användningen en gång per dag, dividerat med 31 och räknas samman för antalet dagar i den aktuella faktureringsmånaden. Det innebär att priserna aldrig är högre än förväntat under en månad och att de är något lägre för de månader som har mindre än 31 dagar.

### <a name="compute-hours-conversion"></a>Konvertering av beräkningstimmar

Före 28 januari 2016 angavs all användning för virtuella A0-, A2-, A3- och A4- Standard-och Basic-datorer och molntjänster som mätarminuter för virtuella A1-datorer. Virtuella A0-datorer räknades som bråkdelar av virtuella A1-datorer medan A2, A3 och A4 konverterades till multipler. Eftersom detta ledde till viss förvirring för våra kunder införde vi en ändring för att tilldela användning per minut till dedikerade A0-, A2-, A3- och A4-mätare.

Den nya mätningen började gälla mellan 27 och 28 januari 2016. I den nedladdningsbara CSV-filen som visar användning under den här övergångsperioden visas båda:

- Användning som angavs på A1-mätaren
- Användning som angavs på den nya dedikerade mätaren som motsvarar storleken på din distribution.

| **Distributionsstorlek** | **Användning anges som en multipel av A1 till och med 26 januari 2016** | **Användning anges på dedikerad mätare från och med 27 januari 2016** |
| --- | --- | --- |
| A0 | 0,25 av A1-timme | 1 av A0-timme |
| A2 | 2 av A1-timme | 1 av A2-timme |
| A3 | 4 av A1-timme | 1 av A3-timme |
| A4 | 8 av A1-timme | 1 av A4-timme |

### <a name="regions"></a>Regioner

För de tjänster där zon och region påverkar prissättningen visar följande tabell en mappning för geografiska områden och regioner:

| Geografi | Dataöverföringsregioner | CDN-regioner |
| --- | --- | --- |
| Zon 1 | Nordeuropa <br> Västeuropa <br> USA, Västra <br> USA, Östra <br> USA, norra centrala <br> USA, södra centrala <br> USA, östra 2 <br> USA, centrala | Nordamerika <br> Europa |
| Zon 2 | Östra stillahavsområdet i Asien <br> Sydöstra stillahavsområdet i Asien <br> Japan, östra <br> Japan, västra <br> Australien, östra <br> Australien, sydöstra | Asien och stillahavsområdet <br> Japan <br> Latinamerika <br> Mellanöstern/Afrika <br> Australien, östra <br> Australien, sydöstra |
| Zon 3 | Brasilien, södra |   |

Det finns inga kostnader för utgående data mellan tjänster i samma datacenter. Till exempel Office 365 och Azure.

### <a name="monetary-commitment-and-unbilled-usage"></a>Ekonomiskt åtagande och ej fakturerad användning

Ekonomiskt åtagande för Azure är ett belopp som förskottsbetalats för Azure-tjänster. Det ekonomiska åtagandet förbrukas allt eftersom tjänster används. Azure-tjänster från första part debiteras mot betalningsåtagandet. Men vissa avgifter debiteras separat och Azure Marketplace-tjänster använder inte Azures betalningsåtagande.

### <a name="charges-billed-separately"></a>Avgifter som debiteras separat

Vissa produkter och tjänster från tredjepartskällor använder inte Azures betalningsåtagande. Dessa artiklar faktureras i stället separat som en del av fakturan för överförbrukning i standardfaktureringsperioden.

Vi har slagit samman alla avgifter för Azure och Azure Marketplace till en enda faktura som sammanfaller med registreringens faktureringsperiod. Den kombinerade fakturan gäller inte för kunder i Australien, Japan eller Singapore.

På den kombinerade fakturan visas först Azure-användning, följt av eventuella avgifter för Azure Marketplace. För kunder i Australien, Japan eller Singapore visas Marketplace-avgifterna på en separat faktura.

Om det inte finns någon överförbrukning i slutet av standardfaktureringsperioden görs en separat fakturering av de avgifter som debiteras separat. Detta gäller kunder i Australien, Japan och Singapore.

Följande tjänster faktureras separat:

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

För direktkunder visas Azure Marketplace-avgifter i Azure Enterprise-portalen. Azure Marketplace-köp och -förbrukning debiteras utanför betalningsåtagandet och debiteras kvartalsvis eller månadsvis och i efterskott.

Indirekta kunder kan se sina Azure Marketplace-prenumerationer på sidan **Hantera prenumerationer** i Azure Enterprise-portalen, men priserna är dolda. Kunderna bör kontakta sin LSP (leverantör av licenslösningar) för information om Azure Marketplace-avgifter.

Nya månatliga eller årligen återkommande Azure Marketplace-köp debiteras hela beloppet inom den period då Azure Marketplace-objekten köptes. Dessa objekt förnyas automatiskt i nästa period på samma dag då det ursprungliga köpet gjordes.

Befintliga månatliga återkommande avgifter fortsätter att förnyas den första i varje kalendermånad. Årsavgifter förnyas på årsdagen för inköpet.

Vissa tjänster från tredjepartsåterförsäljare på Azure Marketplace ingår nu i EA-betalningsåtagandet och påverkar saldot. Tidigare fakturerades dessa tjänster separat och ingick inte i EA-betalningsåtagandet. EA-betalningsåtagandet för dessa tjänster på Azure Marketplace förenklar hanteringen av köp och betalningar från kunder. En fullständig lista över tjänster som ingår i betalningsåtagandet finns i [uppdateringen från 6 mars 2018 på Azure-webbplatsen](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partner

LSP:er kan ladda ned en Azure Marketplace-prislista från prisdokumentsidan i Azure Enterprise-portalen. Välj länken **Marketplace-prislistan** i det övre högra hörnet. Azure Marketplace-prislistan visar alla tillgängliga tjänster och motsvarande priser.

Så här laddar du ned prislistan:

1. I Azure Enterprise-portalen går du till **Rapporter** > **Prisdokument**.
1. Leta reda på länken Azure Marketplace-prislistan under ditt användarnamn i det övre högra hörnet.
1. Högerklicka på länken och välj **Spara mål som**.
1. I fönstret **Spara** ändrar du dokumentets titel till `AzureMarketplacePricelist.zip` så att filen ändras från en XLSX-fil till en ZIP-fil.
1. När hämtningen är klar har du en ZIP-fil med landsspecifika prislistor.
1. LSP:er kan se priser för respektive land i motsvarande fil. På fliken **Notifications** (Meddelanden) kan LSP:er se vilka SKU:er som är nya eller som har tagits bort.
1. Prisändringar är ovanliga. LSP:er meddelas via e-post om prisökningar och valutakursändringar 30 dagar i förväg.
1. LSP:er får en faktura per registrering, ISV och kvartal.

### <a name="enabling-azure-marketplace-purchases"></a>Aktivera Azure Marketplace-inköp

Företagsadministratörer kan inaktivera eller aktivera Marketplace-inköp för alla Azure-prenumerationer som ingår i registreringen. Om företagsadministratören inaktiverar köp och det finns Azure-prenumerationer som redan har Azure Marketplace-prenumerationer, avbryts eller påverkas inte dessa Azure Marketplace-prenumerationer.

Även om kunderna kan konvertera sina Azure-direktprenumerationer till Azure EA genom att koppla dem till registreringen i Azure Enterprise-portalen konverteras inte de underordnade prenumerationerna automatiskt med den åtgärden.

Så här aktiverar du Azure Marketplace-inköp:

1. Logga in på Azure Enterprise-portalen som företagsadministratör.
1. Gå till **Hantera**.
1. Under **Enrollment Detail** (Registreringsinformation) väljer du pennikonen bredvid **Azure Marketplace**.
1. Växla **Aktiverad/inaktiverad** eller **Endast kostnadsfria/BYOL SKU:er** efter behov.
1. Välj **Spara**.

> [!NOTE]
> BYOL (Bring your own license) och alternativet för endast kostnadsfritt begränsar inköp och förvärv av Azure Marketplace-SKU:er till endast BYOL och kostnadsfria SKU:er.

Visa mer information om [Azure Marketplace-debiteringar i rapporter på Azure Enterprise-portalen](#azure-marketplace-charges-in-azure-enterprise-portal-reports).

### <a name="services-billed-hourly-for-azure-ea"></a>Tjänster debiteras per timme för Azure EA

Följande tjänster debiteras per timme enligt Enterprise-avtalet i stället för priset per månad i MOSP:

- Application Delivery Network
- Brandvägg för webbaserade program

### <a name="azure-remoteapp"></a>Azure RemoteApp

Om du har ett Enterprise-avtal betalar du för Azure RemoteApp baserat på prisnivån för ditt Enterprise-avtal. Det finns inga ytterligare avgifter. Standardprisnivån innefattar de första 40 timmarna. Den obegränsade prisnivån innefattar de första 80 timmarna. Remote App slutar generera användning efter dessa 80 timmar.

## <a name="azure-marketplace-faq"></a>Vanliga frågor och svar om Azure Marketplace

I det här avsnittet förklaras hur ditt Azure-betalningsåtagande kan tillämpas för återförsäljartjänster från tredje part i Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Vad har ändrats när det gäller Azure Marketplace-tjänster och EA-betalningsåtagandet?

Från och med 1 mars 2018 används EA-betalningsåtagandet för vissa återförsäljartjänster från tredje part. Förutom för Azure Reserved VM Instances fakturerades tjänsterna tidigare separat utanför EA-betalningsåtagandet.

Vi har utökat användningen av betalningsåtagandet till att nu omfatta några av de mest köpta Azure Marketplace-tjänster som publicerats av tredje part. EA-betalningsåtagandet för dessa tjänster på Azure Marketplace förenklar hanteringen av ditt köp och dina betalningar.

### <a name="why-did-we-make-this-change"></a>Varför har vi gjort den här ändringen?

Kunder letar ständigt efter nya sätt att dra nytta av förskottsbetalningen som de har gjort i form av Azure-betalningsåtagandet. Vi uppfyller en vanlig förfrågan som påverkar en stor del av våra Azure Marketplace-kunder.

### <a name="how-do-you-benefit"></a>Vad innebär det för fördel för dig?

Du får en enklare faktureringsupplevelse och kan se till att du utnyttjar ditt Azure-betalningsåtagande. Eftersom dessa tjänster ingår i det förbetalda betalningsåtagandet skapas ytterligare värde för Azure-betalningsåtagandet.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>För vilka Azure Marketplace-tjänster används EA-betalningsåtagandet och hur vet jag det?

När du köper en tjänst som använder betalningsåtagandet visas en ansvarsfriskrivning i Azure Marketplace. För närvarande stöds vissa tjänster som publiceras av Red Hat, SUSE, Autodesk och Oracle. Tjänster som har liknande namngivningskonventioner men som publiceras av andra parter, dras inte av från betalningsåtagandet. En fullständig lista finns i slutet av dessa vanliga frågor och svar.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>Vad händer om EA-betalningsåtagandet tar slut?

När du har förbrukat betalningsåtagandet övergår användningen till överförbrukning, och avgifterna för dessa tjänster visas på din nästa överförbrukningsfaktura tillsammans med andra förbrukningstjänster. Före ändringen 1 mars 2018 fakturerades dessa avgifter med andra Azure Marketplace-tjänster.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Varför används inte EA-betalningsåtagandet för alla Azure Marketplace-köp?

Vi arbetar ständigt med att skapa den bästa kundupplevelsen för EA-betalningsåtagande. Den här ändringen påverkade ett stort antal kunder och en stor del av de totala köpen i Azure Marketplace. Andra tjänster kan läggas till i framtiden.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Hur påverkar detta indirekt registrering och partner?

Det påverkar inte vår indirekta registrering för kunder eller partner. Dessa tjänster har samma påläggsmöjligheter för partner som andra konsumtionstjänster. Det enda som ändras är att debiteringarna visas på en annan faktura och att betalningen av avgifterna kommer från kundens EA-betalningsåtagande.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>Finns det en lista över de Azure Marketplace-tjänster som använder EA-betalningsåtagandet?

Specifika Azure Marketplace-erbjudanden kan använda betalningsåtagandemedel. Se [tjänster från tredje part som använder betalningsåtagande](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) för en fullständig lista över produkter som medverkar i det här programmet.

## <a name="power-bi-reporting"></a>Power BI-rapportering

Power BI-rapportering är tillgängligt för EA-direktkunder, partner och indirekta kunder som har åtkomst till att via faktureringsinformation.

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro är tillgängligt för EA-kunder. Med Power BI Pro kan du skapa och dela rapporter för att effektivt hantera dina kostnadsdata. Och det finns ytterligare funktioner för samarbete och uppdatering av data. Power BI Pro erbjuder högre gränser för datakapacitet och dataströmning.

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Så här kommer du åt Microsoft Azure Consumption Insights:

1. Gå till [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Välj **Get It Now** (Hämta nu).
1. Ange ett registreringsnummer och antal månader och välj sedan **Nästa**.
1. Ange din API-åtkomstnyckel för att ansluta. Nyckeln för registreringen hittar du i [Enterprise-portalen](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Välj **Logga in**. Därmed startas importen automatiskt.
1. När den är klar visas en ny instrumentpanel, rapport och modell i navigeringsfönstret. Välj instrumentpanelen för att visa dina importerade data.

> [!TIP]
>
> - Information om hur du skapar API-nyckeln för din registrering finns i hjälpfilen för API-rapporter i [Enterprise-portalen](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
> - Mer information om hur du ansluter Power BI till din Azure-förbrukning finns i [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Så här kommer du åt det tidigare Power BI EA-innehållspaketet:

1. Gå till [Power BI-webbplatsen](https://app.powerbi.com/getdata/services/azure-enterprise).
1. Logga in med ett giltigt arbets- eller skolkonto.

   Arbets- eller skolkontot kan vara detsamma eller ett annat än det som du använder för att nå registreringen via Azure Enterprise-portalen.
1. Välj **Microsoft Azure Enterprise** på instrumentpanelen för tjänster och välj **Anslut**.
1. På sidan **Connect to Azure Enterprise** (Anslut till Azure Enterprise) fyller du i fälten:
    - URL till Azure-miljön: [https://ea.azure.com](https://ea.azure.com/)
    - Antal månader: mellan 1 och 36
    - Registreringsnummer: ditt registreringsnummer
1. Välj **Nästa**.
1. I rutan **Autentiseringsnyckel** anger du API-nyckeln.

    Du kan hämta API-nyckeln i Azure EA-portalen under fliken **Download Usage** (Ladda ned användning). Välj **API Access Key** (API-åtkomstnyckel) och klistra sedan in nyckeln i rutan **Account Key** (Kontonyckel).
1. Det tar cirka 5 till 30 minuter att läsa in data i Power BI beroende på datamängdernas storlek.

## <a name="reports-faq"></a>Vanliga frågor och svar om rapporter

I det här avsnittet besvaras vanliga frågor om rapporter.

### <a name="why-is-my-cost-showing-as-0"></a>Varför visas min kostnad som 0 USD?

För kunder med **direktregistrering** kan företagsadministratörer ge kontoägare och avdelningsadministratörer tillgång till information om kostnad/pris i användningsrapporterna. Följ de här stegen:

1. I Azure Enterprise-portalen väljer du **Manage** (Hantera) i det vänstra navigeringsfältet.
1. Välj den blå pennan bredvid Visa avgifter för DA (avdelningsadministratör).
1. Välj **Aktiverad** och spara.
1. Välj den blå pennan bredvid Visa avgifter för AO (kontoägare).
1. Välj **Aktiverad** och spara.

> [!NOTE]
> Om du är kontoägare eller avdelningsadministratör kontaktar du företagsadministratören för att aktivera prissättningsfunktionen.

Om du är kund med **indirekt registrering** kontaktar du din partner och kontrollerar om de har aktiverat prissättningsfunktionen åt dig. Detta kan endast göras av en partner. När de har aktiverat funktionen kan du visa kostnader och priser för din registrering som en företagsadministratör.

Partner som vill aktivera funktionen för att visa avgifter för en kontoägare eller avdelningsadministratör kan följa stegen under **direktregistrering**.

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>Varför finns det ingen SKU-information i rapporten för användningsinformation?

Det finns ingen SKU-information i rapporten för användningsinformation. Men däremot innehåller rapporten användningsinformation, vilket innebär att du kan få tag på SKU-informationen genom att ladda ned prisdokumentrapporten.

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>Varför stämmer inte totalbeloppet i Azure Marketplace överens med rapporterna för användningssammanfattning och användningsinformation?

Rapporten för Azure Marketplace-debiteringar visar endast användningsbaserade debiteringar. Engångsavgifter visas inte. Du kan se de senaste användningsbaserade avgifterna och engångsavgifterna på sidan med användningssammanfattningen.

### <a name="why-is-there-no-information-on-my-api-report"></a>Varför finns det ingen information i min API-rapport?

API-nycklar upphör att gälla var sjätte månad. Be företagsadministratören generera en ny API-nyckel om du har problem. Kom ihåg att följa stegen i vanliga frågor och svar om API-rapporter.

### <a name="why-isnt-my-power-bi-report-working"></a>Varför fungerar inte min Power BI-rapport?

Om du har problem med Power BI loggar du en begäran om teknisk support till [Power BI-supportteamet](https://support.powerbi.com).

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>Varför visas inte resurstaggar i mina rapporter?

Resurstaggar hanteras i Azure-portalen. Du kan kontakta Azure-prenumerationsteamet på [Azure-portalen](https://portal.azure.com). Följ stegen i artikeln [Skapa en supportbegäran för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

### <a name="why-does-my-resource-rate-change-every-day"></a>Varför ändras mitt resurspris varje dag?

Resurspriset i den detaljerade användningsrapporten är ett beräknat värde. Det representerar det genomsnittliga månadspriset som debiteras för en tjänst. Resurspriset beräknas med hjälp av genomsnittet av det månatliga åtagandet och de månatliga överförbrukningsavgifterna för en tjänstenhet. Den del av användningen som debiteras mot åtagande och överförbrukningsavgifter ändras till dagen då månaden slutar. Detta innebär att även resurspriset ändras under månaden. Resurspriser låses den femte dagen efter månadens slut.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Ordlista för processer för beräkning av resurspris

- **Totalt antal RAW-enheter:** Använd kvantitet i den detaljerade användningsrapporten.
- **MOCP-resurs per enhet:** Upstream Usage System anger användning för varje tjänst i olika enheter. (Förinställning)
- **Förbrukning per enhet:** Azure Enterprise-måttenhet. (Förinställning)
- **Pris:** Enhetspris från Azure Enterprise-portalen.
- **Total kostnad:** Utökad kostnad från den detaljerade användningsrapporten eller utnyttjat åtagande plus överförbrukning från Azure Enterprise-portalen.

### <a name="charges-calculations"></a>Avgiftsberäkningar

- **Konvertering till förbrukade MOCP-resurser** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **Konvertering till förbrukade enheter** = `Consumed MOCP Resources / Consumption per Unit`
- **Beräkning av total kostnad** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>Logiken för beräkning av användning

**Resurspris** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

Resurspriset beräknas utifrån dina avgifter. Det kanske inte stämmer överens med det faktiska enhetspriset i prisdokumentet.

I den nedladdningsbara rapporten med användningsdata kan du se rådata för resursanvändning med upp till sex decimaler. Dessa data används för beräkning av överförbrukningsavgifter. Men användningsdata som visas i Azure Enterprise-portalen avrundas till fyra decimaler för åtagandeenheter och trunkeras till noll decimaler för överförbrukningsenheter. I Azure Enterprise-portalen debiterar vi endast för fullständiga enheter för all användning som debiteras som överförbrukning. Skillnaden kan vara stor mellan enhetspriset och resurspriset för den användning som debiteras som överförbrukning eller vid blandade månader.

## <a name="next-steps"></a>Nästa steg

- I Excel-filerna nedan finns information om Azure-tjänster. Informationen uppdateras den 6:e och 20:e i varje månad.

   | Titel | Beskrivning | Filnamn |
   | --- | --- | --- |
   | [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Visar aktiva tjänster och innefattar: <br>  <ul><li>tjänstekategori</li>   <li>beskrivande namn på tjänst</li>   <li>åtagandenamn och artikelnummer</li> <li>förbrukningsnamn och artikelnummer</li>   <li>måttenhet</li>   <li>konverteringsfaktorer mellan den rapporterade användningen och användningen som visas i Enterprise-portalen</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Service Download Fields](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Det här kalkylbladet innehåller en lista över alla möjliga kombinationer av tjänsterelaterade fält i den nedladdningsbara användningsrapporten. | Service\_Download\_Fields.xlsx |

- Information om hur du tolkar din faktura och dina kostnader finns i [Förstå fakturan för ditt Azure Enterprise-avtal](../understand/review-enterprise-agreement-bill.md).
- Om du vill börja använda Azure Enterprise-portalen kan du läsa [Komma igång med Azure EA-portalen](ea-portal-get-started.md).
