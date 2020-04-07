---
title: Miljöer för hantering av flera klienter
description: Azure-delegerad resurshantering möjliggör en hantering av flera innehavare.
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 0ac5d62fbf6b6ee418cd4b2f2b00dfc12e05f809
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754145"
---
# <a name="cross-tenant-management-experiences"></a>Miljöer för hantering av flera klienter

Som tjänsteleverantör kan du använda [Azure-delegerad resurshantering](../concepts/azure-delegated-resource-management.md) för att hantera Azure-resurser för flera kunder från din egen klient i [Azure-portalen](https://portal.azure.com). De flesta uppgifter och tjänster kan utföras på delegerade Azure-resurser över hanterade klienter. I den här artikeln beskrivs några av de förbättrade scenarierna där Azure-delegerad resurshantering kan vara effektiv.

> [!NOTE]
> Azure-delegerad resurshantering kan också användas [i ett företag som har flera Azure AD-klienter för egen hand](enterprise.md) för att förenkla administration mellan innehavare.

## <a name="understanding-customer-tenants"></a>Förstå kundklienter

En Azure Active Directory-klient (Azure AD) är en representation av en organisation. Det är en dedikerad instans av Azure AD som en organisation tar emot när de skapar en relation med Microsoft genom att registrera dig för Azure, Microsoft 365 eller andra tjänster. Varje Azure AD-klient är distinkt och separat från andra Azure AD-klienter och har ett eget klient-ID (ett GUID). Mer information finns i [Vad är Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Vanligtvis måste tjänsteleverantörer logga in på Azure-portalen för att hantera Azure-resurser för att hantera Azure-resurser för att hantera Azure-resurser för att kunna hantera Azure-resurser för att kunna hantera Azure-resurser för att skapa och hantera användarkonton för tjänsteleverantören.

Med Azure-delegerad resurshantering anger introduktionsprocessen användare inom tjänsteleverantörens klientorganisation som ska kunna komma åt och hantera prenumerationer, resursgrupper och resurser i kundens klientorganisation. Dessa användare kan sedan logga in på Azure-portalen med sina egna autentiseringsuppgifter. Inom Azure-portalen kan de hantera resurser som tillhör alla kunder som de har åtkomst till. Detta kan göras genom att besöka sidan [Mina kunder](../how-to/view-manage-customers.md) i Azure-portalen eller genom att arbeta direkt inom ramen för kundens prenumeration, antingen i Azure-portalen eller via API:er.

Azure-delegerad resurshantering ger större flexibilitet att hantera resurser för flera kunder utan att behöva logga in på olika konton i olika klienter. En tjänsteleverantör kan till exempel ha tre kunder med olika ansvarsområden och åtkomstnivåer, som visas här:

![Tre kundklienter som visar tjänsteleverantörens ansvar](../media/azure-delegated-resource-management-customer-tenants.jpg)

Med hjälp av Azure-delegerad resurshantering kan behöriga användare logga in på tjänsteleverantörens klient för att komma åt dessa resurser, som visas här:

![Kundresurser hanteras via en tjänsteleverantörsklient](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Stöd för API:er och hanteringsverktyg

Du kan utföra hanteringsuppgifter på delegerade resurser antingen direkt i portalen eller med hjälp av API:er och hanteringsverktyg (till exempel Azure CLI och Azure PowerShell). Alla befintliga API:er kan användas när du arbetar med delegerade resurser, så länge funktionen stöds för hantering av flera innehavare och användaren har rätt behörighet.

Cmdlet för Azure PowerShell [Get-AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) visar **klient-ID:t** för varje prenumeration, så att du kan identifiera om en returnerad prenumeration tillhör din tjänstleverantörsklient eller till en hanterad kundklient.

På samma sätt visar Azure CLI-kommandon som [az-kontolista](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) **attributen homeTenantId** och **managedByTenants.**

> [!TIP]
> Om du inte ser dessa värden när du använder Azure `az account clear` CLI `az login --identity`kan du prova att rensa cacheminnet genom att köra följt av .

Vi tillhandahåller även API:er som är specifika för att utföra Azure-delegerade resurshanteringsuppgifter. Mer information finns i avsnittet **Referens.**

## <a name="enhanced-services-and-scenarios"></a>Förbättrade tjänster och scenarier

De flesta uppgifter och tjänster kan utföras på delegerade resurser över hanterade klienter. Nedan följer några av de viktigaste scenarierna där hantering av flera innehavare kan vara effektiv.

[Azure Arc för servrar (förhandsversion):](../../azure-arc/servers/overview.md)

- [Ansluta Windows Server- eller Linux-datorer utanför Azure](../../azure-arc/servers/quickstart-onboard-portal.md) till delegerade prenumerationer och/eller resursgrupper i Azure
- Hantera anslutna datorer med Azure-konstruktioner, till exempel Azure-princip och taggning

[Azure Automation](../../automation/index.yml):

- Använda automatiseringskonton för att komma åt och arbeta med delegerade kundresurser

[Azure-säkerhetskopiering:](../../backup/index.yml)

- Säkerhetskopiera och återställa kunddata i kundklienter
- Använd [Säkerhetskopieringsutforskaren](../../backup/monitor-azure-backup-with-backup-explorer.md) för att visa operativ information om säkerhetskopieringsobjekt (inklusive Azure-resurser som ännu inte har konfigurerats för säkerhetskopiering) och övervakningsinformation (jobb och aviseringar) för delegerade prenumerationer. Säkerhetskopieringsutforskaren är för närvarande endast tillgänglig för Azure VM-data.
- Använd [säkerhetskopieringsrapporter](../../backup/configure-reports.md) över delegerade prenumerationer för att spåra historiska trender, analysera förbrukning för säkerhetskopieringslagring och granska säkerhetskopior och återställningar.

[Azure Kubernetes-tjänsten (AKS):](../../aks/index.yml)

- Hantera värdbaserade Kubernetes-miljöer och distribuera och hantera containerprogram inom kundklienter

[Azure-övervakare:](../../azure-monitor/index.yml)

- Visa aviseringar för delegerade prenumerationer, med möjlighet att visa aviseringar för alla prenumerationer
- Visa information om aktivitetsloggen för delegerade prenumerationer
- Logganalys: Frågedata från fjärranslutna kundarbetsytor i flera klienter
- Skapa aviseringar i kundklienter som utlöser automatisering, till exempel Azure Automation-runbooks eller Azure Functions, i tjänstleverantörens klient via webhooks

[Azure-princip:](../../governance/policy/index.yml)

- Ögonblicksbilder av kompatibilitet visar information om tilldelade principer i delegerade prenumerationer
- Skapa och redigera principdefinitioner i en delegerad prenumeration
- Tilldela kunddefinierade principdefinitioner inom den delegerade prenumerationen
- Kunder ser principer som skapats av tjänsteleverantören tillsammans med alla policyer som de har skapat själva
- Kan [åtgärda distribueraOmNotExister eller ändra tilldelningar inom kundklienten](../how-to/deploy-policy-remediation.md)

[Azure-resursdiagram:](../../governance/resource-graph/index.yml)

- Nu ingår klient-ID i returnerade frågeresultat, så att du kan identifiera om en prenumeration tillhör kundens klientorganisation eller tjänsteleverantörsklient

[Azure Security Center:](../../security-center/index.yml)

- Synlighet mellan innehavare
  - Övervaka efterlevnad av säkerhetsprinciper och säkerställa säkerhetstäckning över alla klienters resurser
  - Kontinuerlig övervakning av regelefterlevnad mellan flera kunder i en enda vy
  - Övervaka, triage och prioritera användbara säkerhetsrekommendationer med säker poängberäkning
- Hantering av säkerhetshållning över klienter
  - Hantera säkerhetsprinciper
  - Vidta åtgärder för resurser som inte uppfyller de kompatib senaste säkerhetsrekommendationerna
  - Samla in och lagra säkerhetsrelaterade data
- Identifiering och skydd över klientgränserna
  - Identifiera hot mellan klienters resurser
  - Tillämpa avancerade hotskyddskontroller, till exempel åtkomst till virtuell dator med just-in-time
  - Härda nätverkssäkerhetsgruppkonfiguration med adaptiv nätverkshärdning
  - Se till att servrarna bara kör de program och processer de bör ha med adaptiva programkontroller
  - Övervaka ändringar av viktiga filer och registerposter med FIM (File Integrity Monitoring)

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Hantera Azure Sentinel-resurser [i kundklienter](../../sentinel/multiple-tenants-service-providers.md)
- [Spåra attacker och visa säkerhetsaviseringar över flera kundklienter](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Azure-tjänstens hälsotillstånd:](../../service-health/index.yml)

- Övervaka hälsotillståndet för kundresurser med Azure Resource Health
- Spåra hälsotillståndet för de Azure-tjänster som används av dina kunder

[Återställning av Azure-webbplatser:](../../site-recovery/index.yml)

- Hantera alternativ för haveriberedskap för virtuella Azure-datorer i kundklienter (observera att du inte kan använda RunAs-konton för att kopiera VM-tillägg)

[Virtuella Azure-datorer:](../../virtual-machines/index.yml)

- Använda tillägg för virtuella datorer för att tillhandahålla konfigurations- och automatiseringsuppgifter efter distributionen på virtuella Azure-datorer i kundklienter
- Använd startdiagnostik för att felsöka virtuella Azure-datorer i kundklienter
- Få tillgång till virtuella datorer med seriekonsol i kundklienter
- Observera att du inte kan använda Azure Active Directory för fjärrinloggning till en virtuell dator, och du kan inte integrera en virtuell dator med ett Nyckelvalv för lösenord, hemligheter eller kryptografiska nycklar för diskkryptering

[Virtuella Azure-nätverk:](../../virtual-network/index.yml)

- Distribuera och hantera virtuella nätverk och virtuella nätverkskort (vNICs) inom kundklienter

Supportförfrågningar:

- Öppna supportbegäranden för delegerade resurser från **supportbladet Hjälp +** i Azure-portalen (välja den supportplan som är tillgänglig för det delegerade scopet)

## <a name="current-limitations"></a>Aktuella begränsningar
Med alla scenarier bör du vara medveten om följande aktuella begränsningar:

- Begäranden som hanteras av Azure Resource Manager kan utföras med Azure-delegerad resurshantering. Åtgärds-URI:erna `https://management.azure.com`för dessa begäranden börjar med . Begäranden som hanteras av en instans av en resurstyp (till exempel KeyVault secrets access eller lagringsdataåtkomst) stöds dock inte med Azure-delegerad resurshantering. Åtgärds-URI:erna för dessa begäranden börjar vanligtvis med `https://myaccount.blob.core.windows.net` `https://mykeyvault.vault.azure.net/`en adress som är unik för din instans, till exempel eller . De senare är också vanligtvis dataåtgärder snarare än förvaltningsåtgärder. 
- Rolltilldelningar måste använda [inbyggda rollbaserade](../../role-based-access-control/built-in-roles.md)åtkomstkontroll (RBAC) . Alla inbyggda roller stöds för närvarande med Azure-delegerad resurshantering förutom ägare eller inbyggda roller med [behörigheten DataActions.](../../role-based-access-control/role-definitions.md#dataactions) Rollen Administratör för användaråtkomst stöds endast för begränsad användning vid [tilldelning av roller till hanterade identiteter](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Anpassade roller och [klassiska prenumerationsadministratörsroller](../../role-based-access-control/classic-administrators.md) stöds inte.
- Även om du kan gå ombord på prenumerationer som använder Azure Databricks, kan användare i klienten som hanterar inte starta Azure Databricks-arbetsytor på en delegerad prenumeration just nu.
- Även om du kan gå ombord på prenumerationer och resursgrupper för Azure-delegerad resurshantering som har resurslås, förhindrar dessa lås inte att åtgärder utförs av användare i den hanterande klienten. [Neka tilldelningar](../../role-based-access-control/deny-assignments.md) som skyddar systemhanterade resurser, till exempel de som skapats av Azure-hanterade program eller Azure Blueprints (systemtilldelade neka tilldelningar), hindrar användare i klienten från att agera på dessa resurser. Men vid denna tid användare i kundens klientorganisation kan inte skapa sina egna neka tilldelningar (användartilldelade neka tilldelningar).
- Användare i den hanterande klienten har inte åtkomst till faktureringsinformation för en delegerad kundprenumeration, även om de har en inbyggd roll som normalt tillåter åtkomst. Detta beror på att åtkomst till faktureringsinformation kräver ytterligare steg som för närvarande endast stöds för användare inom samma klientorganisation.

## <a name="next-steps"></a>Nästa steg

- Ombord på dina kunder till Azure-delegerad resurshantering, antingen genom [att använda Azure Resource Manager-mallar](../how-to/onboard-customer.md) eller genom att publicera ett erbjudande om privata eller offentliga hanterade tjänster till Azure [Marketplace](../how-to/publish-managed-services-offers.md).
- [Visa och hantera kunder](../how-to/view-manage-customers.md) genom att gå till **Mina kunder** i Azure-portalen.
