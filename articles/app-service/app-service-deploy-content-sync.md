---
title: Synkronisera innehåll från en mapp i molnet till Azure App Service
description: Lär dig mer om att distribuera din app till Azure App Service via synkronisering av innehåll från en mapp i molnet.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: d456ae2ffbd3745ef976ad94219a3f998838066b
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850227"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synkronisera innehåll från en mapp i molnet till Azure App Service
Den här artikeln visar hur du synkroniserar ditt innehåll till [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) från Dropbox och OneDrive. 

Distributionen av innehåll på begäran-synkronisering drivs av App Service [Kudu-distributionsmotorn](https://github.com/projectkudu/kudu/wiki). Du kan arbeta med din app koden och innehållet i en mapp för angivna molnet och sedan synkronisera till App Service med klickar på en knapp. Synkronisering av innehåll använder Kudu build-servern. 

## <a name="enable-content-sync-deployment"></a>Aktivera synkronisering av innehåll distribution

För att aktivera synkronisering av innehåll, gå till sidan Apptjänst-app i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **OneDrive** eller **Dropbox** > **auktorisera**. Följ anvisningarna för auktorisering. 

![](media/app-service-deploy-content-sync/choose-source.png)

Du behöver bara godkänna med OneDrive eller Dropbox en gång. Om du redan är behörig bara på **Fortsätt**. Du kan ändra det behöriga OneDrive eller Dropbox-kontot genom att klicka på **ändra konto**.

![](media/app-service-deploy-content-sync/continue.png)

I den **konfigurera** sidan, Välj den mapp som du vill synkronisera. Den här mappen skapas under följande avsedda sökväg till innehåll i OneDrive eller Dropbox. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

När du är klar klickar du på **Fortsätt**.

I den **sammanfattning** , kontrollera alternativen och klickar på **Slutför**.

## <a name="synchronize-content"></a>Synkronisera innehåll

Om du vill synkronisera innehåll i mappen moln med Apptjänst kan gå tillbaka till den **Deployment Center** och klickar på **Sync**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > På grund av underliggande skillnader i API: er, **OneDrive för företag** stöds inte just nu. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Inaktivera innehåll sync-distribution

Om du vill inaktivera synkronisering av innehåll, navigera till sidan Apptjänst-app i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **OneDrive** eller **Dropbox** > **frånkoppling**.

![](media/app-service-deploy-content-sync/disable.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera från lokal Git repo](app-service-deploy-local-git.md)
