---
title: "Stacken plan, erbjudande, kvoter och prenumeration översikt över Azure | Microsoft Docs"
description: "Jag vill förstå Azure Stack planer, erbjudanden, kvoter och prenumerationer som en moln-operator."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/22/2017
ms.author: erikje
ms.openlocfilehash: 083ca2f0a06625810d2f90a682ba0b3110032e60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Översikt över plan, erbjudande, kvot och prenumeration

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

[Azure-stacken](azure-stack-poc.md) kan du tillhandahålla en mängd olika tjänster, t.ex. virtuella datorer, SQL Server-databaser, SharePoint, Exchange, och även [Azure Marketplace-objekt](azure-stack-marketplace-azure-items.md). Som operatör Azure Stack du konfigurerar och leverera sådana tjänster i Azure-stacken genom att använda planer, erbjudanden och kvoter.

Erbjudanden som innehåller en eller flera planer och varje plan innehåller en eller flera tjänster. Genom att skapa planer och att kombinera dem till olika erbjudanden kan styra du
- vilka tjänster och resurser som användare kan komma åt
- mängden de resurser som användare kan använda
- vilka regioner som har åtkomst till resurser

När du levererar en tjänst, ska du följa dessa anvisningar:

1. Lägg till en tjänst som du vill skicka till användarna.
2. Skapa en plan som innehåller en eller flera tjänster. När du skapar en plan, kommer du väljer eller skapar kvoter som definierar gränserna för varje tjänst resurs i planen.
3. Skapa ett erbjudande som innehåller en eller flera planer (inklusive basen planer och valfria tillägg planer).

När du har skapat erbjudandet kan användarna prenumerera på komma åt de tjänster och resurser som den innehåller. Användare kan prenumerera på valfritt antal erbjudanden som de vill. Följande diagram visar ett enkelt exempel på en användare som prenumererar på två erbjudanden. Varje erbjudandet har en plan eller ett par och varje plan ger dem åtkomst till tjänster.

![](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Planer

Planer är grupperingar av en eller flera tjänster. Som operatör Azure Stack du [skapa planer](azure-stack-create-plan.md) att erbjuda användarna. I sin tur prenumerera användarna på dina erbjudanden använda planer och tjänster som de innehåller. När du skapar planer, se till att ange din kvoter, definiera dina basen planer och överväga att använda valfria tillägg planer.

### <a name="quotas"></a>Kvoter

För att hjälpa dig att hantera din kapacitet, Välj eller skapa en kvot för varje tjänst i en plan. Kvoter definiera de övre gränserna som en användare-prenumeration kan etablera eller använda. En kvot kan exempelvis tillåta en användare att skapa upp till fem virtuella datorer. Kvoter kan begränsa en mängd resurser, t.ex. virtuella datorer, RAM-minne och processor gränser.

Kvoter kan konfigureras per region. En plan som innehåller beräknings-tjänster från Region A kan till exempel ha en kvot på två virtuella datorer, 4 GB RAM-minne och 10 CPU-kärnor. I Azure-stacken Development Kit endast en region (med namnet *lokala*) är tillgänglig.

### <a name="base-plan"></a>Basplan

När du skapar ett erbjudande kan tjänstadministratören innehålla en Basplan. Dessa basen planer ingår som standard när en användare som prenumererar på det erbjudandet. När en användare prenumererar har åtkomst till alla providrar som anges i dessa basen planer (med motsvarande kvoter).

### <a name="add-on-plans"></a>Tillägget planer

Du kan även inkludera valfria tillägg planer i ett erbjudande. Tillägget planer ingår inte som standard i prenumerationen. Tillägget är ytterligare scheman (med kvoter) tillgängliga i ett erbjudande som en prenumerant kan lägga till sina prenumerationer. Exempelvis kan du erbjuda en grundläggande plan med begränsade resurser för en utvärderingsversion och en plan för tillägget med mer omfattande resurser för kunder som vill använda tjänsten.

## <a name="offers"></a>Erbjudande

Erbjudanden är grupper med en eller flera scheman som du skapar så att användarna kan prenumerera på dem. Erbjuder Alpha kan till exempel innehålla planera en som innehåller en uppsättning tjänster för beräkning och planera B som innehåller en uppsättning tjänster för lagring och nätverk. 

När du [skapa ett erbjudande](azure-stack-create-offer.md), du måste inkludera minst en Basplan, men du kan också skapa tillägg planer som användare kan lägga till prenumerationen.


## <a name="subscriptions"></a>Prenumerationer

En prenumeration är hur användare kommer åt dina erbjudanden. Om du är operatör Azure Stack hos en Internetleverantör köpa användarna (klienter) dina tjänster genom att prenumerera på dina erbjudanden. Om du är en Azure-stacken operator i en organisation kan kan användarna (anställda) prenumerera på de tjänster du erbjuder utan att betala. Varje kombination av en användare med ett erbjudande är en unik prenumeration. Det innebär en användare kan ha flera erbjudanden prenumerationer, men varje prenumeration gäller endast ett erbjudande. Planer, erbjudanden och kvoter gäller endast för varje unik prenumeration – kan inte delas mellan prenumerationer. Varje resurs som en användare skapar associeras med en prenumeration.


### <a name="default-provider-subscription"></a>Standard-providern prenumeration

Standard-providern prenumerationen skapas automatiskt när du distribuerar Azure Stack Development Kit. Den här prenumerationen kan användas för att hantera Azure-stacken, distribuera ytterligare resursproviders och skapa planer och erbjudanden för användare. För säkerhets- och licensiering orsaker ska det inte användas att köra kundens arbetsbelastningar och program. 

## <a name="next-steps"></a>Nästa steg

[Skapa en plan](azure-stack-create-plan.md)
