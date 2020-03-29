---
title: Hantera IoT Hub Device Provisioning Service med Azure CLI & IoT-tillägg
description: Lär dig hur du använder Azure CLI och IoT-tillägget för att hantera DPS (IoT Hub Device Provisioning Service)
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 03ec0b41ad910ff0d1dcdc17148e01ec94ea9fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674520"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Så här använder du Azure CLI och IoT-tillägget för att hantera IoT Hub Device Provisioning Service

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommandoradsverktyg med flera plattformar med öppen källkod för hantering av Azure-resurser som IoT Edge. Azure CLI är tillgängligt på Windows, Linux och MacOS. Med Azure CLI kan du hantera Azure IoT Hub-resurser, tjänstinstanser för enhetsetablering och länkade hubbar.

IoT-tillägget berikar Azure CLI med funktioner som enhetshantering och fullständig IoT Edge-kapacitet.

I den här självstudien slutför du först stegen för att konfigurera Azure CLI och IoT-tillägget. Sedan kan du lära dig hur du kör CLI-kommandon för att utföra grundläggande enhetsetableringstjänståtgärder. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>Installation 

### <a name="install-python"></a>Installera Python

[Python 2.7x eller Python 3.x](https://www.python.org/downloads/) krävs.

### <a name="install-the-azure-cli"></a>Installera Azure CLI

Följ [installationsanvisningen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att konfigurera Azure CLI i din miljö. Azure CLI-versionen måste vara minst 2.0.70 eller högre. Validera med `az –version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. Ett enkelt sätt att installera i Windows är att hämta och installera [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="install-iot-extension"></a>Installera IoT-tillägg

I [IoT-tilläggets Viktigt-fil](https://github.com/Azure/azure-iot-cli-extension) beskrivs olika sätt att installera tillägget. Det enklaste sättet är att köra `az extension add --name azure-iot`. Efter installationen kan du använda `az extension list` för att verifiera de tillägg som finns installerade för närvarande eller `az extension show --name azure-iot` för att se information om IoT-tillägget. Om du vill ta bort tillägget kan du använda `az extension remove --name azure-iot`.


## <a name="basic-device-provisioning-service-operations"></a>Grundläggande åtgärder för enhetsetableringstjänst

Exemplet visar hur du loggar in på ditt Azure-konto, skapar en Azure Resource Group (en behållare som innehåller relaterade resurser för en Azure-lösning), skapar en IoT-hubb, skapar en enhetsetableringstjänst, listar befintliga enhetsetableringstjänster och skapa en länkad IoT-hubb med CLI-kommandon. 

Slutför installationsstegen som beskrivs ovan innan du börjar. Om du inte har ett Azure-konto än kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?v=17.39a) nu. 


### <a name="1-log-in-to-the-azure-account"></a>1. Logga in på Azure-kontot
  
    az login

![inloggning](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Skapa en resursgrupp IoTHubBlogDemo i eastus

    az group create -l eastus -n IoTHubBlogDemo

![Skapa resursgrupp](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. Skapa två enhetsanableringstjänster

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Skapa enhetsetableringstjänst](./media/how-to-manage-dps-with-cli/create-dps.jpg)

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Lista alla befintliga enhetsetableringstjänster under den här resursgruppen

    az iot dps list --resource-group IoTHubBlogDemo

![Lista enhetsetableringstjänster](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Skapa en IoT Hub-bloggDemoHub under den nyskapade resursgruppen

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Skapa IoT Hub](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Länka en befintlig IoT-hubb till en enhetsetableringstjänst

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Länka hubb](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Registrera enheten
> * Starta enheten
> * Kontrollera att enheten är registrerad

Gå vidare till nästa självstudiekurs för att lära dig att etablera flera enheter över belastningsutjämnade hubbar. 

> [!div class="nextstepaction"]
> [Etablera enheter till flera belastningsutjämnade IoT-hubbar](./tutorial-provision-multiple-hubs.md)
