---
title: ta med fil
description: ta med fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 935e2b9e861a889bef48c1d7ba119ab694cddfb3
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094009"
---
## <a name="create-a-device-identity"></a>Skapa en enhetsidentitet

I det här avsnittet ska du använda den [Azure-portalen](https://portal.azure.com) att skapa en enhetsidentitet i identitetsregistret i IoT hub. En enhet kan inte ansluta till IoT Hub om den inte har en post i identitetsregistret. Mer information finns i avsnittet ”identitetsregistret” i den [utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md) använder den **IoT-enheter** panelen i portalen för att generera ett unikt enhets-ID och nyckel för din enhet för att identifiera själva till IoT Hub. Enhets-ID:n är skiftlägeskänsliga.

1. Logga in på [Azure-portalen](https://portal.azure.com)

1. Välj **alla resurser** och hitta din IoT hub-resurs.

1. När din IoT hub-resurs öppnas, klickar du på den **IoT-enheter** för och klicka sedan på **Lägg till** högst upp. 

    ![Skapa en enhetsidentitet i portalen](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

1. Ange ett namn för den nya enheten, till exempel **myDeviceId**, och klicka på **spara**. Den här åtgärden skapar en ny enhetsidentitet för IoT-hubben.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Lägg till en ny enhet](./media/iot-hub-get-started-create-device-identity-portal/add-device.png)

1. I listan över enheter klickar du på den nyligen skapade enheten och kopiera den **anslutningssträngen---primärnyckel** för senare användning.

    ![Enhetens anslutningssträng](./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png)

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till IoT Hub. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
