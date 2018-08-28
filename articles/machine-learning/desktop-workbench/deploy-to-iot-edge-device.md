---
title: Distribuera en Azure Machine Learning-modell till en Azure IoT Edge-enhet | Microsoft Docs
description: Det här dokumentet beskriver hur Azure Machine Learning-modeller kan distribueras till Azure IoT Edge-enheter.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 24d3cf0c4b1a1283e7a6a7f61f0bb23dae7143d5
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43113005"
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Distribuera en Azure Machine Learning-modell till en Azure IoT Edge-enhet

Azure Machine Learning-modeller kan vara behållare som Docker-baserade webbtjänster. Azure IoT Edge kan du distribuera behållare via en fjärranslutning till enheter. Använd de här tjänsterna tillsammans för att köra dina modeller kant för kortare svarstider och mindre dataöverföring. 

Ytterligare skript och instruktioner finns i den [AI-verktyg för Azure IoT Edge](http://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Operationalisera modellen

Azure IoT Edge-moduler är baserade på behållaravbildningar. Om du vill distribuera Machine Learning-modell till en IoT Edge-enhet, måste du skapa en Docker-avbildning.

Driftsätta modellen genom att följa instruktionerna i [Azure Machine Learning Model Management distribution av webbtjänster](model-management-service-deploy.md) att skapa en Docker-avbildning med din modell.

## <a name="deploy-to-azure-iot-edge"></a>Distribuera till Azure IoT Edge

När du har en avbildning av din modell kan distribuera du det till alla Azure IoT Edge-enheter. Alla Machine Learning-modeller kan köras på IoT Edge-enheter. 

### <a name="set-up-an-iot-edge-device"></a>Konfigurera en IoT Edge-enhet

Använd Azure IoT Edge-dokumentation för att förbereda en enhet. 

1. [Registrera en enhet med Azure IoT Hub](../../iot-edge/how-to-register-device-portal.md). Utdata från den här processer är en anslutningssträng som du kan använda för att konfigurera den fysiska enheten. 
2. Installera IoT Edge-körningen på den fysiska enheten och konfigurera den med en anslutningssträng. Du kan installera runtime på [Windows](../../iot-edge/how-to-install-iot-edge-windows-with-windows.md) eller [Linux](../../iot-edge/how-to-install-iot-edge-linux.md) enheter.  


### <a name="find-the-machine-learning-container-image-location"></a>Hitta Machine Learning behållarens Bildplats
Behöver du platsen för din Machine Learning-behållaravbildning. Så här hittar du behållarens plats för avbildningen:

1. Logga in på [Azure Portal](http://portal.azure.com/).
2. I den **Azure Container Registry**, Välj registret som du vill granska.
3. I registret, klickar du på **databaser** att se en lista över alla databaser och deras bilder.

När du tittar på ditt behållarregister i Azure-portalen att hämta autentiseringsuppgifter för container-registret. Dessa autentiseringsuppgifter måste tilldelas till IoT Edge-enhet så att den kan hämta avbildningen från ditt privata register. 

1. I behållarregistret klickar du på **åtkomstnycklar**. 
2. **Aktivera** administratörsanvändaren, om det inte redan. 
3. Spara värdena för **inloggningsserver**, **användarnamn**, och **lösenord**. 

### <a name="deploy-the-container-image-to-your-device"></a>Distribuera behållaravbildningen till din enhet

Med behållaravbildningen och autentiseringsuppgifter för container-registret är du redo att distribuera maskininlärningsmodell till din IoT Edge-enhet. 

Följ instruktionerna i [distribuera IoT Edge-moduler från Azure portal](../../iot-edge/how-to-deploy-modules-portal.md) att starta din modell på din IoT Edge-enhet. 











