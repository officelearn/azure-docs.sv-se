---
title: Snabbstart – etablera simulerad TPM-enhet till Azure IoT Hub med Node.js
description: Snabbstart – Skapa och etablera en simulerad TPM-enhet med Node.js-enheten SDK för Azure IoT Hub Device Provisioning Service (DPS). Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom:
- mvc
- amqp
- mqtt
ms.openlocfilehash: 45e6fac971d7ccf57d6f9bae96a08ccaee023df3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687252"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Snabbstart: Skapa och etablera en simulerad TPM-enhet med Node.js-enheten SDK för IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

I den här snabbstarten skapar du en simulerad IoT-enhet på en Windows-dator. Den simulerade enheten innehåller en TPM-simulator som en maskinvarusäkerhetsmodul (HSM). Du använder enhetsexempel nod.js-kod för att ansluta den här simulerade enheten till din IoT-hubb med hjälp av en enskild registrering med DPS (Device Provisioning Service).

## <a name="prerequisites"></a>Krav

- Översyn av [begrepp för automatisk etablering](concepts-auto-provisioning.md).
- Slutförande av [konfigurera IoT Hub Device Provisioning Service med Azure-portalen](./quick-setup-auto-provision.md).
- Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v4.0+](https://nodejs.org).
- [Git](https://git-scm.com/download/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Förbereda miljön 

1. Kontrollera att [Node.js v4.0 eller senare](https://nodejs.org) är installerat på din dator.

1. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 


## <a name="simulate-a-tpm-device"></a>Simulera en TPM-enhet

1. Öppna en kommandotolk eller Git Bash. Klona `azure-utpm-c` GitHub-lagringsplatsen:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. Navigera till GitHub-rotmappen och kör [TPM-simulatorn](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) för att vara [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) för den simulerade enheten. Den lyssnar via en socket på portarna 2321 och 2322. Stäng inte det här kommandofönstret. du måste hålla den här simulatorn igång till slutet av den här snabbstartsguiden: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Skapa en ny tom mapp med namnet **registerdevice**. I mappen **registerdevice** skapar du en package.json-fil med hjälp av följande kommando i kommandotolken. Svara på alla frågor från `npm`, eller godkänn standardinställningarna om de passar dig:
   
    ```cmd/sh
    npm init
    ```

1. Installera följande tidigare paket:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > Det finns några kända problem som kan uppstå när du installerar ovanstående paket. För att lösa dessa problem kan du köra `npm install --global --production windows-build-tools` med hjälp av en kommandotolk i läget **Kör som administratör**. Kör `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` efter att du ersatt sökvägen med den installerade versionen och kör sedan installationskommandona igen.
    >

1. Installera följande paket som innehåller de komponenter som användes under registreringen:

   - en säkerhetsklient som fungerar med TPM: `azure-iot-security-tpm`
   - en transport för att enheten ska kunna ansluta till enhetsetableringstjänsten: antingen `azure-iot-provisioning-device-http` eller `azure-iot-provisioning-device-amqp`
   - en klient som använder transport- och säkerhetsklienten: `azure-iot-provisioning-device`

     När enheten är registrerad kan du använda vanliga IoT Hub-enhetsklientpaket till att ansluta enheten med de autentiseringsuppgifter som angavs under registreringen. Du behöver:

   - enhetsklienten: `azure-iot-device`
   - en transport: antingen `azure-iot-device-amqp`, `azure-iot-device-mqtt` eller `azure-iot-device-http`
   - säkerhetsklienten som du redan har installerat: `azure-iot-security-tpm`

     > [!NOTE]
     > I exemplen nedan används transporterna `azure-iot-provisioning-device-http` och `azure-iot-device-mqtt`.
     > 

     Du kan installera alla paket samtidigt genom att köra följande kommando i en kommandotolk i mappen **registerdevice**:

       ```cmd/sh
       npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
       ```

1. Med hjälp av en textredigerare skapar du en ny **ExtractDevice.js**-fil i mappen **registerdevice**.

1. Lägg till följande `require`-instruktioner i början av **ExtractDevice.js**-filen:
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Lägg till följande funktion för att implementera metoden:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Spara och stäng **ExtractDevice.js**-filen. Kör exemplet:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. Utdatafönstret visar **_bekräftelsenyckeln_** och **_registrerings-ID:t_** som behövs för enhetsregistrering. Anteckna dessa värden. 


## <a name="create-a-device-entry"></a>Skapa en enhetspost

Azure IoT Device Provisioning Service stöder två typer av registreringar:

- [Registreringsgrupper](concepts-service.md#enrollment-group): används för att registrera flera relaterade enheter.
- [Individuella registreringar](concepts-service.md#individual-enrollment): Används för att registrera en enda enhet.

Den här artikeln visar enskilda registreringar.

1. Logga in på Azure-portalen, välj knappen **Alla resurser** på menyn till vänster och öppna tjänsten Enhetsetablering.

1. Välj **Hantera registreringar**på menyn Tjänst för enhetsetablering . Välj fliken **Enskilda registreringar** och välj knappen **Lägg till individuell registrering** högst upp. 

1. Ange följande information på panelen **Lägg till registrering:**
   - Välj **TPM** som identitet för bestyrkande *mekanism*.
   - Ange *registrerings-ID* och *bekräftelsenyckel* för din TPM-enhet från de värden som du noterade tidigare.
   - Välj en IoT hub som är länkad till din etableringstjänst.
   - Du kan även ange följande information:
       - Ange ett unikt *enhets-ID*. Se till att undvika känsliga data när du namnger din enhet. Om du väljer att inte ange ett används registrerings-ID:et för att identifiera enheten i stället.
       - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
   - När du är klar trycker du på **knappen Spara.** 

     ![Ange information för enhetsregistrering på portalbladet](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Vid lyckad registrering visas *Registrerings-ID* för enheten i listan under fliken *Enskilda registreringar*. 


## <a name="register-the-device"></a>Registrera enheten

1. I Azure-portalen väljer du **bladet Översikt** för tjänsten Enhetsetablering och noterar värdena **_för slutpunkt_** och **_ID-scope_** för globala enheter.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Med hjälp av en textredigerare skapar du en ny **RegisterDevice.js**-fil i mappen **registerdevice**.

1. Lägg till följande `require`-instruktioner i början av **RegisterDevice.js**-filen:
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > **Azure IoT SDK för Node.js** har stöd för fler protokoll, exempelvis _AMQP_, _AMQP WS_ och _MQTT WS_.  Fler exempel finns i [Exempel på enhetsetableringstjänstens SDK för Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).
    > 

1. Lägg till variablerna **globalDeviceEndpoint** och **idScope** och använd dem för att skapa en **ProvisioningDeviceClient**-instans. Ersätt **{globalDeviceEndpoint}** och **{idScope}** med värdena i **_Global enhetsslutpunkt_** och **_ID-omfång_** från **Steg 1**:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Lägg till följande funktion för att implementera metoden på enheten:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Spara och stäng **RegisterDevice.js**-filen. Kör exemplet:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. Lägg märke till de meddelanden som simulerar enhetsstart och anslutning till Device Provisioning-tjänsten för att hämta IoT-hubinformationen. När din simulerade enhet har etablerats till IoT-hubben som är länkad till etableringstjänsten visas enhets-ID:et på hubbens **IoT-enhetsblad.** 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device/hub-registration.png) 

    Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Förstå och använda enhetstvillingar i IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som skapas i den här snabbstarten. Om du inte planerar att fortsätta använder du följande steg för att ta bort alla resurser som skapats av den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Stäng fönstret för TPM-simulatorn på datorn.
1. På menyn till vänster i Azure-portalen väljer du **Alla resurser** och väljer sedan din enhetsetableringstjänst. Öppna bladet **Hantera registreringar** för tjänsten och markera sedan fliken **Enskilda registreringar.** *REGISTRATION ID* **Delete** 
1. På menyn till vänster i Azure-portalen väljer du **Alla resurser** och väljer sedan din IoT-hubb. Öppna **ioT-enhetsbladet** för navet, markera kryssrutan bredvid *DEVICE-ID:t* för den enhet som du registrerade i den här snabbstarten och tryck sedan på knappen **Ta bort** högst upp i fönstret.


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en TPM-simulerad enhet på datorn och etablerat den i din IoT-hubb med hjälp av IoT Hub Device Provisioning Service. Om du vill veta hur du registrerar TPM-enheten programmässigt fortsätter du till snabbstarten för programmatisk registrering av en TPM-enhet. 

> [!div class="nextstepaction"]
> [Snabbstart i Azure – Registrera TPM-enhet till Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-node.md)
