---
title: "Hantera en apptjänstplan i Azure | Microsoft Docs"
description: "Lär dig hur till App Service planer utföra olika uppgifter för att hantera en App Service-plan."
keywords: "App service, azure app service, skala, apptjänstplan, ändra, skapa, hantera, management"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: c1b832895476e2f64bbae638db76f89890e5c804
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="manage-an-app-service-plan-in-azure"></a>Hantera en apptjänstplan i Azure

En [programtjänstplanen](azure-web-sites-web-hosting-plans-in-depth-overview.md) innehåller resurser som en Apptjänst-app måste köras. Den här instruktioner visar hur du hanterar en programtjänstplan. 

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

> [!TIP]
> Om du har en Apptjänst-miljö, se [skapa en apptjänstplan i en Apptjänst-miljö](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Du kan skapa en tom App Service-plan eller som en del av skapa en app.

I den [Azure-portalen](https://portal.azure.com), klickar du på **ny** > **webb + mobilt**, och välj sedan **Web App** eller en annan typ för Apptjänst-app.

![Skapa en app i Azure-portalen.][createWebApp]

Du kan sedan välja en befintlig programtjänstplan eller skapar en plan för den nya appen.

 ![Skapa en apptjänstplan.][createASP]

Klicka för att skapa en apptjänstplan **[+] Skapa nya**, typen av **programtjänstplanen** namn och välj sedan ett lämpligt **plats**. Klicka på **prisnivå**, och välj sedan ett lämpligt prisnivån för tjänsten. Välj **visa alla** att visa priser fler alternativ, exempelvis **lediga** och **delade**. 

När du har valt prisnivå, klickar du på den **Välj** knappen.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Flytta en app till en annan programtjänstplan

Du kan flytta en app till en annan App Service-plan som planen källa och mål-planen finns i den _samma resursgrupp och geografiska region_.

Om du vill flytta en app till en annan plan, navigera till appen som du vill flytta i den [Azure-portalen](https://portal.azure.com).

I den **menyn**, leta efter den **Apptjänstplan** avsnitt.

Välj **ändra programtjänstplanen** att starta processen.

**Ändra programtjänstplanen** öppnar den **programtjänstplanen** väljare. Välj en befintlig plan för att flytta den här appen till. 

> [!IMPORTANT] 
> Den **Välj apptjänstplan** sidan filtreras efter följande kriterier: 
> - Det finns i samma resursgrupp 
> - Det finns i samma geografiska region 
> - Det finns i samma webbutrymmet  
> 
> En _webbutrymmet_ är en logisk konstruktion i App Service som definierar en gruppering av serverresurser. En geografisk region (till exempel USA, västra) innehåller många webspaces för att allokera kunder som använder App Service. Apptjänst resurser är för närvarande inte kan flyttas mellan webspaces. 
> 

![App Service-plan väljare.][change]

Varje plan har sin egen prisnivån. Till exempel flytta en plats från en **lediga** tjänstnivån till en **Standard** tjänstnivån, gör att alla appar som har tilldelats till den använda de funktioner och resurser i den **Standard** nivå. Flytta en app från en högre nivåindelade plan till en lägre nivåindelade plan innebär dock att du inte längre har åtkomst till vissa funktioner. Om din app använder en funktion som inte är tillgänglig i planen mål, får du ett felmeddelande som visar vilken funktion används som inte är tillgänglig. Till exempel om en av dina appar använder SSL-certifikat kan visas felmeddelandet: `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`i detta fall kan du behöva skalas upp prisnivån för planen för målet att **grundläggande** eller högre, eller så måste du ta bort alla SSL-anslutningar till din app, innan du kan flytta appen till mål-plan.

## <a name="move-an-app-to-a-different-region"></a>Flytta en app till en annan region

Den region där din app körs är App Service-plan som den är i regionen. Du kan inte ändra en App Service-plan region. Om du vill köra appen i en annan region är ett alternativ app kloning. Kloningen gör en kopia av din app i en ny eller befintlig programtjänstplan i en region.

Du kan hitta **klona App** i den **utvecklingsverktyg** på menyn.

> [!IMPORTANT]
> Kloningen har vissa begränsningar som du kan läsa om vid [Azure Apptjänst-App kloning](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skala en programtjänstplan

För att skala upp ah App Service-plan prisnivån, se [skala upp en app i Azure](web-sites-scale.md).

Om du vill skala ut en app-instanser finns [skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Ta bort en apptjänstplan

Om du vill undvika oväntade kostnader, när du tar bort den sista app i App Service-plan, bort Apptjänst även planen som standard. Om du välja att behålla planen i stället, bör du ändra planen för att **lediga** nivån så att du inte hämta debiteras.

> [!IMPORTANT]
> **Apptjänstplaner** som har inga appar som är kopplade till dem fortfarande avgifter eftersom de fortsätter att reservera konfigurerade VM-instanser.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skala upp en app i Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
