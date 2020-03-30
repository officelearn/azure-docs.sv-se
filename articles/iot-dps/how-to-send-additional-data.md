---
title: Så här överför du en nyttolast mellan enhet och Azure Device Provisioning Service
description: I det här dokumentet beskrivs hur du överför en nyttolast mellan enhets- och enhetsetableringstjänst (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246695"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Så här överför du en nyttolast mellan enheten och DPS
Ibland behöver DPS mer data från enheter för att korrekt etablera dem till rätt IoT Hub, och att data måste tillhandahållas av enheten. Vice versa, DPS kan returnera data till enheten för att underlätta klientsidan logik. 

## <a name="when-to-use-it"></a>När du ska använda detta
Den här funktionen kan användas som en förbättring för [anpassad allokering](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Du vill till exempel allokera dina enheter baserat på enhetsmodellen utan mänsklig inblandning. I det här fallet använder du [anpassad allokering](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Du kan konfigurera enheten så att modellinformationen rapporterars som en del av [registreringsenhetens anrop](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). DPS skickar enhetens nyttolast till den anpassade allokeringen webhook. Och din funktion kan bestämma vilken IoT Hub den här enheten ska gå till när den tar emot information om enhetsmodell. På samma sätt, om webhook vill returnera vissa data till enheten, kommer det att skicka data tillbaka som en sträng i webhook svar.  

## <a name="device-sends-data-payload-to-dps"></a>Enheten skickar datanyttolast till DPS
När enheten skickar ett [registeranordningsanrop](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) till DPS kan registreringsanropet förbättras så att det tar andra fält i brödtexten. Kroppen ser ut så här: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS returnerar data till enheten
Om den anpassade allokeringsprincipen webhook vill returnera vissa data till enheten, kommer det att skicka tillbaka data som en sträng i webhook-svaret. Ändringen finns i nyttolastavsnittet nedan. 
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
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK-support
Den här funktionen är tillgänglig i C,C#, JAVA och Node.js-klienten [SDK:er](https://docs.microsoft.com/azure/iot-dps/).  

## <a name="next-steps"></a>Nästa steg
* Utveckla med azure [IoT SDK]( https://github.com/Azure/azure-iot-sdks) för Azure IoT Hub och Azure IoT Hub Device Provisioning Service
