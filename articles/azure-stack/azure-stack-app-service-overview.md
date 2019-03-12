---
title: App Service i Azure Stack-översikt | Microsoft Docs
description: Översikt över App Service i Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: ba2a322cdbcf929bef586f9f35ec2dc394f7af53
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778526"
---
# <a name="app-service-on-azure-stack-overview"></a>Översikt över App Service på Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Azure App Service i Azure Stack är en platform-as-a-service (PaaS) erbjuds av Microsoft Azure som är tillgängliga för Azure Stack. Tjänsten gör det möjligt för dina interna eller externa kunder att skapa webbappar, API och Azure Functions-program för valfri plattform eller enhet. De kan integrera dina appar med lokala program och automatisera sina affärsprocesser. Azure Stack-molnoperatörer kan köra kundappar på helt hanterade virtuella datorer (VM), med valfria delade VM-resurser eller dedikerade virtuella datorer.

Azure App Service kan du automatisera affärsprocesser och vara värd för molnet API: er. Som en enda integrerad tjänst gör kan Azure App Service du kombinera olika komponenter, till exempel webbplatser, REST API: er och affärsprocesser, i en enda lösning.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Varför erbjuder Azure App Service i Azure Stack?

Här följer några viktiga funktioner och funktioner i Apptjänst:

- **Flera språk och ramverk**: App Service har förstklassigt stöd för ASP.NET, Node.js, Java, PHP och Python. Du kan också köra Windows PowerShell och andra skript och körbara filer på Apptjänstens virtuella datorer.
- **DevOps-optimering**: Konfigurera kontinuerlig integrering och distribution med GitHub, lokal Git eller BitBucket. Du kan flytta upp uppdateringar via testning och mellanlagringsmiljöer och hantera dina appar i App Service med hjälp av Azure PowerShell eller plattformsoberoende kommandoradsgränssnittet (CLI).
- **Visual Studio-integration**: Dedikerade verktyg i Visual Studio effektiviserar arbetet med att skapa och distribuera program.

## <a name="app-types-in-app-service"></a>Apptyper i Apptjänst

App Service erbjuder flera typer av appar, som är avsedd att vara värd för en specifik arbetsbelastning:

- [Web Apps](../app-service/overview.md) som värd för webbplatser och webbprogram.
- [API Apps](../app-service/overview.md) som värd för REST API: er.
- Azure fungerar som värd för händelsedrivna, serverlösa arbetsbelastningar.

Ordet *app* refererar till de värdresurser som är dedikerade för att köra en arbetsbelastning. Tar *webbapp* kan t.ex, är du antagligen van vid att tänker på en webbapp som både beräkningsresurser och program kod som tillsammans ger funktioner till en webbläsare. I App Service är en webbapp beräkningsresurs som Azure Stack tillhandahåller för hantering av programkoden.

Ditt program kan bestå av flera Apptjänst-appar av olika typer. Till exempel om ditt program består av en frontwebb och en REST-API tillbaka sluta, kan du:

- Distribuera båda (klientdel och API) till en enda webbapp
- Distribuera frontend-koden till en webbapp och backend-koden till en API-app.

   [![Översikt över App Service med övervakningsdata](media/azure-stack-app-service-overview/image01.png "översikt över App Service med övervakning av data")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>Vad är en App Service-plan?

App Service-resursprovider använder samma kod som använder Azure App Service och därmed delar några vanliga begrepp. I App Service, prissättning behållare för program kallas den *App Service-plan*. Representerar en uppsättning dedikerade virtuella datorer som används för att lagra dina appar. Du kan ha flera App Service-planer inom en viss prenumeration.

I Azure finns det delade och dedikerade arbetare. En delad worker stöder Högdensitet app för flera klienter som är värd och det finns endast en uppsättning delade arbetare. Dedikerade servrar används av endast en klient och kommer i tre storlekar: liten, medel och stor. Lokala kunders behov kan inte alltid beskrivas med hjälp av dessa villkor. I App Service i Azure Stack kan resource provider-administratörer definiera arbetarnivåer som ska göras tillgängliga. Utifrån dina behov som värd kan definiera du flera uppsättningar med delade arbetare eller olika uppsättningar av dedikerade arbetare. Med hjälp av dessa definitioner av worker-nivå, kan de du definiera egna priser för SKU: er.

## <a name="portal-features"></a>Portalfunktionerna

App Service i Azure Stack använder samma användargränssnitt som använder Azure App Service. Samma sak gäller med backend-servern. Men är vissa funktioner inaktiverade i Azure Stack. Azure-specifika förväntningar eller tjänster som kräver dessa funktioner är inte tillgängliga i Azure Stack.

## <a name="next-steps"></a>Nästa steg

- [Innan du sätter igång med App Service i Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Installera App Service-resursprovider](azure-stack-app-service-deploy.md)

Du kan också prova att använda andra [plattform som en tjänst (PaaS)-tjänster](azure-stack-tools-paas-services.md), till exempel den [SQL Server-resursleverantör](azure-stack-sql-resource-provider-deploy.md) och [MySQL-resursprovider](azure-stack-mysql-resource-provider-deploy.md).
