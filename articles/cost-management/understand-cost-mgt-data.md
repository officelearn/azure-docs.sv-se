---
title: Förstå Azure Cost Management-data | Microsoft Docs
description: Den här artikeln hjälper dig att bättre förstå vilka data som ingår i Azure Cost Management och hur ofta den bearbetas, samlas in, visas och stängda.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: f0632b0d6e651ba6e0b43586e60d582e29c5ce00
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500550"
---
# <a name="understand-cost-management-data"></a>Förstå Cost Management-data

Den här artikeln hjälper dig att bättre förstå vilka data som ingår i Azure Cost Management. Och det förklarar hur ofta data bearbetas som samlas in, visas och stängda. Du faktureras för användningen av Azure varje månad. Dock avgör din typ av Azure-prenumeration då Faktureringsmånad slutar. Hur ofta kostnadshantering tar emot användning data varierar baserat på olika faktorer. Sådana faktorer är hur lång tid det tar för att bearbeta data och hur ofta användningen av faktureringssystemet generera i Azure-tjänster.

## <a name="supported-microsoft-offers"></a>Microsoft-erbjudanden som stöds

Följande information visar för närvarande stöds [Microsoft Azure erbjuder](https://azure.microsoft.com/support/legal/offer-details/) i Azure Cost Management.  Ett Azure-erbjudande är typ av Azure-prenumerationen som du har.

| Kategori  | **Erbjudandets namn** | **Kvot-ID.** | **Erbjudandets nummer** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure Tyskland betala per användning](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P |
| **Enterprise-avtal (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P |
| **Enterprise-avtal (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P |
| **Microsoft Developer Network (MSDN)** | [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p) | MSDN_2014-09-01 | MS-AZR-0062P |
| **Betala per användning** | [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p)                       | PayAsYouGo_2014-09-01 | MS-AZR-0003P |
| **Betala per användning** | [Betala per användning – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0023p)              | MSDNDevTest_2014-09-01 | MS-AZR-0023P |
| **Betala per användning** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)           | MPN_2014-09-01 | MS-AZR-0025P |
| **Betala per användning** | [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p)                          | FreeTrial_2014-09-01 | MS-AZR-0044P |
| **Betala per användning** | [Azure i Open](https://azure.microsoft.com/offers/ms-azr-0111p)                       | AzureInOpen_2014-09-01 | MS-AZR-0111P |
| **Betala per användning** | [Azure för studenter](https://azure.microsoft.com/offers/ms-azr-0170p)                  | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Betala per användning** | Azure-pass                                                                             | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)      | MPN_2014-09-01 | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)          | MSDN_2014-09-01 | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)     | MSDNDevTest_2014-09-01 | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)            | MSDN_2014-09-01 | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)  | MSDN_2014-09-01 | MS-AZR-0064P |

I följande tabell visar erbjudanden som inte stöds.

| Kategori  | **Erbjudandets namn** | **Kvot-ID.** | **Erbjudandets nummer** |
| --- | --- | --- | --- |
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

För kunder med användningsbaserad betalning, MSDN och Visual Studio erbjudandet kategorier är data tillgängliga i Cost Management från och med 2018-02-10. Du kan använda för att komma åt data för din prenumeration innan 2018-02-10 den [Azure Kontocenter](https://account.azure.com/subscriptions) information i en CSV-fil att ladda ned din användning eller du kan använda den [användning information API](/rest/api/consumption/usagedetails).

## <a name="determine-your-offer-type"></a>Fastställa erbjudandetypen av ditt
Om du inte ser data för en prenumeration och du vill fastställa om din prenumeration omfattas stöds erbjudanden, kan du verifiera att det finns stöd för din prenumeration. För att verifiera att det finns stöd för en Azure-prenumeration kan logga in på den [Azure-portalen](https://portal.azure.com). Välj sedan **alla tjänster** i den vänstra meny-rutan. Välj i listan över tjänster, **prenumerationer**. Klicka på den prenumeration som du vill verifiera i listmenyn prenumeration. Din prenumeration visas på fliken Översikt och du kan se den **erbjuder** och **erbjudande-ID**. I följande bild visas ett exempel.

![Exempel på fliken prenumeration översikt som visar erbjudandet och erbjudande-ID](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Kostnader i Cost Management

Följande tabeller visar data som har inkluderat eller inte finns i Cost Management.

**Kostnad-och användningsdata**

| **Ingår** | **Ingår inte** |
| --- | --- |
| Azure tjänstanvändning<sup>1</sup> | Köp av reservation – mer information finns i [API: er för Azure reservation automation](../billing/billing-reservation-apis.md). |
| Användning av Marketplace-erbjudande | Marketplace-Köp – mer information finns i [från tredje part tjänstavgifter](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Hantera kostnader – mer information, se [villkoren för förklaras](../billing/billing-understand-your-invoice.md). |
|   | Skatter - mer information finns i [villkoren för förklaras](../billing/billing-understand-your-invoice.md). |
|   | Krediter - mer information finns i [villkoren för förklaras](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> azure tjänstanvändning baseras på reservation och förhandlas priser.

**Metadata**

| **Ingår** | **Ingår inte** |
| --- | --- |
| Resurstaggar<sup>2</sup> | gruppen resurstaggar |

<sup>2</sup> resurstaggar tillämpas enligt användning som sänts ut från varje tjänst och är inte tillgängliga retroaktivt för historisk användning.

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

Om du använder den [Cost Management API: er](https://aka.ms/costmgmt/docs), PowerBI eller Azure-portalen för att hämta data, förväntar du dig den aktuella faktureringsperioden avgifter för att hämta klassificerade igen, och därmed ändra tills fakturan stängs.

## <a name="usage-data-update-frequency-varies"></a>Uppdateringsfrekvensen för användning data varierar

Tillgängligheten för användningsdata kostnader i Cost Management är beroende av ett antal faktorer, bland annat:

- Hur ofta generera Azure-tjänster (till exempel lagring, beräkning, CDN och SQL) användning.
- Den tid det tar att bearbeta användningsdata via motorn för klassificering och cost management pipelines.

Vissa tjänster sända användning oftare än andra. Därför kan du se data i Cost Management för vissa tjänster snabbare än andra tjänster som genererar data mindre ofta. Användning för tjänster som tar normalt, 8 – 24 timmar ska visas i Cost Management. Tänk på dessa data för en öppen månad överföringsskärmen uppdateras när du använder mer användning eftersom uppdateringar är kumulativa.

## <a name="see-also"></a>Se också

- Om du inte har redan slutfört den första snabbstarten för kostnadshantering, kan du läsa den på [börja analysera kostnaderna](quick-acm-cost-analysis.md).
