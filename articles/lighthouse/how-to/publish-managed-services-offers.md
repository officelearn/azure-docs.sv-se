---
title: Publicera ett hanterat tjänst erbjudande på Azure Marketplace
description: Lär dig hur du publicerar ett hanterat tjänst erbjudande som registrerar kunder till Azure-delegerad resurs hantering.
ms.date: 05/04/2020
ms.topic: how-to
ms.openlocfilehash: f1aef8bbf5279891fb8edf2222cc1dda3326bdd6
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636520"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publicera ett hanterat tjänst erbjudande på Azure Marketplace

I den här artikeln får du lära dig hur du publicerar ett offentligt eller privat hanterat tjänst erbjudande på [Azure Marketplace](https://azuremarketplace.microsoft.com) med programmet för [kommersiella marknads platser](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) i Partner Center. Kunder som köper erbjudandet kan sedan publicera prenumerationer och resurs grupper för [Azure-delegerad resurs hantering](../concepts/azure-delegated-resource-management.md).

## <a name="publishing-requirements"></a>Publicerings krav

Du måste ha ett giltigt [konto i Partner Center](../../marketplace/partner-center-portal/create-account.md) för att kunna skapa och publicera erbjudanden. Om du inte redan har ett konto kommer [registrerings processen](https://aka.ms/joinmarketplace) att leda dig genom stegen för att skapa ett konto i Partner Center och registrera dig i programmet för kommersiella marknads platser.

Enligt [certifierings kraven för den hanterade tjänstens erbjudande](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)måste du ha en [silver eller guld plattforms plattforms nivå](https://docs.microsoft.com/partner-center/learn-about-competencies) eller vara en [Azure expert-MSP](https://partner.microsoft.com/membership/azure-expert-msp) för att kunna publicera ett hanterat tjänst erbjudande.

Ditt Microsoft Partner Network (MPN) ID kommer [automatiskt att associeras](../../billing/billing-partner-admin-link-started.md) med de erbjudanden du publicerar för att spåra din påverkan på kund engagemang.

> [!NOTE]
> Om du inte vill publicera ett erbjudande på Azure Marketplace kan du publicera kunder manuellt med hjälp av Azure Resource Manager mallar. Mer information finns i [publicera en kund till Azure delegerad resurs hantering](onboard-customer.md).

## <a name="create-your-offer"></a>Skapa ditt erbjudande

Detaljerade anvisningar om hur du skapar ett erbjudande, inklusive all information och till gångar som du behöver ange, finns i [skapa ett hanterat tjänst erbjudande](../../marketplace/partner-center-portal/create-new-managed-service-offer.md).

Mer information om den allmänna publicerings processen finns i [publicerings guiden för Azure Marketplace och AppSource](../../marketplace/marketplace-publishers-guide.md). Du bör också gå igenom de [kommersiella Marketplace-certifierings principerna](https://docs.microsoft.com/legal/marketplace/certification-policies), särskilt avsnittet [hanterade tjänster](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) .

När en kund lägger till ditt erbjudande kommer de att kunna delegera en eller flera prenumerationer eller resurs grupper som sedan kommer att registreras [för Azure-delegerad resurs hantering](#the-customer-onboarding-process).

> [!IMPORTANT]
> Varje plan i ett hanterat tjänst erbjudande innehåller ett avsnitt om **manifest information** , där du definierar de Azure Active Directory (Azure AD) entiteter i din klient som ska ha åtkomst till de delegerade resurs grupperna och/eller prenumerationer för kunder som köper den planen. Det är viktigt att vara medveten om att alla grupper (eller användare eller tjänstens huvud namn) som du inkluderar har samma behörigheter för alla kunder som köper planen. Om du vill tilldela olika grupper för att arbeta med varje kund måste du publicera en separat [privat plan](../../marketplace/private-offers.md) som är exklusiv för varje kund.

## <a name="publish-your-offer"></a>Publicera ditt erbjudande

När du har slutfört alla avsnitt är nästa steg att publicera erbjudandet på Azure Marketplace. Välj knappen **publicera** för att påbörja processen med att göra erbjudandet Live. Mer information om den här processen finns [här](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish). 

Du kan när som helst [publicera en uppdaterad version av erbjudandet](../..//marketplace/partner-center-portal/update-existing-offer.md) . Du kanske till exempel vill lägga till en ny roll definition till ett tidigare publicerat erbjudande. När du gör det, kommer kunder som redan har lagt till erbjudandet att se en ikon på sidan [**tjänst leverantörer**](view-manage-service-providers.md) i Azure Portal som gör att de kan se att en uppdatering är tillgänglig. Varje kund kan [granska ändringarna](view-manage-service-providers.md#update-service-provider-offers) och bestämma om de vill uppdatera till den nya versionen. 

## <a name="the-customer-onboarding-process"></a>Kundens onboarding-process

När en kund lägger till ditt erbjudande kan de [delegera en eller flera specifika prenumerationer eller resurs grupper](view-manage-service-providers.md#delegate-resources)som sedan kommer att registreras för Azure-delegerad resurs hantering. Om en kund har accepterat ett erbjudande men ännu inte har delegerat några resurser visas en anteckning längst upp i avsnittet **leverantörs erbjudanden** på sidan [**tjänst leverantörer**](view-manage-service-providers.md) i Azure Portal.

> [!IMPORTANT]
> Delegeringen måste utföras av ett konto som inte är gäst i kundens klient organisation som har den [inbyggda rollen ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) för den prenumeration som registreras (eller som innehåller de resurs grupper som har registrerats). Om du vill se alla användare som kan delegera prenumerationen kan en användare i kundens klient välja prenumerationen i Azure Portal, öppna **åtkomst kontroll (IAM)** och [Visa alla användare med ägar rollen](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

När kunden delegerar en prenumeration (eller en eller flera resurs grupper inom en prenumeration) registreras **Microsoft. ManagedServices** Resource Provider för den prenumerationen och användare i din klient organisation kan komma åt de delegerade resurserna enligt auktoriseringarna i ditt erbjudande.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [kommersiella marknads platsen](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Lär dig mer om [hanterings upplevelser mellan flera innehavare](../concepts/cross-tenant-management-experience.md).
- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **mina kunder** i Azure Portal.
