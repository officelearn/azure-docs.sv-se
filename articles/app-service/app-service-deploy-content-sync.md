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
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 8e132e4d4a65588d57e3cfb969e785f5a164206c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synkronisera innehåll från en mapp i molnet till Azure App Service
Den här kursen visar hur du synkroniserar ditt innehåll till [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) från populära lagring molntjänster som Dropbox och OneDrive. 

## <a name="overview"></a>Översikt över distribution av innehåll synkronisering
Distributionen av innehåll på begäran-synkronisering drivs av den [Kudu-distributionsmotorn](https://github.com/projectkudu/kudu/wiki) integrerad med App Service. I den [Azure-portalen](https://portal.azure.com), kan du utse en mapp i molnlagringen, fungerar med din app koden och innehållet i mappen och synkronisera till App Service klickar på en knapp. Synkronisering av innehåll använder Kudu-processen för att bygga och distribution. 

## <a name="contentsync"></a>Så här aktiverar du innehåll sync-distribution
Att aktivera synkronisering av innehåll från den [Azure-portalen](https://portal.azure.com), gör du följande:

1. På din app i Azure-portalen klickar du på **inställningar** > **distributionskälla**. Klicka på **Välj källa**och välj **OneDrive** eller **Dropbox** som källa för distribution. 
   
    ![Synkronisering av innehåll](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > På grund av underliggande skillnader i API: er, **OneDrive för företag** stöds inte just nu. 
   > 
   > 
2. Fullständig auktorisering arbetsflödet för att aktivera Apptjänst åtkomst till en specifik fördefinierade avsedda sökväg för OneDrive eller Dropbox, där alla din Apptjänst innehåll lagras. Efter auktoriseringen kan ger Apptjänst-plattformen dig möjlighet att skapa innehållsmappen under angiven sökväg för innehåll eller välj en befintlig mapp som innehåll under den här avsedda sökväg för innehåll. De avsedda sökvägarna till innehåll under din molnlagringskonton som används för synkronisering av Apptjänst är följande:  
   
   * **OneDrive**:`Apps\Azure Web Apps` 
   * **Dropbox**:`Dropbox\Apps\Azure`
3. Efter den första innehåll synkroniseringen kan innehåll synkronisering startas på begäran från Azure-portalen. Distributionshistoriken är tillgängligt på den **distributioner** sidan.
   
    ![Distributionshistoriken](./media/app-service-deploy-content-sync/onedrive_sync.png)

Mer information om Dropbox distributionen är tillgänglig under [distribuera från Dropbox] (https://azure.microsoft.com/en-in/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/).

