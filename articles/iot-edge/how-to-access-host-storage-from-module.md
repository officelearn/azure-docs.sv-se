---
title: Använd lokal lagring av IoT Edge-enheten från en modul – Azure IoT Edge | Microsoft-dokument
description: Använd miljövariabler och skapa alternativ för att aktivera modulåtkomst till lokal lagring av IoT Edge-enheten.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434526"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Ge moduler åtkomst till en enhets lokala lagring

Förutom att lagra data med Hjälp av Azure-lagringstjänster eller i enhetens lagring av behållare kan du också ägna lagring på själva värd-IoT Edge-enheten för förbättrad tillförlitlighet, särskilt när du använder offline.

## <a name="link-module-storage-to-device-storage"></a>Länkmodullagring till enhetslagring

Om du vill aktivera en länk från modullagring till lagring på värdsystemet skapar du en miljövariabel för modulen som pekar på en lagringsmapp i behållaren. Använd sedan skapa alternativ för att binda lagringsmappen till en mapp på värddatorn.

Om du till exempel vill aktivera IoT Edge-hubben för att lagra meddelanden i enhetens lokala lagring och hämta dem senare, kan du konfigurera miljövariabler och skapa alternativ i Azure-portalen i avsnittet **Körningsinställningar.**

1. För både IoT Edge-hubben och IoT Edge-agenten lägger du till en miljövariabel som kallas **storageFolder** som pekar på en katalog i modulen.
1. För både IoT Edge-hubben och IoT Edge-agenten lägger du till bindningar för att ansluta en lokal katalog på värddatorn till en katalog i modulen. Ett exempel:

   ![Lägga till skapa alternativ och miljövariabler för lokal lagring](./media/how-to-access-host-storage-from-module/offline-storage.png)

Du kan också konfigurera den lokala lagringen direkt i distributionsmanifestet. Ett exempel:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Ersätt `<HostStoragePath>` `<ModuleStoragePath>` och med din värd- och modullagringssökväg. båda värdena måste vara en absolut sökväg.

På ett Linux-system `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` betyder det till exempel att katalogen **/etc/iotedge/storage** på ditt värdsystem mappas till katalogen **/iotedge/storage/** i behållaren. På ett Windows-system, `"Binds":["C:\\temp:C:\\contemp"]` som ett annat exempel, betyder katalogen **\\C: temp** på ditt värdsystem mappas till katalogen **C:\\contemp** i behållaren.

På Linux-enheter kontrollerar du dessutom att användarprofilen för din modul har de läs-, skriv- och körningsbehörigheter som krävs för att vara värdsystemkatalogen. Om du återgår till det tidigare exemplet med att IoT Edge-hubben kan lagra meddelanden i enhetens lokala lagring måste du bevilja behörigheter till användarprofilen UID 1000. (IoT Edge-agenten fungerar som root, så det behöver inte ytterligare behörigheter.) Det finns flera sätt att hantera katalogbehörigheter `chown` för Linux-system, `chmod` bland annat genom att använda för att ändra katalogägaren och sedan ändra behörigheterna, till exempel:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Du hittar mer information om hur du skapar alternativ från [docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Nästa steg

Ytterligare ett exempel på åtkomst till värdlagring från en modul finns i [Lagra data på kanten med Azure Blob Storage på IoT Edge](how-to-store-data-blob.md).
