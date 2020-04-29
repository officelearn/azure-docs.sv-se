---
title: Synkronisera innehåll från en molnbaserad mapp
description: Lär dig hur du distribuerar din app för att Azure App Service via synkronisering av innehåll från en molnbaserad mapp, inklusive OneDrive eller Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9f18eef607a5f655aecc0b6ebe1c85a239b34192
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77482980"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synkronisera innehåll från en molnbaserad mapp till Azure App Service
Den här artikeln visar hur du synkroniserar ditt innehåll till [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) från Dropbox och OneDrive. 

Distributionen av innehålls synkronisering på begäran drivs av App Service [kudu-distributions motor](https://github.com/projectkudu/kudu/wiki). Du kan arbeta med din app-kod och innehåll i en angiven molnbaserad mapp och sedan synkronisera till App Service med en knapp klickning. Synkronisering av innehåll använder kudu build-servern. 

## <a name="enable-content-sync-deployment"></a>Aktivera distribution av innehålls synkronisering

Om du vill aktivera synkronisering av innehåll går du till sidan App Service app i [Azure Portal](https://portal.azure.com).

I den vänstra menyn klickar du på **distributions Center** > **OneDrive** eller **Dropbox** > **auktorisera**. Följ anvisningarna i auktoriseringen. 

![](media/app-service-deploy-content-sync/choose-source.png)

Du behöver bara auktorisera med OneDrive eller Dropbox en gång. Om du redan har behörighet klickar du bara på **Fortsätt**. Du kan ändra det auktoriserade OneDrive-eller Dropbox-kontot genom att klicka på **Ändra konto**.

![](media/app-service-deploy-content-sync/continue.png)

På sidan **Konfigurera** väljer du den mapp som du vill synkronisera. Den här mappen skapas under följande angivna innehålls Sök väg i OneDrive eller Dropbox. 
   
* **OneDrive**:`Apps\Azure Web Apps`
* **Dropbox**:`Apps\Azure`

När du är färdig klickar du på **Fortsätt**.

På sidan **Sammanfattning** kontrollerar du dina alternativ och klickar på **Slutför**.

## <a name="synchronize-content"></a>Synkronisera innehåll

När du vill synkronisera innehåll i din Cloud-mapp med App Service går du tillbaka till sidan **distributions Center** och klickar på **Synkronisera**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > På grund av underliggande skillnader i API: erna stöds inte **OneDrive för företag för** tillfället. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Inaktivera distribution av innehålls synkronisering

Om du vill inaktivera synkronisering av innehåll går du till sidan App Service app i [Azure Portal](https://portal.azure.com).

I den vänstra menyn klickar du på **distributions Center** > **Koppla från**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera från lokal git-lagrings platsen](deploy-local-git.md)
