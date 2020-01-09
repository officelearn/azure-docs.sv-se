---
title: Erbjudanden om hanterade tjänster på Azure Marketplace
description: Med hanterade tjänster kan tjänste leverantörer sälja resurs hanterings erbjudanden till kunder på Azure Marketplace.
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: 1b4f0d7457a74afe710a48f429cfe47535a9b122
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453592"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Erbjudanden om hanterade tjänster på Azure Marketplace

I den här artikeln beskrivs de nya **hanterade tjänsternas** typ av erbjudande på [Azure Marketplace](https://azuremarketplace.microsoft.com). Med hanterade tjänster kan du erbjuda resurs hanterings tjänster till kunder med Azure delegerad resurs hantering. Du kan göra dessa erbjudanden tillgängliga för alla potentiella kunder eller bara för en eller flera olika kunder. Eftersom du fakturerar kunder direkt för kostnader som rör dessa hanterade tjänster, finns det inga avgifter som debiteras av Microsoft.

## <a name="understand-managed-services-offers"></a>Förstå erbjudandet om hanterade tjänster

Hanterade tjänster erbjuder en strömlinjeformad process för att registrera kunder för Azure-delegerad resurs hantering. När en kund köper ett erbjudande på Azure Marketplace kan de ange vilka prenumerationer och/eller resurs grupper som ska registreras. Observera att prenumerationen först måste godkännas för onboarding genom att manuellt registrera **Microsoft. ManagedServices** -resurs leverantören.

Efter det kommer användare i organisationen att kunna utföra administrativa uppgifter för dessa resurser inifrån organisationens klient organisation, enligt den åtkomst som du definierade när du skapar erbjudandet i [Cloud Partner Portal](https://cloudpartner.azure.com/). Detta görs via ett manifest som anger de Azure AD-användare, grupper och tjänstens huvud namn som kommer att ha åtkomst till kund resurser med hjälp av Azure delegerad resurs hantering, tillsammans med roller som definierar deras åtkomst nivå. Genom att tilldela behörigheter till en Azure AD-grupp i stället för en serie enskilda användare eller program konton kan du lägga till eller ta bort enskilda användare när åtkomst kraven ändras.

## <a name="public-and-private-offers"></a>Offentliga och privata erbjudanden

Varje hanterings tjänst erbjudande innehåller en eller flera planer. Planer kan vara antingen privata eller offentliga. 

Om du vill begränsa ditt erbjudande till vissa kunder kan du publicera en privat plan. När du gör det kan planen bara köpas för de aktuella] prenumerations-ID: n som du anger. Mer information finns i [privata erbjudanden](../../marketplace/private-offers.md).

Med offentliga planer kan du marknadsföra dina tjänster till nya kunder. Dessa är vanligt vis mer lämpliga när du bara behöver begränsad åtkomst till kundens klient organisation. När du har upprättat en relation med en kund kan du, om de bestämmer dig för att ge organisationen ytterligare åtkomst, göra detta genom att publicera en ny privat plan för den kunden eller genom [att registrera dem för ytterligare åtkomst med hjälp av Azure Resource Manager mallar](../how-to/onboard-customer.md).

Om det behövs kan du inkludera både offentliga och privata planer i samma erbjudande.

> [!IMPORTANT]
> När en plan har publicerats som offentlig kan du inte ändra den till privat. Använd en privat plan för att kontrol lera vilka kunder som kan acceptera ditt erbjudande och delegera resurser. Med en offentlig plan kan du inte begränsa tillgänglighet till vissa kunder eller till och med ett visst antal kunder (även om du kan sluta sälja planen helt om du väljer att göra det). Det finns för närvarande ingen mekanism för att avvisa eller ta bort delegeringar när en kund accepterar ett erbjudande, men du kan alltid nå ut till en kund och be dem att [ta bort din åtkomst](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publicera hanterade tjänst erbjudanden

Information om hur du publicerar ett erbjudande för hanterade tjänster finns i [publicera ett erbjudande för hanterade tjänster på Azure Marketplace](../how-to/publish-managed-services-offers.md). Allmän information om hur du publicerar till Azure Marketplace med hjälp av Cloud Partner Portal finns i [Azure Marketplace och AppSource Publishing guide](../../marketplace/marketplace-publishers-guide.md) och [Hantera Azure-och AppSource Marketplace-erbjudanden](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurs hantering](azure-delegated-resource-management.md) och [flera klient hanterings upplevelser](cross-tenant-management-experience.md).
- [Publicera hanterade tjänster erbjuder](../how-to/publish-managed-services-offers.md) Azure Marketplace.
