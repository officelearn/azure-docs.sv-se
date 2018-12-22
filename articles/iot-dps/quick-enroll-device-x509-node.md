---
title: Den här snabbstarten beskriver hur du registrerar X.509-enheter till Azure Device Provisioning Service med hjälp av Node.js | Microsoft Docs
description: Den här snabbstarten använder gruppregistreringar. I den här snabbstarten registrerar du X.509-enheter till Azure IoT Hub Device Provisioning Service med hjälp av Node.js-tjänstens SDK
author: wesmc7777
ms.author: wesmc
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 852e250d60d112ca7a8e8a98d93121762e2fde4d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187015"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-nodejs"></a>Snabbstart: Registrera X.509-enheter till Device Provisioning Service med hjälp av Node.js

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Den här snabbstarten beskriver hur du använder Node.js för att programmässigt skapa en [registreringsgrupp](concepts-service.md#enrollment-group) som använder mellanliggande CA X.509-certifikat eller CA X.509-rotcertifikat. Registreringsgruppen skapas med hjälp av [IoT SDK för Node.js](https://github.com/Azure/azure-iot-sdk-node) och ett Node.js-exempelprogram. En registreringsgrupp kontrollerar åtkomst till etableringstjänsten för enheter som delar ett gemensamt signeringscertifikat i certifikatkedjan. Läs mer i informationen om att [kontrollera enhetsåtkomst till etableringstjänsten med X.509-certifikat](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Mer information om att använda X.509-certifikatbaserad Public Key Infrastructure (PKI) med Azure IoT Hub och enhetsetableringstjänst finns i [Översikt över certifikatsäkerhet med X.509 CA](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Den här snabbstarten förutsätter att du redan har skapat en IoT-hubb och en Device Provisioning Service-instans. Om du inte redan har skapat dessa resurser slutför du snabbstarten [Konfigurera IoT Hub Device Provisioning-tjänsten med Azure-portalen](./quick-setup-auto-provision.md) innan du fortsätter med den här artikeln.

Även om stegen i den här artikeln fungerar både med Windows- och Linux-datorer är den här artikeln utvecklad för en Windows-utvecklingsdator.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

- Installera [Node.js v4.0 eller senare](https://nodejs.org).
- Installera [Git](https://git-scm.com/download/).


## <a name="prepare-test-certificates"></a>Förbereda testcertifikat

I den här snabbstarten måste du ha en .pem- eller en .cer-fil som innehåller den offentliga delen av ett mellanliggande X.509-certifikat eller ett X.509-rotcertifikat. Det här certifikatet måste laddas upp till din etableringstjänst och verifieras av tjänsten. 

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) innehåller testverktyg som du kan använda för att skapa en X.509-certifikatkedja, ladda upp ett rot- eller mellanliggande certifikat från kedjan och utföra innehavarbevis med tjänsten för att verifiera certifikatet. Certifikat som skapas med SDK-verktygsuppsättningen är utformade för att **endast användas för testutveckling**. Dessa certifikat **får inte användas i produktion**. De innehåller hårdkodade lösenord (”1234”) som upphör att gälla efter 30 dagar. Om du vill lära dig mer om att hämta certifikat som är lämpliga för produktion kan du läsa [How to get an X.509 CA certificate](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) (Hämta ett X.509 CA-certifikat) i dokumentationen för Azure IoT Hub.

Om du vill använda det här testverktyget för att generera certifikat utför du följande steg: 
 
1. Öppna en kommandotolk eller Git Bash-gränssnittet och växla till en arbetsmapp på datorn. Kör följande kommando för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   Storleken på den här lagringsplatsen är för närvarande cirka 220 MB. Den här åtgärden kan förväntas ta flera minuter att slutföra.

   Testverktyget finns i den *azure-iot-sdk-c/tools/CACertificates* för den lagringsplats som du klonade.    

2. Följ stegen på sidan om att [hantera CA-testcertifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). 



## <a name="create-the-enrollment-group-sample"></a>Skapa exemplet för registrering av grupp 

 
1. Från ett kommandofönster i arbetsmappen kör du:
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. Med en textredigerare skapar du filen **create_enrollment_group.js** i arbetsmappen. Lägg till följande kod i filen och spara:

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ````

## <a name="run-the-enrollment-group-sample"></a>Köra exemplet för registrering av grupp
 
1. Om du vill köra exemplet behöver du anslutningssträngen för etableringstjänsten. 
    1. Logga in på Azure-portalen, klicka på knappen **Alla resurser** i den vänstra menyn och öppna Device Provisioning-tjänsten. 
    2. Klicka på **Policyer för delad åtkomst** och sedan på den åtkomstprincip du vill använda för att öppna dess egenskaper. I fönstret **Åtkomstprincip** kopierar du och antecknar primärnyckelns anslutningssträng. 

       ![Hämta etableringsanslutningssträng från portalen](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Som det står i [Förbereda testcertifikat](quick-enroll-device-x509-node.md#prepare-test-certificates) behöver du även en .pem-fil som innehåller ett mellanliggande X.509-certifikat eller ett rotcertifikatutfärdarcertifikat som har laddats upp tidigare och verifierats av din etableringstjänst. Om du vill kontrollera om ditt certifikat har laddats upp och verifierats går du till sammanfattningsbladet för Device Provisioning-tjänsten i Azure Portal och klickar på **Certifikat**. Leta upp det certifikat du vill använda för gruppregistreringen och se till att statusvärdet är *verifierat*.

    ![Verifierat certifikat i portalen](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. Om du vill skapa en registreringsgrupp för certifikatet kör du följande kommando (ta med citattecknen runt kommandoargumenten):
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. När gruppen har skapats visas egenskaperna för den nya registreringsgruppen i kommandofönstret.

    ![Egenskaper för registrering i utdata från kommando](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Verifiera att registreringsgruppen har skapats. I Azure Portal, på sammanfattningsbladet för Device Provisioning-tjänsten väljer du **Hantera registreringar**. Välj fliken **Registreringsgrupper** och kontrollera att den nya registreringsposten (*första*) finns.

    ![Egenskaper för registrering i portalen](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Rensa resurser
Om du tänker utforska Node.js-tjänstexemplet ska du inte rensa resurserna som har skapats i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla Azure-resurser som har skapats i den här snabbstarten.
 
1. Stäng utdatafönstret för Node.js på datorn.
2. Navigera till Device Provisioning-tjänsten i Azure Portal, klicka på **Hantera registreringar** och välj sedan fliken **Registreringsgrupper**. Välj *Registrerings-ID* för registreringsposten du har skapat med den här snabbstarten och klickar på knappen **Ta bort** högst upp på bladet.  
3. I Device Provisioning-tjänsten i Azure-portalen klickar du på **Certifikat**, sedan på certifikatet du laddade upp för den här snabbstarten och till sist på knappen **Ta bort** överst i fönstret **Certifikatinformation**.  
 
## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skapat en gruppregistrering för ett mellanliggande X.509-certifikat eller ett rotcertifikatutfärdarcertifikat med tjänsten Azure IoT Hub Device Provisioning. Om du vill ha mer djupgående information om enhetsetablering kan du fortsätta till självstudien om konfiguration av Device Provisioning-tjänsten i Azure-portalen. 
 
> [!div class="nextstepaction"]
> [Självstudier om Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-cloud.md)