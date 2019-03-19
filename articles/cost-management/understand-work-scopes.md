---
title: Förstå och arbeta med Azure Cost Management scope | Microsoft Docs
description: 'Den här artikeln hjälper dig att förstå fakturerings- och resource management omfång som är tillgängliga i Azure och hur du använder scope i Cost Management och API: er.'
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 4e7956e8873b552fcd73c51a51f51d99f21af324
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002930"
---
# <a name="understand-and-work-with-scopes"></a>Förstå och arbeta med omfång

Den här artikeln hjälper dig att förstå fakturerings- och resource management omfång som är tillgängliga i Azure och hur du använder scope i Cost Management och API: er.

## <a name="scopes"></a>Omfattningar

En _omfång_ är en nod i Azure-resurs-hierarki där Azure AD-användare komma åt och hantera tjänster. De flesta Azure-resurser skapas och distribueras till resursgrupper, vilket är en del av prenumerationer. Microsoft erbjuder även två hierarkier ovan Azure-prenumerationer som har specialiserat roller för att hantera faktureringsinformation:
- Faktureringsdata, till exempel betalningar och fakturor
- Molntjänster, till exempel kostnaden och princip

Scope är där du hantera faktureringsinformation, har rollerna som är specifika för betalningar, visa fakturor och utför allmänna kontohantering. Fakturerings- och roller som hanteras separat från de som används för resurshantering, som använder [Azure RBAC](../role-based-access-control/overview.md). För att tydligt skilja syftet med separata scope, inklusive åtkomstkontroll skillnader, dessa kallas _fakturering scope_ och _RBAC scope_respektive.

## <a name="how-cost-management-uses-scopes"></a>Hur Cost Management använder omfång

Cost Management fungerar på alla scope ovan resurser kan användas att hantera kostnaderna på nivån som de har åtkomst, oavsett om det är hela faktureringskonto eller en enskild resursgrupp. Fakturering scope variera beroende på ditt Microsoft-avtal (prenumerationstyp), RBAC-scope inte.

## <a name="azure-rbac-scopes"></a>Azure RBAC-scope

Azure stöder tre omfång för resurshantering. Varje område stöder hantera åtkomst och styrning, inklusive men inte begränsat till, kostnadshantering.

- [**Hanteringsgrupper** ](../governance/management-groups/index.md) -hierarkisk behållare, upp till åtta nivåer att organisera Azure-prenumerationer.

    Resurstyp: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Prenumerationer** -primära behållare för Azure-resurser.

    Resurstyp: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Resursgrupper** ](../azure-resource-manager/resource-group-overview.md#resource-groups) -logiska grupperingar av relaterade resurser för en Azure-lösning som delar samma livscykel. Till exempel resurser som distribueras och tas bort tillsammans.

    Resurstyp: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Hanteringsgrupper kan du ordna prenumerationer till en hierarki. Du kan till exempel skapa en logisk organisationshierarki med hjälp av hanteringsgrupper. Sedan kan ge teamen prenumerationer för produktion och dev/test-arbetsbelastningar. Och sedan skapa resursgrupper i prenumerationer att hantera varje undersystem eller komponent.

Skapa en organisationshierarki tillåter kostnad och principefterlevnad samlad OUI. Varje ledare kan sedan visa och analysera sina nuvarande kostnader. Och de kan sedan skapa budgetar för att bekämpa felaktiga utgiftsgränsen mönster och optimera kostnader med Advisor-rekommendationer på den lägsta nivån.

Bevilja åtkomst om du vill visa kostnaderna och du kan också hantera kostnaden konfiguration, till exempel budgetar och exporterar, utförs på styrning scope med hjälp av Azure RBAC. Du använder Azure RBAC för att bevilja Azure AD-användare och grupper åtkomst till om du vill utföra en fördefinierad uppsättning åtgärder som definierats i en roll på ett specifikt område och nedan. Exempelvis kan ger en roll som tilldelats till en grupp hanteringsomfång även samma behörigheter för att kapslade prenumerationer och resursgrupper.

Kostnadshantering stöder följande inbyggda roller för var och en av följande områden:

- [**Ägare** ](../role-based-access-control/built-in-roles.md#owner) – Visa kostnader och hantera allt, inklusive kostnaden konfiguration.
- [**Deltagare** ](../role-based-access-control/built-in-roles.md#contributor) – Visa kostnader och hantera allt, inklusive kostnaden konfiguration, men exklusive åtkomstkontroll.
- [**Läsare** ](../role-based-access-control/built-in-roles.md#reader) – kan visa allt, inklusive kostnadsdata och konfiguration, men inte göra några ändringar.
- [**Cost Management deltagare** ](../role-based-access-control/built-in-roles.md#cost-management-contributor) – Visa kostnader och hantera kostnaden konfiguration.
- [**Cost Management läsare** ](../role-based-access-control/built-in-roles.md#cost-management-reader) – kan visa kostnadsdata och konfiguration.

Cost Management-deltagare är den rekommenderade lägsta behörighet-rollen. Den låter personer behörighet att skapa och hantera budgetar och exporterar till mer effektivt, övervaka och rapportera om kostnaderna. Management kostnadsfaktorer kan också kräva ytterligare roller för scenarier för hantering av kostnaden för slutpunkt till slutpunkt. Tänk på följande scenarier:

- **Agerar vid budgetar överskrids** – Management kostnadsfaktorer också ha tillgång till skapa och/eller hantera åtgärdsgrupper för att automatiskt ta hänsyn till överförbrukning. Överväg att bevilja [övervakning deltagare](../role-based-access-control/built-in-roles.md#monitoring-contributor) till en resursgrupp som innehåller åtgärdsgruppen ska användas när budget tröskelvärden överskrids. Automatisera specifika åtgärder kräver ytterligare roller för de specifika tjänster som används, till exempel Automation och Azure Functions.
- **Schema kostnad dataexport** – Cost Management deltagare behöver också åtkomst till att hantera lagringskonton för att schemalägga en export kopiera data till ett lagringskonto. Överväg att bevilja [Lagringskontodeltagare](../role-based-access-control/built-in-roles.md#storage-account-contributor) konto där kostnadsdata exporteras till en resursgrupp som innehåller lagringen.
- **Visa rekommendationer för kostnadsbesparande** – Cost Management läsare och deltagare som inte har åtkomst till rekommendationer som standard. Åtkomst till rekommendationer kräver läsbehörighet till enskilda resurser. Överväg att bevilja [läsare](../role-based-access-control/built-in-roles.md#reader) eller en [roll tjänstspecifik](../role-based-access-control/built-in-roles.md#built-in-role-descriptions).

## <a name="enterprise-agreement-scopes"></a>Enterprise Agreement-scope

Enterprise Agreement (EA) fakturering konton, även kallat registreringar, har följande områden:

- [**Faktureringskonto** ](../billing/billing-view-all-accounts.md) -representerar en EA-registrering. Fakturor genereras i den här omfattningen. Köp som inte är baserat på användning, till exempel Marketplace och -reservationer är endast tillgängliga i den här omfattningen. De visas inte i avdelningar eller registreringskonton.

    Resurstyp: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Avdelning** – valfritt gruppering av registreringskonton.

    Resurstyp: `Billing/billingAccounts/departments`

- **Konto för enhetsregistreringshanterare** -representerar en enskild ägare. Stöder inte bevilja åtkomst till flera personer.

    Resurstyp: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Även om styrning scope är bundna till en enskild katalog, EA fakturering scope finns inte. Ett EA-faktureringskonto kan ha prenumerationer över flera Azure AD-kataloger.

EA fakturering scope stöd för följande roller:

- **Företagsadministratör** – kan hantera fakturering kontoinställningar och kan visa alla kostnader och kan hantera kostnaden konfiguration. Till exempel budget och exporterar. I funktionen EA fakturering omfånget är samma som [Cost Management deltagare Azure RBAC-roll](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Enterprise skrivskyddade användaren** – kan visa fakturering kontoinställningar, kostnadsdata och kostnaden konfiguration. Till exempel budget och exporterar. I funktionen EA fakturering omfånget är samma som den [Cost Management läsare Azure RBAC-roll](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Avdelning admin** – kan hantera avdelning inställningar, till exempel kostnadsställe, och kan komma åt, visa alla kostnader och hantera kostnaden konfiguration. Till exempel budget och exporterar.  Den **DA visa debiteringar** fakturering kontoinställningen måste vara aktiverat för avdelning administratörer och skrivskyddade användare att se kostnaderna. Om **DA visa debiteringar** är inaktiverad, avdelning användarna kan inte se kostnaderna när som helst, även om de är ägare konto eller prenumeration.
- **Avdelning skrivskyddade användaren** – kan visa inställningar för avdelning, kostnadsdata och kostnaden konfiguration. Till exempel budget och exporterar. Om **DA visa debiteringar** är inaktiverad, avdelning användarna kan inte se kostnaderna när som helst, även om de är ägare konto eller prenumeration.
- **Kontoinnehavare** – kan hantera registrering kontoinställningar (till exempel kostnadsställe), visa alla kostnader och hantera kostnaden konfigurationen (till exempel budgetar och exporterar) för registreringskontot. Den **AO visa debiteringar** fakturering kontoinställningen måste vara aktiverat för kontoinnehavare och RBAC-användare att se kostnaderna.

Fakturering EA-kontoanvändare har inte direkt åtkomst till fakturor. Fakturor är tillgängliga från ett externt system för volymlicensiering.

Azure-prenumerationer kapslas under registreringskonton. Fakturering användare har åtkomst till cost data för prenumerationer och resursgrupper som omfattas av deras respektive scope. De har inte åtkomst att visa eller hantera resurser i Azure-portalen. Fakturering användare kan visa kostnader genom att gå till **kostnadshantering + fakturering** i Azure portal listan över tjänster. De kan sedan filtrera kostnader som specifika prenumerationer och resursgrupper som de behöver för att rapportera om.

Fakturering användare har inte åtkomst till hanteringsgrupper uttryckligen inte faller under en viss faktureringskonto. Åtkomst måste uttryckligen beviljas till hanteringsgrupper. -Hanteringsgrupper samlad kostnaderna från alla kapslade prenumerationer. Men innehåller de endast användningsbaserad inköp. De omfattar inte inköp, till exempel reservationer och Marketplace-erbjudanden från tredje part. Du kan visa dessa kostnader med faktureringskonto för EA.

## <a name="individual-agreement-pay-as-you-go-scopes"></a>Omfång för enskilda avtal (betala per användning)

Betala per användning (PAYG) prenumerationer, inklusive relaterade typer som kostnadsfri utvärderingsversion/och utveckling och testning, inte har en explicit omfattning för fakturering konto. I stället har varje prenumeration en ägare eller kontoadministratören som EA ägare.

- [**Faktureringskonto** ](../billing/billing-view-all-accounts.md) -representerar en enskild ägare för en eller flera Azure-prenumerationer. Det stöder för närvarande inte bevilja åtkomst till flera personer eller åtkomst till aggregerad kostnaden vyer.

    Resurstyp: Inte tillämpligt

Konto för PAYG prenumerationsadministratörer kan visa och hantera faktureringen data, till exempel fakturor och betalningar, från den [Azure Kontocenter](https://account.azure.com/subscriptions). Men kan inte de visa kostnadsdata eller hantera resurser i Azure-portalen. Om du vill bevilja åtkomst till kontoadministratören, använda Cost Management-roller som tidigare nämnts.

Till skillnad från EA, kan PAYG konto prenumerationsadministratörer se sina fakturor i Azure-portalen. Tänk på att Cost Management läsare och deltagare i Cost Management inte ger åtkomst till fakturor. Mer information finns i [hur du ger åtkomst till PAYG-fakturor](../billing/billing-manage-access.md#give-access-to-billing).

## <a name="customer-agreement-scopes"></a>Kundens avtal scope

Fakturering kundavtal för Microsoft-konton har följande områden:

- **Faktureringskonto** -representerar en kundavtal för flera Microsoft-produkter och tjänster. Fakturering kundkonton för avtal är inte samma funktioner samma som EA-avtal. EA-avtal är närmare justerade fakturering profiler.

    Resurstyp: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Fakturering profil** -definierar de prenumerationer som ingår i en faktura. Fakturering profiler är den funktionella motsvarigheten till en EA-registrering, eftersom det är den omfattning som fakturor genereras vid. På samma sätt är inköp som inte är användningsbaserad (till exempel Marketplace och reservationer) bara tillgängliga i den här omfattningen. De ingår inte i faktura avsnitt.

    Resurstyp: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Fakturera avsnittet** -representerar en grupp med prenumerationer i en faktura eller profilen för fakturering. Faktura avsnitt liknar avdelningar – flera personer kan ha åtkomst till en faktura-avsnittet.

    Resurstyp: `Microsoft.Billing/billingAccounts/invoiceSections`

Till skillnad från EA fakturering scope, kundavtal fakturering konton _är_ bunden till en enskild katalog och kan inte ha prenumerationer över flera Azure AD-kataloger.

Kundens avtal fakturering scope stöd för följande roller:

- **Ägare** – kan hantera inställningar för fakturering och åtkomst, visa alla kostnader och hantera kostnaden konfiguration. Till exempel budget och exporterar. I funktionen, den här kundavtal fakturering omfånget är samma som den [Cost Management deltagare Azure RBAC-roll](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Deltagare** – kan hantera fakturering inställningar förutom åtkomst, visa alla kostnader och hantera kostnaden konfiguration. Till exempel budget och exporterar. I funktionen, den här kundavtal fakturering omfånget är samma som den [Cost Management deltagare Azure RBAC-roll](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Läsare** – kan visa inställningar för fakturering, kostnadsdata och kostnaden konfiguration. Till exempel budget och exporterar. I funktionen, den här kundavtal fakturering omfånget är samma som den [Cost Management läsare Azure RBAC-roll](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Faktura manager** – kan visa och betala fakturor och kan visa data och konfiguration. Till exempel budget och exporterar. I funktionen, den här kundavtal fakturering omfånget är samma som den [Cost Management läsare Azure RBAC-roll](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Azure-prenumeration skapare** – kan skapa Azure-prenumerationer, visa kostnader och hantera kostnaden konfiguration. Till exempel budget och exporterar. I funktion är standardomfattning kundavtal fakturering samma som rollen ägare konto för EA-registrering.

Azure-prenumerationer kapslas under faktura avsnitt, t.ex. hur de är under konton för EA-registrering. Fakturering användare har åtkomst till cost data för prenumerationer och resursgrupper som ligger under deras respektive scope. De har dock inte åtkomst till se eller hantera resurser i Azure-portalen. Fakturering användare kan visa kostnader genom att gå till **kostnadshantering + fakturering** i Azure portal listan över tjänster. Sedan kan filtrera kostnader som specifika prenumerationer och resursgrupper som de behöver för att rapportera om.

Fakturering användare har inte åtkomst till hanteringsgrupper eftersom de inte uttryckligen omfattas av faktureringskontot. Men när hanteringsgrupper har aktiverats för organisationen, alla prenumerationskostnader är samlade till faktureringskontot och till rot-hanteringsgruppen eftersom de är båda begränsade till en enda katalog. Hanteringsgrupper kan bara innehålla inköp som baseras på användning. Köp som reservationer och Marketplace-erbjudanden från tredje part inte finns med i hanteringsgrupperna. Fakturering konto och rot hanteringsgruppen kan därför rapporterar olika summor. Du kan visa dessa kostnader med faktureringskonto eller respektive fakturering profilen.

## <a name="cloud-solution-provider-csp-scopes"></a>Omfång för cloud Solution Provider (CSP)

Partner för cloud Solution Provider (CSP) stöds inte i Cost Management idag. Använd i stället [Partnercenter](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Växla mellan scope i Cost Management

Alla Cost Management-vyer i Azure-portalen innehåller en **omfång** pill längst upp till vänster i vyn. Du kan använda den för att snabbt ändra omfång. Klicka på den **omfång** pill att öppna omfattningsväljaren. Den visar fakturering konton, rot-hanteringsgruppen och prenumerationer som inte är kapslat under rot-hanteringsgruppen. För att välja ett omfång, klickar du på bakgrunden för att markera den och klicka sedan på **Välj** längst ned på sidan. Om du vill gå in på kapslade omfång som resursgrupper i en prenumeration, klickar du på länken omfång namn. Klicka för att välja den överordnade omfattningen när som helst kapslade **Välj det här &lt;omfång&gt;**  överst i omfattningsväljaren.

## <a name="identify-the-resource-id-for-a-scope"></a>Identifiera resurs-ID för ett omfång

Att veta omfånget är viktigt när du arbetar med Cost Management API: er. Använd följande information för att skapa rätt omfattning URI för Cost Management API: er.

### <a name="billing-accounts"></a>Faktureringskonton

1. Öppna Azure-portalen och gå sedan till **kostnadshantering + fakturering** i listan över tjänster.
2. Välj **egenskaper** i fakturering kontomenyn.
3. Kopiera fakturering konto-ID.
4. Din omfattning är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Faktureringsprofiler

1. Öppna Azure-portalen och gå sedan till **kostnadshantering + fakturering** i listan över tjänster.
2. Välj **fakturering profiler** i fakturering kontomenyn.
3. Klicka på namnet på den önskade fakturering profilen.
4. Välj **egenskaper** på profilmenyn fakturering.
5. Kopiera faktureringskontot och fakturering profil-ID: N.
6. Din omfattning är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Fakturaavsnitt

1. Öppna Azure-portalen och gå sedan till **kostnadshantering + fakturering** i listan över tjänster.
2. Välj **faktura avsnitt** i fakturering kontomenyn.
3. Klicka på namnet på det önskade faktura-avsnittet.
4. Välj **egenskaper** på menyn för faktura-avsnittet.
5. Kopiera faktureringskontot och fakturera avsnittet ID: N.
6. Din omfattning är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA-avdelningar

1. Öppna Azure-portalen och gå sedan till **kostnadshantering + fakturering** i listan över tjänster.
2. Välj **avdelningar** i fakturering kontomenyn.
3. Klicka på namnet på den önskade avdelningen.
4. Välj **egenskaper** på menyn avdelning.
5. Kopiera fakturering-ID-konto och avdelning.
6. Din omfattning är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Konto för EA-registrering

1. Öppna Azure portal och gå till **kostnadshantering + fakturering** i listan över tjänster.
2. Välj **registreringskonton** i fakturering kontomenyn.
3. Klicka på namnet på den önskade registreringskontot.
4. Välj **egenskaper** i kontomenyn registrering.
5. Kopiera faktureringskonto och registreringskontot ID: N.
6. Din omfattning är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Hanteringsgrupp

1. Öppna Azure portal och gå till **hanteringsgrupper** i listan över tjänster.
2. Navigera till den önskade hanteringsgruppen.
3. Kopiera hantering av grupp-ID från tabellen.
4. Din omfattning är: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Prenumeration

1. Öppna Azure portal och gå till **prenumerationer** i listan över tjänster.
2. Kopiera prenumerations-ID från tabellen.
3. Din omfattning är: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Resursgrupper

1. Öppna Azure portal och gå till **resursgrupper** i listan över tjänster.
2. Klicka på namnet på resursgruppen som önskade.
3. Välj **egenskaper** i resursgruppmenyn.
4. Kopiera värdet i resurs-ID: T.
5. Din omfattning är: `"/subscriptions/{id}/resourceGroups/{name}"`

Kostnadshantering stöds för närvarande i [Azure Global](https://management.azure.com) och [Azure Government](https://management.usgovcloudapi.net). Läs mer om Azure Government, [Azure Global och Government API-slutpunkter](../azure-government/documentation-government-developer-guide.md#endpoint-mapping)_._

## <a name="next-steps"></a>Nästa steg

- Om du inte har redan slutfört den första snabbstarten för kostnadshantering, kan du läsa den på [börja analysera kostnaderna](quick-acm-cost-analysis.md).
