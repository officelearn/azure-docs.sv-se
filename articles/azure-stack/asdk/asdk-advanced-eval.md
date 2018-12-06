---
title: Avancerade Azure Stack-utvärderingsaktiviteter | Microsoft Docs
description: Den här artikeln beskrivs avancerade åtgärder för utvärdering av Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 185c4685de0c889c3b6e7b173445546ed5b7d921
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52955420"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Avancerade åtgärder för utvärdering av Azure Stack Development Kit
När du har fått bekant med grundläggande Azure Stack Development Kit (ASDK) service-funktionerna och egenskaperna kan fördjupa du din förståelse av Azure Stack ytterligare genom att testa mer avancerade scenarier. De här mer avancerade uppgifter vid utvärdering dokumenteras fullständigt i Azure Stack-operatör-dokumentationen.

> [!NOTE]
> Många operatorn uppgifter finns stöd för både ASDK och produktion, med flera noder Azure Stack-distributioner, stöds inte alla Användningsscenarier för ASDK distributioner. Se [ASDK och flera noder Azure Stack skillnader](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) för mer information.

## <a name="delegate-offers-in-azure-stack"></a>Delegera erbjudanden i Azure Stack
Som Azure Stack-operatör vill du ofta att andra personer som ansvarar för att skapa erbjudanden och logga in användare. Till exempel om du är en tjänstleverantör kan du återförsäljare att registrera kunder och hantera dem åt dig. Eller om du är en del av en central IT-grupp i ett företag kan du dotterbolag att registrera användare utan att göra något.

[Delegera erbjudanden i Azure Stack](../azure-stack-delegated-provider.md) hjälper dig med dessa uppgifter genom att göra det möjligt att nå och hantera fler användare än vad du kan direkt.

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Tillgängliggöra SQL-databaser till Azure Stack-användare
Som en Azure Stack-operatör kan du skapa erbjudanden som användarna kan (klienter) skapa SQL-databaser som de kan använda med deras molnbaserade appar, webbplatser och arbetsbelastningar. Genom att tillhandahålla dessa anpassade på begäran, molnbaserade databaser till dina användare, kan du spara dem tid och resurser.

Använda SQL Server-resursprovideradaptern till [tillgängliggöra SQL-databaser till Azure Stack-användare](../azure-stack-tutorial-sql-server.md) som en tjänst i Azure Stack. När du har installerat resursprovidern ansluter du den till en eller flera instanser av SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Gör webb- och API-appar tillgängliga för din Azure Stack-användare
Som en Azure Stack-operatör kan du skapa erbjudanden som användarna kan skapa Azure Functions- och webb-och API för (klienter). Genom att ge åtkomst till dessa på begäran, molnbaserade appar till dina användare, kan du spara dem tid och resurser.

Distribuera App Service-resursprovider till [gör webb- och API-appar tillgängliga för din Azure Stack-användare](../azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>Nästa steg

[Mer information om erbjuder tjänster med integrerade Azure Stack-system](../azure-stack-offer-services-overview.md)
