---
title: Hantera IoT Central från Azure-portalen | Microsoft-dokument
description: I den här artikeln beskrivs hur du skapar och hanterar dina IoT Central-program från Azure-portalen.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: e7efda8efa27044168386e3ebbc557bf7fb74e8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157933"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Hantera IoT Central från Azure-portalen

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program på Azure [IoT Central application manager-webbplatsen](https://aka.ms/iotcentral) kan du använda [Azure-portalen](https://portal.azure.com) för att hantera dina program.

## <a name="create-iot-central-applications"></a>Skapa IoT Central-program

Om du vill skapa ett program navigerar du till [Azure-portalen](https://ms.portal.azure.com) och väljer **Skapa en resurs**.

Skriv *IoT Central* **i Sök i fältet Marknad för marknadsplats:**

![Hanteringsportal: sök](media/howto-manage-iot-central-from-portal/image0a1.png)

Välj panelen **IoT Central Application** i sökresultaten:

![Management Portal: sökresultat](media/howto-manage-iot-central-from-portal/image0b1.png)

Välj nu **Skapa:**

![Hanteringsportal: IoT Central resurs](media/howto-manage-iot-central-from-portal/image0c1.png)

Fyll i alla fält i formuläret . Det här formuläret liknar det formulär du fyller i för att skapa program på [Azure IoT Central application](https://aka.ms/iotcentral) manager-webbplatsen. Mer information finns i snabbstarten [Skapa ett IoT Central-program.](quick-deploy-iot-central.md)

![Skapa IoT Central-formulär](media/howto-manage-iot-central-from-portal/image6a.png)

**Plats** är den [geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill skapa ditt program. Vanligtvis bör du välja den plats som är fysiskt närmast dina enheter för att få optimal prestanda. Azure IoT Central är för närvarande tillgängligt i geografin **Australien,** **Asien och Stillahavsområdet,** **Europa,** **USA,** **Storbritannien**och **Japan.** När du har valt en plats kan du inte flytta programmet till en annan plats senare.

När du har fyllt i alla fält väljer du **Skapa**.

## <a name="manage-existing-iot-central-applications"></a>Hantera befintliga IoT Central-program

Om du redan har ett Azure IoT Central-program kan du ta bort det eller flytta det till en annan prenumeration eller resursgrupp i Azure-portalen.

> [!NOTE]
> Du kan inte se program som skapats på den kostnadsfria prisplanen i Azure-portalen eftersom de inte är associerade med din prenumeration.

Du kan komma igång genom att välja **Alla resurser** i portalen. Välj **Visa dolda typer** och börja skriva namnet på programmet i Filtrera efter **namn** för att hitta det. Välj sedan det IoT Central-program som du vill hantera.

Om du vill navigera till programmet väljer du **URL:en för IoT Central Application:**

![Hanteringsportal: resurshantering](media/howto-manage-iot-central-from-portal/image3.png)

Om du vill flytta programmet till en annan resursgrupp väljer du **ändra** bredvid resursgruppen. På sidan **Flytta resurser** väljer du den resursgrupp som du vill flytta det här programmet till:

![Hanteringsportal: resurshantering](media/howto-manage-iot-central-from-portal/image4a.png)

Om du vill flytta programmet till en annan prenumeration väljer du **ändra** bredvid prenumerationen. På sidan **Flytta resurser** väljer du den prenumeration som du vill flytta det här programmet till:

![Hanteringsportal: resurshantering](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central-program från Azure-portalen, här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)