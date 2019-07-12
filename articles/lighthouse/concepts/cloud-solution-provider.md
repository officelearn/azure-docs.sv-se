---
title: Azure fyr och programmet Cloud Solution Provider
description: När du använder Azure delegerat resurshantering, är det viktigt att tänka på säkerheten och åtkomstkontroll.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 09552c66d2dc841cff8b5cb52e26dc657568e08f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809962"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure fyr och programmet Cloud Solution Provider

Om du är en [CSP (Cloud Solution Provider)](https://docs.microsoft.com/partner-center/csp-overview) partner kan du kan komma åt Azure-prenumerationer för dina kunder via CSP-programmet som skapats med hjälp av den [administrera för (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) funktioner. Den här åtkomsten kan du direkt stöd, konfigurera och hantera dina kunders prenumerationer.

Mekanismen AOBO ger fullständig åtkomst till kundmiljöer. Med hjälp av Azure delegerade resource Manager tillsammans med AOBO hjälper till att förbättra säkerheten genom att låta dig minska onödig åtkomst genom att aktivera mer detaljerade behörigheter för dina användare. 

## <a name="administer-on-behalf-of-aobo"></a>Administrera åt (AOBO)

Med AOBO, alla användare med den [Admin agenten](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) roll i din klient har AOBO åtkomst till Azure-prenumerationer som du skapar via CSP-programmet. Alla användare som behöver åtkomst till alla kunders prenumerationer måste vara medlem i den här gruppen. AOBO låter inte möjlighet att skapa distinkta grupper som fungerar med olika kunder, eller att aktivera olika roller för grupper eller användare.

![Klient-hantering med hjälp av AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure delegerade resource Manager

Använda Azure delegerad resurshantering kan du tilldela olika grupper till olika kunder eller roller, som visas i följande diagram. Eftersom användarna har lämplig nivå av åtkomst via Azure delegerade resource Manager, kan du minska antalet användare som har rollen Administratör Agent (och därmed få full AOBO åtkomst). Detta ger ökad säkerhet genom att begränsa onödiga åtkomst till dina kunders resurser. Du får också mer flexibilitet för att hantera flera kunder i stor skala.

Onboarding en prenumeration som du skapade via CSP-programmet följer stegen som beskrivs i [publicerat en prenumeration på Azure delegerad resurshantering](../how-to/onboard-customer.md). Alla användare som har rollen Administratör agenten i din klient kan utföra den här onboarding.

Observera att för prenumerationer som skapats via CSP-program, supportärenden kan endast skapas av användare med rollen Administratör agenten i tjänsteleverantörens klient. Användare som har lagts till via Azure delegerade resource Manager kan inte öppna supportärenden för delegerade resurser i dessa prenumerationer.

![Klient-hantering med AOBO och Azure delegerad resurshantering](../media/csp-2.jpg)

## <a name="partner-admin-link"></a>Partner-administratör länk

Du kan koppla ditt Microsoft Partner Network (MPN)-ID med dina integrerats prenumerationer och spåra din inverkan i arbetet med kunder.

Om du [publicera ett erbjudande med hanterade tjänster på Azure Marketplace](../how-to/publish-managed-services-offers.md), MPN-ID som är associerat med din publisher-profil och associeras automatiskt med erbjudandet. Intäkter som Azure-resurser via det här erbjudandet kommer sedan hänföras till din organisation. I rapporteringssystem, till exempel Partner Center eller MPN-partner, visas information som Partner Admin länk (PAL).

Om du [kunderna för Azure delegerad resurshantering med hjälp av Azure Resource Manager-mallar](../how-to/onboard-customer.md), du kan fortfarande koppla ditt MPN-ID för att få erkännande för dina påverkan på arbetet med kunder, men du har att göra det manuellt. Mer information finns i [länka ett partner-ID till dina Azure-konton](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [mellan klientorganisationer hanteringsupplevelser](cross-tenant-management-experience.md).
- Lär dig mer om den [programmet Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview).
