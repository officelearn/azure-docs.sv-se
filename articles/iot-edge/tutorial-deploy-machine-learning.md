---
title: Distribuera Azure Machine Learning med Azure IoT-Edge | Microsoft Docs
description: Distribuera Azure Machine Learning som en modul till en insticksenhet
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e061e599f365bf3d343cb59b8dc6a61e06627517
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Distribuera Azure Machine Learning som en gräns för IoT-modul - förhandsgranskning

Du kan använda IoT kant-moduler för att distribuera kod som implementerar affärslogiken direkt till IoT Edge-enheter. Den här självstudiekursen vägleder dig genom att distribuera en Azure Machine Learning-modul som beräknar när en enhet inte baserat på sensordata på simulerade IoT gränsenheten som du skapade i distribuera Azure IoT kanten på en simulerad enhet på [Windows] [ lnk-tutorial1-win] eller [Linux] [ lnk-tutorial1-lin] självstudier. Lär dig att: 

> [!div class="checklist"]
> * Distribuera en Azure Machine Learning-modul till din IoT-Edge-enhet
> * Visa genererade data

När du vill använda din egen [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) modellen i din lösning kommer du att [distribuera en modell](https://aka.ms/aml-iot-edge-doc) för IoT kant och värden i registret för en behållare som [Azure Container registret](../container-registry/index.yml) eller Docker.

## <a name="prerequisites"></a>Krav

* Azure IoT gränsenheten som du skapade i Snabbstart eller första självstudierna.
* IoT-hubb anslutningssträngen för IoT-hubb som din IoT insticksenhet ansluter till.
* Azure ML-behållaren

## <a name="create-the-azure-ml-container"></a>Skapa Azure ML-behållare
Om du vill skapa din Azure ML-behållare, följ instruktionerna i den [AI toolkit för Azure IoT kant](https://aka.ms/aml-iot-edge-anomaly-detection).

## <a name="run-the-solution"></a>Köra lösningen

1. På den [Azure-portalen](https://portal.azure.com), navigera till din IoT-hubb.
1. Gå till **IoT kant (förhandsgranskning)** och välj IoT-Edge-enhet.
1. Välj **ange moduler**.
1. Välj **lägga till kant för IoT-modulen**.
1. I den **namn** anger `tempSensor`.
1. I den **avbildningen URI** anger `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
1. Lämna de andra inställningarna oförändrade och välj **spara**.
1. Stanna kvar på den **Lägg till moduler** steg, Välj **lägga till kant för IoT-modulen** igen.
1. I den **namn** , ange namnet på behållaren som du angav i föregående avsnitt. Referera till den [AI toolkit för Azure IoT kant](https://aka.ms/aml-iot-edge-anomaly-detection) för hjälp med att hitta namnet.
1. I den **bild** anger avbildningen URI: N för den behållare som du angav i föregående avsnitt. Referera till den [AI toolkit för Azure IoT kant](https://aka.ms/aml-iot-edge-anomaly-detection) för hjälp med att hitta avbildningen.
1. Klicka på **Spara**.
1. I den **Lägg till moduler** , klicka på **nästa**.
1. Uppdatera vägar för:
1. I den **ange vägar** steg, kopiera JSON nedan i textrutan. Moduler publicera alla meddelanden i Edge-körningsmiljön. Deklarativ regler i körningsmiljön definiera där dessa meddelanden. I den här kursen behöver du två vägar. Första vägen transporter meddelanden från temperatursensor till datorn lärmodulen via slutpunkten ”amlInput”, vilket är den slutpunkt som använder alla moduler i Azure Machine Learning. Andra vägen transporter meddelanden från machine learning-modulen till IoT-hubb. I den här vägen '' amlOutput'' slutpunkten med alla Azure Machine Learning-moduler som utdata och '' uppströms$ '' är särskilda mål som talar om kant-hubb för att skicka meddelanden till IoT-hubb. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Klicka på **Nästa**. 
1. Klicka på '' skicka '' i steget '' granska mallen ''. 
1. Gå tillbaka till informationssidan och klickar på '' uppdatera ''.  Du bör se den nya '' machinelearningmodule'' körs tillsammans med modulen '' tempSensor'' och '' IoT kant runtime''.

## <a name="view-generated-data"></a>Visa genererade data

 I VS-kod, använder den **visa | Kommandot paletten... | IoT: Övervaka D2C startmeddelanden** menykommandot du övervakar data som inkommer på IoT-hubben. 

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en IoT-Edge-modul som tillhandahålls av Azure Machine Learning i den här självstudiekursen. Du kan fortsätta in på någon av följande självstudiekurser vill veta mer om andra sätt att Azure IoT gräns kan du göra data till affärsinsikter kant.

> [!div class="nextstepaction"]
> [Distribuera en Azure-funktion som en modul](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
