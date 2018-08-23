---
title: Hur du använder Azure CLI och IoT-tillägget för att hantera IoT Hub Device Provisioning-tjänsten | Microsoft Docs
description: Lär dig hur du använder Azure CLI och IoT-tillägget för att hantera IoT Hub Device Provisioning-tjänsten
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 70ce30bdc5a12aec198a2bb1b78c9bdfa8a18882
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42059259"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Hur du använder Azure CLI och IoT-tillägget för att hantera IoT Hub Device Provisioning-tjänsten

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är en öppen källkod för flera plattformar kommandoradsverktyget för att hantera Azure-resurser som IoT Edge. Azure CLI är tillgänglig på Windows, Linux och MacOS. Azure CLI kan du hantera resurser i Azure IoT Hub Device Provisioning service-instanser och länkade hubbar rutan.

IoT-tillägget får Azure CLI med funktioner som enhetshantering och full IoT Edge-funktion.

I den här självstudien får slutföra du först konfigurationen av Azure CLI och IoT-tillägget. Sedan får du lära dig hur du kör CLI-kommandon för att utföra grundläggande åtgärder för Device Provisioning-tjänsten. 

## <a name="installation"></a>Installation 

### <a name="step-1---install-python"></a>Steg 1 – Installera Python

[Python 2.7x eller Python 3.x](https://www.python.org/downloads/) krävs.

### <a name="step-2---install-azure-cli"></a>Steg 2 – installera Azure CLI

Följ den [installationsinstruktionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) att konfigurera Azure CLI i din miljö. Azure CLI-version måste minst vara 2.0.24 eller senare. Validera med `az –version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. Ett enkelt sätt att installera i Windows är att hämta och installera [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Steg 3 – Installera IoT-tillägget

I [IoT-tilläggets Viktigt-fil](https://github.com/Azure/azure-iot-cli-extension) beskrivs olika sätt att installera tillägget. Det enklaste sättet är att köra `az extension add --name azure-cli-iot-ext`. Efter installationen kan du använda `az extension list` för att verifiera de tillägg som finns installerade för närvarande eller `az extension show --name azure-cli-iot-ext` för att se information om IoT-tillägget. Om du vill ta bort tillägget kan du använda `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Grundläggande åtgärder för Device Provisioning-tjänsten
Exemplet visar hur du logga in på ditt Azure-konto, skapa en Azure-resursgrupp (en behållare som innehåller relaterade resurser för en Azure-lösning), skapa en IoT Hub, skapa en Device Provisioning-tjänsten, lista över de befintliga Device Provisioning-tjänsterna och Skapa en länkad IoT-hubb med CLI-kommandon. 

Slutför installationsstegen som beskrivs ovan innan du börjar. Om du inte har ett Azure-konto än kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?v=17.39a) nu. 


### <a name="1-log-in-to-the-azure-account"></a>1. Logga in på Azure-kontot
  
    az login

![inloggning][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Skapa resursgruppen IoTHubBlogDemo i eastus

    az group create -l eastus -n IoTHubBlogDemo

![Skapa resursgrupp][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Skapa två Device Provisioning-tjänster

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Skapa Device Provisioning-tjänst][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Lista över alla befintliga Device Provisioning-tjänster under denna resursgrupp

    az iot dps list --resource-group IoTHubBlogDemo

![Lista Enhetsetableringstjänster][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Skapa en blogDemoHub i IoT Hub under den resursgrupp du skapade nyss

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Skapa IoT Hub][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Länka en befintlig IoT Hub till Device Provisioning-tjänsten

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Länka hubb][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Registrera enheten
> * Starta enheten
> * Kontrollera att enheten är registrerad

Gå vidare till nästa självstudiekurs för att lära dig att etablera flera enheter över belastningsutjämnade hubbar. 

> [!div class="nextstepaction"]
> [Etablera enheter till flera belastningsutjämnade IoT-hubbar](./tutorial-provision-multiple-hubs.md)
