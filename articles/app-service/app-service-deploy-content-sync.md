---
title: Synkronisera innehåll från en molnmapp till Azure App Service
description: Lär dig mer om att distribuera din app till Azure App Service via innehållssynkronisering från en molnmapp.
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
ms.openlocfilehash: 3796f5c8956b633a4789baaf31a439746dc96b96
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233770"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synkronisera innehåll från en molnmapp till Azure App Service
Den här artikeln visar hur du synkroniserar ditt innehåll till [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) från Dropbox och OneDrive. 

På begäran innehållssynkronisering-distribution som drivs av App Service- [Kudu-distributionsmotorn](https://github.com/projectkudu/kudu/wiki). Du kan arbeta med din kod och innehållet i en avsedda molnmapp och sedan synkronisera till App Service genom att klicka på en knapp. Innehållssynkronisering använder Kudu build-servern. 

## <a name="enable-content-sync-deployment"></a>Aktivera innehållssynkronisering distribution

Om du vill aktivera innehållssynkronisering inloggningsinformationen för din App Service app i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **OneDrive** eller **Dropbox** > **auktorisera**. Följ anvisningarna för auktorisering. 

![](media/app-service-deploy-content-sync/choose-source.png)

Du behöver bara en gång auktorisera med OneDrive eller Dropbox. Om du redan har auktoriserats kommer du bara på **Fortsätt**. Du kan ändra det auktoriserade OneDrive eller Dropbox-kontot genom att klicka på **ändra konto**.

![](media/app-service-deploy-content-sync/continue.png)

I den **konfigurera** väljer du den mapp som du vill synkronisera. Den här mappen skapas under följande avsedda innehållssökvägen i OneDrive eller Dropbox. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

När du är klar klickar du på **Fortsätt**.

I den **sammanfattning** kontrollerar du dina alternativ och klickar på **Slutför**.

## <a name="synchronize-content"></a>Synkronisera innehåll

När du vill synkronisera innehåll i mappen molnet med App Service går du tillbaka till den **Deployment Center** och klicka på **synkronisering**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > På grund av underliggande skillnader i API: er, **OneDrive för företag** stöds inte just nu. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Inaktivera innehållssynkronisering användning

Om du vill inaktivera innehållssynkronisering inloggningsinformationen för din App Service app i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **OneDrive** eller **Dropbox** > **Disconnect**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera från lokal Git-lagringsplats](app-service-deploy-local-git.md)
