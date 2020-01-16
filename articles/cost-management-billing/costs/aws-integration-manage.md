---
title: Hantera AWS-kostnader och användning i Azure Cost Management
description: Den här artikeln hjälper dig att förstå hur du använder kostnads analys och budget i Cost Management för att hantera dina AWS-kostnader och din användning.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: a7520d7b3bd46aa9151c68598574f9cbec84d6fc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988622"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Hantera AWS-kostnader och användning i Azure

När du har konfigurerat och konfigurerat AWS-integrering för kostnads-och användnings rapporter för Azure Cost Management är du redo att börja hantera dina AWS-kostnader och din användning. Den här artikeln hjälper dig att förstå hur du använder kostnads analys och budget i Cost Management för att hantera dina AWS-kostnader och din användning.

Om du inte redan har konfigurerat integrationen kan du läsa Konfigurera [och konfigurera AWS användnings rapport integrering](aws-integration-set-up-configure.md).

_Innan du börjar_: om du inte är bekant med kostnads analys kan du läsa snabb starten för att [utforska och analysera kostnader med kostnads analys](quick-acm-cost-analysis.md) . Och om du inte är bekant med budgetar i Azure kan du läsa själv studie kursen [skapa och hantera Azure-budgetar](tutorial-acm-create-budgets.md) .

## <a name="view-aws-costs-in-cost-analysis"></a>Visa AWS-kostnader i kostnads analys

AWS-kostnader är tillgängliga i kostnads analys i följande omfång:

- AWS länkade konton under en hanterings grupp
- AWS länkade konto kostnader
- AWS konsoliderade konto kostnader

I nästa avsnitt beskrivs hur du använder omfattningarna så att du ser information om kostnad och användning för var och en.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Visa AWS länkade konton under en hanterings grupp

Att Visa kostnader med hjälp av hanterings gruppens omfång är det enda sättet att se aggregerade kostnader som kommer från olika prenumerationer och länkade konton. Att använda en hanterings grupp är en vy över molnet.

I kostnads analys öppnar du omfattnings väljaren och väljer den hanterings grupp som innehåller dina AWS-länkade konton. Här är en exempel bild i Azure Portal:

![Exempel på vyn Välj omfång](./media/aws-integration-manage/select-scope01.png)



Här är ett exempel som visar kostnaderna för hanterings gruppen i kostnads analyser, grupperade efter leverantör (Azure och AWS).

![Exempel som visar Azure-och AWS-kostnader för ett kvartal i kostnads analys](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Visa AWS länkade konto kostnader

Om du vill visa AWS länk konto kostnader öppnar du omfattnings väljaren och väljer det länkade AWS-kontot. Observera att länkade konton är kopplade till en hanterings grupp, enligt definitionen i AWS-anslutningen.

Här är ett exempel som visar hur du väljer ett AWS-länkat konto omfång.

![Exempel på vyn Välj omfång](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Visa AWS konsoliderade konto kostnader

Om du vill visa AWS konsoliderade konto kostnader öppnar du omfattnings väljaren och väljer det konsoliderade AWS-kontot. Här är ett exempel som visar hur du väljer ett AWS-konsoliderat konto omfång.

![Exempel på vyn Välj omfång](./media/aws-integration-manage/select-scope03.png)



Det här omfånget ger en sammanställd vy över alla AWS-länkade konton som är associerade med det konsoliderade AWS-kontot. Här är ett exempel som visar kostnader för ett AWS konsol IDE rad konto, grupperat efter tjänst namn.

![Exempel som visar AWS konsoliderade kostnader i kostnads analys](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensioner som är tillgängliga för filtrering och gruppering

I följande tabell beskrivs dimensioner som är tillgängliga för gruppering och filtrering efter i kostnads analys.

| Dimension | Amazon, akt rubrik | Omfattningar | Kommentarer |
| --- | --- | --- | --- |
| Tillgänglighetszon | lineitem/AvailabilityZone | Alla |   |
| Location | produkt/region | Alla |   |
| Meter |   | Alla |   |
| Mätar kategori | lineItem/ProductCode | Alla |   |
| Mätar under kategori | lineitem/UsageType | Alla |   |
| Åtgärd | lineItem/åtgärd | Alla |   |
| Resurs | lineItem/ResourceId | Alla |   |
| Resurstyp | produkt-instanceType | Alla | Om produkt-instanceType är null används lineItem/UsageType. |
| ResourceGuid | Gäller inte | Alla | GUID för Azure-mätare. |
| Tjänstnamn | produkt/produkt namn | Alla | Om produkt/ProductName är null används lineItem/ProductCode. |
| Tjänstnivå |   |   |   |
| Prenumerations-ID | lineItem/UsageAccountId | Konsoliderat konto och hanterings grupp |   |
| Prenumerationsnamn | Gäller inte | Konsoliderat konto och hanterings grupp | Konto namn samlas in med AWS-organisationens API. |
| Tagg | resourceTags/\* | Alla | _Användaren:_ prefixet tas bort från användardefinierade taggar för att tillåta Cross-Cloud-taggar. _AWS:_ prefixet lämnas intakt. |
| ID för fakturerings konto | fakturerings-/PayerAccountId | Hanteringsgrupp |   |
| Namn på fakturerings konto | Gäller inte | Hanteringsgrupp | Konto namn samlas in med AWS-organisationens API. |
| Leverantör | Gäller inte | Hanteringsgrupp | Antingen AWS eller Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Ange budgetar för AWS-omfattningar

Använd budgetar för att proaktivt hantera kostnader och enhets ansvar i din organisation. Budgetar anges på det konsoliderade AWS-kontot och AWS länkade konto omfång. Här är ett exempel på budgetar för ett AWS konsol IDE rad konto som visas i Cost Management:

![Exempel som visar budgetar för ett AWS konsoliderat konto](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS data insamlings process

När du har konfigurerat AWS-anslutningen startar data insamling och identifierings processer. Det kan ta några timmar att samla in all användnings information. Varaktigheten är beroende av:

- Den tid det tar att bearbeta de akt filer som finns i AWS S3-Bucket.
- Tiden som krävs för att skapa det konsoliderade AWS-kontot och AWS länkade konto omfång.
- Tiden och frekvensen för AWS skriver filer för kostnads-och användnings rapporter i S3-Bucket

## <a name="aws-integration-pricing"></a>Prissättning för AWS-integrering

Varje AWS-koppling får 90 kostnads fri utvärderings version. Under den offentliga för hands versionen kostar det inget.

List priset är 1% av dina AWS månads kostnader. Varje månad debiteras du utifrån dina fakturerade kostnader från föregående månad.

Åtkomst till AWS-API:er kan medföra ytterligare kostnader.

## <a name="aws-integration-limitations"></a>AWS-integrerings begränsningar

- Cost Management stöder inte kostnads rapporter som innehåller flera valuta typer. Ett fel meddelande visas om du väljer en omfattning som har flera valutor.
- Moln anslutningar stöder inte AWS GovCloud (US), AWS gov eller AWS Kina.
- Cost Management visar endast AWS _användnings kostnader_ . Skatt, support, åter betalningar, RI, krediter eller andra debiterings typer stöds inte än.

## <a name="troubleshooting-aws-integration"></a>Felsöka AWS-integrering

Använd följande felsöknings information för att lösa vanliga problem.

### <a name="no-permission-to-aws-linked-accounts"></a>Ingen behörighet för att AWS länkade konton

**Felkod:** _obehörig_

Det finns två sätt att hämta behörigheter för åtkomst till AWS-länkade konto kostnader:

- Få åtkomst till hanterings gruppen som har AWS-länkade konton.
- Låt någon ge dig behörighet till det länkade AWS-kontot.

Som standard är AWS Connector-skaparen ägare av alla objekt som anslutningen har skapat. Inklusive det konsoliderade AWS-kontot och det länkade AWS-kontot.

För att kunna verifiera anslutnings inställningarna behöver du minst en deltagar roll och läsaren kan inte verifiera anslutnings inställningar

### <a name="collection-failed-with-assumerole"></a>Samlingen misslyckades med AssumeRole

**Felkod:** _FailedToAssumeRole_

Det här felet innebär att Cost Management inte kan anropa AssumeRole-API: et för AWS. Det här problemet kan inträffa på grund av ett problem med roll definitionen. Kontrol lera att följande villkor är uppfyllda:

- Det externa ID: t är samma som det i roll definitionen och anslutnings definitionen.
- Roll typen anges till **ett annat AWS-konto som tillhör dig eller tredje part.**
- Alternativet **KRÄV MFA** är avmarkerat.
- Det betrodda AWS-kontot i AWS-rollen är _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Samlingen misslyckades med åtkomst nekad – akt rapport definitioner

**Felkod:** _AccessDeniedReportDefinitions_

Det här felet innebär att Cost Management inte kan se rapport definitionerna för kostnad och användning. Den här behörigheten används för att verifiera att det aktuella har definierats som förväntat av Azure Cost Management. Se [skapa en kostnads-och användnings rapport i AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Samlingen misslyckades med åtkomst nekad – lista över rapporter

**Felkod:** _AccessDeniedListReports_

Det här felet innebär att Cost Management inte kan lista objektet i S3-Bucket där det finns ett aktuellt objekt. AWS IAM-principen kräver en behörighet för Bucket och objekten i Bucket. Se [skapa en roll och princip i AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Samlingen misslyckades med åtkomst nekad – Hämta rapport

**Felkod:** _AccessDeniedDownloadReport_

Det här felet innebär att Cost Management inte kan komma åt och hämta de aktuella filerna som lagras i Amazon S3-Bucket. Kontrol lera att AWS JSON-principen som är kopplad till rollen liknar exemplet som visas längst ned i avsnittet [skapa en roll och princip i AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Samlingen misslyckades eftersom vi inte hittade rapporten kostnad och användning

**Felkod:** _FailedToFindReport_

Det här felet innebär att Cost Management inte kan hitta rapporten kostnad och användning som definierades i anslutningen. Kontrol lera att den inte har tagits bort och att AWS JSON-principen som är kopplad till rollen liknar exemplet som visas längst ned i avsnittet [skapa en roll och princip i AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Det gick inte att skapa eller verifiera anslutningen på grund av ett matchnings fel för kostnads-och användnings rapporter

**Felkod:** _ReportIsNotValid_

Det här felet relaterar till definitionen av AWS-kostnad och användnings rapport, vi kräver särskilda inställningar för den här rapporten, se kraven i [skapa en kostnads-och användnings rapport i AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har konfigurerat Azure-miljön med hanterings grupper kan du läsa den [första installationen av hanterings grupper](../../governance/management-groups/overview.md#initial-setup-of-management-groups).
