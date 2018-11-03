---
title: Hantera IoT Central från Azure portal | Microsoft Docs
description: Hantera IoT Central från Azure-portalen.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 08/30/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 2977c3d1656bacf8ba8d34080641ea6c5cdf5bbe
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957141"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Hantera IoT Central från Azure portal 
Förutom att skapa och hantera IoT Central-program från IoT Central-webbplatsen, kan du också hantera IoT Central från Azure-portalen. Den här artikeln beskriver vad som är möjligt och gör så.

## <a name="create-iot-central-applications"></a>Skapa IoT Central-program
Om du vill skapa ett nytt program, navigerar du till den [Azure-portalen](https://ms.portal.azure.com) och klicka på ”Skapa en resurs” i den huvudsakliga navigeringsmenyn till vänster. 

![Hanteringsportalen: nav-menyn](media\howto-manage-iot-central-from-portal\image0.png)

I fältet söktyp i termen ”IoT Central”.

![Hanteringsportalen: sökning](media\howto-manage-iot-central-from-portal\image0a.png)

Klicka på IoT Central-App radartikel i sökresultatet.

![Hanteringsportalen: sökresultat](media\howto-manage-iot-central-from-portal\image0b.png)

Klicka på knappen ”Skapa” om du vill se det formulär som du behöver fylla i.

![Hanteringsportalen: IoT Central-resurs](media\howto-manage-iot-central-from-portal\image0c.png)

Fyll i alla fält i formuläret. Det här formuläret liknar formuläret som du behöver fylla så här skapar du program från IoT Central-webbplatsen. Om du vill veta mer om hur du fyller i alla fält kan ta en titt på [skapa ett program med IoT Central](quick-deploy-iot-central.md) Snabbstart. 

![Management-portalen: skapa IoT Central-resurs](media\howto-manage-iot-central-from-portal\image1.png)  

När du fyller i alla fält kan du klicka på knappen ”Skapa”.

## <a name="manage-existing-iot-central-applications"></a>Hantera befintliga IoT Central-program
Om du redan har ett Azure IoT Central program kan du radera den, flytta den till en annan prenumeration eller resursgrupp grupp i Azure-portalen. Du kan inte se utvärderingsversion program i Azure-portalen eftersom ingen prenumeration säkerhetskopierar dessa prover.

Kom igång genom att klicka på ”alla resurser” i den huvudsakliga navigeringsmenyn till vänster. Använd sökrutan för att ange namnet på ditt program och hitta det i din lista över resurser. Klicka sedan på IoT Central-programmet som du vill hantera.

![Hanteringsportalen: resurshantering](media\howto-manage-iot-central-from-portal\image2.png)

Gå till programmet genom att klicka på IoT Central programmets URL.

![Hanteringsportalen: resurshantering](media\howto-manage-iot-central-from-portal\image3.png)

Om du vill flytta program till en annan resursgrupp, klickar du på den **ändra** länken bredvid resursgruppen. Välj den resursgrupp som du vill migrera det här programmet i dialogrutan som visas.

![Hanteringsportalen: resurshantering](media\howto-manage-iot-central-from-portal\image4.png)

Om du vill flytta program till en annan prenumeration, klickar du på den **ändra** länken bredvid prenumerationen. Välj den prenumeration som du vill migrera det här programmet i dialogrutan som visas.

![Hanteringsportalen: resurshantering](media\howto-manage-iot-central-from-portal\image5.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central program från Azure-portalen, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)