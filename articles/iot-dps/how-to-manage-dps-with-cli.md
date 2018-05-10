---
title: Hur du använder Azure CLI 2.0 och IoT-tillägget för att hantera enhetsetableringstjänster | Microsoft Docs
description: Se hur du använder Azure CLI 2.0 och IoT-tillägget för att hantera enhetsetableringstjänster
services: iot-dps
keywords: ''
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 8e8bbf5808c11709a49f1cb6ebeba410837e5810
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-azure-cli-20-and-the-iot-extension-to-manage-device-provisioning-services"></a>Hur du använder Azure CLI 2.0 och IoT-tillägget för att hantera enhetsetableringstjänster

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommandoradsverktyg med öppen källkod för flera plattformar, för hantering av Azure-resurser som IoT Edge. Azure CLI 2.0 finns för Windows, Linux och MacOS. Azure CLI 2.0 kan du hantera Azure IoT Hub-resurser, tjänstinstanser för enhetsetablering samt länkade hubbar direkt.

Med IoT-tillägget får Azure CLI 2.0 funktioner som enhetshantering och full IoT Edge-funktion.

I den här självstudiekursen slutför du först konfigurationen av Azure CLI 2.0 och IoT-tillägget. Sedan får du lära dig hur du kör CLI-kommandon för att utföra grundläggande åtgärder för enhetsetableringstjänster. 

## <a name="installation"></a>Installation 

### <a name="step-1---install-python"></a>Steg 1 – Installera Python

[Python 2.7x eller Python 3.x](https://www.python.org/downloads/) krävs.

### <a name="step-2---install-azure-cli-20"></a>Steg 2 – Installera Azure CLI 2.0

Följ [installationsinstruktionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och konfigurera Azure CLI 2.0 i din miljö. Din Azure CLI 2.0-version måste vara 2.0.24 eller senare. Validera med `az –version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack. Ett enkelt sätt att installera i Windows är att hämta och installera [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Steg 3 – Installera IoT-tillägget

I [IoT-tilläggets Viktigt-fil](https://github.com/Azure/azure-iot-cli-extension) beskrivs olika sätt att installera tillägget. Det enklaste sättet är att köra `az extension add --name azure-cli-iot-ext`. Efter installationen kan du använda `az extension list` för att verifiera de tillägg som finns installerade för närvarande eller `az extension show --name azure-cli-iot-ext` för att se information om IoT-tillägget. Om du vill ta bort tillägget kan du använda `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Grundläggande enhetsetableringstjänster
Exemplet visar hur du loggar in på ditt Azure-konto, skapar en Azure-resursgrupp (en behållare som innehåller relaterade resurser för en Azure-lösning), skapar en IoT-hubb, skapar en enhetsetableringstjänst, listar befintliga enhetsetableringstjänster och skapar en länkad IoT-hubb med CLI-kommandon. 

Slutför installationsstegen som beskrivs ovan innan du börjar. Om du inte har ett Azure-konto än kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?v=17.39a) nu. 


### <a name="1-log-in-to-the-azure-account"></a>1. Logga in på Azure-kontot
  
    az login

![inloggning][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Skapa resursgruppen IoTHubBlogDemo i eastus

    az group create -l eastus -n IoTHubBlogDemo

![Skapa resursgrupp][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Skapa två enhetsetableringstjänster

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Skapa DPS][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Lista alla befintliga enhetsetableringstjänster under denna resursgrupp

    az iot dps list --resource-group IoTHubBlogDemo

![Lista DPS][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Skapa en blogDemoHub i IoT Hub under den resursgrupp du skapade nyss

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Skapa IoT Hub][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Länka en befintlig IoT Hub till en enhetsetableringstjänst

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
