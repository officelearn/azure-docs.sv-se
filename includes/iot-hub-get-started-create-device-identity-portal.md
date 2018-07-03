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
ms.openlocfilehash: 1df3e188b71b8fa2d5223bad8bc5914513e26286
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "34371209"
---
## <a name="create-a-device-identity"></a>Skapa en enhetsidentitet

I det här avsnittet ska du använda den [Azure-portalen] [ lnk-azure-portal] att skapa en enhetsidentitet i identitetsregistret i IoT hub. En enhet kan inte ansluta till IoT Hub om den inte har en post i identitetsregistret. Mer information finns i avsnittet om identitetsregistret i [utvecklarhandboken för IoT Hub][lnk-devguide-identity]. Använd den **IoT-enheter** panelen i portalen för att generera ett unikt enhets-ID och nyckel för din enhet för att identifiera sig själv till IoT Hub. Enhets-ID:n är skiftlägeskänsliga.

1. Logga in på [Azure Portal][lnk-azure-portal].

1. Välj **alla resurser** och hitta din IoT hub-resurs.

1. När din IoT hub-resurs öppnas, klickar du på den **IoT-enheter** för och klicka sedan på **Lägg till** högst upp. 

    ![Skapa en enhetsidentitet i portalen][img-add-device]

1. Ange ett namn för den nya enheten, till exempel **myDeviceId**, och klicka på **spara**. Den här åtgärden skapar en ny enhetsidentitet för IoT-hubben.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Lägg till en ny enhet][img-create-device]

1. I listan över enheter klickar du på den nyligen skapade enheten och kopiera den **anslutningssträngen---primärnyckel** för senare användning.

    ![Enhetens anslutningssträng][img-connection-string]

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till IoT Hub. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [utvecklarhandboken för IoT Hub][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-add-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png
[img-create-device]:./media/iot-hub-get-started-create-device-identity-portal/add-device.png

<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

