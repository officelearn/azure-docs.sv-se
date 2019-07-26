---
title: Förstå Azure Cost Management data | Microsoft Docs
description: Den här artikeln hjälper dig att bättre förstå data som ingår i Azure Cost Management och hur ofta de bearbetas, hämtas, visas och stängs.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 8aaaa6465b501cee83f4c2d8cb60729282651967
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384849"
---
# <a name="understand-cost-management-data"></a>Förstå Cost Management-data

Den här artikeln hjälper dig att få bättre förståelse för Azures kostnads-och användnings data som ingår i Azure Cost Management. Det förklarar hur ofta data bearbetas, samlas in, visas och stängs. Du faktureras för Azure-användning varje månad. Även om fakturerings cykler är månads perioder varierar cykelns start-och slutdatum efter prenumerations typ. Hur ofta Cost Management tar emot användnings data varierar beroende på olika faktorer. Dessa faktorer omfattar hur lång tid det tar att bearbeta data och hur ofta Azure-tjänster genererar användning till fakturerings systemet.

Cost Management omfattar all användning och alla inköp, inklusive reservationer och erbjudanden från tredje part för Enterprise-avtal-konton (EA). Microsoft Customer Agreement-konton (MCA) och enskilda prenumerationer med priser enligt principen betala per användning omfattar bara användning från Azure-och Marketplace-tjänster. Support och andra kostnader ingår inte. Kostnaderna beräknas tills en faktura genereras och inte är en faktor i kredit.

## <a name="supported-microsoft-azure-offers"></a>Microsoft Azure erbjudanden som stöds

Följande information visar de [Microsoft Azure erbjudanden](https://azure.microsoft.com/support/legal/offer-details/) som stöds för närvarande i Azure Cost Management. Ett Azure-erbjudande är den typ av Azure-prenumeration du har. Data är tillgängliga i Cost Management som startar på **tillgängliga data från** datum. Om en prenumeration ändras får kostnader innan det ändrings datumet för erbjudandet inte är tillgängliga. 

| **Kategori**  | **Erbjudandets namn** | **Kvot-ID** | **Erbjudande nummer** | **Data tillgängliga från** |
| --- | --- | --- | --- | --- |
| **Azure Germany** | [Azure Germany – betala per användning](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P | 2 oktober 2018<sup>2</sup> |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Maj 2014<sup>1</sup> |
| **Enterprise-avtal (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Maj 2014<sup>1</sup> |
| **Enterprise-avtal (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Maj 2014<sup>1</sup> |
| **Microsofts kund avtal** | [Microsoft Azure plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | Gäller inte | Mar 2019<sup>3</sup> |
| **Microsofts kund avtal** | [Microsoft Azure plan för utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | Gäller inte | Mar 2019<sup>3</sup> |
| **Microsoft Developer Network (MSDN)** | [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p) <sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Betala per användning – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Kostnads fri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p) <sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Azure i Open](https://azure.microsoft.com/offers/ms-azr-0111p) <sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | [Azure för studenter](https://azure.microsoft.com/offers/ms-azr-0170p) <sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P | 2 oktober 2018<sup>2</sup> |
| **Betala per användning** | Azure-pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p) <sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p) <sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 oktober 2018<sup>2</sup> |

_<sup>**1**</sup> för data före maj 2014 går du till [Azure Enterprise Portal](https://ea.azure.com)._

_<sup>**2**</sup> för data före den 2 oktober 2018 går du till [Azure-kontocenter](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> kund avtal från Microsoft startades i Mar 2019 och har inga historiska data innan den här punkten._

_<sup>**4**</sup> historiska data för kreditbaserade och betala i förväg-prenumerationer kanske inte matchar din faktura. Se [historiska data kanske inte överensstämmer med fakturan](#historical-data-might-not-match-invoice) nedan._

Följande erbjudanden stöds inte än:

| Category  | **Erbjudandets namn** | **Kvot-ID** | **Erbjudande nummer** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure Germany – betala per användning](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Leverantör av moln lösningar (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Leverantör av moln lösningar (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Leverantör av moln lösningar (CSP)** | Azure Tyskland i CSP för Microsoft Cloud i Tyskland   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Betala per användning**                 | Azure för studenter Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Betala per användning**                 | [Microsoft Azure-sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Support avtal** | Standard Support                    | Default_2014-09-01 | MS-AZR-0041P |
| **Support avtal** | Professional Direct support         | Default_2014-09-01 | MS-AZR-0042P |
| **Support avtal** | Support för utvecklare                   | Default_2014-09-01 | MS-AZR-0043P |
| **Support avtal** | Plan för Tyskland-support                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Support avtal** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Support avtal** | Azure Government Pro-Direct-support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Support avtal** | Azure Government Developer Support  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Fastställ din erbjudande typ
Om du inte ser data för en prenumeration och vill ta reda på om din prenumeration omfattas av de erbjudanden som stöds, kan du kontrol lera att din prenumeration stöds. Om du vill kontrol lera att en Azure-prenumeration stöds loggar du in på [Azure Portal](https://portal.azure.com). Välj sedan **alla tjänster** i den vänstra meny rutan. I listan över tjänster väljer du **prenumerationer**. I menyn prenumerations lista klickar du på den prenumeration som du vill verifiera. Din prenumeration visas på fliken Översikt och du kan se **erbjudandet** och **erbjudande-ID**. I följande bild visas ett exempel.

![Exempel på fliken prenumerations översikt som visar erbjudande och erbjudande-ID](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Kostnader som ingår i Cost Management

Följande tabeller visar data som ingår eller som inte finns i Cost Management. Alla kostnader beräknas tills en faktura genereras. De kostnader som visas omfattar inte kostnads fria och förutbetalda krediter.

**Kostnader och användnings data**

| **Inbegrip** | **Ingår inte** |
| --- | --- |
| Azure Service-användning<sup>5</sup>        | Support avgifter – mer information finns i [förklaring av faktura villkor](../billing/billing-understand-your-invoice.md). |
| Användning av Marketplace<sup>6</sup> | Skatter – mer information finns i [förklaring av faktura villkor](../billing/billing-understand-your-invoice.md). |
| Marketplace-köp<sup>6</sup>      | Krediter – mer information finns i [förklaring av faktura villkor](../billing/billing-understand-your-invoice.md). |
| Reservations köp<sup>7</sup>      |  |
| Amortering av reservations köp<sup>7</sup>      |  |

_<sup>**5**</sup> användning av Azure-tjänster baseras på reservationer och förhandlade priser._

_<sup>**6**</sup> marknads plats köp är inte tillgängliga för erbjudandet betala per användning, MSDN och Visual Studio för närvarande._

_<sup>**7**</sup> reservations köp är bara tillgängliga för Enterprise-avtal-konton (EA) för tillfället._

**Metadata**

| **Inbegrip** | **Ingår inte** |
| --- | --- |
| Resurs etiketter<sup>8</sup> | Resurs grupps Taggar |

_<sup>**8**</sup> resurs Taggar används när användning genereras från varje tjänst och är inte tillgänglig retroaktivt för historisk användning._

## <a name="rated-usage-data-refresh-schedule"></a>Schema för data uppdatering av klassificerad användning

Information om kostnader och användning finns i Cost Management + fakturering i Azure Portal och stödda [API: er](index.yml). Tänk på följande när du granskar kostnader:

- Uppskattade avgifter för den aktuella fakturerings perioden uppdateras sex gånger per dag.
- Uppskattade avgifter för den aktuella fakturerings perioden kan ändras när du förbrukar mer användning.
- Varje uppdatering är kumulativ och innehåller alla rad objekt och information från den tidigare uppdateringen.
- Azure Slutför eller _stänger_ den aktuella fakturerings perioden upp till 72 timmar (tre Kalender dagar) när fakturerings perioden är slut.

I följande exempel visas hur fakturerings perioder kan sluta.

Enterprise-avtal (EA)-prenumerationer – om fakturerings månaden upphör den 31 mars, uppdateras de uppskattade kostnaderna upp till 72 timmar senare. I det här exemplet, efter midnatt (UTC) 4 april.

Betala per användning-prenumerationer – om fakturerings månaden slutar den 15 maj kan de uppskattade kostnaderna uppdateras upp till 72 timmar senare. I det här exemplet, efter midnatt (UTC) 19 maj.

### <a name="rerated-data"></a>Klassificerade data

Oavsett om du använder [Cost Management-API: er](index.yml), Power BI eller Azure Portal för att hämta data, förväntas den aktuella fakturerings periodens kostnader för att få en omklassificering och därmed ändras tills fakturan stängs.

## <a name="usage-data-update-frequency-varies"></a>Uppdaterings frekvensen för användnings data varierar

Tillgängligheten för dina inaktuella användnings data i Cost Management är beroende av ett par faktorer, inklusive:

- Hur ofta Azure-tjänster (till exempel lagring, beräkning, CDN och SQL) genererar användning.
- Den tid det tar att bearbeta användnings data via pipeliner för klassificerings motor och kostnads hantering.

Vissa tjänster genererar användning oftare än andra. Därför kan du se data i Cost Management för vissa tjänster tidigare än andra tjänster som genererar data mindre ofta. Normalt tar användningen av tjänster 8-24 timmar innan den visas i Cost Management. Tänk på att data för en öppen månad uppdateras när du förbrukar mer användning eftersom uppdateringarna är kumulativa.

## <a name="historical-data-might-not-match-invoice"></a>Historiska data kanske inte matchar fakturan

Historiska data för kreditbaserade och betala in-Advance-erbjudanden kanske inte överensstämmer med din faktura. Vissa Azure-prenumerationer, MSDN-och Visual Studio-erbjudanden kan ha Azure-krediter och avancerade betalningar som tillämpas på fakturan. Men historiska data som visas i Cost Management baseras bara på dina beräknade förbruknings avgifter. Cost Management historiska data inkluderar inte betalningar och krediter. Det innebär att historiska data som visas för följande erbjudanden kanske inte överensstämmer exakt med din faktura.

- Azure för studenter (MS-AZR-0170P)
- Azure i Open (MS-AZR-0111P)
- Azure-pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Kostnads fri utvärdering (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Se också

- Om du inte har redan slutfört den första snabbstarten för kostnadshantering, kan du läsa den på [börja analysera kostnaderna](quick-acm-cost-analysis.md).
