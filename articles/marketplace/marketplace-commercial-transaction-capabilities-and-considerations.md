---
title: Marketplace affärstransaktion funktioner och överväganden | Azure
description: Den här artikeln beskriver Transact priser, fakturering, fakturering och betalnings överväganden för en typ av erbjudande.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: d266b314f19979578b7e7b8de4e7a7090200c9d2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445442"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Kommersiella marketplace transaktion funktioner och överväganden

Den här artikeln beskriver följande commerce-relaterade ämnen för kommersiella marketplace

* Marketplace publiceringsalternativ
* Transact allmän översikt
* Transact faktureringsmodeller
* Transact krav

## <a name="marketplace-publishing-options"></a>Marketplace publiceringsalternativ

Följande publiceringsalternativ är tillgänglig för kommersiella marketplace-utgivare.

### <a name="list--trial-publishing-options"></a>Lista & utvärderingsversion publiceringsalternativ

Utgivare kan använda i listan, utvärdering och BYOL publiceringsalternativ för erbjudanden och användaren förvärv syften. Microsoft deltar inte direkt i utgivarens programvara licens transaktioner med dessa alternativ, och det finns inga associerade transaktionsavgift. Utgivare har ansvaret för att stödja alla aspekter av programvara licens transaktioner, inklusive men inte begränsat till: ordning, utförande, Avläsning av programvara, fakturering, fakturering, betalning och samling. Med i listan och utvärderingsversion publiceringsalternativ Behåll utgivare 100% av utgivare licensavgifter som samlas in från kunden. 

### <a name="transact-publishing-option"></a>Transact publiceringsalternativ

Förutom i listan och utvärderingsversion publiceringsalternativ är transact publiceringsalternativ tillgängliga för utgivare. Detta drar nytta av Microsofts globalt tillgänglig commerce funktioner och kan Microsoft värd molnet marketplace transaktioner för utgivaren.

## <a name="transact-general-overview"></a>Transact allmän översikt

När du använder transact publicera alternativet, kan Microsoft försäljningen av programvara från tredje part och distribution av vissa typer av erbjudanden till kundens Azure-prenumeration. Utgivaren måste ta hänsyn till att faktureringen för infrastrukturavgifter, och de utgivarens egna licensavgifter, när du väljer en fakturering modellera och erbjudandetyp.

Transact publiceringsalternativ stöds för tillfället för följande typer av erbjudanden: Virtuella datorer, Azure-program och SaaS-appar.


![[Hanterar man gör transaktioner Enterprise på Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Fakturering infrastrukturkostnader

**För virtuella datorer och Azure-program**

För virtuella datorer och program i Azure faktureras Användningsavgift Azure-infrastrukturen till kundens Azure-prenumeration.  Avgifter för användning av infrastruktur prissätts och visas separat från programvaran leverantörens licensavgifter på kundens faktura.

**För SaaS-appar**

För SaaS-appar på utgivaren måste ta hänsyn till avgifter för användning av Azure-infrastrukturen och licensavgifter som ett enda kostnaden-objekt.  Det representeras som en fast avgift för kunden. Azure-infrastrukturen användningen hanteras och debiteras för partnern direkt.  Faktisk användning infrastrukturavgifter ses inte av kunden.  Utgivare välja vanligtvis för att bifoga avgifter för användning av Azure-infrastrukturen i deras programvara licens priser.  Programvara för licensavgifter inte förbrukade eller konsumtionsbaserat.

## <a name="transact-billing-models"></a>Transact faktureringsmodeller

Beroende på Transaktionsalternativet används kan utgivarens programvarulicensavgifter visas på följande sätt:  

* Kostnadsfritt: Ingen extra kostnad för programvarulicenser. 

* Använd din egen licens (BYOL): Eventuella tillämpliga kostnader för programvarulicenser hanteras direkt mellan utgivare och kunden. Microsoft skickar endast via avgifter för användning av Azure-infrastrukturen. (Virtuella datorer och Azure-program endast).

* Pay-as-you-go: Programvarulicensavgifter visas som en per timme, per kärna (vCPU) prissättning baserad på Azure-infrastrukturen används. Detta gäller endast för virtuella datorer och program i Azure.

* • Prenumerationspriser: Programvarulicensavgifter visas som en per månad eller årlig, återkommande avgift debiteras som en fast avgift eller per klient. Detta gäller endast för SaaS-appar och program i Azure-hanterade appar.

* Kostnadsfri utvärderingsversion: Ingen extra kostnad för programvarulicenser för 30 dagar eller 90 dagar.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Kostnadsfria och bring-your-own-license (BYOL) prissättning

När du publicerar ett erbjudande med kostnadsfri eller bring-your-own-license transaktion kan upp Microsoft inte en roll i att underlätta för dina programvarulicenser försäljning transaktionen. Som i listan och utvärderingsversion publiceringsalternativ behåller utgivaren 100% av programvarulicenser. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Betala per användning och prenumerationspriser (plats-baserad)

WPay-som-du-go och prenumerationspriser när du publicerar ett erbjudande med betala per användning eller prenumeration transaktion, Microsoft tillhandahåller tekniken och tjänster för att bearbeta programvarulicensen köp, returnerar och återbetalningar. I det här scenariot godkänner utgivaren att Microsoft ska fungera som en agent för dessa ändamål. Utgivaren kan Microsoft för att underlätta transaktion samtidigt som du behåller deras beteckning som försäljning, leverantör, distributören och licensgivare för programvarulicenser.

Microsoft ger kunder möjlighet att ordning, licens och Använd utgivarens programvara, utsätta till villkoren i Microsofts kommersiella Marketplace såväl utgivarens licensavtalet för slutanvändare. Utgivare måste ange sina licensavtalet för slutanvändare och välja den [standardavtal](https://docs.microsoft.com/azure/marketplace/standard-contract) när du skapar erbjudandet.


### <a name="free-software-trials"></a>Kostnadsfri programvara utvärderingsversioner

För transact publishing scenarier, utgivaren kan göra en programvara som licensierar tillgängliga utan kostnad i 30 dagar eller 90 dagar. Den här discounting funktionen inkluderar inte kostnaden för användning av Azure-infrastrukturen som drivs med hjälp av Partnerlösningen.

### <a name="private-offers"></a>Privata erbjudanden

Förutom att använda erbjuder typer och faktureringsmodellerna att tjäna pengar på ett erbjudande, utgivare kan hantera ett privat erbjudande med förhandlade, erbjudande-specifika prissättning eller anpassade konfigurationer. Privata erbjudanden som stöds av alla 3 transact publiceringsalternativ.

Det här alternativet kan priset för högre eller lägre än offentligt tillgängliga erbjudandet. Privata erbjudanden kan användas till att rabatt eller Lägg till en premium i ett erbjudande. Privata erbjudanden kan göras tillgängliga för kunder med en eller flera av vitt lista över sin Azure-prenumeration på erbjudandet nivå.


### <a name="examples"></a>Exempel

**Betala per användning** 

* Om du aktiverar alternativet betala per användning kan ha du följande kostnadsstruktur.

|Din licens kostnad  | $1.00 per timme  |
|---------|---------|
|Användningen av Azure-kostnad (D1/1-kärna)    |   $0.14 per timme     |
|*Faktureras kunden av Microsoft*    |  *$1.14 per timme*       |

* Microsoft fakturerar $1.14 per timme för användningen av dina publicerad virtuell datoravbildning i det här scenariot.

|Microsoft fakturor  | $1.14 per timme  |
|---------|---------|
|Microsoft betalar du 80% av dina kostnader för licens|   $0.80 per timme     |
|Microsoft behåller 20% av dina kostnader för licens  |  $0.20 per timme       |
|Microsoft behåller 100% av kostnaden för Azure-användning | $0.14 per timme |

**Använd din egen licens (BYOL)**

* Om du aktiverar alternativet BYOL kan ha du följande kostnadsstruktur.

|Din licens kostnad  | Licensavgiften förhandlas och faktureras av dig  |
|---------|---------|
|Användningen av Azure-kostnad (D1/1-kärna)    |   $0.14 per timme     |
|*Faktureras kunden av Microsoft*    |  *$0.14 per timme*       |

* Microsoft fakturerar $0.14 per timme för användningen av dina publicerad virtuell datoravbildning i det här scenariot.

|Microsoft fakturor  | $0.14 per timme  |
|---------|---------|
|Microsoft behåller kostnaden för Azure-användning    |   $0.14 per timme     |
|Microsoft behåller 0% av dina kostnader för licens   |  $0,00 per timme       |

**SaaS-App-prenumeration**

Det här alternativet måste konfigureras för att sälja via Microsoft och kan skilja sig enligt en fast avgift eller per användare per månad eller årlig.
• Om du aktiverar sälj via Microsoft-alternativ för ett SaaS-erbjudande du har följande kostnadsstrukturen.

|Din licens kostnad       | $100,00 per månad  |
|--------------|---------|
|Användningen av Azure-kostnad (D1/1-kärna)    | Faktureras direkt till utgivaren inte kunden |
|*Faktureras kunden av Microsoft*    |  *$100,00 per månad (Obs: utgivaren måste ta hänsyn till några kostnader eller direkt infrastrukturkostnader i licensavgiften)*  |

* I det här scenariot Microsoft fakturerar $100,00 för din programvarulicensen och betalar ut $80.00 till utgivaren.
* Partner som har kvalificerat för den minskar serviceavgift för Marketplace kan se en minskad transaktionsavgift på SaaS erbjuder från maj 2019 fram till juni 2020. I det här scenariot Microsoft fakturerar $100,00 för din programvarulicensen och betalar ut $90.00 till utgivaren.

|Microsoft fakturor  | $100,00 per månad  |
|---------|---------|
|Microsoft betalar du 80% av dina kostnader för licens <br> \* Microsoft betalar du 90% av din licenskostnaden för alla kvalificerade SaaS-appar   |   $80.00 per månad <br> \* $90.00 per månad    |
|Microsoft behåller 20% av dina kostnader för licens <br> \* Microsoft håller 10% av din licenskostnaden för alla kvalificerade SaaS-appar.  |  20,00 per månad <br> \* $10.00     |

* **Minskad Marketplace serviceavgift:** För vissa SaaS-produkter som du publicerar på vår kommersiella Marketplace Microsoft minskar dess serviceavgift för Marketplace från 20% (enligt beskrivningen i Publiceringsavtalet för Microsoft) till 10%.  För din produkt att kvalificera minst en av dina produkter kan bara vara av Microsoft som antingen IP medförsäljning redo eller IP-medförsäljning prioriteras. Kvalificering måste vara uppfyllda minst fem (5) arbetsdagar innan slutet av denna kalendermånad för att ta emot den här minskade serviceavgift för Marketplace för månaden. Minskar avgift inte gäller för virtuella datorer, hanterade appar eller andra produkter som görs tillgängliga via vår kommersiella Marketplace Marketplace-tjänsten.  Det här minskar serviceavgift för Marketplace blir tillgänglig för kvalificerade erbjudanden med licensavgifter som samlas in av Microsoft mellan den 1 maj 2019 och den 30 juni 2020.  Efter den tiden returnerar serviceavgift för Marketplace till dess normala mängden.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Kundfakturering, betalning, fakturering och samlingar

**Fakturering och betalning**

Utgivare kan använda kundens önskade fakturering metoden för att leverera prenumeration eller programvarulicensavgifter för betalning per användning.

**Enterprise-avtal** 

Om kundens önskade Faktureringsmetoden är Microsoft Enterprise Agreement, debiteras din programvarulicensavgifter med det här fakturering metod som en specificerad kostnad separat från förbrukningskostnad specifikt om Azure.

**Kreditkort och månadsfaktura** 

Kunder kan också betala med kreditkort och ett månadsfaktura. I det här fallet faktureras dina programvarulicenser precis som Enterprise Agreement-scenariot som en specificerad kostnad separat från förbrukningskostnad specifikt om Azure.

Till exempel, om kunden köper med ett kreditkort:

|Beskrivning    |    Date  |
|----------|----------|
|Order-Period   | Den 15 augusti 2018-30 augusti 2018 |
|Termen slutar (månad)   | Aug 30, 2018 |
|Faktureringsdatum | Den 1 september 2018 |
|Kunden betalningsdatum | Den 1 september 2018 |
|Deponera Period (kreditkort, 30 dagar) | Den 1 september 2018 – 30 september 2018 |
|Samling periodens starttid | Den 1 september 2018 |
|Samling periodslut (maximalt 30 dagar) | Den 30 september 2018 |
|Datum för Fakturaberäkning payout (per månad på den 15: e) | Den 1 oktober 2018 |
|Payout datum | Den 15 oktober 2018 |

Om kunden köper med hjälp av ett Enterprise-avtal:

| Beskrivning |    Date  |
|----------|----------|
|Order-Period | Den 15 augusti 2018-30 augusti 2018 |
|Termen slutar (kvartal) | Den 30 september 2018 |
|Faktureringsdatum | Den 15 oktober 2018 |
|Deponera Period (kreditkort, 30 dagar) | Saknas |
|Samling periodens starttid | Den 15 oktober 2018 |
|Samling periodslut (maximalt 90 dagar) | 15 jan 2019 |
|Kunden betalningsdatum | Den 30 december 2018 |
|Datum för Fakturaberäkning payout (per månad på den 15: e) | 15 jan 2019 |
|Payout datum | Den 15 februari 2019 |

**Kostnadsfria krediter och åtagandebelopp** 

Vissa kunder väljer att betala i förskott Azure med en summa i Enterprise-avtal eller har angetts kostnadsfria krediter för användning med Azure. Även om dessa krediter kan användas för att betala för användningen av Azure, kan inte de användas för att betala för utgivarens programvarulicensavgifter.

**Fakturerings- och samlingar** 

Utgivarens programvara licens fakturering visas med hjälp av metoden kund som valts för fakturering och följer fakturering tidslinje. Kunder utan Enterprise-avtal på plats faktureras varje månad för marketplace programvarulicenser. Kunder med ett Enterprise-avtal faktureras varje månad via en faktura som presenteras kvartalsvis.

När prenumerationen eller betala per användning prismodellerna är markerade, Microsoft fungerar som agenten utgivarens och ansvarar för alla aspekter av fakturering och betalning samling.

### <a name="publisher-payout-and-reporting"></a>Utgivaren betalnings- och rapportering

* Alla licensavgifter samlas in av Microsoft som en agent omfattas av en 20% transaktionsavgift om inget annat anges och dras vid tidpunkten för utgivaren betalnings.

* Kunder köpa vanligtvis med Enterprise-avtal eller ett kreditkort aktiverade användningsbaserad avtal. Avtalstypen anger fakturering, fakturering, samling och betalnings tidsinställningar.

>[!NOTE] 
>Alla rapporter och insikter för transact publicera alternativ är tillgängliga via avsnittet insikter i avsnittet partnerportalen i molnet eller analys av Partner Center.

#### <a name="billing-questions-and-support"></a>Frågor om fakturering och support

Mer information och juridiska principer finns i den [Publiceringsavtalet](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (tillgängligt i Cloud Partner Portal).

Om du vill ha hjälp om fakturering frågor, kontakta [kommersiella marketplace publisher support](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact krav

Transact-kraven för olika erbjudandetyper beskrivs i det här avsnittet.

### <a name="requirements-for-all-offer-types"></a>Krav för alla erbjuder typer

- Ett Microsoft-konto och finansiell information krävs för transact publicera alternativet, oavsett erbjudandet prismodell.
- Obligatorisk finansiell information innehåller betalnings-konto och skatt profil.

Mer information om hur du konfigurerar dessa konton finns i [hantera Your Partner Center Account](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Krav för specifika erbjudandetyper

Transact publiceringsalternativ är bara tillgängligt för användning med följande typer av marketplace-erbjudanden: 

**Virtuell dator** 

Välj från modeller som ledigt, bring-your-own-license eller betala per användning-as-you-go-priser och presentera som SKU: er som definierats på nivån erbjudandet. På kundens Azure-faktura anger Microsoft utgivarens programvarulicensavgifter separat från de underliggande infrastruktur med Azure-avgifterna. Azure-infrastrukturavgifter styrs av användning av utgivarens programvara.

**Azure-program: Lösningsmallen eller hanterad App** 

Etablera en eller flera virtuella datorer och hämtar via summan av priser för virtuella datorer. För hanterade appar på ett enda abonnemang kan du välja en fast månatlig prenumeration enligt priserna modellera i stället priser för virtuella datorer. I vissa fall skickas avgifter för användning av Azure-infrastrukturen till kunden separat från programvarulicensavgifter, men samma debiteras på. Men om du konfigurerar en hanterad App erbjudande för ISV-infrastrukturavgifter, Azure-resurser faktureras till utgivaren och kunden får en fast avgift som inkluderar kostnaden för infrastrukturen, licenser för program och tjänster.

## <a name="next-steps"></a>Nästa steg

* Granska krav för berättigande i publiceringsalternativ erbjudandet typ avsnittet för att slutföra valet av och konfigurationen av ditt erbjudande.
* Granska publicering mönster genom butik för exempel på hur din lösning mappar till en typ av erbjudande och konfiguration.
