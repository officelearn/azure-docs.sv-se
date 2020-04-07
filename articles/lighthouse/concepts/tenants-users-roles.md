---
title: Klienter, roller och användare i Azure Lighthouse-scenarier
description: Förstå begreppen för Azure Active Directory-klienter, användare och roller samt hur de kan användas i Azure Lighthouse-scenarier.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 32d9214e4d0d204db39b6e6decab4665e9b55069
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754093"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Klienter, roller och användare i Azure Lighthouse-scenarier

Innan du lägger till kunder för [Azure-delegerad resurshantering](azure-delegated-resource-management.md)är det viktigt att förstå hur Azure Active Directory (Azure AD) klienter, användare och roller fungerar samt hur de kan användas i Azure Lighthouse-scenarier.

En *klient* är en dedikerad och betrodd instans av Azure AD. Vanligtvis representerar varje klient en enda organisation. Azure-delegerad resurshantering möjliggör logisk projektion av resurser från en klient till en annan klient. På så sätt kan användare i klienten hantera (till exempel en som tillhör en tjänsteleverantör) komma åt delegerade resurser i en kunds klientorganisation eller låta [företag med flera klienter centralisera sina hanteringsåtgärder](enterprise.md).

För att uppnå den här logiska projektionen måste en prenumeration (eller en eller flera resursgrupper inom en prenumeration) i kundens klientorganisation vara *inboarded* för Azure-delegerad resurshantering. Den här introduktionsprocessen kan göras antingen [via Azure Resource Manager-mallar](../how-to/onboard-customer.md) eller genom [att publicera ett offentligt eller privat erbjudande till Azure Marketplace](../how-to/publish-managed-services-offers.md).

Oavsett vilken introduktionsmetod du väljer måste du definiera *auktoriseringar*. Varje auktorisering anger ett användarkonto i den hanterande klienten som ska ha åtkomst till de delegerade resurserna och en inbyggd roll som anger de behörigheter som var och en av dessa användare ska ha för dessa resurser.

## <a name="role-support-for-azure-delegated-resource-management"></a>Rollstöd för Azure-delegerad resurshantering

När du definierar en auktorisering måste varje användarkonto tilldelas en av de [rollbaserade åtkomstkontroll (RBAC) inbyggda rollerna](../../role-based-access-control/built-in-roles.md). Anpassade roller och [klassiska prenumerationsadministratörsroller](../../role-based-access-control/classic-administrators.md) stöds inte.

Alla [inbyggda roller](../../role-based-access-control/built-in-roles.md) stöds för närvarande med Azure-delegerad resurshantering, med följande undantag:

- Rollen [Ägare](../../role-based-access-control/built-in-roles.md#owner) stöds inte.
- Inbyggda roller med [behörigheten DataActions](../../role-based-access-control/role-definitions.md#dataactions) stöds inte.
- Den inbyggda rollen [för användaråtkomstadministratören](../../role-based-access-control/built-in-roles.md#user-access-administrator) stöds, men endast för det begränsade syftet [att tilldela roller till en hanterad identitet i kundklienten](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Inga andra behörigheter som vanligtvis beviljas av den här rollen gäller. Om du definierar en användare med den här rollen måste du också ange de inbyggda rollerna som den här användaren kan tilldela hanterade identiteter.

> [!NOTE]
> När en tillämplig ny inbyggd roll har lagts till i Azure kan den tilldelas när [en kund registreras med Azure Resource Manager-mallar](../how-to/onboard-customer.md). Det kan finnas en fördröjning innan den nyligen tillagda rollen blir tillgänglig i Cloud Partner Portal när [du publicerar ett hanterat tjänsteerbjudande](../how-to/publish-managed-services-offers.md).

## <a name="best-practices-for-defining-users-and-roles"></a>Metodtips för att definiera användare och roller

När du skapar dina auktoriseringar rekommenderar vi följande metodtips:

- I de flesta fall vill du tilldela behörigheter till en Azure AD-användargrupp eller tjänsthuvudnamn, i stället för till en serie enskilda användarkonton. På så sätt kan du lägga till eller ta bort åtkomst för enskilda användare utan att behöva uppdatera och publicera om planen när åtkomstkraven ändras.
- Var noga med att följa principen om lägsta behörighet så att användarna bara har de behörigheter som behövs för att slutföra sitt jobb, vilket bidrar till att minska risken för oavsiktliga fel. Mer information finns i [Rekommenderade säkerhetsrutiner](../concepts/recommended-security-practices.md).
- Inkludera en användare med [rollen Borttagning av tilldelning](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) av hanterade tjänster så att du kan ta bort [åtkomsten till delegeringen](../how-to/onboard-customer.md#remove-access-to-a-delegation) senare om det behövs. Om den här rollen inte tilldelas kan delegerade resurser endast tas bort av en användare i kundens klientorganisation.
- Se till att alla användare som behöver [visa sidan Mina kunder i Azure-portalen](../how-to/view-manage-customers.md) har rollen [Läsare](../../role-based-access-control/built-in-roles.md#reader) (eller en annan inbyggd roll som inkluderar läsaråtkomst).

> [!IMPORTANT]
> För att kunna lägga till behörigheter för en Azure AD-grupp måste **grupptypen** vara **Säkerhet** och inte **Office 365**. Det här alternativet väljs när gruppen skapas. Mer information finns i [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [rekommenderade säkerhetsmetoder för Azure-delegerad resurshantering](recommended-security-practices.md).
- Ombord på dina kunder till Azure-delegerad resurshantering, antingen genom [att använda Azure Resource Manager-mallar](../how-to/onboard-customer.md) eller genom att publicera ett erbjudande om privata eller offentliga hanterade tjänster till Azure [Marketplace](../how-to/publish-managed-services-offers.md).
