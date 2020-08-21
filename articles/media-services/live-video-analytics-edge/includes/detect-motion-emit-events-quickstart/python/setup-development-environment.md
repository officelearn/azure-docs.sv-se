---
ms.openlocfilehash: be983a643b45847d2a44db018b1383aa56ab2302
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691198"
---
1. Klona lagrings platsen från den här platsen: https://github.com/Azure-Samples/live-video-analytics-iot-edge-python .
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
    