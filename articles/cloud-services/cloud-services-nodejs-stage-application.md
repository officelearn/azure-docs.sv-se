---
title: "Förbered en distribution av service (Node.js) | Microsoft Docs"
description: "Lär dig mer om att distribuera din Azure-program till en fristående miljö och sedan distribuera till en produktionsmiljö med virtuella IP-adresser (VIP) växlingen."
services: cloud-services
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: d65d26a6-b424-49cd-a88c-7ef46bb112a8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tarcher
ms.openlocfilehash: b3000ed769e8c60eccb21e26f53ce7ccb7e68d7f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="staging-an-application-in-azure"></a>Mellanlagring av ett program i Azure
En paketerad App kan distribueras till mellanlagringsmiljön i Azure som ska testas innan du flyttar den till produktionsmiljön som programmet är tillgängligt på Internet. Mellanlagringsmiljön är exakt samma sätt som produktionsmiljön, förutom att du kan bara komma åt mellanlagrade programmet med en fördunklade URL som genereras av Azure. När du har kontrollerat att programmet fungerar korrekt, kan det distribueras till produktionsmiljön genom att utföra en växling för virtuella IP-adresser (VIP).

> [!NOTE]
> Stegen i den här artikeln gäller endast för noden programmen som en tjänst i Azure-molnet.
> 
> 

## <a name="step-1-stage-an-application"></a>Steg 1: Förbered ett program
Den här uppgiften täcker hur man Mellanlagra ett program med hjälp av den **Microsoft Azure PowerShell**.

1. När du publicerar en tjänst skickar den **-fack** parametern till den **Publish-AzureServiceProject** cmdlet.
   
   ```powershell
   Publish-AzureServiceProject -Slot staging
   ```
2. Logga in på den [klassiska Azure-portalen] och välj **molntjänster**. När molnet tjänst skapas och **mellanlagring** kolumnen status har uppdaterats till **kör**, klicka på namnet på tjänsten.
   
   ![portalen visar en aktiv tjänst][cloud-service]
3. Välj den **instrumentpanelen**, och välj sedan **mellanlagring**.
   
   ![cloud service-instrumentpanelen][cloud-service-dashboard]
4. Observera värdet i den **Webbadress** post till höger. DNS-namnet är ett dolda interna ID som genereras av Azure.
   
    ![webbadress][cloud-service-staging-url]

Nu kan du kontrollera att programmet fungerar korrekt i mellanlagringsmiljön med hjälp av fristående webbplatsens URL.

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>Steg 2: Uppgradera ett program i produktion av växling VIP: er
När du har kontrollerat den uppgraderade versionen av ett program i mellanlagringsmiljön du snabbt den tillgänglig i produktionen genom att byta den virtuella IP-adresser (VIP) för mellanlagrings- och produktionsmiljöer.

> [!NOTE]
> Det här steget förutsätter att du redan har distribuerat ett program till produktion och mellanlagrad den uppgraderade versionen av programmet.
> 
> 

1. Logga in på den [klassiska Azure-portalen], klickar du på **molntjänster** och välj sedan namnet på tjänsten.
2. Från den **instrumentpanelen**väljer **mellanlagring**, och klicka sedan på **växla** längst ned på sidan. Detta öppnar dialogrutan VIP-växling.
   
   ![VIP-växling dialogrutan][vip-swap-dialog]
3. Granska informationen och klicka sedan på **OK**. Två distributioner börja uppdatera som fristående distributionen växlar till produktion och produktionsdistributionen växlar till Förproduktion.

Du har mellanlagrad en distribution och uppgraderas en Produktionsdistribution av växla VIP: er med distributionen i Förproduktion.

## <a name="additional-resources"></a>Ytterligare resurser
* [Distribuera en Serviceuppgradering av till produktion av växla VIP: er i Azure]

[klassiska Azure-portalen]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Distribuera en Serviceuppgradering av till produktion av växla VIP: er i Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
