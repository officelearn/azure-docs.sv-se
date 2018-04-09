---
title: ta med fil
description: ta med fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/05/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b08bfcd4cb9e85f9e682efe0f599b6dd88897962
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
## <a name="create-a-device-identity"></a>Skapa en enhetsidentitet

I det här avsnittet kan du använda den [Azure-portalen] [ lnk-azure-portal] att skapa en enhetsidentitet i identitetsregistret i IoT-hubb. En enhet kan inte ansluta till IoT Hub om den inte har en post i identitetsregistret. Mer information finns i avsnittet om identitetsregistret i [utvecklarhandboken för IoT Hub][lnk-devguide-identity]. Använd den **IoT-enheter** panelen i portalen för att generera ett unikt enhets-ID och nyckel för din enhet du använder för att identifiera sig för IoT-hubb. Enhets-ID:n är skiftlägeskänsliga.

1. Kontrollera att du är inloggad på den [Azure-portalen][lnk-azure-portal].

1. I Jumpbar klickar du på **alla resurser** och hitta din IoT-hubb-resurs.

    ![Navigera till din Iot-hubb][img-find-iothub]

1. När din IoT-hubb resurs öppnas, klickar du på den **IoT-enheter** verktyget och klicka sedan på **Lägg till** längst upp. Ange namn för den nya enheten som **myDeviceId**, och klicka på **spara**.

    ![Skapa enhetsidentitet i portalen][img-create-device]

   Den här åtgärden skapar en ny enhetsidentitet för din IoT-hubb.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. I den **IoT-enheter**'s listan över enheter klickar du på den nyligen skapade enheten och anteckna den **anslutningssträngen---primärnyckel**.

    ![Anslutningssträngen för enhet][img-connection-string]

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till IoT Hub. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [utvecklarhandboken för IoT Hub][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

