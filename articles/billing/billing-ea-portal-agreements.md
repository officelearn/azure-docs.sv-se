---
title: Azure EA-avtal och ändringar
description: I den här artikeln förklaras hur Azure EA-avtal och ändringar påverkar din användning av Azure EA-portalen.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 4af0d92b724b3919cf6535910644cac20ce2a7e7
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901091"
---
# <a name="azure-ea-agreements-and-amendments"></a>Azure EA-avtal och ändringar

I den här artikeln beskrivs hur Azure EA-avtal och ändringar kan påverka hur du använder och betalar för Azure-tjänster.

## <a name="amendment-status"></a>Ändringsstatus

Eventuella ändringar för Azure-kunder måste göras via partnern eller Microsoft-kontoteamet och behandlas via det regionala driftcentret. Om du tror att en ändring inte har behandlats kan du kontakta din partner, programvaruguide eller Microsoft-kontoteamet.

## <a name="enrollment-provisioning-status"></a>Status för registreringens etablering

Startdatumet för ett nytt ekonomiskt åtagande definieras som det datum då det regionala åtgärdscentret bearbetade det. Du ser startdatumet för det ekonomiska åtagandet som startdatumet för täckningen i inköpsordern på https://www.explore.ms. Startdatumet för täckningen är när det ekonomiska åtagandet visas i Azure EA-portalen.

## <a name="special-pricing-and-acd-amendments"></a>Särskilda priser och ACD-ändringar

Om du behöver särskilda priser och ACD-ändringar (Azure Commitment discount) måste du diskutera dem med ditt kontoteam. Azure Ops-teamet kan tillämpa ACD när en ändring har bearbetats av affärskontoret och en begäran har skickats till Azure Ops-teamet. ACD-rabatten gäller endast den aktuella månaden. Eventuella tidigare månader kompenseras med en tidigarelagd kredit. 

## <a name="commitment-not-added-to-enrollment"></a>Åtagande som inte lagts till i registreringen

När en inköpsorder för ett ekonomiskt åtagande skickas av din partner och bearbetas av det regionala driftcentret ska den visas i registreringen. Om du inte ser den i registreringen kontaktar du Azure-supporten för att ta reda på varför.

Ett åtagande som bearbetas senare än den andra dagen i månaden visas i Azure EA-portalen den första dagen följande månad.

## <a name="support-offer-not-provisioned"></a>Supporterbjudandet har inte etablerats

Du kan beställa standardsupport eller Pro-Direct-support genom att köpa supportspecifika SKU:er. Beställningen liknar EA Azure-inköpsordrar för monetära SKU:er. Exempelvis 6QK-00001. SKU-numren för support är W6T-00002 (Pro-Direct) och W6T-00003 (Standard). Kontrollera aktuella supporterbjudanden innan du köper support-SKU:er.

Registreringen måste ha minst ett aktivt konto för att supporterbjudandet ska etableras.

 Azures [EA-supportavtalserbjudande](https://azure.microsoft.com/offers/enterprise-agreement-support/) är tillgängligt för vissa kunder.

## <a name="enrollment-status"></a>Registreringsstatus

Registreringar har något av följande statusvärden. Varje värde avgör hur du kan använda registreringen. Registreringsstatusen avgör i vilket stadie registreringen är. Statusen anger om du måste aktivera registreringen innan du kan använda den. Du kan också se om inledande perioden har upphört så att du debiteras för överanvändning.

**Väntar** – registreringsadministratören måste logga in i Azure EA-portalen. Därefter får registreringen statusen **Aktiv**.

**Aktiv** – registreringen är tillgänglig och kan användas. Du kan skapa konton och prenumerationer i Azure EA-portalen. Registreringen är aktiv tills Enterprise-avtalets slutdatum.

**Obegränsad utökad giltighet** – statusen Obegränsad utökad giltighet tilldelas efter Enterprise-avtalets slutdatum. Innan EA-registreringen når Enterprise-avtalets slutdatum bör registreringsadministratören välja att:

- Förnya registreringen genom att lägga till ett nytt ekonomiskt åtagande
- Överföra den befintliga registreringen till en ny registrering
- Migrera till MOSP-programmet (Microsoft Online Subscription program)
- Bekräfta inaktiveringen av alla tjänster som är kopplade till registreringen

**Upphört** – när EA-registreringen når Enterprise-avtalets slutdatum så upphör registreringen att gälla. EA-kunden avregistreras från den utökade giltigheten och alla tjänster inaktiveras.

Från och med den 1 augusti 2019 godkänns inte nya avanmälningsformulär för Azures kommersiella kunder. I stället försätts alla registreringar i statusen Obegränsad utökad giltighet. Om du vill sluta använda Azure-tjänsterna stänger du din prenumeration i [Azure-portalen](https://portal.azure.com). Eller så kan din partner skicka en uppsägningsförfrågan till https://www.explore.ms. Kunder med avtal för myndigheter påverkas inte.

**Överförd** – statusen Överförd används för registreringar vars konton och tjänster har överförts till en ny registrering. Registreringar överförs inte automatiskt om ett nytt registreringsnummer genereras vid förnyandet. Det tidigare registreringsnumret måste tas med i kundens förnyelsebegäran om överföringen ska ske automatiskt.

## <a name="price-protection-and-increasing-cost"></a>Prisskydd och ökande kostnader

En kunds basprissättning är det låsta och garanterade priset. Den skyddas av dokumentet Enterprise Azure Retail Pricing som publicerades när avtalet trädde i kraft. När den inledande registreringsperioden har passerat gäller Betala per användning-priser igen.

Om du vill visa ditt förhandlade prisdokument i Azure EA-portalen går du till **Rapporter** > klicka på **Prisdokument** > **Ladda ned**.

Om du är en indirekt kund kan du kontakta din partner för att få prisdokumentet och information om priserna.

## <a name="partner-markup"></a>Pålägg för partner

Påläggen för partner förbättrar kundernas kostnadsrapportering i Azure EA-portalen. I Azure EA-portalen visas den användning och de priser som partnern har konfigurerat för kunden.

Påläggen gör att partneradministratörer kan lägga till ett procentuellt prispålägg på de indirekta Enterprise-avtalen. Det procentuella pålägget gäller för alla Microsofts förstapartstjänster i Azure EA-portalen. Några exempel är mätarpriser, ekonomiska åtaganden och beställningar. När partnern har publicerat pålägget ser kunderna kostnaderna för Azure i Azure-portalen. Några exempel är användningssammanfattningar, prislistor och nedladdade användningsrapporter.

Från och med september 2019 kan en partner lägga till påslag när som helst under en tidsperiod. De behöver inte vänta till nästa tidsperiod för att tillämpa pålägget.

## <a name="enrollment-not-created"></a>Registreringen har inte skapats

Azure EA-registreringar etableras när någon har gjort ett ekonomiskt åtagande. När en inköpsorder för ett ekonomiskt åtagande har lagts via din partner och bearbetas vid det regionala driftcentret visas den i Azure EA-portalen.

## <a name="next-steps"></a>Nästa steg

- Om du vill börja använda Azures EA-portalen kan du läsa [Kom igång med Azure EA-portalen](billing-ea-portal-get-started.md).
- Administratörer i Azure EA-portalen bör läsa [Administration i Azure EA-portalen](billing-ea-portal-administration.md). Där finns mer information om vanliga administrativa uppgifter.
