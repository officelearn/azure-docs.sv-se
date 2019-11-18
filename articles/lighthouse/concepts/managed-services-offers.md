---
title: Erbjudanden om hanterade tjänster på Azure Marketplace
description: Med hanterade tjänster kan tjänste leverantörer sälja resurs hanterings erbjudanden till kunder på Azure Marketplace.
ms.date: 09/19/2019
ms.topic: overview
ms.openlocfilehash: 0f1ba749a5477f0c006e6666b841e82f4eeb193f
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131874"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Erbjudanden om hanterade tjänster på Azure Marketplace

I den här artikeln beskrivs de nya **hanterade tjänsternas** typ av erbjudande på [Azure Marketplace](https://azuremarketplace.microsoft.com). Med hanterade tjänster kan du erbjuda resurs hanterings tjänster till kunder med Azure delegerad resurs hantering. Du kan göra dessa erbjudanden tillgängliga för alla potentiella kunder eller bara för en eller flera olika kunder. Eftersom du fakturerar kunder direkt för kostnader som rör dessa hanterade tjänster, finns det inga avgifter som debiteras av Microsoft.

## <a name="understand-managed-services-offers"></a>Förstå erbjudandet om hanterade tjänster

Hanterade tjänster erbjuder en strömlinjeformad process för att registrera kunder för Azure-delegerad resurs hantering. När en kund köper ett erbjudande på Azure Marketplace kan de ange vilka prenumerationer och/eller resurs grupper som ska registreras. Observera att prenumerationen först måste godkännas för onboarding genom att manuellt registrera **Microsoft. ManagedServices** -resurs leverantören.

Efter det kommer användare i organisationen att kunna utföra administrativa uppgifter för dessa resurser inifrån organisationens klient organisation, enligt den åtkomst som du definierade när du skapar erbjudandet i [Cloud Partner Portal](https://cloudpartner.azure.com/). Detta görs via ett manifest som anger de Azure AD-användare, grupper och tjänstens huvud namn som kommer att ha åtkomst till kund resurser med hjälp av Azure delegerad resurs hantering, tillsammans med roller som definierar deras åtkomst nivå. Genom att tilldela behörigheter till en Azure AD-grupp i stället för en serie enskilda användare eller program konton kan du lägga till eller ta bort enskilda användare när åtkomst kraven ändras.

## <a name="public-and-private-offers"></a>Offentliga och privata erbjudanden

Varje hanterings tjänst erbjudande innehåller en eller flera planer. Dessa planer kan vara antingen privata eller offentliga.

Om du vill begränsa ditt erbjudande till vissa kunder kan du publicera en privat plan. När du gör det kan planen bara köpas för de aktuella] prenumerations-ID: n som du anger. Mer information finns i [privata erbjudanden](https://docs.microsoft.com/azure/marketplace/private-offers).

Med offentliga planer kan du marknadsföra dina tjänster till nya kunder. Dessa är vanligt vis mer lämpliga när du bara behöver begränsad åtkomst till kundens klient organisation. När du har upprättat en relation med en kund kan du, om de bestämmer dig för att ge organisationen ytterligare åtkomst, göra detta genom att publicera en ny privat plan för den kunden eller genom [att registrera dem för ytterligare åtkomst med hjälp av Azure Resource Manager mallar](../how-to/onboard-customer.md).

Tänk på att när en plan har publicerats som offentlig kan du inte ändra den till privat. Dessutom kan du inte begränsa en offentlig Plans tillgänglighet till vissa kunder eller till och med ett visst antal kunder, även om du kan sluta sälja planen helt om du väljer att göra det.

Om det behövs kan du inkludera både offentliga och privata planer i samma erbjudande.

## <a name="publish-managed-service-offers"></a>Publicera hanterade tjänst erbjudanden

Information om hur du publicerar ett erbjudande för hanterade tjänster finns i [publicera ett erbjudande för hanterade tjänster på Azure Marketplace](../how-to/publish-managed-services-offers.md). Allmän information om hur du publicerar till Azure Marketplace med hjälp av Cloud Partner Portal finns i [Azure Marketplace och AppSource Publishing guide](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) och [Hantera Azure-och AppSource Marketplace-erbjudanden](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurs hantering](azure-delegated-resource-management.md) och [flera klient hanterings upplevelser](cross-tenant-management-experience.md).
- [Publicera hanterade tjänster erbjuder](../how-to/publish-managed-services-offers.md) Azure Marketplace.
