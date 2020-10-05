---
ms.openlocfilehash: e46a56742ab8b98c53c1cd05e840e8ad4b8a73da
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682308"
---
I steget [generera och distribuera IoT Edge distributions manifestet](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) i Visual Studio Code, expanderar du noden **lva-Sample-Device** i **Azure IoT Hub** (i det nedre vänstra avsnittet). Du bör se följande moduler distribuerade:

* Modulen live video analys med namnet `lvaEdge`
* `rtspsim`Modulen, som simulerar en RTSP-server som fungerar som källa för en Live-videofeed

  ![Moduler](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Om du använder en egen Edge-enhet i stället för den som har skapats av vårt installations skript, går du till din Edge-enhet och kör följande kommandon med **administratörs behörighet**för att hämta och lagra exempel video filen som används för den här snabb starten:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
