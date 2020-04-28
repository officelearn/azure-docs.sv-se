---
title: Använd IoT Edge lokal lagrings enhet från en modul – Azure IoT Edge | Microsoft Docs
description: Använd miljövariabler och skapa alternativ för att aktivera modul åtkomst till IoT Edge enhetens lokala lagring.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75434526"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Ge moduler åtkomst till en enhets lokala lagring

Förutom att lagra data med hjälp av Azure Storage-tjänster eller i enhetens behållar lagring kan du också dedikera lagring på värden IoT Edge själva enheten för bättre tillförlitlighet, särskilt när du arbetar offline.

## <a name="link-module-storage-to-device-storage"></a>Länka modulen lagring till enhets lagring

Om du vill aktivera en länk från modul lagring till lagrings platsen på värd systemet skapar du en miljö variabel för modulen som pekar på en lagringsmapp i behållaren. Använd sedan alternativen skapa för att binda den lagringsmappen till en mapp på värddatorn.

Om du till exempel vill aktivera IoT Edge hubben för att lagra meddelanden i enhetens lokala lagring och hämta dem senare, kan du konfigurera miljövariablerna och skapa-alternativen i Azure Portal i avsnittet **körnings inställningar** .

1. För både IoT Edge hubb och IoT Edge agent lägger du till en miljö variabel med namnet **storageFolder** som pekar på en katalog i modulen.
1. För både IoT Edge hubb och IoT Edge agent lägger du till bindningar för att ansluta en lokal katalog på värddatorn till en katalog i modulen. Ett exempel:

   ![Lägg till skapande alternativ och miljövariabler för lokal lagring](./media/how-to-access-host-storage-from-module/offline-storage.png)

Eller så kan du konfigurera den lokala lagringen direkt i distributions manifestet. Ett exempel:

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

Ersätt `<HostStoragePath>` och `<ModuleStoragePath>` med din lagrings Sök väg för värd och modul; båda värdena måste vara en absolut sökväg.

I ett Linux- `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` system innebär till exempel att katalogen **/etc/iotedge/Storage** i värd systemet är mappad till katalogen **/iotedge/Storage/** i behållaren. I ett Windows-system `"Binds":["C:\\temp:C:\\contemp"]` innebär ett annat exempel katalogen **c:\\Temp** på värd systemet mappas till katalogen **c:\\Temp** i behållaren.

På Linux-enheter måste du dessutom se till att användar profilen för modulen har behörighet att läsa, skriva och köra till värd system katalogen. Genom att gå tillbaka till det tidigare exemplet för att aktivera IoT Edge hubb för att lagra meddelanden i enhetens lokala lagring måste du bevilja behörighet till användar profilen, UID 1000. (IoT Edge agenten fungerar som rot, så den behöver inte fler behörigheter.) Det finns flera sätt att hantera katalog behörigheter på Linux-system, inklusive `chown` att använda för att ändra katalogens `chmod` ägare och sedan ändra behörigheter, till exempel:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Du hittar mer information om att skapa alternativ från [Docker-dokument](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Nästa steg

Ett ytterligare exempel på hur du kan komma åt värd lagring från en modul finns i [lagra data på gränsen med Azure Blob Storage på IoT Edge](how-to-store-data-blob.md).
