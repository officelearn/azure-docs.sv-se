---
title: Erbjuder tjänster i Azure Stack | Microsoft Docs
description: Du kan erbjuda tjänster till dina användare som ansvarig för molnet.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 754a0002aca52462910abe50825cff0254a7068f
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244162"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Översikt över erbjudna tjänster i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

[Microsoft Azure Stack](azure-stack-poc.md) är en hybridmolnplattform som du kan leverera tjänster från ditt datacenter. En tjänstleverantör, kan du erbjuda tjänster till dina klienter. Inom ett företag eller en myndighet kan du erbjuda dina anställda lokala tjänster. 

Du kan erbjuda [infrastruktur som en tjänst](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS)-tjänster som kan användas att bygga en infrastruktur med på begäran databehandling etableras och hanteras från Azure Stack-användarportalen.

Du kan också distribuera [plattform som en tjänst](https://azure.microsoft.com/overview/what-is-paas/) (PaaS)-tjänster för Azure Stack från Microsoft och andra 3 leverantörer. De tjänster som du kan leverera inkludera, men inte begränsat till:

- [Lägga till en App Service-resursprovider i Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-overview)

- [Lägga till en SQL Server-resursprovider i Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy)

- [Lägga till en MySQL-Server-resursprovider i Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy)


Du kan även kombinera tjänster för att integrera och skapa komplicerade lösningar för olika användare.

För att leverera tjänsterna till dina användare, måste du skapa [planer, erbjudanden och kvoter](azure-stack-plan-offer-quota-overview.md). Användarna kan sedan prenumerera på dina erbjudanden för att använda tjänsterna.

## <a name="plan-your-service-offers"></a>Planera dina service-erbjudanden

När du planerar dina erbjudanden, Tänk på följande saker övervägas:

**Testversioner**: Du kan använda testversioner för att locka nya användare, som sedan kan uppgradera till ytterligare tjänster. Skapa en utvärderingsversionen kan skapa ett litet [Basplan](azure-stack-plan-offer-quota-overview.md#base-plan) med en valfri större tilläggsplanen.

**Kapacitetsplanering**: Du kan bli orolig över användare som hämta stora mängder resurser och belastning system för alla användare. För att prestanda, kan du [konfigurera dina planer med kvoter](azure-stack-plan-offer-quota-overview.md#plans) tak Usage.

**Delegerade providrar**: Du kan bevilja andra erbjuder möjligheten att skapa i din miljö. Om du är en tjänstleverantör, du kan exempelvis [delegera](azure-stack-delegated-provider.md) möjligheten att din återförsäljare. Eller, om du är en organisation, kan du delegera till andra avdelningar/dotterbolag.

## <a name="next-steps"></a>Nästa steg

[Skapa ett erbjudande i Azure Stack](azure-stack-create-offer.md)
