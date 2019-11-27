---
title: Att tänka på med Cloud Solution Provider-programmet
description: För CSP-partner hjälper Azure delegerad resurs hantering att förbättra säkerheten och kontrollen genom att aktivera detaljerade behörigheter.
ms.date: 10/23/2019
ms.topic: conceptual
ms.openlocfilehash: 7e1e371d8c31c45828ee0565545cb40145b40e92
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463976"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure-Lighthouse och Cloud Solution Provider-programmet

Om du är en [CSP-partner (Cloud Solution Provider)](https://docs.microsoft.com/partner-center/csp-overview) kan du redan komma åt de Azure-prenumerationer som har skapats för dina kunder via CSP-programmet med hjälp av [administrate-funktionen (administrera på uppdrag av)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Med den här åtkomsten kan du direkt stödja, konfigurera och hantera dina kunders prenumerationer.

Med [Azure Lighthouse](../overview.md)kan du använda Azure-delegerad resurs hantering tillsammans med administrate. Detta bidrar till att förbättra säkerheten och minskar onödig åtkomst genom att aktivera mer detaljerade behörigheter för dina användare. Det ger också ökad effektivitet och skalbarhet, eftersom dina användare kan arbeta över flera kund prenumerationer med hjälp av en enda inloggning i din klient organisation.

> [!TIP]
> Du kan skydda kund resurser genom att granska och följa våra [rekommenderade säkerhets metoder](recommended-security-practices.md) tillsammans med [partner säkerhets kraven](https://docs.microsoft.com/partner-center/partner-security-requirements).

## <a name="administer-on-behalf-of-aobo"></a>Administrera på uppdrag av (ADMINISTRATE)

Med ADMINISTRATE kommer alla användare med [Administratörs agent](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) rollen i din klient organisation ha administrate åtkomst till Azure-prenumerationer som du skapar via CSP-programmet. Alla användare som behöver åtkomst till kunders prenumerationer måste vara medlemmar i den här gruppen. ADMINISTRATE tillåter inte flexibiliteten att skapa distinkta grupper som fungerar med olika kunder, eller för att aktivera olika roller för grupper eller användare.

![Hantering av innehavare med ADMINISTRATE](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure-delegerad resurshantering

Med Azure-delegerad resurs hantering kan du tilldela olika grupper till olika kunder eller roller, som du ser i följande diagram. Eftersom användarna får rätt åtkomst nivå via Azure-delegerad resurs hantering kan du minska antalet användare som har rollen administratörs agent (och därmed ha fullständig ADMINISTRATE-åtkomst). Detta bidrar till att förbättra säkerheten genom att begränsa onödig åtkomst till dina kunders resurser. Det ger dig också större flexibilitet att hantera flera kunder i stor skala.

När du registrerar en prenumeration som du har skapat via CSP-programmet följer du stegen som beskrivs i [publicera en prenumeration på Azure-delegerad resurs hantering](../how-to/onboard-customer.md). Alla användare som har rollen administratörs agent i din klient organisation kan utföra denna onboarding.

![Hantering av innehavare med ADMINISTRATE och Azure delegerad resurs hantering](../media/csp-2.jpg)

> [!NOTE]
> Sidan [ **Mina kunder** i Azure Portal](../how-to/view-manage-customers.md) innehåller nu en **moln lösnings leverantör (förhands granskning)** , som visar fakturerings information och resurser för CSP-kunder som har [undertecknat Microsofts kund avtal (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) och som ingår i Azure-prenumerationen. Mer information finns i [Kom igång med ditt fakturerings konto för Microsoft partner avtal](https://docs.microsoft.com/azure/billing/mpa-overview).
>
> CSP-kunder kan visas i det här avsnittet oavsett om de har publicerats för Azure-delegerad resurs hantering. Om de har det visas de också i avsnittet **kunder** , enligt beskrivningen i [Visa och hantera kunder och delegerade resurser](../how-to/view-manage-customers.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](cross-tenant-management-experience.md).
- Lär dig att [publicera en prenumeration på Azure-delegerad resurs hantering](../how-to/onboard-customer.md).
- Lär dig mer om [Cloud Solution Provider-programmet](https://docs.microsoft.com/partner-center/csp-overview).
