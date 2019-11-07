---
title: Klienter, roller och användare i Azure Lighthouse-scenarier
description: Förstå begreppen för Azure Active Directory klienter, användare och roller, samt hur de kan användas i Azure Lighthouse-scenarier.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 11/05/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: b87ef8534dab2c8f08aa8cdee9d939e2d1a3a0e7
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615869"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Klienter, roller och användare i Azure Lighthouse-scenarier

Innan du registrerar kunder för [Azure-delegerad resurs hantering](azure-delegated-resource-management.md)är det viktigt att förstå hur Azure Active Directory (Azure AD)-klienter, användare och roller fungerar, samt hur de kan användas i Azure Lighthouse-scenarier.

En *klient organisation* är en dedikerad och betrodd instans av Azure AD. Varje klient representerar vanligt vis en enda organisation. Azure-delegerad resurs hantering möjliggör logisk projektion av resurser från en klient till en annan klient organisation. Detta gör det möjligt för användare i hanterings klienten (till exempel en som tillhör en tjänst leverantör) att komma åt delegerade resurser i en kunds klient, eller låta [företag med flera innehavare centralisera sina hanterings åtgärder](enterprise.md).

För att uppnå den här logiska projektionen måste en prenumeration (eller en eller flera resurs grupper inom en prenumeration *) registreras för* Azure-delegerad resurs hantering. Den här onboarding-processen kan göras antingen [via Azure Resource Manager mallar](../how-to/onboard-customer.md) eller genom [att publicera ett offentligt eller privat erbjudande till Azure Marketplace](../how-to/publish-managed-services-offers.md).

Vilken onboarding-metod du väljer måste du definiera *auktoriseringar*. Varje auktorisering anger ett användar konto i hanterings klienten som kommer att ha åtkomst till de delegerade resurserna och en inbyggd roll som anger de behörigheter som var och en av dessa användare kommer att ha för dessa resurser.

## <a name="role-support-for-azure-delegated-resource-management"></a>Roll stöd för Azure-delegerad resurs hantering

När du definierar en auktorisering måste varje användar konto tilldelas en av de [inbyggda rollbaserade RBAC-rollerna (rollbaserad åtkomst kontroll)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Administratörs roller för anpassade roller och [klassiska prenumerationer](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) stöds inte.

Alla [inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) stöds för närvarande med Azure-delegerad resurs hantering, med följande undantag:

- [Ägar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) rollen stöds inte.
- Inbyggda roller med [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) -behörighet stöds inte.
- Den inbyggda rollen [administratör för användar åtkomst](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) stöds, men endast för det begränsade syftet med [att tilldela roller till en hanterad identitet i kund klienten](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Inga andra behörigheter som vanligt vis beviljas av den här rollen kommer att gälla. Om du definierar en användare med den här rollen måste du också ange de inbyggda roller som den här användaren kan tilldela till hanterade identiteter.

## <a name="best-practices-for-defining-users-and-roles"></a>Metod tips för att definiera användare och roller

När du skapar dina auktoriseringar rekommenderar vi följande metod tips:

- I de flesta fall vill du tilldela behörigheter till en användar grupp eller ett tjänst huvud namn i Azure AD, i stället för till en serie med enskilda användar konton. På så sätt kan du lägga till eller ta bort åtkomst för enskilda användare utan att behöva uppdatera och publicera om planen när dina åtkomst krav ändras.
- Se till att du följer principen om minsta behörighet så att användarna bara har de behörigheter som krävs för att utföra sitt jobb, vilket bidrar till att minska risken för oavsiktliga fel. Mer information finns i [rekommenderade säkerhets metoder](../concepts/recommended-security-practices.md).
- Ta med en användare med [tilldelnings rollen för hanterade tjänster](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) , så att du kan [ta bort åtkomsten till delegeringen](../how-to/onboard-customer.md#remove-access-to-a-delegation) senare om det behövs. Om den här rollen inte är tilldelad kan delegerade resurser bara tas bort av en användare i kundens klient organisation.
- Se till att alla användare som behöver [Visa sidan mina kunder i Azure Portal](../how-to/view-manage-customers.md) har rollen [läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (eller någon annan inbyggd roll som innehåller läsar åtkomst).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [rekommenderade säkerhets metoder för Azure-delegerad resurs hantering](recommended-security-practices.md).
- Publicera dina kunder till Azure delegerad resurs hantering, antingen genom att [använda Azure Resource Manager mallar](../how-to/onboard-customer.md) eller genom [att publicera ett privat eller offentligt hanterat tjänst erbjudande på Azure Marketplace](../how-to/publish-managed-services-offers.md).
