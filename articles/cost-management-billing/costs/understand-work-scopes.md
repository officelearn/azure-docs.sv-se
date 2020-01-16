---
title: Förstå och arbeta med Azure Cost Management omfattningar
description: 'Den här artikeln hjälper dig att förstå omfattningarna för fakturering och resurs hantering i Azure och hur du använder omfattningarna i Cost Management och API: er.'
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 58bd1d3e3fb27344706b23866a68c7e1363e7ec2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990143"
---
# <a name="understand-and-work-with-scopes"></a>Förstå och arbeta med omfång

Den här artikeln hjälper dig att förstå omfattningarna för fakturering och resurs hantering i Azure och hur du använder omfattningarna i Cost Management och API: er.

## <a name="scopes"></a>Omfattningar

Ett _omfång_ är en nod i Azure-hierarkistrukturen där Azure AD-användare får åtkomst till och hanterar tjänster. De flesta Azure-resurser skapas och distribueras till resurs grupper som ingår i prenumerationer. Microsoft erbjuder även två hierarkier över Azure-prenumerationer som har specialiserade roller för att hantera fakturerings data:
- Fakturerings data, till exempel betalningar och fakturor
- Moln tjänster, till exempel kostnads-och princip styrning

Med omfattningar kan du hantera fakturerings data, ha roller som är begränsade till betalningar, Visa fakturor och utföra allmän konto hantering. Fakturerings-och konto roller hanteras separat från de som används för resurs hantering, som använder [Azure RBAC](../../role-based-access-control/overview.md). För att tydligt särskilja syftet med de separata omfattningarna, inklusive åtkomst kontroll skillnaderna, kallas dessa för _fakturerings omfång_ respektive _RBAC-omfång_.

## <a name="how-cost-management-uses-scopes"></a>Hur Cost Management använder omfång

Cost Management fungerar i alla omfattningar över resurser så att organisationer kan hantera kostnader på den nivå där de har åtkomst, oavsett om det är hela fakturerings kontot eller en enda resurs grupp. Även om fakturerings omfång skiljer sig beroende på ditt Microsoft-avtal (prenumerations typ) gör inte RBAC-omfattningarna det.

## <a name="azure-rbac-scopes"></a>Azure RBAC-omfång

Azure stöder tre områden för resurs hantering. Varje omfattning stöder hantering av åtkomst och styrning, inklusive men inte begränsat till, kostnads hantering.

- [**Hanterings grupper**](../../governance/management-groups/overview.md) – hierarkiska behållare, upp till åtta nivåer, för att organisera Azure-prenumerationer.

    Resurs typ: [Microsoft. Management/managementGroups](/rest/api/resources/managementgroups)

- **Prenumerationer** – primära behållare för Azure-resurser.

    Resurs typ: [Microsoft. Resources/Subscriptions](/rest/api/resources/subscriptions)

- [**Resurs grupper**](../../azure-resource-manager/management/overview.md#resource-groups) – logiska grupperingar av relaterade resurser för en Azure-lösning som delar samma livs cykel. Till exempel resurser som distribueras och tas bort tillsammans.

    Resurs typ: [Microsoft. Resources/Subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Med hanterings grupper kan du organisera prenumerationer i en hierarki. Du kan till exempel skapa en logisk organisationshierarki med hjälp av hanterings grupper. Sedan ger du team prenumerationer för produktions-och dev/test-arbetsbelastningar. Och skapa sedan resurs grupper i prenumerationerna för att hantera varje del system eller komponent.

Genom att skapa en organisationshierarki kan du samla in kostnader och principer för efterlevnad av principer. Sedan kan varje ledare Visa och analysera sina aktuella kostnader. De kan sedan skapa budgetar för att göra dåliga utgifter och optimera kostnaderna med rådgivarens rekommendationer på den lägsta nivån.

Att bevilja åtkomst till att Visa kostnader och eventuellt hantera kostnads konfiguration, till exempel budgetar och exporter, utförs på styrnings omfattningar med Azure RBAC. Du använder Azure RBAC för att ge Azure AD-användare och-grupper åtkomst för att utföra en fördefinierad uppsättning åtgärder som definieras i en roll i en viss omfattning och nedan. En roll som tilldelas till en hanterings grupps omfattning ger till exempel även samma behörigheter till kapslade prenumerationer och resurs grupper.

Cost Management stöder följande inbyggda roller för var och en av följande omfattningar:

- [**Owner**](../../role-based-access-control/built-in-roles.md#owner) – kan visa kostnader och hantera allt, inklusive kostnads konfiguration.
- [**Deltagare**](../../role-based-access-control/built-in-roles.md#contributor) – kan visa kostnader och hantera allt, inklusive kostnads konfiguration, men exklusive åtkomst kontroll.
- [**Reader**](../../role-based-access-control/built-in-roles.md#reader) – kan visa allt, inklusive kostnads data och konfiguration, men kan inte göra några ändringar.
- [**Cost Management Contributor**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) – kan visa kostnader, hantera kostnads konfiguration och Visa rekommendationer.
- [**Cost Management läsare**](../../role-based-access-control/built-in-roles.md#cost-management-reader) – kan visa kostnads data, kostnads konfiguration och Visa rekommendationer.

Cost Management Contributor är den rekommenderade rollen för minsta behörighet. Det ger användare åtkomst till att skapa och hantera budgetar och export till mer effektiv övervakning och rapportering om kostnader. Cost Managements deltagare kan också kräva ytterligare roller för att stödja scenarier från slut punkt till slut punkt för kostnads hantering. Beakta följande scenarier:

- **Agera när budgetar överskrids** – Cost Management deltagare måste också ha åtkomst till skapa och/eller hantera åtgärds grupper för att automatiskt reagera på överförbrukning. Överväg att bevilja [övervaknings deltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor) till en resurs grupp som innehåller den åtgärds grupp som ska användas när budget trösklar överskrids. Att automatisera vissa åtgärder kräver ytterligare roller för de tjänster som används, till exempel Automation och Azure Functions.
- **Schema för kostnads data export** – Cost Management deltagare måste också ha åtkomst till hantera lagrings konton för att schemalägga en export för att kopiera data till ett lagrings konto. Överväg att bevilja [lagrings konto deltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor) till en resurs grupp som innehåller det lagrings konto där kostnads data exporteras.
- **Visa kostnads besparingar** – Cost Management läsare och Cost Management deltagare *har till gång till kostnads rekommendationer* som standard. Åtkomst till att agera på kostnads rekommendationer kräver dock åtkomst till enskilda resurser. Överväg att bevilja en [tjänstebestämd roll](../../role-based-access-control/built-in-roles.md#built-in-role-descriptions) om du vill agera med en kostnads baserad rekommendation.

## <a name="enterprise-agreement-scopes"></a>Enterprise-avtal omfattningar

Enterprise-avtal (EA) fakturerings konton, som även kallas registreringar, har följande omfång:

- [**Fakturerings konto**](../manage/view-all-accounts.md) – representerar EA-registrering. Fakturor skapas i det här omfånget. Köp som inte används, till exempel Marketplace och reservationer, är bara tillgängliga i det här omfånget. De representeras inte på avdelningar eller registrerings konton.

    Resurs typ: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Avdelning** – valfri gruppering av registrerings konton.

    Resurs typ: `Billing/billingAccounts/departments`

- **Registrerings konto** – representerar en enda konto ägare. Har inte stöd för att bevilja åtkomst till flera personer.

    Resurs typ: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Även om styrnings omfattningar är bundna till en enda katalog är EA-fakturerings omfattningar inte. Ett EA-fakturerings konto kan ha prenumerationer i valfritt antal Azure AD-kataloger.

EA-fakturerings omfattningar stöder följande roller:

- **Företags administratör** – kan hantera inställningar för fakturerings konton och åtkomst, kan visa alla kostnader och kan hantera kostnads konfiguration. Till exempel budgetar och export. I funktion är EA-fakturerings omfånget detsamma som [Cost Management Contributor-rollen Azure RBAC](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Användare med Läs** behörighet för företag – kan visa inställningar för fakturerings konto, kostnads data och kostnads konfiguration. Till exempel budgetar och export. I funktion är EA-fakturerings omfånget samma som [Cost Management Reader Azure RBAC-rollen](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Avdelnings administratör** – kan hantera avdelnings inställningar, till exempel kostnads ställe, och kan komma åt, Visa alla kostnader och hantera kostnads konfigurationen. Till exempel budgetar och export.  Debiterings konto inställningen för **da View** måste vara aktive rad för avdelnings administratörer och skrivskyddade användare för att se kostnaderna. Om **Visa avgifter för da** är inaktiverat kan inte avdelnings användare se kostnader på någon nivå, även om de är ett konto eller en prenumerations ägare.
- **Läs** behörighet för avdelning – kan visa avdelnings inställningar, kostnads data och kostnads konfiguration. Till exempel budgetar och export. Om **Visa avgifter för da** är inaktiverat kan inte avdelnings användare se kostnader på någon nivå, även om de är ett konto eller en prenumerations ägare.
- **Konto ägare** – kan hantera inställningar för registrerings kontot (till exempel kostnads ställe), Visa alla kostnader och hantera kostnads konfigurationen (till exempel budgetar och export) för registrerings kontot. Fakturerings konto inställningen för **Ao View** måste vara aktive rad för konto ägare och RBAC-användare för att se kostnaderna.

Användare av EA-fakturerings konto har inte direkt åtkomst till fakturor. Fakturor är tillgängliga från ett externt volym licensierings system.

Azure-prenumerationer kapslas under registrerings konton. Fakturerings användare har till gång till kostnads data för prenumerationer och resurs grupper som tillhör respektive omfång. De har inte åtkomst att se eller hantera resurser i Azure Portal. Fakturerings användare kan visa kostnader genom att gå till **Cost Management + fakturering** i Azure Portal listan över tjänster. Sedan kan de filtrera kostnader till de olika prenumerationer och resurs grupper som de behöver för att rapportera om.

Fakturerings användare har inte åtkomst till hanterings grupper eftersom de inte uttryckligen faller under ett specifikt fakturerings konto. Åtkomst måste beviljas till hanterings grupper explicit. Sammanslagnings kostnader för hanterings grupper från alla kapslade prenumerationer. De omfattar dock bara användnings inköp. De omfattar inte köp som reservationer och Marketplace-erbjudanden från tredje part. Om du vill visa dessa kostnader använder du kontot för EA-fakturering.

## <a name="individual-agreement-scopes"></a>Enskilda avtals områden

Azure-prenumerationer som skapats från enskilda erbjudanden som betala per användning och relaterade typer, till exempel kostnads fria utvärderings versioner och utveckling/testning, har ingen explicit fakturerings konto omfattning. I stället har varje prenumeration en konto ägare eller konto administratör, till exempel EA-kontots ägare.

- [**Fakturerings konto**](../manage/view-all-accounts.md) – representerar en enda konto ägare för en eller flera Azure-prenumerationer. Den har för närvarande inte stöd för att bevilja åtkomst till flera personer eller åtkomst till sammanställda kostnads vyer.

    Resurs typ: inte tillämpligt

Enskilda Azure-prenumerations konto administratörer kan visa och hantera fakturerings data, till exempel fakturor och betalningar, från [Azure-kontocenter](https://account.azure.com/subscriptions). De kan dock inte visa kostnads data eller hantera resurser i Azure Portal. Om du vill bevilja åtkomst till konto administratören använder du Cost Management roller som nämns ovan.

Till skillnad från EA kan enskilda Azure-prenumerations konto administratörer se sina fakturor i Azure Portal. Tänk på att Cost Management läsare och Cost Management deltagar roller inte ger åtkomst till fakturor. Mer information finns i [så här beviljar du åtkomst till fakturor](../manage/manage-billing-access.md#give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Microsofts kund avtals områden

Fakturerings konton för Microsofts kund avtal har följande omfång:

- **Fakturerings konto** – representerar ett kund avtal för flera produkter och tjänster från Microsoft. Fakturerings konton för kund avtal fungerar inte på samma sätt som EA-registreringar. EA-registreringar är mer noggrant justerade för fakturerings profiler.

    Resurs typ: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Fakturerings profil** – definierar de prenumerationer som ingår i en faktura. Fakturerings profiler är den funktionella motsvarigheten till en EA-registrering, eftersom det är det omfång som fakturor genereras på. På samma sätt är inköp som inte används (till exempel Marketplace och reservationer) bara tillgängliga i det här omfånget. De ingår inte i faktura avsnitten.

    Resurs typ: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Avsnittet faktura** – representerar en grupp prenumerationer i en faktura eller fakturerings profil. Faktura avsnitt är som avdelningar – flera personer kan ha åtkomst till avsnittet faktura.

    Resurs typ: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Kund** – representerar en grupp prenumerationer som är kopplade till en specifik kund som har publicerats till ett kund avtal från Microsoft per partner. Det här omfånget är begränsat till CSP.

Till skillnad från EA-fakturerings omfattningar _är_ kund avtals fakturerings konton kopplade till en enda katalog och kan inte ha prenumerationer i flera Azure AD-kataloger.

Fakturerings omfång för kund avtal gäller inte för partner. Partner roller och behörigheter dokumenteras i [Tilldela användar roller och behörigheter](/partner-center/permissions-overview).

Fakturerings omfattningar för kund avtal har stöd för följande roller:

- **Owner** – kan hantera fakturerings inställningar och åtkomst, Visa alla kostnader och hantera kostnads konfiguration. Till exempel budgetar och export. I funktion är detta fakturerings omfång för kund avtal detsamma som [rollen Cost Management Contributor Azure RBAC](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Deltagare** – kan hantera fakturerings inställningar förutom åtkomst, Visa alla kostnader och hantera kostnads konfiguration. Till exempel budgetar och export. I funktion är detta fakturerings omfång för kund avtal detsamma som [rollen Cost Management Contributor Azure RBAC](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Reader** – kan visa fakturerings inställningar, kostnads data och kostnads konfiguration. Till exempel budgetar och export. I funktion är detta fakturerings omfång för kund avtal detsamma som [Cost Management Reader Azure RBAC-rollen](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Faktura ansvarig** – kan visa och betala fakturor och kan visa kostnads data och konfiguration. Till exempel budgetar och export. I funktion är detta fakturerings omfång för kund avtal detsamma som [Cost Management Reader Azure RBAC-rollen](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Azure-prenumerations skapare** – kan skapa Azure-prenumerationer, Visa kostnader och hantera kostnads konfiguration. Till exempel budgetar och export. I funktion är detta fakturerings omfång för kund avtal detsamma som ägar rollen EA-registrerings konto.

Azure-prenumerationer kapslas under faktura avsnitt, t. ex. hur de är under EA-registrerings konton. Fakturerings användare har till gång till kostnads data för prenumerationer och resurs grupper som tillhör respektive omfång. De har dock inte åtkomst att se eller hantera resurser i Azure Portal. Fakturerings användare kan visa kostnader genom att gå till **Cost Management + fakturering** i Azure Portal listan över tjänster. Filtrera sedan kostnaderna till de olika prenumerationer och resurs grupper som de måste rapportera om.

Fakturerings användare har inte åtkomst till hanterings grupper eftersom de inte uttryckligen faller under fakturerings kontot. Men när hanterings grupper aktive ras för organisationen, samlas alla prenumerations kostnader upp till fakturerings kontot och till rot hanterings gruppen eftersom de båda är begränsade till en enda katalog. Hanterings grupper innehåller endast inköp som är användnings. Köp som reservationer och tredje parts Marketplace-erbjudanden ingår inte i hanterings grupper. Därför kan fakturerings kontot och rot hanterings gruppen rapportera olika summor. Om du vill visa dessa kostnader använder du fakturerings kontot eller respektive fakturerings profil.

## <a name="aws-scopes"></a>AWS-omfång

När AWS-integreringen är klar kan du läsa mer i [Konfigurera och konfigurera AWS-integrering](aws-integration-set-up-configure.md). Följande omfattningar är tillgängliga:

- **Externt fakturerings konto** – representerar ett kund avtal med en tredjepartsleverantör. Det liknar fakturerings kontot för EA.

    Resurs typ: `Microsoft.CostManagement/externalBillingAccounts`

- **Extern prenumeration** – representerar ett kund drifts konto med en tredjepartsleverantör. Det liknar en Azure-prenumeration.

    Resurs typ: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Omfattningar för Cloud Solution Provider (CSP)

Följande omfattningar stöds för CSP: er med kunder i ett Microsoft-kund avtal:

- **Fakturerings konto** – representerar ett kund avtal för flera produkter och tjänster från Microsoft. Fakturerings konton för kund avtal fungerar inte på samma sätt som EA-registreringar. EA-registreringar är mer noggrant justerade för fakturerings profiler.

    Resurs typ: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Fakturerings profil** – definierar de prenumerationer som ingår i en faktura. Fakturerings profiler är den funktionella motsvarigheten till en EA-registrering, eftersom det är det omfång som fakturor genereras på. På samma sätt är inköp som inte används (till exempel Marketplace och reservationer) bara tillgängliga i det här omfånget.

    Resurs typ: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Kund** – representerar en grupp prenumerationer som är kopplade till en specifik kund som har publicerats till ett kund avtal från Microsoft av en partner.

Endast användare med rollen *Global administratör* och *administratör* kan hantera och Visa kostnader för fakturerings konton, fakturerings profiler och kunder direkt i partnerns Azure-klient. Mer information om Partner Center-roller finns i [Tilldela användar roller och behörigheter](/partner-center/permissions-overview).

Azure Cost Management stöder bara CSP-partner kunder om kunderna har ett Microsoft-kundavtal. För CSP som har stöd för kunder som ännu inte har ett kund avtal från Microsoft, se [partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Växla mellan omfattningar i Cost Management

Alla Cost Management vyer i Azure Portal innehåller en **omfattnings** markerings Pill längst upp till vänster i vyn. Använd den för att snabbt ändra omfattningen. Öppna omfattnings väljaren genom att klicka på **området** Pill. Det visar fakturerings konton, rot hanterings gruppen och eventuella prenumerationer som inte är kapslade under rot hanterings gruppen. Om du vill välja ett omfång klickar du på bakgrunden för att markera den och klickar sedan på **Välj** längst ned. Om du vill öka detalj nivån till kapslade omfattningar, t. ex. resurs grupper i en prenumeration, klickar du på länken omfångs namn. Om du vill välja överordnat omfång på en kapslad nivå klickar du på **Välj den här &lt;omfattningen&gt;** överst i omfattnings väljaren.

## <a name="identify-the-resource-id-for-a-scope"></a>Identifiera resurs-ID för ett omfång

När du arbetar med Cost Management-API: er är det viktigt att känna till omfattningen. Använd följande information för att bygga rätt omfattnings-URI för Cost Management API: er.

### <a name="billing-accounts"></a>Fakturerings konton

1. Öppna Azure Portal och gå sedan till **Cost Management + fakturering** i listan över tjänster.
2. Välj **Egenskaper** på menyn fakturerings konto.
3. Kopiera ID för fakturerings konto.
4. Ditt omfång är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Faktureringsprofiler

1. Öppna Azure Portal och gå sedan till **Cost Management + fakturering** i listan över tjänster.
2. Välj **fakturerings profiler** på menyn fakturerings konto.
3. Klicka på namnet på önskad fakturerings profil.
4. Välj **Egenskaper** på menyn fakturerings profil.
5. Kopiera ID för fakturerings konto och fakturerings profil.
6. Ditt omfång är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Fakturaavsnitt

1. Öppna Azure Portal och gå sedan till **Cost Management + fakturering** i listan över tjänster.
2. Välj **faktura avsnitt** på menyn fakturerings konto.
3. Klicka på namnet på det önskade faktura avsnittet.
4. Välj **Egenskaper** på menyn faktura avsnitt.
5. Kopiera ID för fakturerings konto och faktura avsnitt.
6. Ditt omfång är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA-avdelningar

1. Öppna Azure Portal och gå sedan till **Cost Management + fakturering** i listan över tjänster.
2. Välj **avdelningar** på menyn fakturerings konto.
3. Klicka på namnet på den önskade avdelningen.
4. Välj **Egenskaper** på avdelnings menyn.
5. Kopiera fakturerings kontot och avdelnings-ID: n.
6. Ditt omfång är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA-registrerings konto

1. Öppna Azure Portal och gå till **Cost Management + fakturering** i listan över tjänster.
2. Välj **registrerings konton** på menyn fakturerings konto.
3. Klicka på namnet på det önskade registrerings kontot.
4. Välj **Egenskaper** på menyn registrerings konto.
5. Kopiera fakturerings kontot och ID för registrerings kontot.
6. Ditt omfång är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Hanteringsgrupp

1. Öppna Azure Portal och gå till **hanterings grupper** i listan över tjänster.
2. Navigera till önskad hanterings grupp.
3. Kopiera hanterings gruppens ID från tabellen.
4. Ditt omfång är: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Prenumeration

1. Öppna Azure Portal och navigera till **prenumerationer** i listan över tjänster.
2. Kopiera prenumerations-ID: t från tabellen.
3. Ditt omfång är: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Resursgrupper

1. Öppna Azure Portal och navigera till **resurs grupper** i listan över tjänster.
2. Klicka på namnet på den önskade resurs gruppen.
3. Välj **Egenskaper** på menyn resurs grupp.
4. Kopiera värdet för resurs-ID-fältet.
5. Ditt omfång är: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management stöds för närvarande i [Azure Global](https://management.azure.com) och [Azure Government](https://management.usgovcloudapi.net). Mer information om Azure Government finns i [Azures globala och offentliga API-slutpunkter](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping).

## <a name="next-steps"></a>Nästa steg

- Om du inte har redan slutfört den första snabbstarten för kostnadshantering, kan du läsa den på [börja analysera kostnaderna](quick-acm-cost-analysis.md).
