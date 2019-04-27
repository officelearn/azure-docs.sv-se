---
title: Hantera IoT Central från Azure portal | Microsoft Docs
description: Hantera IoT Central från Azure-portalen.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: f58d06cd191166f47d864241564bc57019b59132
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707432"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Hantera IoT Central från Azure portal

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program från IoT Central [Programhanterare](https://aka.ms/iotcentral) kan du använda den [Azure-portalen](https://portal.azure.com) att hantera dina program.

## <a name="create-iot-central-applications"></a>Skapa IoT Central-program

Skapa ett program, gå till den [Azure-portalen](https://ms.portal.azure.com) och välj **skapa en resurs** i den huvudsakliga navigeringsmenyn till vänster.

![Hanteringsportalen: nav-menyn](media/howto-manage-iot-central-from-portal/image0.png)

I sökfältet skriver **IoT Central**.

![Hanteringsportalen: sökning](media/howto-manage-iot-central-from-portal/image0a.png)

Välj den **IoT centralt program** radobjekt i sökresultatet.

![Hanteringsportalen: sökresultat](media/howto-manage-iot-central-from-portal/image0b.png)

Välj nu **Skapa**.

![Hanteringsportalen: IoT Central-resurs](media/howto-manage-iot-central-from-portal/image0c.png)

Fyll i alla fält i formuläret. Det här formuläret liknar formuläret du fyller i att skapa program på IoT Central [Programhanterare](https://aka.ms/iotcentral) sidan. Mer information finns i den [skapa ett program med IoT Central](quick-deploy-iot-central.md) Snabbstart.

![Management-portalen: skapa IoT Central-resurs](media/howto-manage-iot-central-from-portal/image1.png)  

När du har fyllt i alla fält, Välj **skapa**.

## <a name="manage-existing-iot-central-applications"></a>Hantera befintliga IoT Central-program

Om du redan har ett Azure IoT Central program som du kan ta bort den eller flytta den till en annan prenumeration eller resursgrupp grupp i Azure-portalen.

> [!NOTE]
> Du kan inte se utvärderingsversion program i Azure-portalen eftersom de inte är associerade med prenumerationen.

För att komma igång, Välj **alla resurser** i den huvudsakliga navigeringsmenyn till vänster. Använd sökrutan för att ange namnet på ditt program för att hitta det i din lista över resurser. Välj sedan IoT Central-programmet som du vill hantera.

![Hanteringsportalen: resurshantering](media/howto-manage-iot-central-from-portal/image2.png)

Gå till programmet genom att välja IoT Central programmets URL.

![Hanteringsportalen: resurshantering](media/howto-manage-iot-central-from-portal/image3.png)

För att flytta programmet till en annan resursgrupp, Välj **ändra** bredvid resursgruppen. På den **flytta resurser** sidan, Välj den resursgrupp som du vill flytta över det här programmet.

![Hanteringsportalen: resurshantering](media/howto-manage-iot-central-from-portal/image4.png)

Om du vill flytta program till en annan prenumeration, Välj den **ändra** länken bredvid prenumerationen. Välj den prenumeration som du vill migrera det här programmet i dialogrutan som visas.

![Hanteringsportalen: resurshantering](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central program från Azure-portalen, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)