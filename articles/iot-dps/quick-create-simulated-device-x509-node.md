---
title: Provision simulated X.509 device to Azure IoT Hub using Node.js
description: Skapa och etablera en simulerad X.509-enhet med Node.js-enhets-SDK för Azure IoT Hub Device Provisioning-tjänsten. Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 5ba18ae304ee1d72306f233a5b288c358efcd696
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423401"
---
# <a name="quickstart-create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Create and provision an X.509 simulated device using Node.js device SDK for IoT Hub Device Provisioning Service
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Dessa steg visar hur du skapar en registreringspost i enhetsetableringstjänsten, simulerar en X.509-enhet på utvecklingsmaskinen, ansluter den simulerade enheten med enhetsetableringstjänsten och registrerar enheten i din IoT Hub med [Azure IoT Hub Node.js-enhets-SDK](https://github.com/Azure/azure-iot-sdk-node).

Om du inte känner till processen för automatisk etablering, bör du även gå igenom [Begrepp inom automatisk etablering](concepts-auto-provisioning.md). Se också till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub Device Provisioning-tjänsten med Azure Portal) innan du fortsätter. 

Azure IoT Device Provisioning Service stöder två typer av registreringar:
- [Registreringsgrupper](concepts-service.md#enrollment-group): används för att registrera flera relaterade enheter.
- [Enskilda registreringar](concepts-service.md#individual-enrollment): används för att registrera en enskild enhet.

Den här artikeln visar enskilda registreringar.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Förbereda miljön 

1. Slutför stegen i [Konfigurera IoT Hub Device Provisioning-tjänsten med Azure Portal](./quick-setup-auto-provision.md) innan du fortsätter.

2. Kontrollera att [Node.js v4.0 eller senare](https://nodejs.org) är installerat på din dator.

3. Kontrollera att [Git](https://git-scm.com/download/) är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. 

4. Kontrollera att [OpenSSL](https://www.openssl.org/) är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Biblioteket kan antingen byggas och installeras från källkoden eller laddas ned och installeras från en [tredje part](https://wiki.openssl.org/index.php/Binaries) som [det här](https://sourceforge.net/projects/openssl/). 

    > [!NOTE]
    > Om du redan skapat _rotcertifikat_,  _mellanliggande certifikat_ och/eller X.509-_lövcertifikat_ kan du hoppa över det här steget och alla efterföljande steg som rör skapande av certifikat.
    >

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Skapa ett självsignerat X.509-enhetscertifikat och en post för enskild registrering

I det här avsnittet använder du ett självsignerat X.509-certifikat. Det är viktigt att tänka på följande:

* Självsignerade certifikat är endast till för testning och ska inte användas i produktion.
* Standardutgångsdatumet för ett självsignerat certifikat är ett år.

Du kommer att använda exempelkoden från [Azure IoT SDK för Node.js](https://github.com/Azure/azure-iot-sdk-node.git) för att skapa det certifikat som ska användas med posten för enskild registrering för den simulerade enheten.


1. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för att hämta exempelkoden:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

2. Navigera till certifikatgeneratorskriptet och skapa projektet. 

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. Skapa ett _lövcertifikat_ förX.509 genom att köra skriptet med ditt eget _certifikatnamn_. Lövcertifikatets namn blir [Registrerings-ID](https://docs.microsoft.com/azure/iot-dps/concepts-device#registration-id) så var noga med att bara använda små bokstäver, siffror och bindestreck.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

4. Sign in to the [Azure portal](https://portal.azure.com), select the **All resources** button on the left-hand menu and open your Device Provisioning Service instance.

5. From the Device Provisioning Service menu, select **Manage enrollments**. Select **Individual Enrollments** tab and select the **Add individual enrollment** button at the top. 

6. In the **Add Enrollment** panel, enter the following information:
   - Välj **X.509** som identitet för bestyrkande *mekanism*.
   - Under the *Primary certificate .pem or .cer file*, choose *Select a file* to select the certificate file **{certificate-name}_cert.pem** created in the previous steps.  
   - Du kan även ange följande information:
     - Välj en IoT hub som är länkad till din etableringstjänst.
     - Ange ett unikt enhets-ID. Se till att undvika känsliga data när du namnger din enhet. 
     - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
     - Once complete, press the **Save** button. 

     [![Lägga till en enskild registrering för X.509-attestering i portalen](./media/quick-create-simulated-device-x509-node/device-enrollment.png)](./media/quick-create-simulated-device-x509-node/device-enrollment.png#lightbox)

     On successful enrollment, your X.509 device appears as **{certificatename}** under the *Registration ID* column in the *Individual Enrollments* tab. Note this value for later.

## <a name="simulate-the-device"></a>Simulera enheten

[Azure IoT Hub Node.js-enhets-SDK](https://github.com/Azure/azure-iot-sdk-node) gör det enkelt att simulera en enhet. Läs mer i [Enhetskoncept](https://docs.microsoft.com/azure/iot-dps/concepts-device).

1. In the Azure portal, select the **Overview** blade for your Device Provisioning service and note the **_GLobal Device Endpoint_** and **_ID Scope_** values.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509-node/extract-dps-endpoints.png) 

2. Kopiera ditt _certifikat_ och din _nyckel_ till exempelmappen.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

3. Navigera till skriptet för enhetstest och skapa projektet. 

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

4. Redigera **register\_x509.js**-filen. Spara filen när du gjort följande ändringar.
    - Ersätt `provisioning host` med den **_globala enhetsslutpunkt_** du antecknade i **steg 1** ovan.
    - Replace `id scope` with the **_ID Scope_** noted in **Step 1** above. 
    - Replace `registration id` with the **_Registration ID_** noted in the previous section.
    - Ersätt `cert filename` och `key filename` med de filer du kopierade i **steg 2** ovan. 

5. Kör skriptet och verifiera att enheten etablerats korrekt.

    ```cmd/sh
    node register_x509.js
    ```   

6. In the portal, navigate to the IoT hub linked to your provisioning service and open the **IoT devices** blade. On successful provisioning of the simulated X.509 device to the hub, its device ID appears on the **IoT devices** blade, with *STATUS* as **enabled**. You might need to press the **Refresh** button at the top if you already opened the blade prior to running the sample device application. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-x509-node/hubregistration.png) 

    Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).


## <a name="clean-up-resources"></a>Rensa resurser

If you plan to continue working on and exploring the device client sample, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
2. From the left-hand menu in the Azure portal, select **All resources** and then select your Device Provisioning service. Open the **Manage Enrollments** blade for your service, and then select the **Individual Enrollments** tab. Select the check box next to the *REGISTRATION ID* of the device you enrolled in this quickstart, and press the **Delete** button at the top of the pane. 
3. From the left-hand menu in the Azure portal, select **All resources** and then select your IoT hub. Open the **IoT devices** blade for your hub, select the check box next to the *DEVICE ID* of the device you registered in this quickstart, and then press the **Delete** button at the top of the pane.


## <a name="next-steps"></a>Nästa steg

In this quickstart, you’ve created a simulated X.509 device and provisioned it to your IoT hub using the Azure IoT Hub Device Provisioning Service on the portal. To learn how to enroll your X.509 device programmatically, continue to the quickstart for programmatic enrollment of X.509 devices. 

> [!div class="nextstepaction"]
> [Azure quickstart - Enroll X.509 devices to Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-node.md)
