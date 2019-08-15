---
title: Hantera App Service plan – Azure | Microsoft Docs
description: Lär dig hur du utför olika uppgifter för att hantera en App Service plan.
keywords: App Service, Azure App Service, skala, App Service-plan, ändra, skapa, hantera, hantering
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
ms.date: 10/31/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c749f090cbb0aeb036a53a01e6cd516617c3186d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68953882"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Hantera ett App Service plan i Azure

En [Azure App Service-plan](overview-hosting-plans.md) tillhandahåller de resurser som en app service app måste köra. Den här guiden visar hur du hanterar en App Service plan.

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

> [!TIP]
> Om du har en App Service-miljön kan du läsa [skapa ett App Service plan i en app service-miljön](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Du kan skapa en tom App Service plan, eller så kan du skapa en plan som en del av att skapa en app.

1. I [Azure Portal](https://portal.azure.com)väljer du **ny** > **webb + mobil**och väljer sedan webbapp eller någon annan typ av App Service app.

2. Välj en befintlig App Service plan eller skapa en plan för den nya appen.

   ![Skapa en app i Azure Portal.][createWebApp]

   Så här skapar du en plan:

   a. Välj **[+] Skapa ny**.

      ![Skapa en App Service plan.][createASP] 

   b. För **App Service plan**anger du namnet på planen.

   c. För **plats**väljer du en lämplig plats.

   d. Välj en lämplig pris nivå för tjänsten för **pris nivå**. Välj **Visa alla** för att visa fler pris alternativ, till exempel **kostnads fri** och **delad**. När du har valt pris nivån klickar du på knappen **Välj** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Flytta en app till en annan App Service plan

Du kan flytta en app till en annan App Service plan, så länge käll planen och mål planen finns i _samma resurs grupp och geografiska region_.

> [!NOTE]
> Azure distribuerar varje ny App Service plan till en distributions enhet som internt kallas för ett webb utrymme. Varje region kan ha många webb utrymmen, men appen kan bara flyttas mellan planer som skapas i samma webb utrymme. Ett App Service-miljön är ett isolerat webb utrymme, så att appar kan flyttas mellan planer i samma App Service-miljön, men inte mellan planer i olika App Service miljöer.
>
> Du kan inte ange det webb utrymme du vill ha när du skapar en plan, men det är möjligt att se till att en plan skapas i samma webb utrymme som ett befintligt abonnemang. I korthet har alla planer som skapats med samma resurs grupp och regions kombination distribuerats till samma webb utrymme. Om du till exempel har skapat en plan i resurs grupp A och region B, så distribueras alla planer som du sedan skapar i resurs grupp A och region B till samma webb utrymme. Observera att det inte går att flytta webb utrymmen efter att de har skapats, så du kan inte flytta en plan till "samma webb utrymme" som en annan plan genom att flytta den till en annan resurs grupp.
> 

1. I [Azure Portal](https://portal.azure.com)bläddrar du till den app som du vill flytta.

1. Gå till avsnittet **App Service plan** på menyn.

1. Välj **ändra App Service plan** för att öppna **App Service plan** väljaren.

   ![App Service plan väljare.][change] 

1. I **App Service plan** väljar väljer du en befintlig plan att flytta appen till.   

Sidan **välj App Service plan** visar bara planer som finns i samma resurs grupp och geografiska region som den aktuella appens App Service plan.

Varje plan har sin egen pris nivå. Om du till exempel flyttar en webbplats från en **kostnads fri** nivå till en **standard** -nivå kan alla appar tilldelas till den för att använda **standard** nivåns funktioner och resurser. Men om du flyttar en app från en plan med högre nivå till en plan med lägre nivåer innebär det att du inte längre har åtkomst till vissa funktioner. Om din app använder en funktion som inte är tillgänglig i mål planen får du ett fel meddelande som visar vilken funktion som inte är tillgänglig. 

Om till exempel en av dina appar använder SSL-certifikat kan du se det här fel meddelandet:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

I det här fallet måste du, innan du kan flytta appen till mål planen, antingen:
- Skala upp pris nivån för mål planen till **Basic** eller högre.
- Ta bort alla SSL-anslutningar till din app.

## <a name="move-an-app-to-a-different-region"></a>Flytta en app till en annan region

Den region där appen körs är den region i App Service plan den finns i. Du kan dock inte ändra en App Service plan region. Om du vill köra din app i en annan region är det ett alternativ som är kloning av appar. Kloningen gör en kopia av din app i en ny eller befintlig App Service plan i vilken region som helst.

Du hittar **klonings app** i avsnittet **utvecklingsverktyg** i menyn.

> [!IMPORTANT]
> Kloning har vissa begränsningar. Du kan läsa om dem i [Azure App Service kloning av appar](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skala en App Service plan

Om du vill skala upp en App Service plans pris nivå går du till [skala upp en app i Azure](web-sites-scale.md).

Information om hur du skalar ut antalet instanser för en app finns i [skala antalet instanser manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Ta bort ett App Service plan

För att undvika oväntade kostnader, tar App Service även bort planen som standard när du tar bort den senaste appen i en App Service plan. Om du väljer att behålla planen i stället bör du ändra planen till den **kostnads fria** nivån så att du inte debiteras.

> [!IMPORTANT]
> App Service planer som inte har några program kopplade till sig debiteras ändå, eftersom de fortsätter att reservera de konfigurerade VM-instanserna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skala upp en app i Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
