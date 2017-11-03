---
title: "Översikt över App Service: Azure Stack | Microsoft Docs"
description: "Översikt över Apptjänst på Azure-stacken"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: 19b712d622276b6521317d79c68fc093dba547db
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="app-service-on-azure-stack-overview"></a>Översikt över App Service på Azure Stack

Azure Apptjänst Azure-stacken är en plattform som en-tjänst (PaaS) erbjuds med Microsoft Azure som är tillgängliga för Azure-stacken. Tjänsten kan kunderna - interna eller externa – skapa webb-, API- och Azure Functions program för alla plattformar och enheter. De kan integrera dina appar med lokala program och automatisera affärsprocesserna. Azure Stack molnoperatörer kan köra kunden appar på helt hanterade virtuella datorer (VM) med deras val av delade VM-resurser eller dedikerade virtuella datorer.

Azure Apptjänst innehåller funktioner för att automatisera affärsprocesser och hantera moln-API: er. Som en enda integrerad tjänst kan du sätta ihop olika komponenter – webbplatser, RESTful-API: er och affärsprocesser – i en enda lösning i Azure App Service.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Varför erbjuder Azure App Service på Azure-stacken?

Här följer några viktiga funktioner och funktioner i Apptjänst:
- **Flera språk och ramverk**: Apptjänst har förstklassigt stöd för ASP.NET, Node.js, Java, PHP och Python. Du kan också köra Windows PowerShell och andra skript eller körbara filer på Apptjänstens virtuella datorer.
- **DevOps-optimering**: Ställ in kontinuerlig integrering och distribution med GitHub, lokala Git eller BitBucket. Flytta upp uppdateringar via test- och mellanlagringsmiljöer. Hantera dina appar i App Service med hjälp av Azure PowerShell eller plattformsoberoende kommandoradsgränssnittet (CLI).
- **Visual Studio-integrationen**: dedikerade verktyg i Visual Studio effektiviserar arbetet med att skapa och distribuera program.

## <a name="app-types-in-app-service"></a>Apptyper i Apptjänst

Apptjänst erbjuder flera apptyper som är avsedd att vara värd för en viss arbetsbelastning:

- [Web Apps](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-overview) som värd för webbplatser och webbprogram.
- [API Apps](https://docs.microsoft.com/en-us/azure/app-service-api/app-service-api-apps-why-best-platform) som värd för RESTful-API: er.
- Azure Functions som värd för händelsen som drivs serverlösa arbetsbelastningar.

Word-appen här refererar till de värdresurser som är dedikerade för att köra en arbetsbelastning. Med ”webbapp” som exempel är du antagligen van vid att tänka på en webbapp som både beräkningsresurser och programkod som tillsammans ger funktioner till en webbläsare. Men i App Service är en webbapp de beräkningsresurser som Azure Stack tillhandahåller för hantering av programkoden.

Programmet kan bestå av flera Apptjänst-appar av olika slag. Till exempel om ditt program består av en frontwebb och en RESTful-API tillbaka avslutas, kan du:
- Distribuera båda (klientdel och API) till en enda webbapp
- Distribuera frontend-koden till en webbapp och backend-koden till en API-app.

   ![](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>Vad är en App Service-plan?

Resursprovidern Apptjänst använder samma kod som använder Azure App Service. Därför är några vanliga begrepp värda som beskriver. I App Service kallas prisnivå behållaren för program App Service-plan. Representerar en uppsättning dedikerade virtuella datorer som används för att lagra dina appar. Du kan ha flera programtjänstplaner inom en viss prenumeration.

I Azure finns delad och dedikerad arbetare. En delad arbetsrutinen värd med hög densitet multitenant app och det finns bara en uppsättning delade arbetare. Dedicerade servrar används av en enda klient och finns i tre storlekar: liten, medel och stor. Lokala kunders behov kan inte alltid beskrivas med hjälp av dessa villkor. I App Service på Azure-stacken kan resource provider administratörer definiera worker-nivåer som de vill göra tillgängliga. Baserat på dina unika behov som värd, kan du definiera flera uppsättningar av delade Worker eller olika uppsättningar av dedikerade Worker. Med hjälp av dessa worker nivådefinitioner, kan de sedan definiera egna priser SKU: er.

## <a name="portal-features"></a>Portalen finns

Apptjänst Azure stacken använder samma användargränssnitt som används för Azure App Service, som stämmer med serverdelen. Vissa funktioner har inaktiverats och är inte fungerar i Azure-stacken. Azure-specifika förväntningar eller tjänster som kräver att dessa funktioner är inte ännu i Azure-stacken.

## <a name="next-steps"></a>Nästa steg


- [Innan du börjar med App Service på Azure-stacken](azure-stack-app-service-before-you-get-started.md)
- [Installera App-tjänstresursprovider](azure-stack-app-service-deploy.md)

Du kan också prova att använda andra [plattform som en tjänst (PaaS) services](azure-stack-tools-paas-services.md), till exempel den [SQL Server-resursprovidern](azure-stack-sql-resource-provider-deploy.md) och [MySQL-resursprovidern](azure-stack-mysql-resource-provider-deploy.md).
