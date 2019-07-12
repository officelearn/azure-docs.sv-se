---
title: Hanterade tjänster erbjudanden på Azure Marketplace
description: Hanterade tjänster erbjudanden tillåta tjänstleverantörer att sälja resource management erbjudanden till kunder i Azure Marketplace.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a6fcf5f1d0ac194d60f834fb8d26db019c538410
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809878"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Hanterade tjänster erbjudanden på Azure Marketplace

Den här artikeln beskriver den nya **hanterade tjänster** erbjudandetyp i [Azure Marketplace](https://azuremarketplace.microsoft.com). Hanterade tjänster som erbjuder kan du erbjuda resource management-tjänster till kunder med Azure delegerade resource Manager. Du kan göra dessa erbjudanden tillgängliga för alla potentiella kunder eller bara till en eller flera specifika kunder. Eftersom du debitera kunder direkt för kostnaderna för dessa hanterade tjänster finns det inga avgifter som tas ut av Microsoft.

## <a name="understand-managed-services-offers"></a>Förstå hanteringstjänster erbjudanden

Hanterade tjänster erbjudanden effektivisera processen med registrering av kunder för Azure delegerad resurshantering. När en kund köper ett erbjudande på Azure Marketplace, kommer de att kunna ange vilka prenumerationer och/eller resursgrupper bör ska publiceras så att de angivna användarna i organisationen kan utföra administrationsuppgifter för kunden inifrån din organisationens klient.

Efter det krävs ingen ytterligare åtgärd av kunden eller tjänstleverantören du kommer igång för kunden. Detta beror på att när du definierar erbjudandet i den [Cloud Partner Portal](https://cloudpartner.azure.com/), skapar du ett manifest som anger Azure AD-användare, grupper och principer för tjänsten som har åtkomst till kundresurser med hjälp av Azure delegeras resurs hantering. tillsammans med roller som definierar sina åtkomstnivå. Genom att tilldela behörigheter till en Azure AD-grupp i stället för en serie med enskilda användare eller program-konton, kan du lägga till eller ta bort enskilda användare när ändrar dina krav för åtkomst.

## <a name="public-and-private-offers"></a>Offentliga och privata erbjudanden

Varje erbjudande för hanterade tjänster innehåller en eller flera planer. De här planerna kan vara antingen privat eller offentlig.

Du kan publicera en privat plan om du vill begränsa ditt erbjudande till specifika kunder. När du gör detta kan endast planen köpas för specifikt] prenumerations-ID som du anger. Mer information finns i [privata erbjudanden](https://docs.microsoft.com/azure/marketplace/private-offers).

Offentliga planer kan du uppgradera dina tjänster för nya kunder. Det här är vanligtvis mer effektivt när du behöver endast begränsad åtkomst till kundens klient. När du har skapat en relation med en kund, om de vill ge din organisation ytterligare åtkomst, kan du göra detta antingen genom att publicera en ny privat plan för kunden eller genom att [onboarding för ytterligare åtkomst med hjälp av Azure Resource Manager-mallar](../how-to/onboard-customer.md).

Tänk på att när en plan har publicerats som offentliga kan du inte ändra den till privat. Dessutom kan kan inte du begränsa en offentlig plan tillgänglighet för vissa kunder eller till och med ett visst antal kunder, även om du kan stoppa sälja planen helt om du väljer att göra det.

Om det är lämpligt, kan du inkludera både offentliga och privata planer i erbjudandet i samma.

## <a name="publish-managed-service-offers"></a>Publicera erbjudanden för hanterad tjänst

Läs hur du publicerar ett erbjudande med hanterade tjänster i [publicera ett erbjudande med hanterade tjänster på Azure Marketplace](../how-to/publish-managed-services-offers.md). Allmän information om hur du publicerar på Azure Marketplace med hjälp av Cloud Partner Portal finns i [Azure Marketplace och AppSource publiceringsguide](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) och [hantera Azure och AppSource-marknadsplatsen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure delegerad resurshantering](azure-delegated-resource-management.md) och [mellan klientorganisationer hanteringsupplevelser](cross-tenant-management-experience.md).
- [Publicera erbjudanden för hanterade tjänster](../how-to/publish-managed-services-offers.md) på Azure Marketplace.
