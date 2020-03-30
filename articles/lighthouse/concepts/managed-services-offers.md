---
title: Erbjudanden om hanterade tjänster på Azure Marketplace
description: Med hanterade tjänster kan tjänsteleverantörer sälja resurshanteringserbjudanden till kunder på Azure Marketplace.
ms.date: 03/17/2020
ms.topic: conceptual
ms.openlocfilehash: 2d6e39f753736c0582e9d91870a99b66ae41255b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500781"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Erbjudanden om hanterade tjänster på Azure Marketplace

I den här artikeln beskrivs den nya erbjudandetypen **För hanterade tjänster** i Azure [Marketplace](https://azuremarketplace.microsoft.com). Med erbjudanden om hanterade tjänster kan du erbjuda resurshanteringstjänster till kunder med Azure-delegerad resurshantering. Du kan göra dessa erbjudanden tillgängliga för alla potentiella kunder eller endast för en eller flera specifika kunder. Eftersom du fakturerar kunder direkt för kostnader relaterade till dessa hanterade tjänster finns det inga avgifter som tas ut av Microsoft.

## <a name="understand-managed-services-offers"></a>Förstå erbjudanden om hanterade tjänster

Hanterade tjänster erbjuder effektivisera processen för introduktion av kunder för Azure-delegerad resurshantering. När en kund köper ett erbjudande på Azure Marketplace kan de ange vilka prenumerationer och/eller resursgrupper som ska finnas ombord. Observera att prenumerationen först måste godkännas för introduktion genom att manuellt registrera **microsoft.ManagedServices** resursprovider.

Därefter kan användare i organisationen utföra administrativa uppgifter för dessa resurser från organisationens klientorganisation, beroende på vilken åtkomst du definierade när du skapade erbjudandet i [Cloud Partner Portal](https://cloudpartner.azure.com/). Detta görs genom ett manifest som anger Azure AD-användare, grupper och tjänsthuvudnamn som kommer att ha åtkomst till kundresurser med Azure-delegerad resurshantering, tillsammans med roller som definierar deras åtkomstnivå. Genom att tilldela behörigheter till en Azure AD-grupp i stället för en serie enskilda användarkonton eller programkonton kan du lägga till eller ta bort enskilda användare när åtkomstkraven ändras.

## <a name="public-and-private-offers"></a>Offentliga och privata erbjudanden

Varje erbjudande om hanterade tjänster innehåller en eller flera planer. Planer kan vara antingen privata eller offentliga. 

Om du vill begränsa erbjudandet till specifika kunder kan du publicera en privat plan. När du gör det kan planen bara köpas för de specifika prenumerations-ID:n som du anger. Mer information finns i [Privata erbjudanden](../../marketplace/private-offers.md).

Med offentliga planer kan du marknadsföra dina tjänster till nya kunder. Dessa är vanligtvis lämpligare när du bara behöver begränsad åtkomst till kundens klientorganisation. När du har upprättat en relation med en kund, om de bestämmer sig för att ge din organisation ytterligare åtkomst, kan du göra det antingen genom att publicera en ny privat plan endast för den kunden eller genom att [lägga till dem för ytterligare åtkomst med Hjälp av Azure Resource Manager-mallar](../how-to/onboard-customer.md).

Om det är lämpligt kan du inkludera både offentliga och privata planer i samma erbjudande.

> [!IMPORTANT]
> När en plan har publicerats som offentlig kan du inte ändra den till privat. Om du vill styra vilka kunder som kan acceptera erbjudandet och delegera resurser använder du en privat plan. Med en offentlig plan kan du inte begränsa tillgängligheten till vissa kunder eller ens till ett visst antal kunder (även om du kan sluta sälja planen helt om du väljer att göra det). Du kan [ta bort åtkomsten till en delegering](../how-to/onboard-customer.md#remove-access-to-a-delegation) när en kund bara har accepterat ett erbjudande om du har inkluderat ett **auktorisering med** **rolldefinitionen** inställd på [Borttagning av tilldelning](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) av hanterade tjänster när du publicerade erbjudandet. Du kan också nå ut till kunden och be dem att [ta bort din åtkomst](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publicera erbjudanden om hanterade tjänster

Mer information om hur du publicerar ett erbjudande om hanterade tjänster finns i [Publicera ett erbjudande om hanterade tjänster till Azure Marketplace](../how-to/publish-managed-services-offers.md). Allmän information om publicering till Azure Marketplace med Cloud Partner Portal finns i [Azure Marketplace och AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md) och Hantera [Azure- och AppSource Marketplace-erbjudanden](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurshantering](azure-delegated-resource-management.md) och [hantering av flera innehavare.](cross-tenant-management-experience.md)
- [Publicera erbjudanden om hanterade tjänster](../how-to/publish-managed-services-offers.md) till Azure Marketplace.
