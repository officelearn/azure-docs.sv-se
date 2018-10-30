---
title: Snabbstart – Utforska Azure-kostnader med kostnadsanalys | Microsoft Docs
description: Den här snabbstarten hjälper dig att använda kostnadsanalys för att utforska och analysera dina Azure-organisationskostnader.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/19/2018
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 6b935322c9d892793f3695e0922d15f5886c7e25
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471296"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Snabbstart: Utforska och analysera kostnader med kostnadsanalys

För att kunna kontrollera och optimera dina Azure-kostnader på rätt sätt behöver du förstå var kostnaderna har sitt ursprung i organisationen. Det är även bra att veta hur mycket pengar dina tjänster kostar och vilka miljöer och system de stödjer. Insyn i hela spektrumet av kostnader är mycket viktigt för kunna förstå organisationens utgiftsmönster. Utgiftsmönster kan användas för att tillämpa kostnadskontrollmekanismer, till exempel budgetar.

I den här snabbstarten använder du kostnadsanalys för att utforska och analysera dina organisationskostnader. Du kan visa aggregerade kostnader efter organisation för att se var kostnader sker över tid och för att identifiera utgiftstrender. Du kan se ackumulerade kostnader över tid för att beräkna månatliga, kvartalsvisa eller årliga kostnadstrender mot en budget. En budget gör det lättare att hålla sig till ekonomiska begränsningar. En budget används även till att visa dagliga och månatliga kostnader för att isolera oregelbundenheter vad gäller utgifter. Dessutom kan du ladda ned den aktuella rapportens data för ytterligare analys eller för användning i ett externt system.

I den här snabbstarten lär du dig att:

- Granska kostnader i kostnadsanalys
- Anpassa kostnadsvyer
- Ladda ned kostnadsanalysdata


## <a name="prerequisites"></a>Nödvändiga komponenter

Kostnadsanalys är tillgängligt för alla [Enterprise-avtalskunder (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Du måste minst ha skrivskyddad åtkomst till ett eller flera av följande omfång för att visa kostnadsdata.


|**Omfång**|**Definieras på**|**Åtkomst som krävs för analys av kostnader för omfånget**|**Nödvändig EA-inställning**|**Konsoliderar faktureringsinformation till**|
|---                |---                  |---                   |---            |---           |
|Faktureringskonto<sup>1</sup>|[https://ea.azure.com ](https://ea.azure.com )|Företagsadministratör|Ingen|Alla prenumerationer från Enterprise-avtalet|
|Avdelning|[https://ea.azure.com ](https://ea.azure.com )|Avdelningsadministratör|DA-visningsdebitering aktiverad|Alla prenumerationer som tillhör ett registreringskonto som är länkade till avdelningen|
|Registreringskonto<sup>2</sup2>|[https://ea.azure.com ](https://ea.azure.com )|Kontoägare|AO-visningsdebitering aktiverad|Alla prenumerationer från registreringskontot|
|Hanteringsgrupp|[https://portal.azure.com ](https://portal.azure.com )|Cost Management-läsare (eller Läsare)|AO-visningsdebitering aktiverad|Alla prenumerationer under hanteringsgruppen|
|Prenumeration|[https://portal.azure.com ](https://portal.azure.com )|Cost Management-läsare (eller Läsare)|AO-visningsdebitering aktiverad|Alla resurser/resursgrupper i prenumerationen|
|Resursgrupp|[https://portal.azure.com ](https://portal.azure.com )|Cost Management-läsare (eller Läsare)|AO-visningsdebitering aktiverad|Alla resurser i resursgruppen|

<sup>1</sup>Faktureringskontot kallas ofta Enterprise-avtal eller Registrering.

<sup>2</sup>Registreringskontot kallas ofta kontoägare.

Mer information om hur du konfigurerar inställningar för **DA-visningsdebitering** och **AO-visningsdebitering** finns i [Aktivera åtkomst till kostnader](../billing/billing-enterprise-mgmt-grp-troubleshoot-cost-view.md#enabling-access-to-costs).





## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på http://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Granska kostnader i kostnadsanalys

Om du vill granska dina kostnader med kostnadsanalys går du i Azure-portalen till **Kostnadshantering + fakturering** &gt; **Kostnadshantering** &gt; **Ändra omfång**, väljer ett omfång och klickar sedan på **Välj**.

Det omfång som du väljer används i hela Cost Management för att ge datakonsolidering och styra åtkomsten till kostnadsinformation. När du använder omfång så använder du inte flerval för dem. I stället väljer du ett större omfång som andra ackumuleras till, och sedan filtrerar du ned till det du vill. Detta är viktigt att förstå eftersom vissa användare inte ska ha åtkomst till ett överordnat omfång som underordnade omfång ackumuleras till.

Klicka på **Öppna kostnadsanalys**.

Den initiala kostnadsanalysvyn innehåller följande områden:

**Total** (Totalt) – visar den totala kostnaden för aktuell månad.

**Budget** – visar den planerade utgiftsgränsen för det valda omfånget, om en sådan är tillgänglig.

**Accumulated cost** (Ackumulerad kostnad) – visar de totala ackumulerade dagliga utgifterna med start i början av månaden. När du har [skapat en budget](tutorial-acm-create-budgets.md) för ditt faktureringskonto eller din prenumeration kan du snabbt se din utgiftstrend jämfört med budgeten. Hovra över ett datum om du vill visa den ackumulerade kostnaden för den dagen.

**Pivotdiagram (ringdiagram)** – visar dynamiska pivoter som delar upp den totala kostnaden enligt en gemensam uppsättning standardegenskaper. De visar den högsta till minsta kostnaden som ackumulerats för den aktuella månaden. Du kan ändra pivotdiagram när som helst genom att välja en annan pivot. Kostnaderna kategoriseras efter: tjänst (mätarkategori), plats (region) och underordnat omfång som standard. Exempel: registreringskonton under faktureringskonton, resursgrupper under prenumerationer och resurser under resursgrupper.

![Initial vy över kostnadsanalys](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Anpassa kostnadsvyer

Standardvyn ger snabba svar på vanliga frågor som:

- Hur mycket spenderade jag?
- Följer jag budgeten?

Det finns dock många fall där du behöver djupare analys. Anpassning startar överst på sidan med valet av datum.

Kostnadsanalys visar data för den aktuella månaden som standard. Använd datumväljaren för att snabbt växla till: föregående månad, denna månad, detta kalenderkvartal, detta kalenderår eller ett eget datumintervall som du väljer. Att välja föregående månaden är det snabbaste sättet att analysera din senaste Azure-faktura och enkelt stämma av avgifter. Alternativen för aktuellt kvartal och år är till hjälp för att spåra kostnader mot mer långsiktiga budgetar. Du kan även välja ett annat datumintervall. Till exempel kan du välja en enstaka dag, de senaste sju dagarna eller något annat så långt tillbaka som ett år före den aktuella månaden.

![Datumväljare](./media/quick-acm-cost-analysis/date-selector.png)

Kostnadsanalysen visar **ackumulerade** kostnader som standard. Ackumulerade kostnader omfattar alla kostnader för varje dag plus föregående dagar, för en ständigt växande vy över dina dagliga ackumulerade kostnader. Den här vyn är optimerad för att visa hur du trendar mot en budget för det valda tidsintervallet.

Det finns även den **dagsvyn**, som visar kostnaderna för varje dag. Dagsvyn visar inte någon tillväxttrend. Vyn har utformats för att visa oregelbundenheter såsom toppar eller dalar i utgifter från dag till dag. Om du har valt en budget visar dagsvyn även en uppskattning av hur din dagliga budget kan se ut. När dina dagliga kostnader konsekvent är över den beräknade dagliga budgeten kan du förvänta dig att du överskrider din månatliga budget. Den beräknade dagliga budgeten är bara ett sätt att visualisera din budget på en lägre nivå. När det förekommer variationer i de dagliga kostnaderna är jämförelsen mellan den uppskattade dagliga budgeten och den månatliga budgeten mindre exakt.

![Dagsvy](./media/quick-acm-cost-analysis/daily-view.png)

Du kan **gruppera efter** för att välja en gruppkategori och ändra data som visas i diagrammet med total area längst upp. Med gruppering kan du snabbt se hur dina utgifter kategoriseras efter resurstyp. Här är en vy över Azure-tjänstkostnaderna för en vy över föregående månad.

![Grupperad daglig ackumulerad vy](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Pivotdiagram under vyn Totalt längst upp visar vyer för olika grupperings- och filtreringskategorier. När du väljer en gruppkategori visas den fullständiga datamängden för vyn Totalt längst ned i vyn. Här är ett exempel för resursgrupper.

![Fullständiga data för aktuell vy](./media/quick-acm-cost-analysis/full-data-set.png)

I föregående bild visas resursgruppnamn. Visning av taggar för resurser är inte tillgängligt i någon av vyerna, filtren eller grupperna för kostnadsanalys.

När du grupperar kostnader efter ett specifikt attribut visas de tio viktigaste kostnadsfaktorerna från högsta till lägsta. Om det finns fler än tio grupper visas de nio viktigaste grupperna plus gruppen **Others** (Övriga), där alla övriga grupper ingår.

*Klassiska* virtuella datorer (Azure Service Management eller ASM), nätverk och lagringsresurser delar inte detaljerad faktureringsinformation. De slås samman som **klassiska tjänster** när du grupperar kostnader.


## <a name="download-cost-analysis-data"></a>Ladda ned kostnadsanalysdata

Du kan **ladda ned** information från kostnadsanalys att generera en CSV-fil för alla data som för närvarande visas i Azure-portalen. Alla filter eller grupper som du tillämpar ingår i filen. Underliggande data för totaldiagrammet längst upp som inte visas aktivt ingår i CSV-filen.

## <a name="next-steps"></a>Nästa steg

Gå vidare till den första självstudien om du vill lära dig att skapa och hantera budgetar.

> [!div class="nextstepaction"]
> [Skapa och hantera budgetar](tutorial-acm-create-budgets.md)
