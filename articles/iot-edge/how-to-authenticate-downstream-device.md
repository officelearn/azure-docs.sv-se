---
title: Autentisera underordnade enheter – Azure IoT Edge | Microsoft Docs
description: Så här autentiserar du underordnade enheter eller löv enheter till IoT Hub och dirigerar deras anslutning via Azure IoT Edge gateway-enheter.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 74ef00a1b7310284c5f8c51e3c2c6685ffcd5071
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434498"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autentisera en underordnad enhet på Azure IoT Hub

I ett scenario med transparent Gateway behöver underordnade enheter (ibland kallade löv enheter eller underordnade enheter) identiteter i IoT Hub som andra enheter. Den här artikeln vägleder dig genom alternativen för att autentisera en underordnad enhet till IoT Hub och visar hur du deklarerar Gateway-anslutningen.

Det finns tre allmänna steg för att konfigurera en lyckad transparent Gateway-anslutning. Den här artikeln beskriver det andra steget:

1. Gateway-enheten måste kunna ansluta till underordnade enheter på ett säkert sätt, ta emot kommunikation från underordnade enheter och dirigera meddelanden till rätt mål. Mer information finns i [Konfigurera en IoT Edge-enhet så att den fungerar som en transparent Gateway](how-to-create-transparent-gateway.md).
2. **Den underordnade enheten måste ha en enhets identitet för att kunna autentisera med IoT Hub och kunna kommunicera via dess gateway-enhet.**
3. Den underordnade enheten måste ansluta till sin gateway-enhet på ett säkert sätt. Mer information finns i [en underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).

Underordnade enheter kan autentiseras med IoT Hub med hjälp av någon av tre metoder: symmetriska nycklar (kallas ibland för delade åtkomst nycklar), X. 509 självsignerade certifikat eller certifikat från X. 509 certifikat utfärdare (CA) signerade certifikat. Stegen för autentisering liknar de steg som används för att konfigurera en icke-IoT-Edge-enhet med IoT Hub, med små skillnader för att deklarera Gateway-relationen.

Stegen i den här artikeln visar manuell enhets etablering, inte automatisk etablering med Azure-IoT Hub Device Provisioning Service (DPS). Det finns inte stöd för att allokera underordnade enheter med DPS. 

## <a name="prerequisites"></a>Krav

Slutför stegen i [Konfigurera en IoT Edge-enhet så att den fungerar som en transparent Gateway](how-to-create-transparent-gateway.md). Om du använder X. 509-autentisering för en underordnad enhet måste du använda samma skript för certifikat skapande som du har skapat i artikeln transparent Gateway. 

Den här artikeln hänvisar till *Gateway-värdnamnet* på flera punkter. Gateway-värdnamnet deklareras i parametern **hostname** i filen config. yaml på den IoT Edge gateway-enheten. Det kallas i anslutnings strängen för den underordnade enheten. Gateway-värdnamnet måste matchas till en IP-adress, antingen med DNS eller en värd fil post.

## <a name="register-device-symmetric-key"></a>Registrera enhet (symmetrisk nyckel)

Autentisering med symmetrisk nyckel eller autentisering med delad åtkomst nyckel är det enklaste sättet att autentisera med IoT Hub. Med symmetrisk nyckel autentisering är en Base64-nyckel kopplad till ditt IoT-enhets-ID i IoT Hub. Du inkluderar den nyckeln i dina IoT-program så att enheten kan presentera den när den ansluter till IoT Hub. 

### <a name="create-the-device-identity"></a>Skapa enhets identiteten 

Lägg till en ny IoT-enhet i IoT Hub med hjälp av antingen Azure Portal, Azure CLI eller IoT-tillägget för Visual Studio Code. Kom ihåg att underordnade enheter måste identifieras i IoT Hub som vanliga IoT-enheter, inte IoT Edge enheter. 

Ange följande information när du skapar den nya enhets identiteten: 

* Skapa ett ID för enheten.

* Välj **symmetrisk nyckel** som autentiseringstyp. 

* Du kan också välja att **Ange en överordnad enhet** och välja den IoT Edge gateway-enhet som den här underordnade enheten ska ansluta till. Det här steget är valfritt för autentisering med symmetrisk nyckel, men det rekommenderas eftersom inställning av en överordnad enhet möjliggör [Offline-kapacitet](offline-capabilities.md) för din underordnade enhet. Du kan alltid uppdatera enhets informationen för att lägga till eller ändra den överordnade senare. 

   ![Skapa enhets-ID med symmetrisk nyckel-auth i portalen](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Du kan använda [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) för att slutföra samma åtgärd. I följande exempel skapas en ny IoT-enhet med autentisering med symmetrisk nyckel och tilldelar en överordnad enhet: 

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Mer information om Azure CLI-kommandon för att skapa och överordnad och underordnad hantering av enheter finns i referens innehållet för [AZ IoT Hub Device-Identity-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) kommandon.


[Hämta och ändra sedan anslutnings strängen](#retrieve-and-modify-connection-string) så att enheten kan ansluta via sin Gateway. 

## <a name="register-device-x509-self-signed"></a>Registrera enhet (X. 509-egensignerat) 

För X. 509-självsignerad autentisering, som ibland kallas tumavtryck-autentisering, måste du skapa nya certifikat som ska placeras på din IoT-enhet. Dessa certifikat har ett tumavtryck i dem som du delar med IoT Hub för autentisering. 

Om du inte har någon certifikat utfärdare för att skapa X. 509-certifikat kan du [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md). När du genererar test certifikat för en underordnad enhet ska du använda samma rot certifikat utfärdares certifikat som genererade certifikat för din gateway-enhet. 

1. Skapa två enhets certifikat (primära och sekundära) för den underordnade enheten med ditt CA-certifikat. 

   Enhets certifikatet måste ha ett ämnes namn inställt på det enhets-ID som du ska använda när du registrerar IoT-enheten i Azure-IoT Hub. Den här inställningen krävs för autentisering.

2. Hämta SHA1-finger avtryck (kallas ett tumavtryck i IoT Hub-gränssnittet) från varje certifikat, som är en hexadecimal tecken sträng i 40. Använd följande openssl-kommando för att visa certifikatet och hitta finger avtrycket:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Kör det här kommandot två gånger, en gång för det primära certifikatet och en gång för det sekundära certifikatet. Du ger finger avtryck för båda certifikaten när du registrerar en ny IoT-enhet med självsignerade X. 509-certifikat. 

3. Navigera till din IoT-hubb i Azure Portal och skapa en ny IoT-enhets identitet med följande värden: 

   * Ange **enhets-ID: t** som matchar ämnes namnet för enhets certifikaten. 
   * Välj **X. 509 själv signerad** som autentiseringstyp.
   * Klistra in de hexadecimala strängarna som du kopierade från enhetens primära och sekundära certifikat.
   * Välj **Ange en överordnad enhet** och välj den IoT Edge gateway-enhet som den här underordnade enheten ska ansluta till. En överordnad enhet krävs för X. 509-autentisering av en underordnad enhet. 

   ![Skapa enhets-ID med X. 509 självsignerad autentisering i portalen](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Kopiera enhetens certifikat och nycklar till valfri plats på den underordnade enheten. Flytta också en kopia av det delade rot certifikat utfärdarens certifikat som skapade både gateway-enhetens certifikat och de efterföljande enhets certifikaten. 

   Du refererar till de här filerna i de löv enhets program som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfiler.

5. Beroende på vilket språk du föredrar kan du läsa exempel på hur X. 509-certifikat kan refereras i IoT-program: 

   * C#: [Konfigurera X. 509-säkerhet i din Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

Du kan använda [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) för att slutföra samma enhets skapande åtgärd. I följande exempel skapas en ny IoT-enhet med en självsignerad X. 509-autentisering och tilldelar en överordnad enhet: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Mer information om Azure CLI-kommandon för skapande av enheter, rapportgenerering och över-och underordnade hantering finns i referens innehållet för [AZ IoT Hub Device-Identity-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) kommandon.


[Hämta och ändra sedan anslutnings strängen](#retrieve-and-modify-connection-string) så att enheten kan ansluta via sin Gateway. 


## <a name="register-device-x509-ca-signed"></a>Registrera enhet (X. 509 CA signerad)

För X. 509-signerad autentisering (CA) behöver du ett rot certifikat för certifikat utfärdare som registrerats i IoT Hub som du använder för att signera certifikat för din IoT-enhet. Alla enheter som använder ett certifikat som har problem med rot certifikat utfärdaren eller något av dess mellanliggande certifikat kommer att tillåtas att autentisera. 

Det här avsnittet baseras på instruktionerna som beskrivs i IoT Hub artikel konfigurera [säkerhet för X. 509 i Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md). Följ stegen i det här avsnittet för att veta vilka värden som ska användas för att konfigurera en underordnad enhet som ansluter via en gateway. 

Om du inte har någon certifikat utfärdare för att skapa X. 509-certifikat kan du [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md). När du genererar test certifikat för en underordnad enhet ska du använda samma rot certifikat utfärdares certifikat som genererade certifikat för din gateway-enhet. 

1. Följ anvisningarna i avsnittet [Registrera x. 509 CA-certifikat i IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) i *Konfigurera X. 509-säkerhet i Azure IoT Hub*. I det avsnittet utför du följande steg: 

   1. Ladda upp ett rot certifikat för certifikat utfärdare. Om du använder demonstrations certifikaten är rot certifikat utfärdaren **\<sökväg >/certs/Azure-IoT-test-Only.root.ca.cert.pem**. 

   2. Kontrol lera att du äger det rot certifikat utfärdarens certifikat.

2. Följ anvisningarna i avsnittet [skapa en X. 509-enhet för din IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) i *Konfigurera X. 509-säkerhet i Azure IoT Hub*. I det avsnittet utför du följande steg: 

   1. Lägg till en ny enhet. Ange ett gement namn för **enhets-ID**och välj autentiseringstypen **X. 509 ca signerad**. 
   2. Ange en överordnad enhet. För underordnade enheter väljer du **Ange en överordnad enhet** och väljer den IoT Edge gateway-enhet som ska anslutas till IoT Hub. 

3. Skapa en certifikat kedja för din underordnade enhet. Använd samma rot certifikat för certifikat utfärdare som du laddade upp till IoT Hub för att skapa den här kedjan. Använd samma små enhets-ID som du gav din enhets identitet i portalen.

4. Kopiera enhetens certifikat och nycklar till valfri plats på den underordnade enheten. Flytta också en kopia av det delade rot certifikat utfärdarens certifikat som skapade både gateway-enhetens certifikat och de efterföljande enhets certifikaten. 

   Du refererar till de här filerna i de löv enhets program som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfiler.

5. Beroende på vilket språk du föredrar kan du läsa exempel på hur X. 509-certifikat kan refereras i IoT-program: 

   * C#: [Konfigurera X. 509-säkerhet i din Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

Du kan använda [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) för att slutföra samma enhets skapande åtgärd. I följande exempel skapas en ny IoT-enhet med X. 509 CA-signerad autentisering och tilldelar en överordnad enhet: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Mer information om Azure CLI-kommandon för att skapa och överordnad och underordnad hantering av enheter finns i referens innehållet för [AZ IoT Hub Device-Identity-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) kommandon.


[Hämta och ändra sedan anslutnings strängen](#retrieve-and-modify-connection-string) så att enheten kan ansluta via sin Gateway. 


## <a name="retrieve-and-modify-connection-string"></a>Hämta och ändra anslutnings sträng

När du har skapat en IoT-enhets identitet i portalen kan du hämta dess primära eller sekundära nycklar. En av dessa nycklar måste inkluderas i anslutnings strängen som du inkluderar i alla program som kommunicerar med IoT Hub. För symmetrisk nyckel autentisering ger IoT Hub den fullständigt utformade anslutnings strängen i enhets informationen för din bekvämlighet. Du måste lägga till extra information om gateway-enheten i anslutnings strängen. 

Anslutnings strängar för underordnade enheter behöver följande komponenter: 

* IoT-hubben som enheten ansluter till: `Hostname={iothub name}.azure-devices.net`
* Enhets-ID som är registrerat i hubben: `DeviceID={device ID}`
* Antingen den primära eller sekundära nyckeln: `SharedAccessKey={key}`
* Den gateway-enhet som enheten ansluter via. Ange **hostname** -värdet från filen config. yaml för IoT Edge gateway-enheten: `GatewayHostName={gateway hostname}`

Tillsammans ser en fullständig anslutnings sträng ut så här:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Om du har upprättat en överordnad/underordnad-relation för denna underordnade enhet (krävs för X. 509-autentisering men valfritt för symmetrisk nyckel autentisering), kan du förenkla anslutnings strängen genom att anropa gatewayen direkt som anslutnings värd. Ett exempel: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

I det här läget bör du ha en IoT Edge enhet registrerad och konfigurerad som en gateway. Du har också en underordnad IoT-enhet registrerad och pekar på dess gateway-enhet. Det sista steget är att placera certifikat på en underordnad enhet så att det kan ansluta till gatewayen på ett säkert sätt. 

Fortsätt till nästa artikel i Gateway-serien och [Anslut en underordnad enhet till en Azure IoT Edge Gateway](how-to-connect-downstream-device.md).


## <a name="next-steps"></a>Nästa steg

Genom att slutföra den här artikeln bör du ha en IoT Edge-enhet som fungerar som en transparent gateway och en underordnad enhet som är registrerad i IoT Hub. Därefter måste du konfigurera de underordnade enheterna så att de litar på gateway-enheten och ansluta till den på ett säkert sätt. Mer information finns i [en underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).
