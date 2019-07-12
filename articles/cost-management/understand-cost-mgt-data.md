---
title: Förstå Azure Cost Management-data | Microsoft Docs
description: Den här artikeln hjälper dig att bättre förstå vilka data som ingår i Azure Cost Management och hur ofta den bearbetas, samlas in, visas och stängda.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 44b95c92f51ca9782fca492f3dec3142087ecc91
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797020"
---
# <a name="understand-cost-management-data"></a>Förstå Cost Management-data

Den här artikeln hjälper dig att bättre förstå Azure-kostnader och användning data som ingår i Azure Cost Management. Den förklarar hur ofta data bearbetas som samlas in, visas och stängda. Du faktureras för användningen av Azure varje månad. Även om faktureringscykel är Månadsperioder, cykelstart och slut-datum varierar beroende på typ av prenumeration. Hur ofta kostnadshantering tar emot användning data varierar baserat på olika faktorer. Sådana faktorer är hur lång tid det tar för att bearbeta data och hur ofta användningen av faktureringssystemet generera i Azure-tjänster.

Kostnadshantering omfattar all användning och inköp, inklusive reservationer och erbjudanden från tredje part för Enterprise Agreement (EA)-konton. Avtal för Microsoft-kund (MCA)-konton och enskilda prenumerationer med användningsbaserad betalning innefattar endast användning från Azure och Marketplace-tjänster. Support och andra kostnader ingår inte. Kostnader beräknas tills en faktura genereras och inte ta med i kredit.

## <a name="supported-microsoft-azure-offers"></a>Microsoft Azure-erbjudanden som stöds

Följande information visar för närvarande stöds [Microsoft Azure erbjuder](https://azure.microsoft.com/support/legal/offer-details/) i Azure Cost Management. Ett Azure-erbjudande är typ av Azure-prenumerationen som du har. Data är tillgängliga i Cost Management från och med den **Data är tillgängliga från** datum. Om en prenumeration ändras erbjudanden kostnaderna innan ändringsdatum för erbjudandet inte tillgängligt. 

| **Kategori**  | **Erbjudandets namn** | **Kvot-ID.** | **Erbjudandets nummer** | **Data som är tillgängliga från** |
| --- | --- | --- | --- | --- |
| **Azure Germany** | [Azure Tyskland betala per användning](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P | Den 2 oktober 2018<sup>2</sup> |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Maj 2014<sup>1</sup> |
| **Enterprise-avtal (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Maj 2014<sup>1</sup> |
| **Enterprise-avtal (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Maj 2014<sup>1</sup> |
| **Microsoft kundavtal** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | Gäller inte | Mar 2019<sup>3</sup> |
| **Microsoft kundavtal** | [Microsoft Azure-Plan för utveckling och testning](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | Gäller inte | Mar 2019<sup>3</sup> |
| **Microsoft Developer Network (MSDN)** | [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | Den 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | Den 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Betala per användning – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | Den 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | Den 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | Den 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Azure i Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | Den 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Azure för studenter](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P | Den 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | Azure-Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | Den 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | Den 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | Den 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | Den 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | Den 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | Den 2 oktober 2018<sup>2</sup> |

_<sup>**1** </sup> data innan maj 2014, finns det [Azure Enterprise portal](https://ea.azure.com)._

_<sup>**2** </sup> data innan den 2 oktober 2018, finns det [Azure Kontocenter](https://account.azure.com/subscriptions)._

_<sup>**3** </sup> Microsoft Customer avtal igång på Mar 2019 och inte har några historiska data innan den här punkten._

_<sup>**4** </sup> historiska data för prenumerationer kredit-baserade och betala i förskott kanske inte matchar din faktura. Se [historiska data kanske inte matchar faktura](#historical-data-might-not-match-invoice) nedan._

I följande tabell visar erbjudanden som inte stöds ännu.

| Category  | **Erbjudandets namn** | **Kvot-ID.** | **Erbjudandets nummer** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure Tyskland betala per användning](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Leverantörer av Molnlösningar (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Leverantörer av Molnlösningar (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Leverantörer av Molnlösningar (CSP)** | Azure Tyskland i CSP för Microsoft Cloud i Tyskland   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Betala per användning**                 | Microsoft Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Betala per användning**                 | [Microsoft Azure sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Supportavtal** | Standard Support                    | Default_2014-09-01 | MS-AZR-0041P |
| **Supportavtal** | Professional Direct-support         | Default_2014-09-01 | MS-AZR-0042P |
| **Supportavtal** | Utvecklarsupport                   | Default_2014-09-01 | MS-AZR-0043P |
| **Supportavtal** | Tyskland supportavtal                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Supportavtal** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Supportavtal** | Azure Government Pro-Direct Support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Supportavtal** | Azure Government Developer Support  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Fastställa erbjudandetypen av ditt
Om du inte ser data för en prenumeration och du vill fastställa om din prenumeration omfattas stöds erbjudanden, kan du verifiera att det finns stöd för din prenumeration. För att verifiera att det finns stöd för en Azure-prenumeration kan logga in på den [Azure-portalen](https://portal.azure.com). Välj sedan **alla tjänster** i den vänstra meny-rutan. Välj i listan över tjänster, **prenumerationer**. Klicka på den prenumeration som du vill verifiera i listmenyn prenumeration. Din prenumeration visas på fliken Översikt och du kan se den **erbjuder** och **erbjudande-ID**. I följande bild visas ett exempel.

![Exempel på fliken prenumeration översikt som visar erbjudandet och erbjudande-ID](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Kostnader i Cost Management

Följande tabeller visar data som har inkluderat eller inte finns i Cost Management. Alla kostnader beräknas tills en faktura genereras. Kostnaderna som visas omfattar inte kostnadsfri och förbetald kredit.

**Kostnad-och användningsdata**

| **Ingår** | **Ingår inte** |
| --- | --- |
| Azure tjänstanvändning<sup>5</sup>        | Hantera kostnader – mer information, se [villkoren för förklaras](../billing/billing-understand-your-invoice.md). |
| Marketplace erbjuder användning<sup>6</sup> | Skatter - mer information finns i [villkoren för förklaras](../billing/billing-understand-your-invoice.md). |
| Marketplace-köp<sup>6</sup>      | Krediter - mer information finns i [villkoren för förklaras](../billing/billing-understand-your-invoice.md). |
| Köp av reservation<sup>7</sup>      |  |

_<sup>**5** </sup> azure tjänstanvändning baseras på reservation och förhandlas priser._

_<sup>**6** </sup> marketplace användnings- och inköp är inte tillgängliga för användningsbaserad betalning, MSDN, och Visual Studio erbjuder just nu._

_<sup>**7** </sup> köp av reservation är bara tillgängligt för Enterprise Agreement (EA) konton just nu._

**Metadata**

| **Ingår** | **Ingår inte** |
| --- | --- |
| Resurstaggar<sup>8</sup> | gruppen resurstaggar |

_<sup>**8** </sup> resurstaggar tillämpas enligt användning som sänts ut från varje tjänst och är inte tillgängliga retroaktivt för historisk användning._

## <a name="rated-usage-data-refresh-schedule"></a>Graderad användning datauppdateringsschemat

Kostnader och användning data är tillgängliga i kostnadshantering + fakturering i Azure-portalen och [stöd API: er](index.yml). Tänk på följande när du granskar kostnader:

- Uppskattade kostnader för den aktuella faktureringsperioden uppdateras sex gånger per dag.
- Uppskattade kostnader för den aktuella faktureringsperioden kan ändras när du använder mer användning.
- Varje uppdatering är kumulativ och innehåller alla rader och information från den tidigare uppdateringen.
- Slutför Azure eller _stängs_ den aktuella faktureringsperioden upp till 72 timmar (tre dagar) efter det fakturering löpt ut.

I följande exempel visas hur faktureringsperioder slut.

Enterprise Agreement (EA) prenumerationer – uppskattade om faktureringsmånaden som upphör den 31 mars kostnader uppdateras upp till 72 timmar senare. I det här exemplet genom att midnatt (UTC) 4 April.

Prenumerationer med användningsbaserad betalning – om den aktuella faktureringsmånaden upphör den 15 maj och sedan de uppskattade kostnader kan uppdateras upp till 72 timmar senare. I det här exemplet genom att midnatt (UTC) maj 19.

### <a name="rerated-data"></a>Rerated data

Om du använder den [Cost Management API: er](index.yml), Power BI eller Azure-portalen för att hämta data, förväntar du dig den aktuella faktureringsperioden avgifter för att hämta klassificerade igen, och därmed ändra tills fakturan stängs.

## <a name="usage-data-update-frequency-varies"></a>Uppdateringsfrekvensen för användning data varierar

Tillgängligheten för användningsdata kostnader i Cost Management är beroende av ett antal faktorer, bland annat:

- Hur ofta generera Azure-tjänster (till exempel lagring, beräkning, CDN och SQL) användning.
- Den tid det tar att bearbeta användningsdata via motorn för klassificering och cost management pipelines.

Vissa tjänster sända användning oftare än andra. Därför kan du se data i Cost Management för vissa tjänster snabbare än andra tjänster som genererar data mindre ofta. Användning för tjänster som tar normalt, 8 – 24 timmar ska visas i Cost Management. Tänk på dessa data för en öppen månad överföringsskärmen uppdateras när du använder mer användning eftersom uppdateringar är kumulativa.

## <a name="historical-data-might-not-match-invoice"></a>Historiska data kanske inte överensstämmer med faktura

Historiska data för kredit-baserade och betala i förskott kanske inte överensstämmer med din faktura. Vissa Azure betala per användning, MSDN och Visual Studio erbjuder kan har använt Azure-krediter och avancerade betalningar för fakturan. Historiska data som visas i Cost Management är dock baserat på dina uppskattade förbrukningsavgifter. Cost Management historiska data omfattar inte betalningar och krediter. Historiska data som visas för följande erbjudanden kanske därför inte matchar exakt med din faktura.

- Azure för studenter (MS-AZR - 0170P)
- Azure i Open (MS-AZR - 0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Kostnadsfri utvärderingsversion (MS-AZR - 0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Se också

- Om du inte har redan slutfört den första snabbstarten för kostnadshantering, kan du läsa den på [börja analysera kostnaderna](quick-acm-cost-analysis.md).
