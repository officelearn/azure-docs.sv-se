---
title: 'Översikt över App Service: Azure Stack | Microsoft Docs'
description: Översikt över App Service i Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: cf2d65e7e2927aee99e533ea0bca0818f3ab98f6
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079165"
---
# <a name="app-service-on-azure-stack-overview"></a>Översikt över App Service på Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Azure App Service i Azure Stack är en platform-as-a-service (PaaS) erbjuds av Microsoft Azure som är tillgängliga för Azure Stack. Tjänsten kan kunderna - interna eller externa – skapa webb-API och Azure Functions-program för valfri plattform eller enhet. De kan integrera dina appar med dina lokala program och automatisera sina affärsprocesser. Azure Stack-moln-operatörer kan köra kundappar på helt hanterade virtuella datorer (VM), med deras val av delade VM-resurser eller dedikerade virtuella datorer.

Azure App Service kan du automatisera affärsprocesser och värdbaserade moln API: er. En enda integrerad tjänst kan du kombinera olika komponenter – webbplatser, RESTful API: er och affärsprocesser – i en enda lösning i Azure App Service.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Varför erbjuder Azure App Service i Azure Stack?

Här följer några viktiga funktioner och funktioner i Apptjänst:

- **Flera språk och ramverk**: App Service har förstklassigt stöd för ASP.NET, Node.js, Java, PHP och Python. Du kan också köra Windows PowerShell och andra skript och körbara filer på Apptjänstens virtuella datorer.
- **DevOps-optimering**: Konfigurera kontinuerlig integrering och distribution med GitHub, lokal Git eller BitBucket. Flytta upp uppdateringar via testning och mellanlagringsmiljöer. Hantera dina appar i App Service med hjälp av Azure PowerShell eller plattformsoberoende kommandoradsgränssnittet (CLI).
- **Visual Studio-integration**: dedikerade verktyg i Visual Studio effektiviserar arbetet med att skapa och distribuera program.

## <a name="app-types-in-app-service"></a>Apptyper i Apptjänst

App Service erbjuder flera typer av appar, som är avsedd att vara värd för en specifik arbetsbelastning:

- [Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) som värd för webbplatser och webbprogram.
- [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) som värd för RESTful API: er.
- Azure fungerar som värd för händelsedrivna, serverlösa arbetsbelastningar.

Word-appen här refererar till de värdresurser som är dedikerade för att köra en arbetsbelastning. Med ”webbapp” som exempel är du antagligen van vid att tänka på en webbapp som både beräkningsresurser och programkod som tillsammans ger funktioner till en webbläsare. Men i App Service är en webbapp de beräkningsresurser som Azure Stack tillhandahåller för hantering av programkoden.

Ditt program kan bestå av flera Apptjänst-appar av olika typer. Till exempel om ditt program består av en frontwebb och en RESTful-API tillbaka sluta, kan du:

- Distribuera båda (klientdel och API) till en enda webbapp
- Distribuera frontend-koden till en webbapp och backend-koden till en API-app.

   ![Översikt över App Service med övervakning av data](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>Vad är en App Service-plan?

App Service-resursprovider använder samma kod som används av Azure App Service. Därför är några vanliga begrepp värda som beskriver. I App Service heter prissättning behållaren för program som App Service-planen. Representerar en uppsättning dedikerade virtuella datorer som används för att lagra dina appar. Du kan ha flera App Service-planer inom en viss prenumeration.

I Azure finns det delade och dedikerade arbetare. En delad worker stöder Högdensitet app för flera klienter som är värd och det finns endast en uppsättning delade arbetare. Dedikerade servrar används av endast en klient och kommer i tre storlekar: liten, medel och stor. Lokala kunders behov kan inte alltid beskrivas med hjälp av dessa villkor. I App Service i Azure Stack kan resource provider-administratörer definiera arbetarnivåer som ska göras tillgängliga. Utifrån dina behov som värd kan definiera du flera uppsättningar med delade arbetare eller olika uppsättningar av dedikerade arbetare. Med hjälp av dessa definitioner av worker-nivå, kan de du definiera egna priser för SKU: er.

## <a name="portal-features"></a>Portalfunktionerna

App Service i Azure Stack använder samma användargränssnitt som används av Azure App Service, samma sak gäller med backend-servern. Vissa funktioner är inaktiverade och inte fungerar i Azure Stack. Azure-specifika förväntningar eller tjänster som kräver dessa funktioner är inte tillgängliga i Azure Stack.

## <a name="next-steps"></a>Nästa steg

- [Innan du sätter igång med App Service i Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Installera App Service-resursprovider](azure-stack-app-service-deploy.md)

Du kan också prova att använda andra [plattform som en tjänst (PaaS)-tjänster](azure-stack-tools-paas-services.md), till exempel den [SQL Server-resursleverantör](azure-stack-sql-resource-provider-deploy.md) och [MySQL-resursprovider](azure-stack-mysql-resource-provider-deploy.md).
