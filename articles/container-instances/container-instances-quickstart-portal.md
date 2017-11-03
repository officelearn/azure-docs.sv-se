---
title: "Snabbstart - skapa din första Azure Behållarinstanser behållare med Azure-portalen"
description: "Distribuera och kom igång med Azure Container Instances"
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 0179107ece1e150246ab40836783d810425be3ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Skapa din första behållare i Azure Container Instances

Med Azure Container Instances är det enkelt att skapa och hantera behållare i Azure. I Snabbstart, skapa en behållare i Azure och exponera till internet med en offentlig IP-adress. Den här åtgärden har slutförts med hjälp av Azure portal. Med bara några klickningar visas det i webbläsaren:

![App som distribuerats via Azure Container Instances visas i webbläsare][aci-app-browser]

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-container-instance"></a>Skapa en instans av behållare

Välj den **ny** > **behållare** > **Behållarinstanser som Azure (förhandsversion)**.

![Skapa en ny instans av behållare i Azure-portalen][aci-portal-01]

Ange följande värden i den **behållarnamn**, **behållaren image**, och **resursgruppen** textrutor. Lämna de andra värdena standardvärden och klicka sedan på **OK**.

* Behållarens namn:`mycontainer`
* Bild av behållare:`microsoft/aci-helloworld`
* Resursgrupp:`myResourceGroup`

![Konfigurera grundläggande inställningar för en ny instans av behållare i Azure-portalen][aci-portal-03]

Du kan skapa Windows- och Linux-behållare i Azure Container instanser. I den här snabbstarten vi lämnar du standardinställningen **Linux** eftersom vi har angetts en Linux-baserade behållare (`microsoft/aci-helloworld`) i föregående steg.

Lämna de andra inställningarna i **Configuration** standardvärden, sedan klickar du på **OK** att validera konfigurationen.

![Konfigurera en ny instans av behållare i Azure-portalen][aci-portal-04]

När verifieringen är klar visas en sammanfattning av inställningarna för behållaren. Välj **OK** skicka din begäran för distribution av behållare.

![Inställningsöversikt för en ny behållare instans i Azure-portalen][aci-portal-05]

När distributionen startar placeras en panel på instrumentpanelen portal som visar förloppet för distributionen. När distributionen är klar panelen uppdateras så att din nya **minbehållare myc1** behållargruppen.

![Förlopp för en ny behållare instans i Azure-portalen][aci-portal-08]

Välj den **minbehållare myc1** behållargruppen att visa behållaren gruppens egenskaper. Anteckna den **Ip-adress** i behållargruppen samt de **tillstånd** för din behållare.

![Översikt över group behållare i Azure-portalen][aci-portal-06]

När behållaren flyttas till den **kör** tillstånd, navigera till IP-adressen som du antecknade i föregående steg för att visa programmet finns i en ny behållare.

![App som distribuerats via Azure Container Instances visas i webbläsare][aci-app-browser]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-app-browser]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapat du en instans för Azure-behållare från en avbildning i en offentlig Docker-hubb-databas. Om du vill försöka skapa en behållare själv och distribuerar den till Azure-Behållarinstanser som använder Azure Container registret fortsätta att Azure Behållarinstanser kursen.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudier](./container-instances-tutorial-prepare-app.md)