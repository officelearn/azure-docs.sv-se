---
title: "Avancerade Azure stacken utvärderingsaktiviteter | Microsoft Docs"
description: "Den här artikeln beskrivs avancerade uppgifter vid utvärdering Azure stacken."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: c4bf76aa07ec5025d9e53b5518929199ace27e18
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Avancerade Azure Stack Development Kit utvärderingsaktiviteter
När du har förvärvat om du är bekant med grundläggande Azure Stack Development Kit (ASDK) tjänstens funktioner och kapacitet fördjupa du dig att förstå ytterligare Azure-stacken genom att testa ut mer avancerade scenarier. Dessa avancerade uppgifter vid utvärdering dokumenteras helt i Azure-stacken Operator-dokumentationen.

> [!NOTE]
> Många operatorn uppgifter finns stöd för både ASDK och produktion, med flera noder Azure Stack distributioner stöds inte alla Användningsscenarier för ASDK distributioner. Se [ASDK och flera noder Azure Stack skillnader](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) för mer information.

## <a name="delegate-offers-in-azure-stack"></a>Delegera erbjudanden i Azure Stack
Som operatorn Azure Stack vill du ofta placera andra personer som ansvarar för att skapa erbjudanden och logga in användare. Om du är en tjänstprovider kan kanske du vill återförsäljare att registrera kunder och hantera dem å dina vägnar. Eller om du är en del av en central IT-grupp i ett företag, dotterbolag registrera användare igen.

[Delegera erbjudanden i Azure-stacken](.\.\azure-stack-delegated-provider.md) hjälper dig med dessa uppgifter genom att göra det möjligt att nå och hantera fler användare än kan du direkt. 

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Tillgängliggöra SQL-databaser till Azure Stack-användare
Som operatör Azure Stack kan du skapa erbjudanden som användarna kan (klienter) skapa SQL-databaser som de kan använda med sina moln-ursprungliga appar, webbplatser och arbetsbelastningar. Genom att tillhandahålla dessa anpassade på begäran, molnbaserade databaser till dina användare kan spara du dem tid och resurser. 

Använda SQL Server resource provider-kort till [tillgängliggöra SQL-databaser till Azure-stacken användarna](.\.\azure-stack-tutorial-sql-server.md) som en tjänst i Azure-stacken. När du har installerat resursprovidern ansluter du den till en eller flera instanser av SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Göra webb- och API apps tillgängliga för användarna i Azure-stacken
Som operatör Azure Stack kan du skapa erbjudanden som användarna kan (klienter) skapa Azure Functions- och webb- och API-program. Genom att ge tillgång till apparna på begäran, molnbaserad till användarna, kan du spara dem tid och resurser.

Distribuera App Service-resursprovidern till [tillgängliggöra webb- och API apps till Azure Stack-användare](.\.\azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>Nästa steg
[Mer information om att erbjuda tjänster med Azure-stacken integrerat system](.\.\azure-stack-offer-services-overview.md)