---
title: Miljöer för hantering av flera klienter
description: Azure-delegerad resurs hantering möjliggör hantering av flera innehavare.
ms.date: 04/24/2020
ms.topic: conceptual
ms.openlocfilehash: 51cd464da417bfd1d6d4ff52e2a2595a7ce77fe6
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82201711"
---
# <a name="cross-tenant-management-experiences"></a>Miljöer för hantering av flera klienter

Som tjänst leverantör kan du använda Azure- [delegerad resurs hantering](../concepts/azure-delegated-resource-management.md) för att hantera Azure-resurser för flera kunder från din egen klient i [Azure Portal](https://portal.azure.com). De flesta uppgifter och tjänster kan utföras på delegerade Azure-resurser över hanterade klienter. I den här artikeln beskrivs några av de förbättrade scenarier där Azure-delegerad resurs hantering kan vara effektiv.

> [!NOTE]
> Azure-delegerad resurs hantering kan också användas [inom ett företag som har flera Azure AD-klienter för](enterprise.md) att förenkla administrationen av flera innehavare.

## <a name="understanding-customer-tenants"></a>Förstå kund klienter

En Azure Active Directory-klient (Azure AD) är en representation av en organisation. Det är en dedikerad instans av Azure AD som en organisation tar emot när de skapar en relation med Microsoft genom att registrera sig för Azure, Microsoft 365 eller andra tjänster. Varje Azure AD-klient är distinkt och åtskild från andra Azure AD-klienter och har ett eget klient-ID (ett GUID). Mer information finns i [Vad är Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

För att kunna hantera Azure-resurser för en kund måste tjänst leverantörer ofta logga in på Azure Portal med ett konto som är kopplat till den kundens klient organisation, vilket kräver att en administratör i kundens klient organisation skapar och hanterar användar konton för tjänst leverantören.

Med Azure-delegerad resurs hantering anger onboarding-processen användare inom tjänst leverantörens klient organisation som ska kunna komma åt och hantera prenumerationer, resurs grupper och resurser i kundens klient organisation. Dessa användare kan sedan logga in på Azure Portal med sina egna autentiseringsuppgifter. I Azure Portal kan de hantera resurser som hör till alla kunder som de har åtkomst till. Detta kan göras genom att gå till sidan [Mina kunder](../how-to/view-manage-customers.md) i Azure Portal eller genom att arbeta direkt inom kontexten för kundens prenumeration, antingen i Azure Portal eller via API: er.

Azure delegerad resurs hantering ger större flexibilitet för att hantera resurser för flera kunder utan att behöva logga in på olika konton i olika klienter. En tjänst leverantör kan till exempel ha två kunder med olika ansvars områden och åtkomst nivåer. Med Azure-delegerad resurs hantering kan behöriga användare logga in på tjänst leverantörens klient för att få åtkomst till dessa resurser.

![Kund resurser som hanteras via en tjänst leverantörs klient](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Stöd för API: er och hanterings verktyg

Du kan utföra hanterings uppgifter för delegerade resurser antingen direkt i portalen eller med hjälp av API: er och hanterings verktyg (till exempel Azure CLI och Azure PowerShell). Alla befintliga API: er kan användas när du arbetar med delegerade resurser, så länge som funktionerna stöds för hantering av flera innehavare och användaren har rätt behörigheter.

[Cmdleten Azure PowerShell get-AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) visar **tenantID** för varje prenumeration, så att du kan identifiera om en returnerad prenumeration hör till din tjänst leverantörs klient eller till en hanterad kund klient.

På samma sätt visar Azure CLI-kommandon som [AZ-konto lista](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) **HomeTenantId** -och **managedByTenants** -attribut.

> [!TIP]
> Om du inte ser dessa värden när du använder Azure CLI kan du försöka rensa cacheminnet `az account clear` genom att `az login --identity`köra följt av.

Vi tillhandahåller också API: er som är speciella för att utföra Azure-delegerade resurs hanterings uppgifter. Mer information finns i avsnittet **referens** .

## <a name="enhanced-services-and-scenarios"></a>Förbättrade tjänster och scenarier

De flesta uppgifter och tjänster kan utföras på delegerade resurser över hanterade klienter. Nedan visas några viktiga scenarier där hantering av flera innehavare kan vara effektiv.

[Azure-båge för servrar (för hands version)](../../azure-arc/servers/overview.md):

- [Anslut Windows Server-eller Linux-datorer utanför Azure](../../azure-arc/servers/quickstart-onboard-portal.md) till delegerade prenumerationer och/eller resurs grupper i Azure
- Hantera anslutna datorer med hjälp av Azure-konstruktioner, till exempel Azure Policy och taggning

[Azure Automation](../../automation/index.yml):

- Använd Automation-konton för att komma åt och arbeta med delegerade kund resurser

[Azure Backup](../../backup/index.yml):

- Säkerhetskopiera och återställa kund information i kund klienter
- Använd [Backup Explorer](../../backup/monitor-azure-backup-with-backup-explorer.md) för att Visa användnings information om säkerhets kopierings objekt (inklusive Azure-resurser som ännu inte har kon figurer ATS för säkerhets kopiering) och övervaknings information (jobb och aviseringar) för delegerade prenumerationer. Backup Explorer är för närvarande endast tillgängligt för Azure VM-data.
- Använd [säkerhets kopierings rapporter](../../backup/configure-reports.md) över delegerade prenumerationer för att spåra historiska trender, analysera förbrukning av lagrings utrymme för säkerhets kopior och granska säkerhets kopieringar och återställning.

[Azure Kubernetes service (AKS)](../../aks/index.yml):

- Hantera värdbaserade Kubernetes-miljöer och distribuera och hantera program i behållare inom kund klienter

[Azure Monitor](../../azure-monitor/index.yml):

- Visa aviseringar för delegerade prenumerationer, med möjlighet att visa aviseringar över alla prenumerationer
- Visa aktivitets logg information för delegerade prenumerationer
- Log Analytics: fråga efter data från fjärranslutna kund arbets ytor i flera klienter
- Skapa aviseringar i kund klienter som utlöser automatisering, till exempel Azure Automation runbooks eller Azure Functions, i tjänst leverantörens klient organisation via Webhooks

[Azure-nätverk](../../networking/networking-overview.md):

- Distribuera och hantera [Azure Virtual Network (VNet)](../../virtual-network/index.yml) och virtuella nätverks gränssnitts kort (virtuella nätverkskort) i kund klienter
- Distribuera och konfigurera [Azure-brandväggen](../../firewall/overview.md) för att skydda kunders Virtual Network resurser
- Hantera anslutnings tjänster som [Azure Virtual WAN](../../virtual-wan/virtual-wan-about.md), [ExpressRoute](../../expressroute/expressroute-introduction.md)och [VPN-gatewayer](../../vpn-gateway/vpn-gateway-about-vpngateways.md) för kunder
- Använd Azure-Lighthouse för att stödja viktiga scenarier för [Azure Network MSP-programmet](../../networking/networking-partners-msp.md)


[Azure policy](../../governance/policy/index.yml):

- Ögonblicks bilder av kompatibilitet visar information om tilldelade principer i delegerade prenumerationer
- Skapa och redigera princip definitioner i en delegerad prenumeration
- Tilldela kunddefinierade princip definitioner inom den delegerade prenumerationen
- Kunder ser principer som skapats av tjänst leverantören tillsammans med eventuella principer som de har skapat själva
- Kan [åtgärda deployIfNotExists eller ändra tilldelningar inom kund innehavaren](../how-to/deploy-policy-remediation.md)

[Azure-resurs diagram](../../governance/resource-graph/index.yml):

- Innehåller nu klient-ID: t i returnerade frågeresultat, så att du kan identifiera om en prenumeration tillhör kunden eller tjänst leverantörens klient organisation

[Azure Security Center](../../security-center/index.yml):

- Synlighet mellan klienter
  - Övervaka efterlevnad av säkerhets principer och garantera säkerhets täckning för alla klienters resurser
  - Kontinuerlig övervakning av efterlevnad för flera kunder i en enda vy
  - Övervaka, prioritering och prioritera åtgärds bara säkerhets rekommendationer med säker Poäng beräkning
- Hantering av säkerhets position över flera innehavare
  - Hantera säkerhets principer
  - Vidta åtgärder för resurser som inte är kompatibla med åtgärds bara säkerhets rekommendationer
  - Samla in och lagra säkerhetsrelaterade data
- Hot identifiering och skydd mellan klienter
  - Identifiera hot över klient organisations resurser
  - Använd avancerade skydds kontroller som just-in-Time (JIT) VM-åtkomst
  - Härdning av nätverks säkerhets grupp konfigurationen med anpassad nätverks härdning
  - Se till att servrarna bara kör de program och processer som de ska vara med anpassningsbara program kontroller
  - Övervaka ändringar i viktiga filer och register poster med File Integrity Monitoring (FIM)

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Hantera Azure Sentinel-resurser [i kund klienter](../../sentinel/multiple-tenants-service-providers.md)
- [Spåra attacker och Visa säkerhets aviseringar över flera kund klienter](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Azure Service Health](../../service-health/index.yml):

- Övervaka hälso tillståndet för kund resurser med Azure Resource Health
- Spåra hälsan hos de Azure-tjänster som används av dina kunder

[Azure Site Recovery](../../site-recovery/index.yml):

- Hantera katastrof återställnings alternativ för virtuella Azure-datorer i kund klienter (Observera att du inte kan använda RunAs-konton för att kopiera VM-tillägg)

[Azure-Virtual Machines](../../virtual-machines/index.yml):

- Använd tillägg för virtuella datorer för att tillhandahålla konfigurations-och automatiserings uppgifter efter distributionen på virtuella Azure-datorer i kund klienter
- Använd startdiagnostik för att felsöka virtuella Azure-datorer i kund klienter
- Få åtkomst till virtuella datorer med serie konsolen i kund klienter
- Integrera virtuella datorer med Azure-nyckel valv för lösen ord, hemligheter eller kryptografiska nycklar för disk kryptering med hjälp av [hanterad identitet via princip](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret), och se till att hemligheter lagras i en Key Vault i kund klienter
- Observera att du inte kan använda Azure Active Directory för fjärrinloggning till virtuella datorer i kundernas klienter

Support förfrågningar:

- Öppna support förfrågningar för delegerade resurser från **Hjälp + Support** -bladet i Azure Portal (välja det support avtal som är tillgängligt för det delegerade omfånget)

## <a name="current-limitations"></a>Aktuella begränsningar
Tänk på följande nuvarande begränsningar i alla scenarier:

- Begär Anden som hanteras av Azure Resource Manager kan utföras med Azure-delegerad resurs hantering. Åtgärds-URI: erna för dessa `https://management.azure.com`begär Anden börjar med. Förfrågningar som hanteras av en instans av en resurs typ (till exempel åtkomst till nyckel valv hemligheter eller åtkomst till lagrings data) stöds dock inte med Azure-delegerad resurs hantering. Åtgärds-URI: erna för dessa begär Anden börjar vanligt vis med en adress som är unik för `https://myaccount.blob.core.windows.net` din `https://mykeyvault.vault.azure.net/`instans, till exempel eller. De sistnämnda är också vanliga data åtgärder i stället för hanterings åtgärder. 
- Roll tilldelningar måste använda [Inbyggda RBAC-roller](../../role-based-access-control/built-in-roles.md)(rollbaserad åtkomst kontroll). Alla inbyggda roller stöds för närvarande med Azure-delegerad resurs hantering förutom för ägare eller inbyggda roller med [DataActions](../../role-based-access-control/role-definitions.md#dataactions) -behörighet. Rollen administratör för användar åtkomst stöds endast för begränsad användning i [tilldela roller till hanterade identiteter](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Administratörs roller för anpassade roller och [klassiska prenumerationer](../../role-based-access-control/classic-administrators.md) stöds inte.
- Även om du kan publicera prenumerationer som använder Azure Databricks kan användare i hanterings klienten inte starta Azure Databricks arbets ytor i en delegerad prenumeration just nu.
- Även om du kan publicera prenumerationer och resurs grupper för Azure delegerad resurs hantering som har resurs lås, kommer dessa lås inte att förhindra att åtgärder utförs av användare i hanterings klienten. [Neka tilldelningar](../../role-based-access-control/deny-assignments.md) som skyddar systemhanterade resurser, t. ex. de som skapats av Azure-hanterade program eller Azure-skisser (systemtilldelade neka-tilldelningar), förhindrar användare i hanterings klienten från att fungera på dessa resurser. men vid den här tidpunkten kan användare i kund klienten inte skapa sina egna neka-tilldelningar (användar tilldelning neka tilldelningar).
- Användare i hanterings klienten kommer inte att ha åtkomst till att Visa fakturerings information för en delegerad kund prenumeration, även om de har en inbyggd roll som vanligt vis tillåter åtkomst. Detta beror på att åtkomsten till fakturerings information kräver ytterligare steg som för närvarande endast stöds för användare inom samma klient organisation.

## <a name="next-steps"></a>Nästa steg

- Publicera dina kunder till Azure delegerad resurs hantering, antingen genom att [använda Azure Resource Manager mallar](../how-to/onboard-customer.md) eller genom [att publicera ett privat eller offentligt hanterat tjänst erbjudande på Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Visa och hantera kunder](../how-to/view-manage-customers.md) genom att gå till **mina kunder** i Azure Portal.
