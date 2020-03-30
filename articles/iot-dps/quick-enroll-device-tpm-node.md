---
title: Registrera TPM-enhet till Azure Device Provisioning Service med Node.js
description: Snabbstart – Registrera TPM-enhet till DPS (Azure IoT Hub Device Provisioning Service) med Node.js-tjänsten SDK. Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: e21aaa20edf6d3a2f690bf9f77e8c9973a7b1c52
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77604923"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Snabbstart: Registrera TPM-enhet till IoT Hub Device Provisioning Service med Node.js-tjänsten SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

I den här snabbstarten skapar du programmässigt en individuell registrering för en TPM-enhet i Azure IoT Hub Device Provisioning Service med node.js-tjänsten SDK och ett exempelnod.js-program. Om du vill kan du registrera en simulerad TPM-enhet för etableringstjänsten med den här posten för enskild registrering.

## <a name="prerequisites"></a>Krav

- Slutförd av [Konfigurera IoT Hub Device Provisioning Service med Azure-portalen](./quick-setup-auto-provision.md).
- Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v4.0+](https://nodejs.org). Den här snabbstarten installerar [Node.js Service SDK](https://github.com/Azure/azure-iot-sdk-node) nedan.
- Bekräftelsenyckel (valfritt). Följ stegen i [Skapa och etablera en simulerad enhet](quick-create-simulated-device.md) tills du får nyckeln. Skapa inte en enskild registrering med Azure-portalen.

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
    1. Logga in på Azure-portalen, välj knappen **Alla resurser** på menyn till vänster och öppna tjänsten Enhetsetablering. 
    2. Välj **Principer för delad åtkomst**och välj sedan den åtkomstprincip som du vill använda för att öppna dess egenskaper. I fönstret **Åtkomstprincip** kopierar du och antecknar primärnyckelns anslutningssträng. 

       ![Hämta etableringsanslutningssträng från portalen](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. Du behöver också bekräftelsenyckeln för din enhet. Om du har följt snabbstarten om att [skapa och etablera en simulerad enhet](quick-create-simulated-device.md) för att skapa en simulerad TPM-enhet använder du nyckeln som skapats för den enheten. Om du vill skapa en enskild exempelregistrering kan du använda följande bekräftelsenyckel som medföljer [Node.js-tjänsten SDK:](https://github.com/Azure/azure-iot-sdk-node)

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. Om du vill skapa en enskild registrering för din TPM-enhet kör du följande kommando (ta med citattecknen runt kommandoargumenten):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. När gruppen har skapats visas egenskaperna för den nya enskilda registreringen i kommandofönstret.

    ![Egenskaper för registrering i utdata från kommando](./media/quick-enroll-device-tpm-node/output.png) 

4. Verifiera att en enskild registrering har skapats. I Azure Portal, på sammanfattningsbladet för Device Provisioning-tjänsten väljer du **Hantera registreringar**. Välj fliken Enskilda registreringar och välj den nya **registreringsposten** (*först*) för att verifiera bekräftelsenyckeln och andra egenskaper för transaktionen.

    ![Egenskaper för registrering i portalen](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Nu när du har skapat en enskild registrering för en TPM-enhet och vill registrera en simulerad enhet kan du fortsätta med resten av stegen för att [skapa och etablera en simulerad enhet](quick-create-simulated-device.md). Var noga med att hoppa över stegen för att skapa en enskild registrering med hjälp av Azure-portalen i snabbstarten.

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att utforska nod.js-tjänstexemplen ska du inte rensa de resurser som skapas i den här snabbstarten. Om du inte planerar att fortsätta använder du följande steg för att ta bort alla resurser som skapats av den här snabbstarten.

1. Stäng utdatafönstret för Node.js på datorn.
1. Om du har skapat en simulerad TPM-enhet stänger du TPM-simulatorfönstret.
2. Navigera till tjänsten Enhetsetablering i Azure-portalen, välj **Hantera registreringar**och välj sedan *Registration ID* fliken **Enskilda registreringar.** **Delete** 
 
## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skapat en individuell registreringspost för en TPM-enhet och skapat en TPM-simulerad enhet på datorn och etablerat den i din IoT-hubb med hjälp av Azure IoT Hub Device Provisioning Service. Om du vill ha mer djupgående information om enhetsetablering kan du fortsätta till självstudien om konfiguration av Device Provisioning-tjänsten i Azure-portalen. 
 
> [!div class="nextstepaction"]
> [Självstudier om Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-cloud.md)