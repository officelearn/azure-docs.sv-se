---
title: "Azure Container registret kursen - Push en uppdaterad bild för regional distributioner"
description: "Pusha en ändrade Docker-avbildning till din georeplikerad Azure registret och sedan se ändringarna distribueras automatiskt till webbprogram som körs i flera områden. Del tre av en serie i tre delar."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
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
ms.openlocfilehash: 05c5149ed6c8502c31539f31bfff046f98dc633d
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="push-an-updated-image-to-regional-deployments"></a>Push-installera en uppdaterad bild regionala distributioner

Detta är en del tre i en serie i tre delar självstudiekursen. I den [tidigare kursen](container-registry-tutorial-deploy-app.md), geo-replikering har konfigurerats för två olika regionala webbprogrammet distributioner. I den här självstudiekursen du först ändra programmet, sedan skapa en ny avbildning för behållaren och push-installera den georeplikerad registret. Slutligen kan du visa ändringen, distribueras automatiskt med Azure Container registret webhooks i båda fallen Web App.

I den här självstudiekursen den sista delen i serien:

> [!div class="checklist"]
> * Ändra webbprogrammet HTML
> * Skapa och tagga Docker-bild
> * Push-ändringen till registret för Azure-behållare
> * Visa den uppdaterade appen i två olika regioner

Om du ännu inte har konfigurerat två *Web App för behållare* regionala distributioner, gå tillbaka till föregående kurs i serien, [distribuera webbappen från Azure-behållare registret](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>Göra ändringar i webbprogrammet

I det här steget ändra till webbprogram som ska vara mycket synliga när du push uppdaterade behållaren avbildningen till registret för Azure-behållare.

Hitta den `AcrHelloworld/Views/Home/Index.cshtml` fil i källan för programmet du [klonad från GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) i en tidigare vägledning och öppna den i valfri textredigerare. Lägg till följande rad ovanför den `<img>` rad:

```html
<h1>MODIFIED</h1>
```

Din ändrade `Index.cshtml` bör likna:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<h1>MODIFIED</h1>
<img width="700" src="~/images/@ViewData["MAPIMAGE"]" />
<ul>
<li>Registry URL: @ViewData["REGISTRYURL"]</li>
<li>Registry IP: @ViewData["REGISTRYIP"]</li>
<li>HostEntry: @ViewData["HOSTENTRY"]</li>
<li>Region: @ViewData["REGION"]</li>
<li>Map: @ViewData["MAPIMAGE"]</li>
</ul>
```

## <a name="rebuild-the-image"></a>Återskapa avbildningen

Nu när du har uppdaterat webbprogrammet återskapa dess behållare avbildning. Som tidigare använda fullständiga Avbildningsnamnet, inklusive Inloggningswebbadressen för server, för taggen:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="run-the-container-locally"></a>Kör behållaren lokalt

Innan du distribuerar till Azure-behållare registret, kör du avbildningen lokalt för att verifiera genereringen lyckades.

```bash
docker run -d -p 8080:80 <acrName>.azurecr.io/acr-helloworld:v1
```

Gå till http://localhost: 8080 i webbläsaren för att bekräfta att behållaren är igång och att ändringen visas.

![LOKALA BEHÅLLAREN BILD][local-container-01]

## <a name="push-image-to-azure-container-registry"></a>Push-avbildningen till registret för Azure-behållare

Nu push den uppdaterade *acr helloworld* behållare avbildningen till georeplikerad registret. Du här, utför en enda `docker push` kommando för att distribuera den uppdaterade avbildningen till registret repliker i både den *västra USA* och *östra USA* regioner.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Resultatet bör likna följande:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
c003ed6fc8b8: Pushed
02b11afef3fd: Layer already exists
cf17b6f921be: Layer already exists
c93ae914d31e: Layer already exists
2eea44510cee: Layer already exists
670f809bd6d5: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:e44c0956a21c91e1f5f7bc83f23f1de710c798246df1e0e508c0c88025449646 size: 1792
```

## <a name="view-the-webhook-logs"></a>Visa webhook-loggar

Du kan se Azure Container registret-webhooks som utlöses när avbildningen replikeras.

Se regionala webhooks som skapades när du har distribuerat behållaren *Web Apps för behållare* i en tidigare självstudier, navigerar du till behållaren registret i Azure-portalen och väljer sedan **Webhooks**under **SERVICES**.

![Behållaren registret Webhooks i Azure-portalen][tutorial-portal-01]

Markera varje Webhook att se historiken för dess anrop och svar. Du bör se en rad för den **push** åtgärd i loggarna för båda Webhooks. Här, logg för Webhooken finns i den *västra USA* region visar den **push** åtgärden utlöses av den `docker push` i föregående steg:

![Behållaren registernyckeln Webhook logga in på Azure-portalen (västra USA)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Visa det uppdaterade webbprogrammet

Webhooks meddela Web Apps att en ny avbildning har tagits pushas till registret, som automatiskt distribuerar den uppdaterade behållaren till två regionala webbprogram.

Kontrollera att programmet har uppdaterats i båda distributionerna genom att gå till båda regionala webbprogrammet distributioner i webbläsaren. Som en påminnelse hittar du URL: en för den distribuerade webbappen i upp till höger på varje flik för översikt över Apptjänst.

![Översikt över App Service i Azure-portalen][tutorial-portal-03]

Om du vill se det uppdaterade programmet, Välj länken i App Service-Översikt. Här är ett exempel vy av appen körs i *västra USA*:

![Webbläsarvy över ändrade webbapp körs i regionen USA, västra][deployed-app-westus-modified]

Kontrollera att bilden uppdaterade behållare också distribueras till den *östra USA* distributionen genom att visa den i webbläsaren.

![Webbläsarvy över ändrade webbapp körs i östra USA][deployed-app-eastus-modified]

Med en enda `docker push`, du har uppdaterat båda regionala Web App-distributioner och Azure Container registret hanteras behållaren bilder från nätverket Stäng databaser.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen, uppdateras och pushas en ny version av web application-behållaren i georeplikerad-registret. Webhooks i Azure Container registret meddelas Web Apps för behållare för uppdateringen som utlöses av en lokal pull från registret replikerna.

I det slutliga kurs i serien du:

> [!div class="checklist"]
> * Uppdatera webbprogrammet HTML
> * Inbyggda och taggas Docker-bild
> * Pushas ändringen till registret för Azure-behållare
> * Visa den uppdaterade appen i två olika regioner

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png