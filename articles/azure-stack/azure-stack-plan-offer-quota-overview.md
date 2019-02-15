---
title: Azure Stack-plan, erbjudande, kvot och prenumeration översikt | Microsoft Docs
description: Jag vill förstå Azure Stack-planer, erbjudanden, kvoter och prenumerationer som ansvarig för molnet.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 10/12/2018
ms.openlocfilehash: fba5c66f3006de6b65b2db27187449201d40250e
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269713"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Översikt över plan, erbjudande, kvot och prenumeration

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

[Azure Stack](azure-stack-poc.md) kan du leverera en mängd olika tjänster, till exempel virtuella datorer, SQL Server-databaser, SharePoint, Exchange, och även [Azure Marketplace-objekt](azure-stack-marketplace-azure-items.md). Som Azure Stack-operatör, konfigurera och skicka sådana tjänster i Azure Stack via planer, erbjudanden och kvoter.

Erbjudanden innehålla en eller flera planer och varje planen innehåller en eller flera tjänster. Genom att skapa planer och kombinera dem till olika erbjudanden, kan du hantera:

- Vilka tjänster och resurser som användarna kan nå.
- Mängden resurser som användare kan använda.
- Vilka regioner har åtkomst till resurserna.

Följ dessa anvisningar när du levererar en tjänst:

1. Lägg till en tjänst som du vill förse dina användare.
2. Skapa en plan som har en eller flera tjänster. När du skapar en plan, Välj eller skapa kvoter som definierar resursgränser för varje tjänst i planen.
3. Skapa ett erbjudande som innehåller en eller flera planer. Erbjudandet kan innehålla basplaner och valfritt tilläggsplaner.

När du har skapat erbjudandet kan kan dina användare prenumerera komma åt tjänster och resurser i erbjudandet ger tillgång till. Användare kan prenumerera så många erbjudanden som de vill. Följande bild visar ett enkelt exempel på en användare som prenumererar på två erbjudanden. Varje erbjudande har en plan eller två, och varje plan ger dem åtkomst till tjänster.

![Klientprenumeration med erbjudanden och planer](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Planer

Prenumerationer är grupperingar av en eller flera tjänster. Som en Azure Stack-operatör du [skapa planer](azure-stack-create-plan.md) att erbjuda användarna. Användarna prenumererar i sin tur dina erbjudanden för att använda de planer och tjänster som ingår. När du skapar planer kan du se till att ange dina kvoter, definiera din basplaner och överväga att använda valfritt tilläggsplaner.

### <a name="quotas"></a>Kvoter

Du kan använda för att hjälpa dig att hantera din kapacitet, förkonfigurerade *kvoter*, eller skapa en ny kvot för varje tjänst i en plan. Kvoter definiera övre resursbegränsningar som en användarprenumeration kan etablera eller använda. En kvot kan till exempel tillåta en användare kan skapa upp till fem virtuella datorer (VM).

Du kan konfigurera kvoter efter region. En plan som tillhandahåller beräkning för Region A kan till exempel ha en kvot på två virtuella datorer.

>[!NOTE]
>I den Azure Stack Development Kit, endast en region (med namnet *lokala*) är tillgänglig.

Läs mer om [kvottyper i Azure Stack](azure-stack-quota-types.md).

### <a name="base-plan"></a>Basplan

När du skapar ett erbjudande, kan tjänstadministratören innehålla en Basplan. Dessa basplaner ingår som standard när en användare prenumererar på erbjudandet. När en användare prenumererar har de åtkomst till alla resursprovidrar som anges i dessa basplaner (med motsvarande kvoter).

### <a name="add-on-plans"></a>Tilläggsplaner

Tilläggsplaner finns valfria planer som du lägger till i ett erbjudande. Tilläggsplaner ingår inte som standard i prenumerationen. Tilläggsplaner är ytterligare scheman (med kvoter) tillgängliga i ett erbjudande som en prenumerant kan lägga till prenumerationerna. Du kan exempelvis erbjuda Basplan med begränsade resurser för en utvärderingsversion och en tilläggsplanen med mer omfattande resurser till kunder som väljer att införa tjänsten.

## <a name="offers"></a>Erbjudanden

Erbjudanden är grupper med en eller flera planer som du skapar så att användare kan prenumerera på dem. Erbjuder Alpha kan exempelvis innehålla Plan A, vilket ger en uppsättning Beräkningstjänster och planera B, som innehåller en uppsättning tjänster för lagring och nätverk.

När du [skapa ett erbjudande](azure-stack-create-offer.md), du måste inkludera minst en Basplan, men du kan också skapa tilläggsplaner som användare kan lägga till prenumerationen.

## <a name="subscriptions"></a>Prenumerationer

En prenumeration hör hur användare kommer åt dina erbjudanden. Om du är Azure Stack-operatör för en tjänstleverantör kan dina användare (klienter) att köpa dina tjänster genom att prenumerera på dina erbjudanden. Om du är Azure Stack-operatör i en organisation kan kan dina användare (-anställda) prenumerera på de tjänster du erbjuder utan att betala.

Varje kombination av en användare med ett erbjudande är en unik prenumeration. En användare kan ha flera erbjudanden prenumerationer, men varje prenumeration gäller endast för ett erbjudande. Planer, erbjudanden och kvoter gäller endast för en prenumeration som unik – de kan inte delas mellan prenumerationer. Varje resurs som en användare skapar är associerad med en prenumeration.

### <a name="default-provider-subscription"></a>Standard providerprenumeration

Standard-providerprenumeration skapas automatiskt när du distribuerar Azure Stack Development Kit. Den här prenumerationen kan användas för att hantera Azure Stack, distribuera ytterligare resursprovidrar och skapa planer och erbjudanden för användare. För säkerhet och licensiering orsaker, bör det inte användas för att köra kundens arbetsbelastningar och program.

## <a name="next-steps"></a>Nästa steg

Läs mer om planer och erbjudanden, [skapa en plan](azure-stack-create-plan.md).
