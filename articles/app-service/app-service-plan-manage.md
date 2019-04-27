---
title: Hantera App Service-plan – Azure | Microsoft Docs
description: Lär dig hur du utför olika åtgärder för att hantera en App Service plan.
keywords: App service, azure app service, skala, app service-plan, ändra, skapa, hantera, management
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 936abe80a66c1dbe99e7d8a255fe8995a2df0803
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852303"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Hantera en App Service-plan i Azure

En [Azure App Service-plan](overview-hosting-plans.md) tillhandahåller de resurser som en App Service-app ska köras. Den här guiden visar hur du hanterar en App Service plan.

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

> [!TIP]
> Om du har en App Service Environment, se [skapa en App Service-plan i en App Service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Du kan skapa en tom App Service-plan eller du kan skapa en plan som en del av skapa appar.

1. I den [Azure-portalen](https://portal.azure.com)väljer **New** > **webb + mobilt**, och välj sedan **Webbapp** eller en annan typ av App Service-app.

2. Välj en befintlig App Service-plan eller skapa en plan för den nya appen.

   ![Skapa en app i Azure-portalen.][createWebApp]

   Skapa en plan:

   a. Välj **[+] Skapa en ny**.

      ![Skapa en App Service-plan.][createASP] 

   b. För **App Service-plan**, anger du namnet på planen.

   c. För **plats**, Välj en lämplig plats.

   d. För **prisnivå**, Välj en prisnivå för tjänsten. Välj **visa alla** att visa fler prisalternativ, till exempel **kostnadsfri** och **delade**. När du har valt prisnivån, klickar du på den **Välj** knappen.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Flytta en app till en annan App Service-plan

Du kan flytta en app till en annan App Service-planen så länge planen källa och målplanen finns i den _samma resursgrupp och geografisk region_.

> [!NOTE]
> Azure har distribuerat varje ny App Service-plan i en distributionsenhet internt kallas ett webbutrymme. Varje region kan ha många webbutrymmen, men din app kan bara flytta mellan planer som skapas i samma webbutrymmet. En App Service Environment är en isolerad webbutrymmet så att appar kan flyttas mellan prenumerationer i samma App Service Environment, men inte mellan planer i olika App Service-miljöer.
>
> Du kan inte ange webbutrymmet som du vill när du skapar en plan, men det är möjligt att se till att en plan har skapats i samma webbutrymmet som en befintlig plan. Enkelt uttryckt alla planer som skapas med samma resursgrupp och region kombination distribueras i samma webbutrymmet. Till exempel om du har skapat en plan i resursgruppen A och B-region, distribueras sedan vilken plan som du skapar i A-resursgrupp och region B till samma webbutrymmet. Observera inte att planer kan flytta webspaces när de skapas, så du inte kan flytta en plan i ”samma webbutrymmet” som en annan plan för genom att flytta den till en annan resursgrupp.
> 

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den app som du vill flytta.

1. På menyn letar den **Apptjänstplan** avsnittet.

1. Välj **ändra App Service-plan** att öppna den **App Service-plan** väljare.

   ![Väljare för App Service-plan.][change] 

1. I den **App Service-plan** Väljaren, väljer en befintlig planerar att flytta den här appen till.   

Den **Välj App Service-plan** visar endast de planer som finns i samma resursgrupp och geografiska region som den aktuella appen App Service-plan.

Varje prenumeration har sin egen prisnivå. Till exempel flytta en plats från en **kostnadsfri** nivå till en **Standard** nivå kan alla appar som har tilldelats till den använda de funktioner och resurser av den **Standard** nivå. Flytta en app från en högre nivåer plan till en lägre nivåer plan innebär dock att du inte längre har åtkomst till vissa funktioner. Om din app använder en funktion som inte är tillgänglig i målplanen, får du ett felmeddelande som visar vilken funktion som används som inte är tillgänglig. 

Om en av dina appar använder SSL-certifikat, kan du se felmeddelandet:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

I det här fallet innan du kan flytta appen till målplanen, måste du antingen:
- Skala upp prisnivån för målplanen till **grundläggande** eller högre.
- Ta bort alla SSL-anslutningar till din app.

## <a name="move-an-app-to-a-different-region"></a>Flytta en app till en annan region

Den region där din app körs är regionen som den är i App Service-planen. Du kan inte ändra en App Service-plan region. Om du vill köra din app i en annan region, ett alternativ är app kloning. Klona gör en kopia av din app i en ny eller befintlig App Service-plan i valfri region.

Du kan hitta **Klonapp** i den **utvecklingsverktyg** på menyn.

> [!IMPORTANT]
> Kloning har vissa begränsningar. Du kan läsa om dem i [Azure App Service-App kloning](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skala en App Service-plan

För att skala upp en App Service plan prisnivå, se [skala upp en app i Azure](web-sites-scale.md).

Om du vill öka instansantalet för en app, se [skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Ta bort en App Service-plan

Du kan undvika oväntade kostnader när du tar bort den sista appen i en App Service-plan, bort App Service även planen som standard. Om du väljer att behålla planen i stället bör du ändra planerna på att **kostnadsfri** nivå så att du inte debiteras.

> [!IMPORTANT]
> App Service-planer som har inga appar som är associerade med dem fortfarande avgifter eftersom de fortsätter att reservera de konfigurerade VM-instanserna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skala upp en app i Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
