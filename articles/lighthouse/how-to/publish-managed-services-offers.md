---
title: Publicera ett hanterat tjänsterbjudande på Azure Marketplace
description: Lär dig hur du publicerar ett hanterat tjänsterbjudande som ombordar kunder till Azure-delegerad resurshantering.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 922e78a0715ce5c758ca7d068a38af43e214a524
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673941"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publicera ett hanterat tjänsterbjudande på Azure Marketplace

I den här artikeln får du lära dig hur du publicerar ett offentligt eller privat hanterat tjänsterbjudande till [Azure Marketplace](https://azuremarketplace.microsoft.com) med hjälp av programmet [Commercial Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) i Partner [Center](https://partner.microsoft.com/). Kunder som köper erbjudandet kan sedan lägga ombord på prenumerationer och resursgrupper för [Azure-delegerad resurshantering](../concepts/azure-delegated-resource-management.md).

## <a name="publishing-requirements"></a>Publiceringskrav

Du måste ha ett giltigt [konto i Partner Center](../../marketplace/partner-center-portal/create-account.md) för att skapa och publicera erbjudanden. Om du inte redan har ett konto leder [registreringsprocessen](https://aka.ms/joinmarketplace) dig genom stegen för att skapa ett konto i Partner Center och registrera dig i programmet Commercial Marketplace.

Enligt [certifieringskraven för managed service-erbjudandet](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)måste du ha en [kompetensnivå för Silver- eller Gold Cloud Platform](https://docs.microsoft.com/partner-center/learn-about-competencies) eller vara en Azure Expert [MSP](https://partner.microsoft.com/membership/azure-expert-msp) för att kunna publicera ett erbjudande om hanterad tjänst.

Mpn-ID-nätverket (Microsoft Partner Network) [kopplas automatiskt till](../../billing/billing-partner-admin-link-started.md) de erbjudanden du publicerar för att spåra din påverkan på kundengagemang.

> [!NOTE]
> Om du inte vill publicera ett erbjudande till Azure Marketplace kan du registrera kunder manuellt med hjälp av Azure Resource Manager-mallar. Mer information finns [i Ombord på en kund till Azure-delegerad resurshantering](onboard-customer.md).

## <a name="create-your-offer"></a>Skapa ditt erbjudande

Detaljerade instruktioner om hur du skapar erbjudandet, inklusive all information och alla resurser som du behöver tillhandahålla, finns i [Skapa ett nytt erbjudande om hanterade tjänster](../../marketplace/partner-center-portal/create-new-managed-service-offer.md).

Mer information om den allmänna publiceringsprocessen finns i [Azure Marketplace och AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md). Du bör också granska [certifieringsprinciperna för kommersiella marknadsplatser,](https://docs.microsoft.com/legal/marketplace/certification-policies)särskilt avsnittet [Managed Services.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

När en kund lägger till ditt erbjudande kan de delegera en eller flera prenumerationer eller resursgrupper, som sedan registreras [för Azure-delegerad resurshantering](#the-customer-onboarding-process).

> [!IMPORTANT]
> Varje plan i ett erbjudande om hanterad tjänst innehåller ett **manifestinformationsavsnitt,** där du definierar Azure Active Directory (Azure AD) entiteterna i din klientorganisation som kommer att ha åtkomst till de delegerade resursgrupperna och/eller prenumerationerna för kunder som köper den planen. Det är viktigt att vara medveten om att alla grupper (eller användar- eller tjänsthuvudnamn) som du inkluderar har samma behörighet för varje kund som köper planen. Om du vill tilldela olika grupper att arbeta med varje kund måste du publicera en separat [privat plan](../../marketplace/private-offers.md) som är exklusiv för varje kund.

## <a name="publish-your-offer"></a>Publicera ditt erbjudande

När du har slutfört alla avsnitt är nästa steg att publicera erbjudandet till Azure Marketplace. Välj knappen **Publicera** för att starta processen med att göra erbjudandet live. Mer information om denna process finns [här](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish).

Du kan när som helst [publicera en uppdaterad version av erbjudandet.](../../marketplace/partner-center-portal/update-existing-offer.md) Du kanske till exempel vill lägga till en ny rolldefinition i ett tidigare publicerat erbjudande. När du gör det ser kunder som redan har lagt till erbjudandet en ikon på sidan [**Tjänstleverantörer**](view-manage-service-providers.md) i Azure-portalen som låter dem veta att en uppdatering är tillgänglig. Varje kund kommer att kunna [granska ändringarna](view-manage-service-providers.md#update-service-provider-offers) och bestämma om de vill uppdatera till den nya versionen. 

## <a name="the-customer-onboarding-process"></a>Kundens introduktionsprocess

När en kund har lägger till ditt erbjudande kan de [delegera en eller flera specifika prenumerationer eller resursgrupper](view-manage-service-providers.md#delegate-resources), som sedan kommer att finnas ombord för Azure-delegerad resurshantering. Om en kund har accepterat ett erbjudande men ännu inte har delegerat några resurser visas en anteckning högst upp på avsnittet **Provider-erbjudanden** på sidan [**Tjänstleverantörer**](view-manage-service-providers.md) i Azure-portalen.

> [!IMPORTANT]
> Delegering måste göras av ett icke-gästkonto i kundens klientorganisation som har [rollen Ägare inbyggt](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) för prenumerationen som är ombord (eller som innehåller de resursgrupper som är inbyggda). Om du vill visa alla användare som kan delegera prenumerationen kan en användare i kundens klientorganisation välja prenumerationen i Azure-portalen, öppna **åtkomstkontroll (IAM)** och [visa alla användare med ägarrollen](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

När kunden har delegerat en prenumeration (eller en eller flera resursgrupper inom en prenumeration) registreras **resursleverantören Microsoft.ManagedServices** för den prenumerationen och användare i din klientorganisation kan komma åt de delegerade resurserna enligt auktoriseringarna i erbjudandet.

## <a name="next-steps"></a>Nästa steg

- Läs mer om den [kommersiella marknadsplatsen.](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)
- Lär dig mer om [hanteringsupplevelser mellan klienter.](../concepts/cross-tenant-management-experience.md)
- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **Mina kunder** i Azure-portalen.
