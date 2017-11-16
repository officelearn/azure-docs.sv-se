---
title: Distribuera en Azure Machine Learning-modell till en Azure IoT-enhet | Microsoft Docs
description: "Det här dokumentet beskrivs hur Azure Machine Learning-modeller kan distribueras till Azure IoT-gränsenheterna."
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/10/2017
ms.openlocfilehash: 924766aee7486f0cf5006dd89d5d77b83753833b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Distribuera och Azure Machine Learning modell till en Azure IoT-enhet

Alla Azure Machine Learning-modeller av som Docker-baserade webbtjänster kan också köra på Azure IoT-gränsenheterna. Ytterligare skript och instruktioner finns i den [AI Toolkit för Azure IoT kant](http://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Operationalisera modellen
Driftsätta modellen genom att följa instruktionerna i [Azure Machine Learning modellen Management Web Service-distributionen](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy) att skapa en Docker-avbildning med din modell.

## <a name="deploy-to-azure-iot-edge"></a>Distribuera till Azure IoT kant
Azure IoT-Edge flyttar molnet analyser och anpassad affärslogik till enheter. Alla Machine Learning-modeller kan köras på IoT Edge-enheter. Dokumentation för att ställa in en IoT-enhet och skapa en distribution finns på [aka.ms/azure-iot-edge-doc](https://aka.ms/azure-iot-edge-doc).

Följande är ytterligare saker att Observera.

### <a name="add-registry-credentials-to-the-edge-runtime-on-your-edge-device"></a>Lägga till registret autentiseringsuppgifter körningsmiljön kanten på Edge-enhet
Lägg till autentiseringsuppgifterna i registret på datorn där du kör IoT kant så att körningen kan få åtkomst till pull-behållaren.

För Windows, kör du följande kommando:
```cmd/sh
iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```
Kör följande kommando för Linux:
```cmd/sh
sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```

### <a name="find-the-machine-learning-container-image-location"></a>Hitta bildplatsen Machine Learning-behållare
Du måste platsen för Machine Learning behållaren avbildningen. Hitta bildplatsen behållare:

1. Logga in på den [Azure-portalen](http://portal.azure.com/).
2. I den **Azure Container registret**, Välj registret som du vill kontrollera.
3. I registret, klickar du på **databaser** att se en lista över alla databaser och deras bilder.













