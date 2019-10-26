---
title: Miljöer för hantering av flera klienter
description: Azure-delegerad resurs hantering möjliggör hantering av flera innehavare.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 10/24/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: eb4ec10755b7ca2227623ba0842d2b1175635594
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901816"
---
# <a name="cross-tenant-management-experiences"></a>Miljöer för hantering av flera klienter

Som tjänst leverantör kan du använda Azure- [delegerad resurs hantering](../concepts/azure-delegated-resource-management.md) för att hantera Azure-resurser för flera kunder från din egen klient i [Azure Portal](https://portal.azure.com). De flesta uppgifter och tjänster kan utföras på delegerade Azure-resurser över hanterade klienter. I den här artikeln beskrivs några av de förbättrade scenarier där Azure-delegerad resurs hantering kan vara effektiv.

> [!NOTE]
> Azure-delegerad resurs hantering kan också användas i ett företag som har flera klient organisationer för att förenkla administrationen av flera innehavare.

## <a name="understanding-customer-tenants"></a>Förstå kund klienter

En Azure Active Directory-klient (Azure AD) är en representation av en organisation. Det är en dedikerad instans av Azure AD som en organisation tar emot när de skapar en relation med Microsoft genom att registrera sig för Azure, Microsoft 365 eller andra tjänster. Varje Azure AD-klient är distinkt och åtskild från andra Azure AD-klienter och har ett eget klient-ID (ett GUID). Mer information finns i [Vad är Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

För att kunna hantera Azure-resurser för en kund måste tjänst leverantörer ofta logga in på Azure Portal med ett konto som är kopplat till den kundens klient organisation, vilket kräver att en administratör i kundens klient organisation skapar och hanterar användar konton för tjänst leverantören.

Med Azure-delegerad resurs hantering anger onboarding-processen användare inom tjänst leverantörens klient organisation som ska kunna komma åt och hantera prenumerationer, resurs grupper och resurser i kundens klient organisation. Dessa användare kan sedan logga in på Azure Portal med sina egna autentiseringsuppgifter. I Azure Portal kan de hantera resurser som hör till alla kunder som de har åtkomst till. Detta kan göras genom att gå till sidan [Mina kunder](../how-to/view-manage-customers.md) i Azure Portal eller genom att arbeta direkt inom kontexten för kundens prenumeration, antingen i Azure Portal eller via API: er.

Azure delegerad resurs hantering ger större flexibilitet för att hantera resurser för flera kunder utan att behöva logga in på olika konton i olika klienter. En tjänst leverantör kan till exempel ha tre kunder, med olika ansvars områden och åtkomst nivåer, som du ser här:

![Tre kund innehavare som visar service leverantörens ansvar](../media/azure-delegated-resource-management-customer-tenants.jpg)

Med Azure-delegerad resurs hantering kan behöriga användare logga in på tjänst leverantörens klient för att få åtkomst till dessa resurser, som du ser här:

![Kund resurser som hanteras via en tjänst leverantörs klient](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Stöd för API: er och hanterings verktyg

Du kan utföra hanterings uppgifter för delegerade resurser antingen direkt i portalen eller med hjälp av API: er och hanterings verktyg (till exempel Azure CLI och Azure PowerShell). Alla befintliga API: er kan användas när du arbetar med delegerade resurser, så länge som funktionerna stöds för hantering av flera innehavare och användaren har rätt behörigheter.

Vi tillhandahåller också API: er för att utföra Azure delegerade resurs hanterings uppgifter. Mer information finns i avsnittet **referens** .

## <a name="enhanced-services-and-scenarios"></a>Förbättrade tjänster och scenarier

De flesta uppgifter och tjänster kan utföras på delegerade resurser över hanterade klienter. Nedan visas några viktiga scenarier där hantering av flera innehavare kan vara effektiv.

[Azure Automation](https://docs.microsoft.com/azure/automation/):

- Använd Automation-konton för att komma åt och arbeta med delegerade kund resurser

[Azure Backup](https://docs.microsoft.com/azure/backup/):

- Säkerhetskopiera och återställa kund information i kund klienter

[Azure Kubernetes service (AKS)](https://docs.microsoft.com//azure/aks/):

- Hantera värdbaserade Kubernetes-miljöer och distribuera och hantera program i behållare inom kund klienter

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/):

- Visa aviseringar för delegerade prenumerationer, med möjlighet att visa aviseringar över alla prenumerationer
- Visa aktivitets logg information för delegerade prenumerationer
- Log Analytics: fråga efter data från fjärranslutna kund arbets ytor i flera klienter
- Skapa aviseringar i kund klienter som utlöser automatisering, till exempel Azure Automation runbooks eller Azure Functions, i tjänst leverantörens klient organisation via Webhooks

[Azure policy](https://docs.microsoft.com/azure/governance/policy/):

- Ögonblicks bilder av kompatibilitet visar information om tilldelade principer i delegerade prenumerationer
- Skapa och redigera princip definitioner i en delegerad prenumeration
- Tilldela kunddefinierade princip definitioner inom den delegerade prenumerationen
- Kunder ser principer som skapats av tjänst leverantören tillsammans med eventuella principer som de har skapat själva
- Kan [åtgärda deployIfNotExists eller ändra tilldelningar inom kund innehavaren](../how-to/deploy-policy-remediation.md)

[Azure-resurs diagram](https://docs.microsoft.com/azure/governance/resource-graph/):

- Innehåller nu klient-ID: t i returnerade frågeresultat, så att du kan identifiera om en prenumeration tillhör kunden eller tjänst leverantörens klient organisation

[Azure Security Center](https://docs.microsoft.com/azure/security-center/):

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

[Azure Sentinel](https://docs.microsoft.com/azure/sentinel/multiple-tenants-service-providers):

- Hantera Azure Sentinel-resurser i kund klienter

[Azure Service Health](https://docs.microsoft.com/azure/service-health/):

- Övervaka hälso tillståndet för kund resurser med Azure Resource Health
- Spåra hälsan hos de Azure-tjänster som används av dina kunder

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/):

- Hantera katastrof återställnings alternativ för virtuella Azure-datorer i kund klienter (Observera att du inte kan använda RunAs-konton för att kopiera VM-tillägg)

[Azure-Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/):

- Använd tillägg för virtuella datorer för att tillhandahålla konfigurations-och automatiserings uppgifter efter distributionen på virtuella Azure-datorer i kund klienter
- Använd startdiagnostik för att felsöka virtuella Azure-datorer i kund klienter
- Få åtkomst till virtuella datorer med serie konsolen i kund klienter
- Observera att du inte kan använda Azure Active Directory för fjärrinloggning till en virtuell dator och du kan inte integrera en virtuell dator med en Key Vault för lösen ord, hemligheter eller kryptografiska nycklar för disk kryptering

[Azure-Virtual Network](https://docs.microsoft.com/azure/virtual-network/):

- Distribuera och hantera virtuella nätverk och virtuella nätverks gränssnitts kort (virtuella nätverkskort) i kund klienter

Support förfrågningar:

- Öppna support förfrågningar för delegerade resurser från **Hjälp + Support** -bladet i Azure Portal (välja det support avtal som är tillgängligt för det delegerade omfånget)

## <a name="current-limitations"></a>Aktuella begränsningar
Tänk på följande nuvarande begränsningar i alla scenarier:

- Begär Anden som hanteras av Azure Resource Manager kan utföras med Azure-delegerad resurs hantering. Åtgärds-URI: erna för dessa begär Anden börjar med `https://management.azure.com`. Förfrågningar som hanteras av en instans av en resurs typ (till exempel åtkomst till nyckel valv hemligheter eller åtkomst till lagrings data) stöds dock inte med Azure-delegerad resurs hantering. Åtgärds-URI: erna för dessa begär Anden börjar vanligt vis med en adress som är unik för din instans, till exempel `https://myaccount.blob.core.windows.net` eller `https://mykeyvault.vault.azure.net/`. De sistnämnda är också vanliga data åtgärder i stället för hanterings åtgärder. 
- Roll tilldelningar måste använda [Inbyggda RBAC-roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)(rollbaserad åtkomst kontroll). Alla inbyggda roller stöds för närvarande med Azure-delegerad resurs hantering förutom för ägare eller inbyggda roller med [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) -behörighet. Rollen administratör för användar åtkomst stöds endast för begränsad användning i [tilldela roller till hanterade identiteter](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Administratörs roller för anpassade roller och [klassiska prenumerationer](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) stöds inte.
- För närvarande kan du inte publicera en prenumeration (eller resurs grupp i en prenumeration) för Azure-delegerad resurs hantering om prenumerationen använder Azure Databricks. Om en prenumeration har registrerats för onboarding med **Microsoft. ManagedServices** Resource Provider kan du inte heller skapa en Databricks-arbetsyta för den prenumerationen just nu.
- Även om du kan publicera prenumerationer och resurs grupper för Azure delegerad resurs hantering som har resurs lås, kommer dessa lås inte att förhindra att åtgärder utförs av användare i hanterings klienten. [Neka tilldelningar](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) som skyddar systemhanterade resurser, t. ex. de som skapats av Azure-hanterade program eller Azure-skisser (systemtilldelade neka-tilldelningar), förhindrar användare i hanterings klienten från att fungera på dessa resurser. men vid den här tidpunkten kan användare i kund klienten inte skapa sina egna neka-tilldelningar (användar tilldelning neka tilldelningar).

## <a name="next-steps"></a>Nästa steg

- Publicera dina kunder till Azure delegerad resurs hantering, antingen genom att [använda Azure Resource Manager mallar](../how-to/onboard-customer.md) eller genom [att publicera ett privat eller offentligt hanterat tjänst erbjudande på Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Visa och hantera kunder](../how-to/view-manage-customers.md) genom att gå till **mina kunder** i Azure Portal.
