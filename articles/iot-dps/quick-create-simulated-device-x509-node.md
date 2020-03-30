---
title: Etablera simulerad X.509-enhet till Azure IoT Hub med Node.js
description: Skapa och etablera en simulerad X.509-enhet med Node.js-enheten SDK för Azure IoT Hub Device Provisioning Service (DPS). Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 746f4adbf616f95c21874d7c1c48881f88c38d34
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605399"
---
# <a name="quickstart-create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Snabbstart: Skapa och etablera en X.509-simulerad enhet med Node.js-enheten SDK för IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

I den här snabbstarten skapar du en simulerad X.509-enhet på en Windows-dator. Du använder enhetsexempel nod.js-kod för att ansluta den här simulerade enheten till din IoT-hubb med hjälp av en enskild registrering med DPS (Device Provisioning Service).

## <a name="prerequisites"></a>Krav

- Översyn av [begrepp för automatisk etablering](concepts-auto-provisioning.md).
- Slutförande av [konfigurera IoT Hub Device Provisioning Service med Azure-portalen](./quick-setup-auto-provision.md).
- Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v4.0+](https://nodejs.org).
- [Git](https://git-scm.com/download/).
- [OpenSSL](https://www.openssl.org/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Förbereda miljön 

1. Slutför stegen i [Konfigurera IoT Hub Device Provisioning-tjänsten med Azure Portal](./quick-setup-auto-provision.md) innan du fortsätter.

2. Kontrollera att [Node.js v4.0 eller senare](https://nodejs.org) är installerat på din dator.

3. Kontrollera att [Git](https://git-scm.com/download/) är installerat på datorn och läggs till i de miljövariabler som är tillgängliga för kommandofönstret. 

4. Kontrollera att [OpenSSL](https://www.openssl.org/) är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Biblioteket kan antingen byggas och installeras från källkoden eller laddas ned och installeras från en [tredje part](https://wiki.openssl.org/index.php/Binaries) som [det här](https://sourceforge.net/projects/openssl/). 

    > [!NOTE]
    > Om du redan skapat _rotcertifikat_, _ mellanliggande certifikat_ och/eller X.509-_lövcertifikat_ kan du hoppa över det här steget och alla efterföljande steg som rör skapande av certifikat.
    >

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Skapa ett självsignerat X.509-enhetscertifikat och en post för enskild registrering

I det här avsnittet använder du ett självsignerat X.509-certifikat. Det är viktigt att tänka på följande:

* Självsignerade certifikat är endast till för testning och ska inte användas i produktion.
* Standardutgångsdatumet för ett självsignerat certifikat är ett år.

Du kommer att använda exempelkoden från [Azure IoT SDK för Node.js](https://github.com/Azure/azure-iot-sdk-node.git) för att skapa det certifikat som ska användas med posten för enskild registrering för den simulerade enheten.

Azure IoT Device Provisioning Service stöder två typer av registreringar:

- [Registreringsgrupper](concepts-service.md#enrollment-group): används för att registrera flera relaterade enheter.
- [Individuella registreringar](concepts-service.md#individual-enrollment): Används för att registrera en enda enhet.

Den här artikeln visar enskilda registreringar.

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

4. Logga in på [Azure-portalen,](https://portal.azure.com)välj knappen **Alla resurser** på menyn till vänster och öppna instansen Enhetsetableringstjänst.

5. Välj **Hantera registreringar**på menyn Tjänst för enhetsetablering . Välj fliken **Enskilda registreringar** och välj knappen **Lägg till individuell registrering** högst upp. 

6. Ange följande information på panelen **Lägg till registrering:**
   - Välj **X.509** som identitet för bestyrkande *mekanism*.
   - Under filen *Primär certifikat .pem eller .cer*väljer du *Välj en fil* för att välja certifikatfilen **{certifikatnamn}_cert.pem** som skapats i föregående steg.  
   - Du kan även ange följande information:
     - Välj en IoT hub som är länkad till din etableringstjänst.
     - Ange ett unikt enhets-ID. Se till att undvika känsliga data när du namnger din enhet. 
     - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
     - När du är klar trycker du på **knappen Spara.** 

     [![Lägga till individuell registrering för X.509-intyg i portalen](./media/quick-create-simulated-device-x509-node/device-enrollment.png)](./media/quick-create-simulated-device-x509-node/device-enrollment.png#lightbox)

     Vid lyckad registrering visas din X.509-enhet som **{certificatename}** under kolumnen *Registrerings-ID* på fliken *Enskilda registreringar.*

## <a name="simulate-the-device"></a>Simulera enheten

[Azure IoT Hub Node.js-enhets-SDK](https://github.com/Azure/azure-iot-sdk-node) gör det enkelt att simulera en enhet. Läs mer i [Enhetskoncept](https://docs.microsoft.com/azure/iot-dps/concepts-device).

1. I Azure-portalen väljer du **bladet Översikt** för tjänsten Enhetsetablering och noterar värdena för slutpunkt för **_den lobbbara enheten_** och **_ID-scopet._**

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
    - Ersätt `id scope` med **_ID-scopet_** som anges i **steg 1** ovan. 
    - Ersätt `registration id` med **_registrerings-ID_** som anges i föregående avsnitt.
    - Ersätt `cert filename` och `key filename` med de filer du kopierade i **steg 2** ovan. 

5. Kör skriptet och verifiera att enheten etablerats korrekt.

    ```cmd/sh
    node register_x509.js
    ```   

6. I portalen navigerar du till IoT-hubben som är länkad till etableringstjänsten och öppnar **bladet IoT-enheter.** Vid en lyckad etablering av den simulerade X.509-enheten till navet visas dess enhets-ID på **IoT-enhetsbladet,** med *STATUS* som **aktiverat**. Du kan behöva trycka på **knappen Uppdatera** högst upp om du redan har öppnat bladet innan du kör exempelenhetsprogrammet. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-x509-node/hubregistration.png) 

    Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som skapas i den här snabbstarten. Om du inte planerar att fortsätta använder du följande steg för att ta bort alla resurser som skapats av den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
2. På menyn till vänster i Azure-portalen väljer du **Alla resurser** och väljer sedan din enhetsetableringstjänst. Öppna bladet **Hantera registreringar** för tjänsten och markera sedan fliken **Enskilda registreringar.** *REGISTRATION ID* **Delete** 
3. På menyn till vänster i Azure-portalen väljer du **Alla resurser** och väljer sedan din IoT-hubb. Öppna **ioT-enhetsbladet** för navet, markera kryssrutan bredvid *DEVICE-ID:t* för den enhet som du registrerade i den här snabbstarten och tryck sedan på knappen **Ta bort** högst upp i fönstret.


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en simulerad X.509-enhet och etablerat den i din IoT-hubb med hjälp av Azure IoT Hub Device Provisioning Service på portalen. Om du vill veta hur du registrerar din X.509-enhet programmässigt fortsätter du till snabbstarten för programmatisk registrering av X.509-enheter. 

> [!div class="nextstepaction"]
> [Snabbstart i Azure – Registrera X.509-enheter till Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-node.md)
