---
ms.openlocfilehash: ea30fed81a7f97b6577f41692274036d2714e0c1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691108"
---
1. Klona lagrings platsen från den här platsen: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Öppna mappen där lagrings platsen har hämtats i Visual Studio Code.
1. I Visual Studio Code går du till mappen *src/Cloud-to-Device-console-app* . Där skapar du en fil och namnger den *appsettings.js*. Den här filen innehåller de inställningar som krävs för att köra programmet.
1. Kopiera innehållet från *~/clouddrive/lva-Sample/-appsettings.jspå* filen som du skapade tidigare i den här snabb starten.

    Texten bör se ut som följande utdata.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Gå till mappen *src/Edge* och skapa en fil med namnet *. kuvert*.
1. Kopiera innehållet i */clouddrive/lva-Sample/Edge-Deployment/.env* -filen. Texten bör se ut som i följande kod.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    