---
title: Hantera IoT Central från Azure Portal | Microsoft Docs
description: Hantera IoT Central från Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 37e016da25be5fad86bc57e3ddf42458852a14bd
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950280"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Hantera IoT Central från Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program på webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) kan du använda [Azure Portal](https://portal.azure.com) för att hantera dina program.

## <a name="create-iot-central-applications"></a>Skapa IoT Central program

Om du vill skapa ett program går du till [Azure Portal](https://ms.portal.azure.com) och väljer **skapa en resurs** i huvud fönstret till vänster.

![Hanterings Portal: navigerings meny](media/howto-manage-iot-central-from-portal/image0.png)

I Sök fältet skriver du **IoT Central**.

![Hanterings Portal: Sök](media/howto-manage-iot-central-from-portal/image0a1.png)

Välj raden för **IoT Central programmet** -objektet i Sök resultaten.

![Hanteringsportal: Sök Resultat](media/howto-manage-iot-central-from-portal/image0b1.png)

Välj nu **Skapa**.

![Hanterings Portal: IoT Central resurs](media/howto-manage-iot-central-from-portal/image0c1.png)

Fyll i alla fält i formuläret. Det här formuläret liknar det formulär som du fyller i för att skapa program på webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) . Mer information finns i snabb starten för att [skapa ett IoT Central-program](quick-deploy-iot-central.md) .

**Plats** är den fysiska plats eller det [geografiskt](https://azure.microsoft.com/global-infrastructure/geographies/) område där du vill skapa ditt program. Normalt bör du välja den plats som är fysiskt närmast dina enheter för att få optimala prestanda. Du kan se de regioner där Azure IoT Central är tillgängligt på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) . När du har valt en plats kan du inte flytta programmet till en annan plats senare.

> [!NOTE]
> För **hands versions** mal len är för närvarande endast tillgänglig i regionerna **Nord Europa** och **centrala USA** .

![Hanterings Portal: skapa IoT Central resurs](media/howto-manage-iot-central-from-portal/image1a.png)  

När du har fyllt i alla fält väljer du **skapa**.

## <a name="manage-existing-iot-central-applications"></a>Hantera befintliga IoT Central-program

Om du redan har ett Azure IoT Central-program kan du ta bort det eller flytta det till en annan prenumeration eller resurs grupp i Azure Portal.

> [!NOTE]
> Du kan inte se test program i Azure Portal eftersom de inte är associerade med din prenumeration.

Kom igång genom att välja **alla resurser** i huvud fönstret till vänster. Använd sökrutan för att ange namnet på programmet för att hitta det i listan över resurser. Välj sedan det IoT Central program som du vill hantera.

![Hanterings Portal: resurs hantering](media/howto-manage-iot-central-from-portal/image2a.png)

Välj IoT Central programmets URL för att navigera till programmet.

![Hanterings Portal: resurs hantering](media/howto-manage-iot-central-from-portal/image3.png)

Om du vill flytta programmet till en annan resurs grupp väljer du **ändra** bredvid resurs gruppen. På sidan **Flytta resurser** väljer du den resurs grupp som du vill migrera programmet till.

![Hanterings Portal: resurs hantering](media/howto-manage-iot-central-from-portal/image4a.png)

Om du vill flytta programmet till en annan prenumeration väljer du länken **ändra** bredvid prenumerationen. Välj den prenumeration som du vill migrera det här programmet till i dialog rutan som visas.

![Hanterings Portal: resurs hantering](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central-program från Azure Portal, är det här det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)