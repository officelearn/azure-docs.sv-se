---
title: Att tänka på med Cloud Solution Provider-programmet
description: För CSP-partner hjälper Azure-delegerad resurshantering till att förbättra säkerheten och kontrollen genom att aktivera detaljerade behörigheter.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 66ea74751f12a499a1e2d9e083497da31746e3c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456909"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse och cloud solution provider-programmet

Om du är [CSP-partner (Cloud Solution Provider)](https://docs.microsoft.com/partner-center/csp-overview) kan du redan komma åt De Azure-prenumerationer som skapats för dina kunder via CSP-programmet med hjälp av funktionen [Administrera på uppdrag av (AOBO).](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) Med den här åtkomsten kan du direkt stödja, konfigurera och hantera dina kunders prenumerationer.

Med [Azure Lighthouse](../overview.md)kan du använda Azure-delegerad resurshantering tillsammans med AOBO. Detta bidrar till att förbättra säkerheten och minskar onödig åtkomst genom att aktivera fler detaljerade behörigheter för användarna. Det möjliggör också större effektivitet och skalbarhet, eftersom användarna kan arbeta över flera kundprenumerationer med en enda inloggning i din klientorganisation.

> [!TIP]
> För att skydda kundernas resurser, se till att granska och följa våra [rekommenderade säkerhetsrutiner](recommended-security-practices.md) tillsammans med [partnersäkerhetskraven.](https://docs.microsoft.com/partner-center/partner-security-requirements)

## <a name="administer-on-behalf-of-aobo"></a>Administrera på uppdrag av (AOBO)

Med AOBO får alla användare med [rollen Administratörsagent](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) i din klientorganisation AOBO-åtkomst till Azure-prenumerationer som du skapar via CSP-programmet. Alla användare som behöver åtkomst till kunders prenumerationer måste vara medlemmar i den här gruppen. AOBO tillåter inte flexibiliteten att skapa olika grupper som arbetar med olika kunder eller att aktivera olika roller för grupper eller användare.

![Klienthantering med AOBO](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure-delegerad resurshantering

Med hjälp av Azure-delegerad resurshantering kan du tilldela olika grupper till olika kunder eller roller, som visas i följande diagram. Eftersom användarna har rätt åtkomstnivå via Azure-delegerad resurshantering kan du minska antalet användare som har rollen Administratörsagent (och därmed ha fullständig AOBO-åtkomst). Detta bidrar till att förbättra säkerheten genom att begränsa onödig åtkomst till dina kunders resurser. Det ger dig också mer flexibilitet att hantera flera kunder i stor skala.

Introduktion till en prenumeration som du har skapat via CSP-programmet följer stegen som beskrivs i [Inboard en prenumeration på Azure-delegerad resurshantering](../how-to/onboard-customer.md). Alla användare som har rollen Administratörsagent i din klientorganisation kan utföra den här introduktionen.

![Klienthantering med AOBO och Azure-delegerad resurshantering](../media/csp-2.jpg)

> [!NOTE]
> Sidan [ **Mina kunder** i Azure-portalen](../how-to/view-manage-customers.md) innehåller nu avsnittet **Cloud Solution Provider (Preview),** som visar faktureringsinformation och resurser för CSP-kunder som har [undertecknat McA (Microsoft Customer Agreement)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) och som omfattas [av Azure-planen](https://docs.microsoft.com/partner-center/azure-plan-get-started). Mer information finns i [Komma igång med ditt faktureringskonto för Microsoft Partner Agreement](../../billing/mpa-overview.md).
>
> CSP-kunder kan visas i det här avsnittet oavsett om de också har varit inbyggda för Azure-delegerad resurshantering. Om de har gjort det visas de också i avsnittet **Kunder,** enligt beskrivningen i [Visa och hanterar kunder och delegerade resurser](../how-to/view-manage-customers.md). På samma sätt behöver en CSP-kund inte visas i avsnittet **Cloud Solution Provider (Preview)** i **Mina kunder** för att du ska kunna gå ombord på dem för Azure-delegerad resurshantering.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanteringsupplevelser mellan klienter.](cross-tenant-management-experience.md)
- Lär dig hur du [tecknar en prenumeration på Azure-delegerad resurshantering](../how-to/onboard-customer.md).
- Läs mer om [cloud solution provider-programmet](https://docs.microsoft.com/partner-center/csp-overview).
