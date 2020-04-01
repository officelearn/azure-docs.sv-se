---
title: Autentisera nedströmsenheter – Azure IoT Edge | Microsoft-dokument
description: Så här autentiserar du underordnade enheter eller bladenheter till IoT Hub och dirigerar deras anslutning via Azure IoT Edge gateway-enheter.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 999204cf2fc8ce18b42f873b9d34af4e6c08052b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411501"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autentisera en underordnad enhet på Azure IoT Hub

I ett genomskinligt gatewayscenario behöver nedströmsenheter (kallas ibland lövenheter eller underordnade enheter) identiteter i IoT Hub som alla andra enheter. Den här artikeln går igenom alternativen för att autentisera en nedströmsenhet till IoT Hub och visar sedan hur gatewayanslutningen deklareras.

Det finns tre allmänna steg för att konfigurera en lyckad transparent gateway-anslutning. Den här artikeln beskriver det andra steget:

1. Gateway-enheten måste kunna ansluta till nedströmsenheter på ett säkert sätt, ta emot kommunikation från nedströmsenheter och dirigera meddelanden till rätt mål. Mer information finns i [Konfigurera en IoT Edge-enhet så att den fungerar som en transparent gateway](how-to-create-transparent-gateway.md).
2. **Nedströmsenheten måste ha en enhetsidentitet för att kunna autentisera med IoT Hub och veta att kommunicera via sin gateway-enhet.**
3. Nedströmsenheten måste ansluta till gatewayenheten på ett säkert sätt. Mer information finns i [Ansluta en nedströmsenhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).

Underordnade enheter kan autentisera med IoT Hub med en av tre metoder: symmetriska nycklar (kallas ibland delade åtkomstnycklar), X.509 självsignerade certifikat eller X.509-certifikatutfärdare (CA) signerade certifikat. Autentiseringsstegen liknar de steg som används för att konfigurera alla enheter som inte är IoT-Edge-enheter med IoT Hub, med små skillnader för att deklarera gateway-relationen.

Stegen i den här artikeln visar manuell enhetsetablering, inte automatisk etablering med Azure IoT Hub Device Provisioning Service (DPS). Det går inte att etablera underordnade enheter med DPS.

## <a name="prerequisites"></a>Krav

Slutför stegen i [Konfigurera en IoT Edge-enhet för att fungera som en transparent gateway](how-to-create-transparent-gateway.md). Om du använder X.509-autentisering för nedströmsenheten måste du använda samma certifikatgenererande skript som du konfigurerar i den genomskinliga gateway-artikeln.

Den här artikeln refererar till *gateway-värdnamnet* vid flera tillfällen. Gateway-värdnamnet deklareras i parametern **hostname** för filen config.yaml på IoT Edge gateway-enheten. Det refereras till i anslutningssträngen för nedströmsenheten. Gateway-värdnamnet måste kunna lösas till en IP-adress, antingen med DNS eller en värdfilpost.

## <a name="register-device-symmetric-key"></a>Registrera enhet (symmetrisk nyckel)

Autentisering av symmetrisk nyckel, eller autentisering med delad åtkomstnyckel, är det enklaste sättet att autentisera med IoT Hub. Med symmetrisk nyckelautentisering associeras en base64-nyckel med IoT-enhets-ID i IoT Hub. Du inkluderar den nyckeln i dina IoT-program så att enheten kan presentera den när den ansluter till IoT Hub.

### <a name="create-the-device-identity"></a>Skapa enhetsidentiteten

Lägg till en ny IoT-enhet i din IoT-hubb med antingen Azure-portalen, Azure CLI eller IoT-tillägget för Visual Studio Code. Kom ihåg att nedströmsenheter måste identifieras i IoT Hub som vanliga IoT-enheter, inte IoT Edge-enheter.

När du skapar den nya enhetsidentiteten anger du följande information:

* Skapa ett ID för enheten.

* Välj **Symmetrisk nyckel** som autentiseringstyp.

* Du kan också välja att **ange en överordnad enhet** och välja den IoT Edge-gatewayenhet som den här nedströmsenheten ska anslutas via. Det här steget är valfritt för symmetrisk nyckelautentisering, men det rekommenderas eftersom inställningen av en överordnad enhet aktiverar [offlinefunktioner](offline-capabilities.md) för nedströmsenheten. Du kan alltid uppdatera enhetsinformationen för att lägga till eller ändra den överordnade senare.

   ![Skapa enhets-ID med symmetrisk nyckelautentisering i portalen](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Du kan använda [IoT-tillägget för Azure CLI för](https://github.com/Azure/azure-iot-cli-extension) att slutföra samma åtgärd. I följande exempel skapas en ny IoT-enhet med symmetrisk nyckelautentisering och tilldelas en överordnad enhet:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Mer information om Azure CLI-kommandon för att skapa enheter och hantering av överordnade/underordnade enheter finns i referensinnehållet för [az iot-hubenhetsidentitetskommandon.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)


Hämta [och ändra sedan anslutningssträngen](#retrieve-and-modify-connection-string) så att enheten vet att den ansluter via gatewayen.

## <a name="register-device-x509-self-signed"></a>Registrera enhet (X.509 självsignerad)

För X.509 självsignerad autentisering, ibland kallad tumavtrycksautentisering, måste du skapa nya certifikat som ska placeras på din IoT-enhet. Dessa certifikat har ett tumavtryck i dem som du delar med IoT Hub för autentisering.

Om du inte har någon certifikatutfärdare för att skapa X.509-certifikat kan du [skapa democertifikat för att testa IoT Edge-enhetsfunktioner](how-to-create-test-certificates.md). När du skapar testcertifikat för nedströmsenheten använder du samma rotcertifikatutfärdare som genererade certifikaten för gateway-enheten.

1. Med hjälp av certifikatutfärdarcertifikatet skapar du två enhetscertifikat (primära och sekundära) för nedströmsenheten.

   Enhetscertifikatet måste ha ämnesnamnet inställt på enhets-ID som du ska använda när du registrerar IoT-enheten i Azure IoT Hub. Den här inställningen krävs för autentisering.

2. Hämta SHA1-fingeravtrycket (kallas ett tumavtryck i IoT Hub-gränssnittet) från varje certifikat, vilket är en 40 hexadecimal teckensträng. Använd följande kommandot openssl för att visa certifikatet och hitta fingeravtrycket:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Kör det här kommandot två gånger, en gång för det primära certifikatet och en gång för det sekundära certifikatet. Du anger fingeravtryck för båda certifikaten när du registrerar en ny IoT-enhet med självsignerade X.509-certifikat.

3. Navigera till din IoT-hubb i Azure-portalen och skapa en ny IoT-enhetsidentitet med följande värden:

   * Ange **enhets-ID:n** som matchar enhetscertifikatens ämnesnamn.
   * Välj **X.509 Självsignerad** som autentiseringstyp.
   * Klistra in de hexadecimala strängarna som du kopierade från enhetens primära och sekundära certifikat.
   * Välj **Ange en överordnad enhet** och välj den IoT Edge-gatewayenhet som den här nedströmsenheten ska anslutas via. En överordnad enhet krävs för X.509-autentisering av en nedströmsenhet.

   ![Skapa enhets-ID med X.509 självsignerad autentisering i portalen](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Kopiera enhetscertifikatet och nycklarna till valfri plats på nedströmsenheten. Flytta också en kopia av det delade rotcertifikatutfärdarcertifikatet som genererade både gateway-enhetscertifikatet och de underordnade enhetscertifikaten.

   Du refererar till dessa filer i bladenhetsprogram som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som Secure copy [protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfilerna.

5. Beroende på vilket språk du föredrar kan du granska exempel på hur X.509-certifikat kan refereras i IoT-program:

   * C#: [Konfigurera X.509-säkerhet i azure IoT-hubben](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Du kan använda [IoT-tillägget för Azure CLI för](https://github.com/Azure/azure-iot-cli-extension) att slutföra samma enhetsskapande åtgärd. I följande exempel skapas en ny IoT-enhet med självsignerad X.509-autentisering och en överordnad enhet tilldelas:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Mer information om Azure CLI-kommandon för skapande av enheter, certifikatgenerering och överordnad och underordnad hantering finns i referensinnehållet för [az iot-hubenhetsidentitetskommandon.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Hämta [och ändra sedan anslutningssträngen](#retrieve-and-modify-connection-string) så att enheten vet att den ansluter via gatewayen.

## <a name="register-device-x509-ca-signed"></a>Registrera enhet (X.509 CA signerad)

För X.509 certifikatutfärdare (CA) signerad autentisering behöver du ett rotcertifikatutfärdarcertifikat som registrerats i IoT Hub och som du använder för att signera certifikat för din IoT-enhet. Alla enheter som använder ett certifikat som har problem med rotcertifikatutfärdaren eller något av dess mellanliggande certifikat tillåts autentiseras.

Det här avsnittet baseras på instruktionerna i IoT Hub-artikeln [Konfigurera X.509-säkerhet i azure IoT-hubben](../iot-hub/iot-hub-security-x509-get-started.md). Följ stegen i det här avsnittet för att veta vilka värden som ska användas för att konfigurera en nedströmsenhet som ansluter via en gateway.

Om du inte har någon certifikatutfärdare för att skapa X.509-certifikat kan du [skapa democertifikat för att testa IoT Edge-enhetsfunktioner](how-to-create-test-certificates.md). När du skapar testcertifikat för nedströmsenheten använder du samma rotcertifikatutfärdare som genererade certifikaten för gateway-enheten.

1. Följ instruktionerna i [register X.509 CA-certifikaten till avsnittet IoT-hubb](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) i *Konfigurera X.509-säkerhet i azure IoT-hubben*. I det avsnittet utför du följande steg:

   1. Ladda upp ett rotcertifikatutfärdarcertifikat. Om du använder democertifikaten är rotcertifikatutfärdaren ** \<sökväg>/certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Kontrollera att du äger rotcertifikatet.

2. Följ instruktionerna i [avsnittet Skapa en X.509-enhet för din IoT-hubb](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) i *Konfigurera X.509-säkerhet i azure IoT-hubben*. I det avsnittet utför du följande steg:

   1. Lägg till en ny enhet. Ange ett gemener namn för **enhets-ID**och välj autentiseringstyp **X.509 CA Signerad**.
   2. Ange en överordnad enhet. För underordnade enheter väljer du **Ange en överordnad enhet** och väljer den IoT Edge-gatewayenhet som tillhandahåller anslutningen till IoT Hub.

3. Skapa en certifikatkedja för nedströmsenheten. Använd samma rotcertifikatutfärdarcertifikat som du har laddat upp till IoT Hub för att skapa den här kedjan. Använd samma gemener enhets-ID som du gav till enhetens identitet i portalen.

4. Kopiera enhetscertifikatet och nycklarna till valfri plats på nedströmsenheten. Flytta också en kopia av det delade rotcertifikatutfärdarcertifikatet som genererade både gateway-enhetscertifikatet och de underordnade enhetscertifikaten.

   Du refererar till dessa filer i bladenhetsprogram som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som Secure copy [protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfilerna.

5. Beroende på vilket språk du föredrar kan du granska exempel på hur X.509-certifikat kan refereras i IoT-program:

   * C#: [Konfigurera X.509-säkerhet i azure IoT-hubben](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Du kan använda [IoT-tillägget för Azure CLI för](https://github.com/Azure/azure-iot-cli-extension) att slutföra samma enhetsskapande åtgärd. I följande exempel skapas en ny IoT-enhet med X.509 CA-signerad autentisering och en överordnad enhet tilldelas:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Mer information finns i Azure CLI-referensinnehållet för [az iot-hubenhetsidentitetskommandon.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Hämta [och ändra sedan anslutningssträngen](#retrieve-and-modify-connection-string) så att enheten vet att den ansluter via gatewayen.

## <a name="retrieve-and-modify-connection-string"></a>Hämta och ändra anslutningssträng

När du har skapat en IoT-enhetsidentitet i portalen kan du hämta dess primära eller sekundära nycklar. En av dessa nycklar måste inkluderas i anslutningssträngen som program använder för att kommunicera med IoT Hub. För symmetrisk nyckelautentisering tillhandahåller IoT Hub den fullt formade anslutningssträngen i enhetsinformationen för din bekvämlighet. Du måste lägga till extra information om gateway-enheten till anslutningssträngen.

Anslutningssträngar för nedströmsenheter behöver följande komponenter:

* IoT-hubben som enheten ansluter till:`Hostname={iothub name}.azure-devices.net`
* Enhets-ID som registrerats med navet:`DeviceID={device ID}`
* Antingen den primära eller sekundära nyckeln:`SharedAccessKey={key}`
* Gateway-enheten som enheten ansluter via. Ange **värdnamnsvärdet** från IoT Edge-gateway-enhetens config.yaml-fil:`GatewayHostName={gateway hostname}`

Sammantaget ser en komplett anslutningssträng ut:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Om du har upprättat en överordnad/underordnad relation för den här underordnade enheten kan du förenkla anslutningssträngen genom att anropa gatewayen direkt som anslutningsvärd. Överordnade/underordnade relationer krävs för X.509-autentisering men är valfria för symmetrisk nyckelautentisering. Ett exempel:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Nu bör du ha en IoT Edge-enhet registrerad och konfigurerad som en gateway. Du har också en nedströms IoT-enhet registrerad och pekar på dess gateway-enhet. Det sista steget är att placera certifikat på din nedströms enhet så att den kan ansluta till gatewayen på ett säkert sätt.

Fortsätt till nästa artikel i gateway-serien, [Anslut en nedströmsenhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>Nästa steg

Genom att fylla i den här artikeln bör du ha en IoT Edge-enhet som fungerar som en transparent gateway och en nedströmsenhet som är registrerad med en IoT-hubb. Därefter måste du konfigurera dina nedströmsenheter för att lita på gateway-enheten och ansluta till den på ett säkert sätt. Mer information finns i [Ansluta en nedströmsenhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).
