---
title: Set up IoT Hub Device Provisioning Service in the Azure portal
description: Quickstart - Set up the Azure IoT Hub Device Provisioning Service in the Azure portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b983a9591743b1fda79e23aedc1aca88add2a3e2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228525"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with the Azure portal

Dessa steg beskriver hur du konfigurerar Azure-molnresurserna på portalen för att etablera dina enheter. I den här artikeln hittar du anvisningar för att: skapa en IoT-hubb, skapa en ny instans av IoT Hub Device Provisioning-tjänsten och länka de två tjänsterna. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Skapa en ny instans av IoT Hub Device Provisioning-tjänsten

1. Select the **+ Create a resource** button again.

2. *Search the Marketplace* for the **Device Provisioning Service**. Select **IoT Hub Device Provisioning Service** and hit the **Create** button. 

3. Provide the following information for your new Device Provisioning Service instance and hit **Create**.

    * **Namn:** Ange ett unikt namn för den nya instansen av enhetsetableringstjänsten. Om namnet som du anger är tillgängligt visas en grön bockmarkering.
    * **Prenumeration**: Välj den prenumeration du vill använda för att skapa instansen av enhetsetableringstjänsten.
    * **Resursgrupp:** I det här fältet kan du skapa en ny resursgrupp eller välja en befintlig som ska innehålla den nya instansen. Välj resursgruppen som innehåller den IoT-hubb du skapade, till exempel **TestResources**. Genom att lägga till alla relaterade resurser i en grupp kan du hantera dem tillsammans. Till exempel tas alla resurser som ingår i gruppen bort om resursgruppen tas bort. Mer information finns i [Hantera Azure Resource Manager-resursgrupper](../azure-resource-manager/manage-resource-groups-portal.md).
    * **Plats**: Välj den plats som är närmast enheten.

      ![Enter basic information about your Device Provisioning Service instance in the portal blade](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Select the notification button to monitor the creation of the resource instance. Once the service is successfully deployed, select **Pin to dashboard**, and then **Go to resource**.

    ![Övervaka distributionsmeddelandet](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Link the IoT hub and your Device Provisioning Service

In this section, you will add a configuration to the Device Provisioning Service instance. Den här konfigurationen anger den IoT-hubb för vilken enheter tillhandahålls.

1. Select the **All resources** button from the left-hand menu of the Azure portal. Välj Device Provisioning-tjänstinstansen som du skapade i det föregående avsnittet.  

2. From the Device Provisioning Service's menu, select **Linked IoT hubs**. Hit the **+ Add** button seen at the top. 

3. På sidan **Lägg till länk i IoT Hub** fyller du i följande information för att länka den nya instansen av enhetsetableringstjänsten till en IoT-hubb. Then hit **Save**. 

    * **Prenumeration:** Välj den prenumeration som innehåller den IoT-hubb som du vill länka till den nya instansen av enhetsetableringstjänsten.
    * **IoT-hubb:** Välj den IoT-hubb som du vill länka till den nya instansen av enhetsetableringstjänsten.
    * **Åtkomstprincip:** Välj **iothubowner** som autentiseringsuppgifter när du upprättar länken till IoT-hubben.  

      ![Link the hub name to link to the Device Provisioning Service instance in the portal blade](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Nu bör den valda hubben visas under bladet **Linked IoT hubs** (Länkade IoT-hubbar). You might need to hit **Refresh** for it to show up.


## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure-portalen.

1. From the left-hand menu in the Azure portal, select **All resources** and then select your Device Provisioning Service. At the top of the device detail pane, select **Delete**.  
2. From the left-hand menu in the Azure portal, select **All resources** and then select your IoT hub. At the top of the hub detail pane, select **Delete**.  

## <a name="next-steps"></a>Nästa steg

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)
