---
title: Hantera AWS kostnader och användning i Azure Cost Management
description: Den här artikeln hjälper dig att använda kostnadsanalys och budgetar i Cost Management för att hantera din AWS-kostnaderna och användning.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 007b6c409dde248a4dde7a15fd16b543add234bc
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870319"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Hantera AWS kostnader och användning i Azure

När du har ställt in och konfigurerat AWS kostnader och användning rapport-integrering för Azure Cost Management, är du redo att börja hantera din AWS-kostnaderna och användning. Den här artikeln hjälper dig att använda kostnadsanalys och budgetar i Cost Management för att hantera din AWS-kostnaderna och användning.

Om du inte redan har konfigurerat integrationen, se [ange upp och konfigurera AWS användning rapporten integrering](aws-integration-set-up-configure.md).

_Innan du börjar_: Om du är bekant med kostnadsanalys kan se den [utforska och analysera kostnaderna med kostnadsanalys](quick-acm-cost-analysis.md) Snabbstart. Och om du är bekant med budgetar i Azure kan se den [skapa och hantera Azure budgetar](tutorial-acm-create-budgets.md) självstudien.

## <a name="view-aws-costs-in-cost-analysis"></a>Visa AWS kostnader i kostnadsanalys

AWS-kostnaderna är tillgängliga i kostnadsanalys på följande områden:

- Länkad AWS-konton i en hanteringsgrupp
- Kostnaderna för AWS länkad-konto
- AWS konsoliderade kostnader för konto

I nästa avsnitt beskrivs hur du använder scope så att du ser data om kostnader och användning för var och en.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Visa AWS länkade konton under en hanteringsgrupp

Visa kostnaderna genom att använda gruppen hanteringsomfång är det enda sättet att visa aggregerad kostnaderna som kommer från olika prenumerationer och länkade konton. Med hjälp av en hanteringsgrupp kan du visa molnöverskridande.

Öppna omfattningsväljaren i kostnadsanalys, och välj den hanteringsgrupp som innehåller dina länkade AWS-konton. Här är en exempelbild i Azure portal:

![Exempel på vyn väljer omfång](./media/aws-integration-manage/select-scope01.png)



Här är ett exempel som visar management group kostnaden i kostnadsanalys, grupperade efter leverantör (Azure och AWS).

![Exempel på Azure och AWS-kostnaderna för ett kvartal i kostnadsanalys](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Visa AWS länkat konto kostnader

Om du vill visa kostnaderna för AWS länk konto öppnar omfattningsväljaren och väljer AWS länkat konto. Observera att länkade konton är kopplade till en hanteringsgrupp som definierats i AWS-anslutningen.

Här är ett exempel som visar att välja en AWS länkad kontoomfånget.

![Exempel på vyn väljer omfång](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Visa AWS konsoliderade kostnader för konto

Om du vill visa AWS konsoliderat konsoliderade konto kostnader, öppna omfattningsväljaren och välj AWS konto. Här är ett exempel som visar att välja en AWS konsoliderade kontoomfånget.

![Exempel på vyn väljer omfång](./media/aws-integration-manage/select-scope03.png)



Det här området innehåller en aggregerad vy över alla AWS länkade konton som är associerade med kontot AWS konsolideras. Här är ett exempel som visar kostnaderna för AWS konsoliderat konto, grupperade efter namn.

![Exemplet visar AWS konsoliderade kostnader i kostnadsanalys](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensioner tillgängliga för att filtrera och gruppera

I följande tabell beskrivs dimensioner som är tillgängliga för gruppen och filtrera efter i kostnadsanalys.

| Dimension | Amazon akt rubrik | Omfattningar | Kommentarer |
| --- | --- | --- | --- |
| Tillgänglighetszon | lineitem/AvailabilityZone | Alla |   |
| Location | produkt/Region | Alla |   |
| Meter |   | Alla |   |
| Mätarkategori | lineItem/ProductCode | Alla |   |
| Mätarunderkategori | lineitem/UsageType | Alla |   |
| Åtgärd | lineItem/Operation | Alla |   |
| Resurs | lineItem/ResourceId | Alla |   |
| Resurstyp | produkt/instanceType | Alla | Om produkt/instanceType är null används lineItem/UsageType. |
| ResourceGuid | Gäller inte | Alla | Azure mätaren GUID. |
| Tjänstnamn | produkt/ProductName | Alla | Om produkt/ProductName är null används lineItem/ProductCode. |
| Tjänstenivå |   |   |   |
| Prenumerations-ID:t | lineItem/UsageAccountId | Konsoliderad grupp för kontot och hantering |   |
| Prenumerationsnamn | Gäller inte | Konsoliderad grupp för kontot och hantering | Kontonamn samlas in med hjälp av API för AWS-organisation. |
| Tagga | resourceTags/\* | Alla | Den _användare:_ prefix tas bort från en användardefinierad taggar om du vill tillåta molnöverskridande taggar. Den _aws:_ prefix lämnas intakt. |
| Faktureringskonto-ID | bill/PayerAccountId | Hanteringsgrupp |   |
| Faktureringskontonamn | Gäller inte | Hanteringsgrupp | Kontonamn samlas in med hjälp av API för AWS-organisation. |
| Leverantör | Gäller inte | Hanteringsgrupp | AWS eller Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Ange budgetar på AWS-omfång

Använd budgetar för att proaktivt hantera kostnader och enheten ansvar i din organisation. Budgetar är inställda på konsoliderad AWS-konto och AWS länkat konto omfång. Här är ett exempel på budgetar för ett konto för AWS konsolideras som visas i Cost Management:

![Exemplet visar budgetar för AWS konsoliderat konto](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Processen för AWS för datainsamling

När du har installerat den AWS-anslutningen, insamling av data och processer för identifiering av startas. Det kan ta några timmar att samla in alla användningsdata. Varaktigheten beror på:

- Den tid som behövs för att bearbeta akt-filer som finns i AWS S3-bucket.
- Den tid som behövs för att skapa sammanslagna AWS-konto och AWS länkade kontoområden.
- Den tid och frekvensen för AWS skriver kostnaden och användningsrapporten filerna i S3-bucket

## <a name="aws-integration-pricing"></a>Priser för AWS-integrering

Varje AWS-koppling hämtar 90 dagar av den kostnadsfria utvärderingsversionen. Allmänt tillgängliga förhandsversionen kostar ingenting.

Listpris är 1% av din AWS-månadskostnad. Varje månad som du debiteras baserat på dina fakturerade kostnader från föregående månad.

Åtkomst till API: er i AWS kan medföra ytterligare kostnader.

## <a name="aws-integration-limitations"></a>Begränsningar för AWS-integrering

- Kostnadshantering stöder inte kostnadsrapporter som innehåller flera valutatyper. Ett felmeddelande visas om du väljer du en omfattning som har flera valutor.
- Molnet anslutningsappar stöder inte AWS GovCloud (USA), AWS-förvaltad region eller AWS Kina.
- Kostnadshantering visar AWS _kostnader_ endast. Skatt, support, återbetalningar, RI, krediter eller andra kostnad-typer stöds inte ännu.

## <a name="troubleshooting-aws-integration"></a>Felsöka AWS-integrering

Du kan använda följande felsökningsinformation för att lösa vanliga problem.

### <a name="no-permission-to-aws-linked-accounts"></a>Ingen behörighet till den länkade AWS-konton

Det finns två sätt att få behörighet att komma åt AWS länkade konton kostnader:

- Få åtkomst till den hanteringsgrupp som har länkade till AWS-konton.
- Har någon ger dig behörighet att länkad AWS-kontot.

Som standard är AWS connector skapare ägare av alla objekt som skapats för anslutningen. Inklusive AWS länkat konsoliderade konto och AWS konto.

### <a name="collection-failed-with-assumerole"></a>Samlingen misslyckades med AssumeRole

Detta fel innebär att Cost Management är det går inte att anropa API: et för AWS AssumeRole. Det här problemet kan inträffa på grund av ett problem med rolldefinitionen. Kontrollera att följande villkor är uppfyllda:

- Externt ID är samma som den i rolldefinitionen och definitionen för anslutningsappen.
- Rolltypen är inställd på **en annan AWS-konto som tillhör för dig eller 3 part.**
- Den **kräver MFA** val är avmarkerad.
- Det betrodda AWS-kontot i AWS-rollen är _432263259397_.

### <a name="collection-failed-with-access-denied"></a>Samlingen misslyckades med åtkomst nekad

Det här felmeddelandet innebär att Cost Management är inte kan komma åt akt filer som lagras i en Amazon S3-bucket. Se till att principen AWS JSON kopplade till rollen liknar exemplet som visas längst ned på den [skapar en roll och principen i AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) avsnittet.

### <a name="connector-error-with-failedtofindreport"></a>Connector-fel med FailedToFindReport

Det här felet innebär att kostnadshantering det går inte att hitta rapporten kostnader och användning som definierades i kopplingen. Kontrollera att den inte bort och att principen AWS JSON kopplade till rollen liknar exemplet som visas längst ned på den [skapar en roll och principen i AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) avsnittet.

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har konfigurerat Azure-miljön med hanteringsgrupper, se [inledande installationen av hanteringsgrupper](../governance/management-groups/index.md#initial-setup-of-management-groups).
