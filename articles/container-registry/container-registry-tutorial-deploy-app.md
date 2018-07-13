---
title: Självstudie om Azure Container Registry – Distribuera webbapp från Azure Container Registry
description: Distribuera en Linux-baserad webbapp med hjälp av en behållaravbildning från ett geo-replikerat Azure-behållarregister. Del två av en serie i tre delar.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6347c460ab88929152424d301445a219720b98e8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38582309"
---
# <a name="tutorial-deploy-web-app-from-azure-container-registry"></a>Självstudier: Distribuera webbapp från Azure Container Registry

Det här är del två i en serie självstudier i tre delar. I [del ett](container-registry-tutorial-prepare-registry.md) skapade vi ett privat geo-replikerat behållarregister, och en behållaravbildning skapades från källan och pushades till registret. I den här artikeln drar du nytta av den nätverksnära aspekten av det geo-replikerade registret genom att distribuera behållaren till två Web App-instanser i två olika Azure-regioner. Varje instans hämtar sedan behållaravbildningen från det närmaste registret.

Den här självstudien är del två i serien:

> [!div class="checklist"]
> * Distribuera en behållaravbildning av till två instanser av *Web Apps för behållare*
> * Verifiera det distribuerade programmet

Om du inte har skapat något geo-replikerat register och pushat avbildningen av behållarexempelprogrammet till registret, så gå tillbaka till föregående självstudiekurs i serien: [Förbered ett geo-replikerat Azure-behållarregister](container-registry-tutorial-prepare-registry.md).

I nästa artikel i serien uppdaterar du programmet och push-överför sedan den uppdaterade behållaravbildning till registret. Sedan kan du bläddra till varje Web App-instans som körs och se hur ändringen automatiskt återspeglas i dem genom att visa geo-replikering av Azure Container Registry och aktiva webhookar.

## <a name="automatic-deployment-to-web-apps-for-containers"></a>Automatisk distribution till Web Apps för behållare

Azure Container Registry ger stöd för distribution av program i behållare direkt till [Web Apps för behållare](../app-service/containers/index.yml). I den här självstudiekursen använder du Azure Portal för att distribuera den behållaravbildning som du skapade i den föregående kursen till två webbappsplaner i olika Azure-regioner.

När du distribuerar en webbapp från en behållaravbildning i registret, och har ett geo-replikerat register i samma region, så skapar Azure Container Registry en [webhook](container-registry-webhook.md) för avbildningsdistribution för dig. När du push-överför en ny avbildning till behållarlagringsplatsen så hämtar webhooken ändringen och distribuerar automatiskt den nya behållaravbildningen till din webbapp.

## <a name="deploy-a-web-app-for-containers-instance"></a>Distribuera en instans av Web App for Containers

Nu är det dags att skapa en instans av Web App for Containers i regionen *USA, västra*.

Logga in på [Azure Portal](https://portal.azure.com) och navigera till den registernyckel som du skapade i den föregående självstudiekursen.

Välj **Databaser** > **acr helloworld**, högerklicka på taggen **v1** under **Taggar** och välj **Distribuera till webbapp**.

![Distribuera till App Service i Azure Portal][deploy-app-portal-01]

Ange följande värden för respektive inställning under **Web App for Containers**:

| Inställning | Värde |
|---|---|
| **Webbplatsnamn** | Ett globalt unikt namn för webbappen. I det här exemplet använder vi formatet `<acrName>-westus` för att lätt kunna identifiera det register och den region som webbappen distribueras från. |
| **Resursgrupp** | **Använd befintlig** > `myResourceGroup` |
| **App Service-plan/plats** | Skapa ett nytt schema med namnet `plan-westus` i regionen **USA, västra**. |
| **Avbildning** | `acr-helloworld:v1`

Etablera webbappen i regionen *USA, västra* genom att välja **Skapa**.

![Webbappen i Linux-konfiguration i Azure Portal][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>Visa den distribuerade webbappen

När distributionen är klar kan du visa programmet som körs genom att gå till URL:en i webbläsaren.

Välj **App Services** i portalen, och sedan den webbapp som du etablerade i föregående steg. I det här exemplet får webbappen namnet *uniqueregistryname-westus*.

Markera webbappens hyperlänks-URL högst upp till höger i **App Service**-översikten, så att du kan se programmet som körs i din webbläsare.

![Webbappen i Linux-konfiguration i Azure Portal][deploy-app-portal-04]

När Docker-avbildningen har distribuerats från din geo-replikerade behållare, så visar webbplatsen en avbildning som representerar den Azure-regionen som är värd för behållarregistret.

![Distribuerad webbapp som visas i en webbläsare][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>Distribuera en andra instans av Web App for Containers

Distribuera en andra webbapp till regionen *USA, östra* med den procedur som beskrivs i föregående avsnitt. Ange följande värden under **Web App for Containers**:

| Inställning | Värde |
|---|---|
| **Webbplatsnamn** | Ett globalt unikt namn för webbappen. I det här exemplet använder vi formatet `<acrName>-eastus` för att lätt kunna identifiera det register och den region som webbappen distribueras från. |
| **Resursgrupp** | **Använd befintlig** > `myResourceGroup` |
| **App Service-plan/plats** | Skapa ett nytt schema med namnet `plan-eastus` i regionen **USA, östra**. |
| **Avbildning** | `acr-helloworld:v1`

Etablera webbappen i regionen *USA, östra* genom att välja **Skapa**.

![Webbappen i Linux-konfiguration i Azure Portal][deploy-app-portal-06]

## <a name="view-the-deployed-web-app"></a>Visa den distribuerade webbappen

Liksom tidigare kan du visa programmet som körs genom att gå till URL:en i webbläsaren.

Välj **App Services** i portalen, och sedan den webbapp som du etablerade i föregående steg. I det här exemplet får webbappen namnet *uniqueregistryname-eastus*.

Markera webbappens hyperlänks-URL högst upp till höger i **App Service**-översikten, så att du kan se programmet som körs i din webbläsare.

![Webbappen i Linux-konfiguration i Azure Portal][deploy-app-portal-07]

När Docker-avbildningen har distribuerats från din geo-replikerade behållare, så visar webbplatsen en avbildning som representerar den Azure-regionen som är värd för behållarregistret.

![Distribuerad webbapp som visas i en webbläsare][deployed-app-eastus]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du distribuerat två instanser av Web App for Containers från ett geo-replikerat Azure-behållarregister.

Gå vidare till nästa självstudie där du lär dig att uppdatera och sedan distribuera en ny behållaravbildning till behållarregistret, och sedan kontrollera att de webbappar som körs i båda regionerna uppdaterades automatiskt.

> [!div class="nextstepaction"]
> [Distribuera en uppdatering till en georeplikerad behållaravbildning](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png