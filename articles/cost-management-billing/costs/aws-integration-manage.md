---
title: Hantera AWS-kostnader och AWS-användning i Azure Cost Management
description: Den här artikeln hjälper dig att förstå hur du använder kostnadsanalys och budgetar i Cost Management för att hantera dina AWS-kostnader och din AWS-användning.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: matrive
ms.custom: ''
ms.openlocfilehash: fd4d4a16642e764276d49dd732a785be48197d01
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200035"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Hantera AWS-kostnader och AWS-användning i Azure

När du har konfigurerat integrering av rapport för AWS-kostnader och AWS-användning för Azure Cost Management kan du börjar hantera dina AWS-kostnader och din AWS-användning. Den här artikeln hjälper dig att förstå hur du använder kostnadsanalys och budgetar i Cost Management för att hantera dina AWS-kostnader och din AWS-användning.

Om du inte redan har konfigurerat integreringen kan du se [Konfigurera integrering av rapport för AWS-användning](aws-integration-set-up-configure.md).

_Innan du börjar_: Om du inte är bekant med kostnadsanalys kan du se snabbstarten [Utforska och analysera kostnader med kostnadsanalys](quick-acm-cost-analysis.md). Och om du inte är bekant med budgetar i Azure kan du se självstudien [Skapa och hantera Azure-budgetar](tutorial-acm-create-budgets.md).

## <a name="view-aws-costs-in-cost-analysis"></a>Visa AWS-kostnader i kostnadsanalys

AWS-kostnader är tillgängliga i kostnadsanalys i följande omfattningar:

- AWS-länkade konton under en hanteringsgrupp
- Kostnader för AWS-länkat konto
- Kostnader för AWS-konsoliderat konto

I nästa avsnitt beskrivs hur du visar information om kostnader och användning för var och en av dessa omfattningar.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Visa AWS-länkade konton under en hanteringsgrupp

Att visa kostnader med hjälp av hanteringsgruppens omfattning är det enda sättet att se aggregerade kostnader som kommer från olika prenumerationer och länkade konton. När du använder en hanteringsgrupp får du en molnvy.

I kostnadsanalys öppnar du omfattningsväljaren och väljer den hanteringsgrupp som innehåller dina AWS-länkade konton. Här är en exempelbild i Azure-portalen:

![Exempel på vyn Välj omfattning](./media/aws-integration-manage/select-scope01.png)



Här är ett exempel som visar kostnaderna för hanteringsgruppen i kostnadsanalys, grupperade efter leverantör (Azure och AWS).

![Exempel som visar Azure- och AWS-kostnader för ett kvartal i kostnadsanalys](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Visa kostnader för ett AWS-länkat konto

Om du vill visa kostnader för ett AWS-länkat konto öppnar du omfattningsväljaren och väljer det AWS-länkade kontot. Observera att länkade konton är associerade med en hanteringsgrupp, enligt definitionen i AWS-anslutningsprogrammet.

Här är ett exempel som visar hur du väljer en omfattning för ett AWS-länkat konto.

![Exempel på vyn Välj omfattning](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Visa kostnader för ett AWS-konsoliderat konto

Om du vill visa kostnader för ett AWS-konsoliderat konto öppnar du omfattningsväljaren och väljer det AWS-konsoliderade kontot. Här är ett exempel som visar hur du väljer en omfattning för ett AWS-konsoliderat konto.

![Exempel på vyn Välj omfattning](./media/aws-integration-manage/select-scope03.png)



Den här omfattningen ger en översikt över alla AWS-länkade konton som är associerade med det AWS-konsoliderade kontot. Här är ett exempel som visar kostnader för ett AWS-konsoliderat konto, grupperade efter tjänstnamn.

![Exempel som visar AWS-konsoliderade kostnader i kostnadsanalys](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensioner som är tillgängliga för filtrering och gruppering

I följande tabell beskrivs de dimensioner som är tillgängliga för gruppering och filtrering i kostnadsanalys.

| Dimension | Amazon, CUR-huvud | Omfattningar | Kommentarer |
| --- | --- | --- | --- |
| Tillgänglighetszon | lineitem/AvailabilityZone | Alla |   |
| Location | product/Region | Alla |   |
| Mätare |   | Alla |   |
| Mätarkategori | lineItem/ProductCode | Alla |   |
| Mätarunderkategori | lineitem/UsageType | Alla |   |
| Åtgärd | lineItem/Operation | Alla |   |
| Resurs | lineItem/ResourceId | Alla |   |
| Resurstyp | product/instanceType | Alla | Om product/instanceType är null används lineItem/UsageType. |
| ResourceGuid | Ej tillämpligt | Alla | GUID för Azure-mätare. |
| Tjänstnamn | product/ProductName | Alla | Om product/ProductName är null används lineItem/ProductCode. |
| Tjänstenivå |   |   |   |
| Prenumerations-ID:t | lineItem/UsageAccountId | Konsoliderat konto och hanteringsgrupp |   |
| Prenumerationens namn | Ej tillämpligt | Konsoliderat konto och hanteringsgrupp | Kontonamn samlas in med AWS-organisationens API. |
| Tagga | resourceTags/\* | Alla | Prefixet _user:_ tas bort från användardefinierade taggar för att tillåta molntaggar. Prefixet _aws:_ lämnas orört. |
| Faktureringskonto-ID | bill/PayerAccountId | Hanteringsgrupp |   |
| Faktureringskontonamn | Ej tillämpligt | Hanteringsgrupp | Kontonamn samlas in med AWS-organisationens API. |
| Leverantör | Ej tillämpligt | Hanteringsgrupp | Antingen AWS eller Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Ange budgetar för AWS-omfattningar

Med budgetar kan du hantera kostnader proaktivt och öka ansvarstagandet i organisationen. Budgetar anges på omfattningarna för AWS-konsoliderat konto och AWS länkat konto. Här är ett exempel på budgetar för ett AWS-konsoliderat konto i Cost Management:

![Exempel som visar budgetar för ett AWS-konsoliderat konto](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS-datainsamlingsprocess

När du har konfigurerat AWS-anslutningsprogrammet startar datainsamlings- och identifieringsprocesserna. Det kan ta några timmar att samla in alla användningsdata. Hur lång tid det tar beror på:

- Tiden det tar att bearbeta CUR-filerna i AWS S3-bucketen.
- Tiden det tar att skapa omfattningarna för AWS-konsoliderat konto och AWS-länkat konto.
- Hur lång tid och hur ofta AWS skriver rapportfiler för kostnader och användning i S3-bucketen

## <a name="aws-integration-pricing"></a>Prissättning för AWS-integrering

Varje AWS-anslutningsprogram får 90 dagars kostnadsfri utvärdering. Under den allmänt tillgängliga förhandsversionen kostar det ingenting.

Listpriset är 1 % av dina AWS-månadskostnader. Varje månad debiteras du baserat på dina fakturerade kostnader från föregående månad.

Åtkomst till AWS-API:er kan medföra ytterligare kostnader.

## <a name="aws-integration-limitations"></a>Begränsningar för AWS-integrering

- Cost Management stöder inte kostnadsrapporter som innehåller flera valutatyper. Ett felmeddelande visas om du väljer en omfattning som har flera valutor.
- Molnanslutningsprogram stöder inte AWS GovCloud (US), AWS Gov eller AWS China.
- I Cost Management visas endast _AWS-användningskostnader_. Skatt, support, återbetalningar, RI, krediter eller andra debiteringstyper stöds inte ännu.

## <a name="troubleshooting-aws-integration"></a>Felsöka AWS-integrering

Du kan lösa vanliga problem med hjälp av följande felsökningsinformation.

### <a name="no-permission-to-aws-linked-accounts"></a>Behörighet saknas för AWS-länkade konton

**Felkod:** _Behörighet saknas_

Det finns två sätt att få behörighet för att visa kostnader för AWS-länkade konton:

- Få åtkomst till hanteringsgruppen som har AWS-länkade konton.
- Få någon att ge dig behörighet till det AWS-länkade kontot.

Som standard är den som har skapat AWS-anslutningsprogrammet ägare till alla objekt som anslutningsprogrammet har skapat. Det innefattar även det AWS-konsoliderade kontot och det AWS-länkade kontot.

För att kunna verifiera inställningarna för anslutningsprogrammet måste du minst ha deltagarrollen (det går inte att verifiera inställningar för anslutningsprogrammet med läsarrollen)

### <a name="collection-failed-with-assumerole"></a>Samlingen misslyckades med AssumeRole

**Felkod:** _FailedToAssumeRole_

Det här felet innebär att Cost Management inte kan anropa AWS AssumeRole-API:et. Det här problemet kan inträffa på grund av ett problem med rolldefinitionen. Kontrollera att följande villkor är uppfyllda:

- Det externa ID:t är samma som det som finns i rolldefinitionen och anslutningsdefinitionen.
- Rolltypen är inställd på **ett annat AWS-konto som tillhör dig eller tredje part.**
- Alternativet **Kräv MFA** är avmarkerat.
- Det betrodda AWS-kontot i AWS-rollen är _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Samlingen misslyckades med nekad åtkomst – CUR-rapportdefinitioner

**Felkod:** _AccessDeniedReportDefinitions_

Det här felet innebär att Cost Management inte kan se definitionerna för kostnads- och användningsrapporten (CUR). Den här behörigheten används för att verifiera att CUR har definierats som förväntat av Azure Cost Management. Se [Skapa en kostnads- och användningsrapport i AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Samlingen misslyckades med nekad åtkomst – visa rapporter

**Felkod:** _AccessDeniedListReports_

Det här felet innebär att Cost Management inte kan visa objektet i den S3-bucket där CUR finns. AWS IAM-principen kräver behörighet för bucketen och objekten i bucketen. Se [Skapa en roll och princip i AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Samlingen misslyckades med nekad åtkomst – ladda ned rapport

**Felkod:** _AccessDeniedDownloadReport_

Det här felet innebär att Cost Management inte kan komma åt och hämta CUR-filerna som lagras i Amazon S3-bucketen. Kontrollera att AWS JSON-principen som är kopplad till rollen ser ut som i exemplet som visas längst ned i avsnittet [Skapa en roll och princip i AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Samlingen misslyckades eftersom vi inte hittade kostnads- och användningsrapporten

**Felkod:** _FailedToFindReport_

Det här felet innebär att Cost Management inte kan hitta kostnads- och användningsrapporten som definierades i anslutningsprogrammet. Kontrollera att den inte har tagits bort och att AWS JSON-principen som är kopplad till rollen ser ut som i exemplet som visas längst ned i avsnittet [Skapa en roll och princip i AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Det gick inte att skapa eller verifiera anslutningsprogrammet på grund av att definitionerna för kostnads- och användningsrapporten inte stämde överens

**Felkod:** _ReportIsNotValid_

Det här felet gäller definitionen för kostnads- och användningsrapporten för AWS. Specifika inställningar krävs för rapporten. Dessa krav finns i [Skapa en kostnads- och användningsrapport i AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har konfigurerat din Azure-miljö med hanteringsgrupper kan du se [Initial konfiguration av hanteringsgrupper](../../governance/management-groups/overview.md#initial-setup-of-management-groups).
