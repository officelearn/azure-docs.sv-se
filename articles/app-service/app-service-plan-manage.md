---
title: Hantera apptjänstplan
description: Lär dig hur du utför olika uppgifter för att hantera en App Service-plan, till exempel skapa, flytta, skala och ta bort.
keywords: apptjänst, azure app-tjänst, skala, apptjänstplan, ändra, skapa, hantera, hantera
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 68cabbd00dd0b738590109cc39d8df82f5b7362d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280760"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Hantera en apptjänstplan i Azure

En [Azure App Service-plan](overview-hosting-plans.md) innehåller de resurser som en App Service-app behöver för att köra. Den här guiden visar hur du hanterar en App Service-plan.

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

> [!TIP]
> Om du har en apptjänstmiljö läser du [Skapa en apptjänstplan i en apptjänstmiljö](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Du kan skapa en tom App Service-plan eller skapa en plan som en del av appskapande.

1. I [Azure-portalen](https://portal.azure.com)väljer du **Skapa en resurs**.

   ![Skapa en resurs i Azure-portalen.][createResource] 

1. Välj **Ny** > **webbapp** eller en annan typ av apptjänstapp.

   ![Skapa en app i Azure-portalen.][createWebApp] 

2. Konfigurera avsnittet **Instansinformation** innan appserviceplanen konfigureras. Inställningar som **Publicera** och **operativsystem** kan ändra tillgängliga prisnivåer för apptjänstplanen. **Regionen** bestämmer var apptjänstplanen skapas. 
   
3. I avsnittet **App Service Plan** väljer du en befintlig plan eller skapar en plan genom att välja Skapa **ny**.

   ![Skapa en apptjänstplan.][createASP] 

4. När du skapar en plan kan du välja prisnivån för den nya planen. I **Sku och storlek**väljer du Ändra **storlek** om du vill ändra prisnivån. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Flytta en app till en annan apptjänstplan

Du kan flytta en app till en annan App Service-plan så länge källplanen och målplanen finns i _samma resursgrupp och geografiska område_.

> [!NOTE]
> Azure distribuerar varje ny App Service-plan till en distributionsenhet, internt kallat ett webbutrymme. Varje region kan ha många webbutrymmen, men appen kan bara flyttas mellan planer som skapas i samma webbutrymme. En App Service-miljö är ett isolerat webbutrymme, så att appar kan flyttas mellan planer i samma App Service-miljö, men inte mellan planer i olika App Service-miljöer.
>
> Du kan inte ange det webbområde du vill använda när du skapar en plan, men det är möjligt att se till att en plan skapas i samma webbområde som en befintlig plan. Kort sagt, alla planer som skapas med samma resursgrupp och regionkombination distribueras till samma webbområde. Om du till exempel har skapat en plan i resursgrupp A och region B distribueras alla planer som du sedan skapar i resursgrupp A och region B till samma webbområde. Observera att planer inte kan flytta webbutrymmen när de har skapats, så du kan inte flytta en plan till "samma webbområde" som en annan plan genom att flytta den till en annan resursgrupp.
> 

1. Sök efter och välj **App-tjänster** i [Azure-portalen](https://portal.azure.com)och välj den app som du vill flytta.

2. Välj **Ändra apptjänstplan på**den vänstra menyn .

3. I listrutan **App Service-plan** väljer du en befintlig plan som appen ska flyttas till. Listrutan visar bara planer som finns i samma resursgrupp och geografiska region som den aktuella App Service-planen. Om det inte finns någon sådan plan kan du skapa en plan som standard. Du kan också skapa en ny plan manuellt genom att välja **Skapa ny**.

4. Om du skapar en plan kan du välja prisnivån för det nya abonnemanget. I **Prisnivå**väljer du den befintliga nivån för att ändra den. 
   
   > [!IMPORTANT]
   > Om du flyttar en app från en plan med högre nivå till en plan med lägre nivå, till exempel från **D1** till **F1,** kan appen förlora vissa funktioner i målplanen. Om din app till exempel använder SSL-certifikat kan det här felmeddelandet visas:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. När du är klar väljer du **OK**.
   
   ![App Service-planväljare.][change] 

## <a name="move-an-app-to-a-different-region"></a>Flytta en app till en annan region

Den region där appen körs är den region i apptjänstplanen som den finns i. Du kan dock inte ändra en apptjänstplans region. Om du vill köra din app i en annan region är ett alternativ appkloning. Kloning gör en kopia av din app i en ny eller befintlig App Service-plan i alla regioner.

Du hittar **Clone App** i avsnittet **Utvecklingsverktyg** på menyn.

> [!IMPORTANT]
> Kloning har vissa begränsningar. Du kan läsa om dem i [kloning av Azure App Service App](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skala en apptjänstplan

Om du vill skala upp prisnivån för apptjänstplanen finns [i Skala upp en app i Azure](manage-scale-up.md).

Om du vill skala ut en apps instansantal läser du [Skala instansantal manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Ta bort en apptjänstplan

Om du vill undvika oväntade debiteringar tas även planen bort som standard när du tar bort den senaste appen i en App Service-plan. Om du väljer att behålla planen i stället bör du ändra planen till **den kostnadsfria** nivån så att du inte debiteras.

> [!IMPORTANT]
> App Service-planer som inte har några appar associerade med dem ådrar sig fortfarande avgifter eftersom de fortsätter att reservera de konfigurerade VM-instanserna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skala upp en app i Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
