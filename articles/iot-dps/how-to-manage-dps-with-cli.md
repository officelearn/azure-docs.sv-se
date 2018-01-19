---
title: "Hur du använder Azure CLI 2.0 och IoT-tillägg för att hantera tjänster för etablering av enheter | Microsoft Docs"
description: "Lär dig hur du använder Azure CLI 2.0 och IoT-tillägg för att hantera tjänster för etablering av enheter"
services: iot-dps
keywords: 
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 674245f1e284e7308474fed0f6c53b350ec1c819
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-cli-20-and-the-iot-extension-to-manage-device-provisioning-services"></a>Hur du använder Azure CLI 2.0 och IoT-tillägg för att hantera tjänster för etablering av enheter

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) är en öppen källkod mellan plattform kommandoradsverktyg för att hantera Azure-resurser som IoT kant. Azure CLI 2.0 är tillgänglig på Windows, Linux och MacOS. Azure CLI 2.0 kan du hantera Azure IoT Hub resurser enheten etablering tjänstinstanser och länkade hubs direkt.

IoT-tillägget förbättra Azure CLI 2.0 med funktioner som enhetshantering och kapaciteten för fullständig IoT kant.

I kursen får slutföra du först stegen för att konfigurera Azure CLI 2.0 och IoT-tillägget. Sedan får du lära dig hur du kör CLI-kommandon för att utföra grundläggande enhet etablering tjänståtgärder. 

## <a name="installation"></a>Installation 

### <a name="step-1---install-python"></a>Steg 1 – Installera Python

[Python 2.7 x eller Python 3.x](https://www.python.org/downloads/) krävs.

### <a name="step-2---install-azure-cli-20"></a>Steg 2 – installera Azure CLI 2.0

Följ den [installationsinstruktion](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) att konfigurera Azure CLI 2.0 i din miljö. Minst Azure CLI 2.0 måste du ha 2.0.24 eller senare. Använd `az –version` att validera. Den här versionen stöder az tillägget kommandon och introducerar Knack kommandot ramen. Ett enkelt sätt att installera på Windows är att hämta och installera den [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Steg 3 – installation IoT-tillägg

[IoT-tillägget viktigt](https://github.com/Azure/azure-iot-cli-extension) beskrivs flera olika sätt att installera tillägget. Det enklaste sättet är att köra `az extension add --name azure-cli-iot-ext`. Efter installationen kan du använda `az extension list` att verifiera de installerade tilläggen eller `az extension show --name azure-cli-iot-ext` att se information om IoT-tillägget. Du kan använda för att ta bort tillägget `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Grundläggande tjänståtgärder för etablering av enheter
Exemplet visar hur du logga in på ditt Azure-konto, skapa en Azure-resursgrupp (en behållare som innehåller relaterade resurser för en Azure-lösning), en IoT-hubb, skapa en enhet som etablerar servie, visa en lista med befintliga enheten etablerar tjänster och Skapa en länkad IoT-hubb med CLI-kommandona. 

Slutför installationssteg som beskrivs ovan innan du börjar. Om du inte har ett Azure-konto ännu, kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?v=17.39a) idag. 


### <a name="1-log-in-to-the-azure-account"></a>1. Logga in på Azure-konto
  
    az login

![inloggning][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Skapa en resursgrupp IoTHubBlogDemo i eastus

    az group create -l eastus -n IoTHubBlogDemo

![Skapa resursgrupp][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Skapa två tjänster för etablering av enheter

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Skapa DP][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Visa en lista med alla befintliga enheten etablerar tjänster under denna resursgrupp

    az iot dps list --resource-group IoTHubBlogDemo

![Lista DP][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Skapa en IoT-hubb blogDemoHub under den nyligen skapade resursgruppen

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Skapa IoT-hubb][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Länka ett befintligt IoT-hubben till en enhet som etablerar tjänsten

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Link Hub][5]

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

Gå vidare till nästa kurs att lära dig att etablera flera enheter över Utjämning av nätverksbelastning hubs. 

> [!div class="nextstepaction"]
> [Etablera enheter över Utjämning av nätverksbelastning IoT-hubbar](./tutorial-provision-multiple-hubs.md)
