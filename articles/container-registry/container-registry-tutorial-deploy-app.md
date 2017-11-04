---
title: "Azure Container registret tutorial – distribuera webbapp från Azure-behållare registret"
description: "Distribuera en Linux-baserade webbapp med hjälp av en behållare avbildning registret georeplikerad Azure-behållaren. En del två av en serie i tre delar."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: neilpeterson
tags: acr, azure-container-registry, geo-replication
keywords: "Docker behållare, registret, Azure"
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 90d4b51dfaad409298f72887480dfaf827aef9f0
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="deploy-web-app-from-azure-container-registry"></a>Distribuera webbapp från Azure-behållare registret

Detta är en del två i självstudiekursen serie i tre delar. I [del ett](container-registry-tutorial-prepare-registry.md), ett privat, georeplikerad behållare register har skapats och en behållare avbildning har skapats från källan och pushas till registret. I den här artikeln distribuera behållaren i två Web App-instanser i två olika Azure-regioner kan dra nytta av nätverket Stäng georeplikerad-registret.

Del två i serien i den här självstudiekursen:

> [!div class="checklist"]
> * Distribuera en avbildning av behållare till två *Web Apps för behållare* instanser
> * Kontrollera det distribuerade programmet

Om du inte har skapat ett geo-replikerade register och pushas bild av container exempelprogrammet i registret återgår till föregående kurs i serien, [förbereda ett geo-replikerade Azure-behållaren register](container-registry-tutorial-prepare-registry.md).

I nästa del av serien du uppdatera programmet och sedan push-en ny behållare avbildning till registret. Slutligen Bläddra du till varje Web App-instans som körs ska kunna se ändringen återspeglas automatiskt i både visar Azure Container registret geo-replikering och webhooks i åtgärden.

## <a name="automatic-deployment-to-web-apps-for-containers"></a>Automatisk distribution för Web Apps för behållare

Azure Container registret ger stöd för distribution av program direkt till [Web Apps för behållare](../app-service/containers/index.yml). I den här kursen använder du Azure-portalen ska distribuera avbildningen behållaren skapade i föregående kursen till två web app planer finns i olika Azure-regioner.

När du distribuerar en webbapp från en avbildning av behållare i registret, och du har ett geo-replikerade register i samma region, Azure Container registret skapar en distribution av avbildningar [webhook](container-registry-webhook.md) för dig. När du skicka en ny avbildning till lagringsplatsen för behållaren webhooken hämtar ändringen och distribuerar den nya behållare avbildningen automatiskt till ditt webbprogram.

## <a name="deploy-a-web-app-for-containers-instance"></a>Distribuera en Webbapp för behållare instans

I det här steget skapar du en Webbapp för behållare instans i den *västra USA* region.

Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till registernyckeln som du skapade i föregående kursen.

Välj **databaser** > **acr helloworld**, högerklicka på den **v1** tagg **taggar** och välj **Till webbprogrammet**.

![Distribuera till app service i Azure-portalen][deploy-app-portal-01]

Under **Web App för behållare** som visas, ange följande värden för varje inställning:

| Inställning | Värde |
|---|---|
| **Platsnamn** | Ett globalt unikt namn för webbappen. I det här exemplet använder vi formatet `<acrName>-westus` lätt kan identifiera registret och region webbprogrammet distribueras från. |
| **Resursgrupp** | **Använd befintlig** > `myResourceGroup` |
| **App service-plan/plats** | Skapa ett nytt schema med namnet `plan-westus` i den **västra USA** region. |
| **Bild** | `acr-helloworld:v1`

Välj **skapa** att etablera webbprogram till den *västra USA* region.

![Webbprogrammet på Linux-konfiguration i Azure-portalen][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>Visa den distribuerade webbappen

När installationen är klar använder kan du visa körs programmet genom att gå till URL: en i webbläsaren.

I portalen, Välj **Apptjänster**, och sedan webbprogrammet etableras i föregående steg. I det här exemplet webbprogrammet med namnet *uniqueregistryname westus*.

Välj länkade URL: en för webbappen i upp till höger på den **Apptjänst** översikt för att visa programmet som körs i webbläsaren.

![Webbprogrammet på Linux-konfiguration i Azure-portalen][deploy-app-portal-04]

När Docker-avbildningen har distribuerats från registret georeplikerad behållare visas webbplatsen en bild som representerar den Azure-regionen där behållaren registret.

![Distribuerade webbprogram som visas i en webbläsare][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>Distribuera andra webbprogram för behållare instans

Använd proceduren som beskrivs i föregående avsnitt för att distribuera en andra webbprogram för att den *östra USA* region. Under **Web App för behållare**, ange följande värden:

| Inställning | Värde |
|---|---|
| **Platsnamn** | Ett globalt unikt namn för webbappen. I det här exemplet använder vi formatet `<acrName>-eastus` lätt kan identifiera registret och region webbprogrammet distribueras från. |
| **Resursgrupp** | **Använd befintlig** > `myResourceGroup` |
| **App service-plan/plats** | Skapa ett nytt schema med namnet `plan-eastus` i den **östra USA** region. |
| **Bild** | `acr-helloworld:v1`

Välj **skapa** att etablera webbprogram till den *östra USA* region.

![Webbprogrammet på Linux-konfiguration i Azure-portalen][deploy-app-portal-06]

## <a name="view-the-deployed-web-app"></a>Visa den distribuerade webbappen

Som kan tidigare, du visa körs programmet genom att gå till URL: en i webbläsaren.

I portalen, Välj **Apptjänster**, och sedan webbprogrammet etableras i föregående steg. I det här exemplet webbprogrammet med namnet *uniqueregistryname eastus*.

Välj länkade URL: en för webbappen i upp till höger på den **översikt över App Service** att visa program som körs i webbläsaren.

![Webbprogrammet på Linux-konfiguration i Azure-portalen][deploy-app-portal-07]

När Docker-avbildningen har distribuerats från registret georeplikerad behållare visas webbplatsen en bild som representerar den Azure-regionen där behållaren registret.

![Distribuerade webbprogram som visas i en webbläsare][deployed-app-eastus]

## <a name="next-steps"></a>Nästa steg

I kursen får distribuerat du två Web App för behållare instanser registret georeplikerad Azure-behållaren. Genom att följa stegen i kursen får du:

> [!div class="checklist"]
> * Distribuera en avbildning av behållare till två *Web Apps för behållare* instanser
> * Verifiera det distribuerade programmet

Gå vidare till nästa guiden för att uppdatera och distribuera en ny behållare avbildning till behållaren registret och sedan kontrollera att webbprogram som körs i båda regioner uppdaterades automatiskt.

> [!div class="nextstepaction"]
> [Distribuera en uppdatering till georeplikerad behållare bild](./container-registry-tutorial-deploy-update.md)

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