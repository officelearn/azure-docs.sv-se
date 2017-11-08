---
title: "Synkronisera innehåll från en mapp i molnet till Azure App Service"
description: "Lär dig mer om att distribuera din app till Azure App Service via synkronisering av innehåll från en mapp i molnet."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 04d1d226093f131a521f32f47c333ff9aefc6f3b
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synkronisera innehåll från en mapp i molnet till Azure App Service
Den här kursen visar hur du distribuerar till [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) genom att synkronisera innehållet från populära lagring molntjänster som Dropbox och OneDrive. 

## <a name="overview"></a>Översikt över distribution av innehåll synkronisering
Distributionen av innehåll på begäran-synkronisering drivs av den [Kudu-distributionsmotorn](https://github.com/projectkudu/kudu/wiki) integrerad med App Service. I den [Azure Portal](https://portal.azure.com), kan du utse en mapp i molnlagringen, fungerar med din app koden och innehållet i mappen och synkronisera till App Service klickar på en knapp. Synkronisering av innehåll använder Kudu-processen för att bygga och distribution. 

## <a name="contentsync"></a>Så här aktiverar du innehåll sync-distribution
Att aktivera synkronisering av innehåll från den [Azure Portal](https://portal.azure.com), gör du följande:

1. I bladet för din app i Azure-portalen klickar du på **inställningar** > **distributionskälla**. Klicka på **Välj källa**och välj **OneDrive** eller **Dropbox** som källa för distribution. 
   
    ![Synkronisering av innehåll](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > På grund av underliggande skillnader i API: er, **OneDrive för företag** stöds inte just nu. 
   > 
   > 
2. Slutför auktorisering arbetsflödet om du vill aktivera App Service att få åtkomst till en specifik fördefinierade avsedda sökväg för OneDrive eller Dropbox där allt Apptjänst innehåll sparas.  
    Efter auktoriseringen App Service får plattform du alternativet att skapa innehållsmappen under angiven sökväg för innehåll eller välj en befintlig mapp som innehåll under den här avsedda sökväg för innehåll. De avsedda sökvägarna till innehåll under din molnlagringskonton som används för synkronisering av Apptjänst är följande:  
   
   * **OneDrive**:`Apps\Azure Web Apps` 
   * **Dropbox**:`Dropbox\Apps\Azure`
3. Efter den första synkroniseringen av innehåll kan innehåll synkroniseringen initieras på begäran från Azure-portalen. Distributionshistoriken är tillgängliga med den **distributioner** bladet.
   
    ![Distributionshistoriken](./media/app-service-deploy-content-sync/onedrive_sync.png)

Mer information om Dropbox distributionen är tillgänglig under [distribuera från Dropbox] (https://azure.microsoft.com/en-in/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/).

