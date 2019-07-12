---
title: Av flera klienter hanteringsupplevelser med Azure fyr
description: Azure delegerade resource Manager kan en hanteringsupplevelse för flera klienter.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 15454d4b3f0abad6166c4b163df6c8652669d649
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809906"
---
# <a name="cross-tenant-management-experiences"></a>Hanteringsupplevelser för flera klienter

Den här artikeln beskrivs de scenarier som du, som en tjänstleverantör kan använda med [Azure delegerad resurshantering](../concepts/azure-delegated-resource-management.md) att hantera Azure-resurser för flera kunder i din egen klientorganisation i den [Azure-portalen ](https://portal.azure.com).

> [!NOTE]
> Azure delegerade resource Manager kan också användas i ett företag som har flera klienter för att förenkla administrationen av flera klienter.

## <a name="understanding-customer-tenants"></a>Förstå kundens klienter

En Azure Active Directory (Azure AD)-klient är en representation av en organisation. Det är en dedikerad instans av Azure AD som en organisation tar emot när de skapar en relation med Microsoft genom att registrera dig för Azure, Microsoft 365 eller andra tjänster. Varje Azure AD-klient är unik och avgränsad från andra Azure AD-klienter och har sin egen klient-ID (en GUID). Mer information finns i [vad är Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

För att hantera Azure-resurser för en kund behöver normalt tjänstleverantörer att logga in på Azure-portalen med ett konto som är kopplad till kundens klient, som kräver en administratör i kundens klient för att skapa och hantera användarkonton för tjänstleverantören.

Med Azure delegerade resource Manager anger onboarding-processen användare i tjänsteleverantörens klient som kommer att kunna komma åt och hantera prenumerationer, resursgrupper och resurser i kundens klient. Dessa användare kan sedan logga in på Azure-portalen med sina egna autentiseringsuppgifter. De kan hantera resurser som tillhör alla kunder som de har åtkomst till i Azure-portalen. Detta kan göras genom att besöka den [mina kunder](../how-to/view-manage-customers.md) i Azure-portalen eller genom att arbeta direkt inom ramen för kundens prenumeration på Azure-portalen eller via API: er.

Azure delegerade resource Manager kan större flexibilitet att hantera resurser för flera kunder utan att behöva logga in på olika konton i olika klienter. Exempelvis kanske en tjänstleverantör har tre kunder med olika ansvarsområden och åtkomstnivåer, som visas här:

![Tre kunden klienter som visar service provider ansvarsområden](../media/azure-delegated-resource-management-customer-tenants.jpg)

Med Azure delegerade resource Manager kan behöriga användare kan logga in på tjänstleverantörens klient åtkomst till dessa resurser, som visas här:

![Kundresurser som hanteras via en service provider-klient](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="supported-services-and-scenarios"></a>Tjänster som stöds och scenarier

Hantering av flera klienter stöder för närvarande följande scenarier med delegerad kundresurser:

[Azure Automation](https://docs.microsoft.com/azure/automation/):

- Använda automation-konton för att komma åt och arbeta med delegerade kundresurser

[Azure Backup](https://docs.microsoft.com/azure/backup/):

- Säkerhetskopiera och återställa kunddata i kund-klienter

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com//azure/aks/):

- Hantera värdbaserade Kubernetes-miljöer och distribuera och hantera behållarbaserade program i kund-klienter

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/):

- Visa aviseringar för delegerade prenumerationer i Azure portal eller via REST API-anrop, med möjlighet att visa aviseringar för alla prenumerationer
- Visa loggen aktivitetsinformation för delegerade prenumerationer
- Log analytics: Fråga efter data från fjärranslutna kunden arbetsytor i flera klienter

[Azure Policy](https://docs.microsoft.com/azure/governance/policy/):

- Efterlevnad ögonblicksbilder visa information om tilldelade principer i delegerade prenumerationer
- Skapa och redigera principdefinitionerna inom en delegerad prenumeration
- Tilldela kunddefinierad principdefinitionerna inom den delegerade prenumerationen
- Kunderna ser principer som skapats av tjänstleverantören tillsammans med eventuella principer som de har skapat själva
- Kan reparera deployIfNotExists tilldelningar inom innehavarna som kunden om kunden har konfigurerat den hanterade identitet och *roleDefinitionIds* för den principtilldelningen

[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/):

- Nu innehåller klient-ID i returnerade resultatet och så att du kan identifiera om en prenumeration hör till den kund eller klient för service provider

[Azure Security Center](https://docs.microsoft.com/azure/security-center/):

- Synlighet för flera klienter
  - Övervaka efterlevnad av säkerhetsprinciper och se till att security täckning för alla klienter resurser
  - Kontinuerlig regelefterlevnad övervakning över flera kunder i en enda vy
  - Övervaka, hantera och prioritera handlingsbara rekommendationer med säkra poängberäkningen
- Flera klienter hållning säkerhetshantering
  - Hantera säkerhetsprinciper
  - Vidta åtgärder på resurser som är inte kompatibel med handlingsbara rekommendationer
  - Samla in och lagra säkerhetsrelaterade data
- Flera klienter hotidentifiering och skydd
  - Identifiera hot för klienternas resurser
  - Använd Avancerat skydd-kontroller, till exempel åtkomst till just-in-time-(åtkomst JIT) virtuella datorer
  - Förstärka nätverket säkerhetsgruppens konfiguration med anpassningsbar Härdning av nätverk
  - Se till att servrar körs bara de program och processer som de ska vara med anpassningsbara programkontroller
  - Övervaka ändringar i viktiga filer och registerposter med filen integritet övervakning (FIM)

[Azure Service Health](https://docs.microsoft.com/azure/service-health/):

- Övervaka hälsotillståndet hos kundresurser med Azure Resource Health
- Spåra hälsotillståndet för de Azure-tjänster används av dina kunder

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/):

- Hantera möjligheterna för katastrofåterställning för virtuella Azure-datorer i kund-klienter (Observera att du inte kan använda Kör som-konton för att kopiera VM-tillägg)

[Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/):

- Använda tillägg för virtuell dator för att tillhandahålla konfiguration och automatisering av uppgifter efter distributionen på Azure Virtual Machines i kund-klienter
- Använd startdiagnostik för felsökning av virtuella Azure-datorer i kund-klienter
- Åtkomst till virtuella datorer med seriekonsolen i kund-klienter
- Observera att du kan inte använda Azure Active Directory för fjärrinloggning till en virtuell dator och du kan inte integrera en virtuell dator med ett Key Vault för lösenord, hemligheter och kryptografiska nycklar för diskkryptering av

[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/):

- Distribuera och hantera virtuella nätverk och virtuella nätverkskort (Vnic) i kundens klienter

Supportförfrågningar:

- Öppna supportärenden för delegerade resurser från den **hjälp + support** bladet i Azure-portalen (genom att markera supportplan som är tillgängliga för delegerade scope)

Med alla scenarier, Tänk på följande aktuella begränsningar:

- Begäranden som hanteras av Azure Resource Manager kan utföras med hjälp av Azure delegerade resource Manager. Utgå från åtgärden URI: er för dessa begäranden `https://management.azure.com`. Begäranden som hanteras av en instans av en resurstyp (till exempel KeyVault hemligheter åtkomst eller åtkomst till lagringsdata) stöds dock inte med Azure delegerade resource Manager. Åtgärden URI: er för dessa begäranden normalt börja med en adress som är unik för din instans som `https://myaccount.blob.core.windows.net` eller `https://mykeyvault.vault.azure.net/`. Dessa också är vanligtvis dataåtgärder i stället för hanteringsåtgärder. 
- Rolltilldelningar måste använda rollbaserad åtkomstkontroll (RBAC) [inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Alla inbyggda roller stöds för närvarande med Azure delegerade resource Manager förutom ägare eller administratör för användaråtkomst alla inbyggda roller med [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) behörighet. Anpassade roller och [administratörsroller för klassiska prenumerationer](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) stöds också inte.
- För närvarande kan du inte publicera en prenumeration eller resursgrupp i en prenumeration för Azure delegerad resurshantering om prenumerationen använder Azure Databricks. På samma sätt, om en prenumeration har registrerats för integrering med den **Microsoft.ManagedServices** resursprovider, du kan inte skapa en Databricks-arbetsyta för den aktuella prenumerationen just nu.

## <a name="using-apis-and-management-tools-with-cross-tenant-management"></a>Med hjälp av API: er och hanteringsverktyg med hantering av flera klienter

För tjänster som stöds och scenarier som anges ovan kan utföra du administrativa uppgifter direkt i portalen eller genom att använda API: er och hanteringsverktyg (till exempel Azure CLI och Azure PowerShell). Alla befintliga API: er kan användas när du arbetar med delegerade resurser (för tjänster som stöds).

Det finns även API: er för specifika hanteringsuppgifter för Azure delegerade resursen. Mer information finns i den **referens** avsnittet.


## <a name="next-steps"></a>Nästa steg

- Publicera dina kunder till Azure delegerad resurshantering, antingen genom att [med hjälp av Azure Resource Manager-mallar](../how-to/onboard-customer.md) eller [publicera ett erbjudande för privata eller offentliga hanterade tjänster på Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Visa och hantera kunder](../how-to/view-manage-customers.md) genom att gå till **mina kunder** i Azure-portalen.