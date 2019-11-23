---
title: Enroll TPM device to Azure Device Provisioning Service using Node.js
description: Quickstart - Enroll TPM device to Azure IoT Hub Device Provisioning Service using Node.js service SDK. Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 890ad28d99bfc53fa8a3fb40caf0469b31aeee61
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422977"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Quickstart: Enroll TPM device to IoT Hub Device Provisioning Service using Node.js service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


De här stegen visar hur du programmässigt skapar en enskild registrering för en TPM-enhet i Azure IoT Hub Device Provisioning-tjänsten med [Node.js-tjänst-SDK](https://github.com/Azure/azure-iot-sdk-node) och ett Node.js-exempelprogram. Om du vill kan du registrera en simulerad TPM-enhet för etableringstjänsten med den här posten för enskild registrering. Även om de här stegen fungerar både med Windows- och Linux-datorer använder vi en Windows-utvecklingsdator i den här artikeln.

## <a name="prerequisites"></a>Krav

- Se till att slutföra stegen i [Set up the IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub Device Provisioning-tjänsten med Azure-portalen) innan du fortsätter. 
-  Kontrollera att [Node.js v4.0 eller senare](https://nodejs.org) är installerat på din dator.
- If you want to enroll a simulated device at the end of this quickstart, follow the steps in [Create and provision a simulated device](quick-create-simulated-device.md) up until the step where you get an endorsement key for the device. Note down the endorsement key, you will use it later in this quickstart. **Följ inte stegen för att skapa en enskild registrering med Azure-portalen.**
 
## <a name="create-the-individual-enrollment-sample"></a>Skapa ett exempel på enskild registrering 

 
1. Från ett kommandofönster i arbetsmappen kör du:
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. Med en textredigerare skapar du filen **create_individual_enrollment.js** i arbetsmappen. Lägg till följande kod i filen och spara:

    ```
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

## <a name="run-the-individual-enrollment-sample"></a>Köra exemplet på enskild registrering
  
1. Om du vill köra exemplet behöver du anslutningssträngen för etableringstjänsten. 
    1. Sign in to the Azure portal, select the **All resources** button on the left-hand menu and open your Device Provisioning service. 
    2. Select **Shared access policies**, then select the access policy you want to use to open its properties. I fönstret **Åtkomstprincip** kopierar du och antecknar primärnyckelns anslutningssträng. 

       ![Hämta etableringsanslutningssträng från portalen](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. Du behöver också bekräftelsenyckeln för din enhet. Om du har följt snabbstarten om att [skapa och etablera en simulerad enhet](quick-create-simulated-device.md) för att skapa en simulerad TPM-enhet använder du nyckeln som skapats för den enheten. När du vill skapa ett exempel på en enskild registrering kan du också använda följande bekräftelsenyckel som medföljer SDK:n:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. Om du vill skapa en enskild registrering för din TPM-enhet kör du följande kommando (ta med citattecknen runt kommandoargumenten):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. När gruppen har skapats visas egenskaperna för den nya enskilda registreringen i kommandofönstret.

    ![Egenskaper för registrering i utdata från kommando](./media/quick-enroll-device-tpm-node/output.png) 

4. Verifiera att en enskild registrering har skapats. I Azure Portal, på sammanfattningsbladet för Device Provisioning-tjänsten väljer du **Hantera registreringar**. Select the **Individual Enrollments** tab and select the new enrollment entry (*first*) to verify the endorsement key and other properties for the entry.

    ![Egenskaper för registrering i portalen](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Nu när du har skapat en enskild registrering för en TPM-enhet och vill registrera en simulerad enhet kan du fortsätta med resten av stegen för att [skapa och etablera en simulerad enhet](quick-create-simulated-device.md). Be sure to skip the steps to create an individual enrollment using the Azure portal in that quickstart.

## <a name="clean-up-resources"></a>Rensa resurser
If you plan to explore the Node.js service samples, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Stäng utdatafönstret för Node.js på datorn.
1. Om du har skapat en simulerad TPM-enhet stänger du TPM-simulatorfönstret.
2. Navigate to your Device Provisioning service in the Azure portal, select **Manage enrollments**, and then select the **Individual Enrollments** tab. Select the check box next to the *Registration ID* for the enrollment entry you created using this quickstart, and press the **Delete** button at the top of the pane. 
 
## <a name="next-steps"></a>Nästa steg
In this quickstart, you’ve programmatically created an individual enrollment entry for a TPM device, and, optionally, created a TPM simulated device on your machine and provisioned it to your IoT hub using the Azure IoT Hub Device Provisioning Service. Om du vill ha mer djupgående information om enhetsetablering kan du fortsätta till självstudien om konfiguration av Device Provisioning-tjänsten i Azure-portalen. 
 
> [!div class="nextstepaction"]
> [Självstudier om Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-cloud.md)