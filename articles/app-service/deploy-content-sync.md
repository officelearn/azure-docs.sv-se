---
title: Synkronisera innehåll från en molnmapp
description: Läs om hur du distribuerar din app till Azure App Service via innehållssynkronisering från en molnmapp, inklusive OneDrive eller Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9f18eef607a5f655aecc0b6ebe1c85a239b34192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482980"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synkronisera innehåll från en molnmapp till Azure App Service
I den här artikeln visas hur du synkroniserar ditt innehåll med [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) från Dropbox och OneDrive. 

Distributionen av innehållssynkronisering på begäran drivs av [distributionsmotorn](https://github.com/projectkudu/kudu/wiki)För App Service Kudu . Du kan arbeta med din appkod och ditt innehåll i en angiven molnmapp och sedan synkronisera med App Service med ett klick på en knapp. Innehållssynkronisering använder Kudu-byggservern. 

## <a name="enable-content-sync-deployment"></a>Aktivera distribution av innehållssynkronisering

Om du vill aktivera innehållssynkronisering navigerar du till appsidan för App Service i [Azure-portalen](https://portal.azure.com).

Klicka på Distribution **Center** > **OneDrive** eller **Dropbox** > **Authorize**på menyn till vänster. Följ auktoriseringsprompten. 

![](media/app-service-deploy-content-sync/choose-source.png)

Du behöver bara auktorisera med OneDrive eller Dropbox en gång. Om du redan är auktoriserad klickar du bara på **Fortsätt**. Du kan ändra det auktoriserade OneDrive- eller Dropbox-kontot genom att klicka på **Ändra konto**.

![](media/app-service-deploy-content-sync/continue.png)

Markera den mapp som du vill synkronisera på sidan **Konfigurera.** Den här mappen skapas under följande avsedda innehållssökväg i OneDrive eller Dropbox. 
   
* **OneDrive**:`Apps\Azure Web Apps`
* **Dropbox**:`Apps\Azure`

När du är klar klickar du på **Fortsätt**.

Kontrollera alternativen på sidan **Sammanfattning** och klicka på **Slutför**.

## <a name="synchronize-content"></a>Synkronisera innehåll

När du vill synkronisera innehåll i molnmappen med App Service går du tillbaka till sidan **Distributionscenter** och klickar på **Synkronisera**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > På grund av underliggande skillnader i API:erna stöds inte **OneDrive för företag** just nu. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Inaktivera distribution av innehållssynkronisering

Om du vill inaktivera synkronisering av innehåll navigerar du till appsidan för App Service i [Azure-portalen](https://portal.azure.com).

Klicka på **Frånkoppling av Distributionscenter** > **Disconnect**på menyn.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera från lokala Git-repo](deploy-local-git.md)
