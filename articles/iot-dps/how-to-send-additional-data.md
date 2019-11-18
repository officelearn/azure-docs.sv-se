---
title: Så här överför du ytterligare data mellan enhets-och Azure Device Provisioning-tjänsten
description: Det här dokumentet beskriver hur du överför ytterligare data mellan enhet och DPS
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 90402dd6fbe19811b5bb6d5ac0fbdd984b71fd33
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123177"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>Så här överför du ytterligare data mellan enhet och DPS
Ibland behöver DPS mer data från enheter för att kunna etablera dem till rätt IoT Hub, och dessa data måste skickas från enheten. Vice versa kan DPS returnera data till enheten för att under lätta klient sidans logik. 

## <a name="when-to-use-it"></a>När du ska använda den
Den här funktionen kan användas som en förbättring för [anpassad allokering](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Till exempel vill du allokera dina enheter baserat på enhets modellen utan mänsklig inblandning. I det här fallet ska du använda [anpassad allokering](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Du kan konfigurera enheten för att rapportera modell informationen som en del av det [registrerade enhets anropet](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). DPS skickar enhetens information till en anpassad allokerings-webhook. Och din funktion kan bestämma vilken IoT Hub enheten ska gå till när den tar emot information om enhets modellen. Om webhook vill returnera data till enheten skickar den data tillbaka som en sträng i webbhook-svaret.  

## <a name="device-sends-data-to-dps"></a>Enheten skickar data till DPS
När din enhet skickar ett [register för enhets anrop](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) till DPS kan register samtalet förbättras för att ta andra fält i bröd texten. Texten ser ut ungefär så här: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS returnerar data till enheten
Om webhooken för den anpassade allokeringsregeln vill returnera data till enheten, kommer de att skickas tillbaka till en sträng i webhook-svaret. Ändringen är i avsnittet returnData nedan. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK-stöd
Den här funktionen är tillgänglig i klient C#- [SDK](https://docs.microsoft.com/azure/iot-dps/): er för C, Java och Node. js.  

## <a name="next-steps"></a>Nästa steg
* Utveckla med [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) för Azure IoT Hub och Azure IoT Hub Device Provisioning service
