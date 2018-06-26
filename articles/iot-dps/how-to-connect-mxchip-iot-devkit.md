---
title: Hur du använder Azure IoT-hubb Device etablering Service automatiskt-etablering för att registrera MXChip IoT DevKit med IoT-hubb | Microsoft Docs
description: Hur du använder Azure IoT-hubb Device etablering Service automatiskt-etablering för att registrera MXChip IoT DevKit med IoT-hubb.
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: a5030c7363d02fe9600183e058f2ff3cc23e9475
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750374"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Använd Azure IoT-hubb Device etablering Service automatiskt-etablering för att registrera MXChip IoT DevKit med IoT-hubb

Den här artikeln beskriver hur du använder Azure IoT-hubb Device etablering Service [Automatisk etablering](concepts-auto-provisioning.md), för att registrera MXChip IoT DevKit med Azure IoT Hub. I den här guiden får du lära dig hur man:

* Konfigurera globala slutpunkten för enheten etableras på en enhet.
* Använd ett unikt enhets-hemlighet (UDS) för att generera ett X.509-certifikat.
* Registrera en enskild enhet.
* Kontrollera att enheten är registrerad.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino-kompatibel skiva med sensorer och omfattande kringutrustning. Du kan utveckla för den med hjälp av den [Visual Studio Code-tillägget för Arduino](https://aka.ms/arduino). DevKit medföljer en växande [projekt katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att hjälpa dina prototyp Sakernas Internet (IoT) lösningar som utnyttjar Azure-tjänster.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här självstudiekursen måste du först göra följande uppgifter:

* Förbered din DevKit genom att följa stegen i [ansluta IoT DevKit AZ3166 till Azure IoT-hubben i molnet](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Uppgradera till den senaste inbyggda (1.3.0 eller senare) med den [uppdatering DevKit firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) kursen.
* Skapa och länka en IoT-hubb med en enhet som etablerar tjänstinstans genom att följa stegen i [konfigurera IoT Hub-enhet Etableringstjänsten med Azure-portalen](/azure/iot-dps/quick-setup-auto-provision).

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>Skapa och distribuera programvara för etablering av automatisk registrering till enheten

Att ansluta DevKit till enheten etablering service-instans som du skapade:

1. I Azure portal, väljer du den **översikt** rutan av din enhet för etablering av tjänst och Observera ned den **globala enheten endpoint** och **ID Scope** värden.
  ![DP globala slutpunkt och Scope-ID](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Kontrollera att du har `git` installerat på datorn och att den har lagts till miljövariabler som är tillgängliga för kommandofönstret. Se [programvara frihet naturvårdande Git-klientverktyg](https://git-scm.com/download/) har den senaste versionen installerad.

3. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för enheten etablering exempelkod för tjänsten:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Öppna Visual Studio Code, ansluta DevKit till din dator och öppna sedan den mapp som innehåller den kod som du har klonat.

5. Öppna **DevKitDPS.ino**. Sök och Ersätt `[Global Device Endpoint]` och `[ID Scope]` med värden som du antecknade bara ned.
  ![DP Endpoint](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) du kan lämna den **registrationId** tomt. Genereras ett automatiskt baserat på MAC-adress och inbyggd programvara version. Om du vill anpassa registrerings-ID måste du bara använda alfanumeriska, gemener och bindestreck kombinationer med högst 128 tecken. Mer information finns i [hantera enhetsregistrering med Azure-portalen](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

6. Använda snabb öppna i VS-kod (Windows: `Ctrl+P`, macOS: `Cmd+P`) och skriv *uppgift enheten överför* skapa och ladda upp koden till DevKit.

7. Utdatafönstret visar om aktiviteten har slutförts.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Spara en hemlighet som unikt enhets på en STSAFE säkerhetschip

Automatisk etablering kan konfigureras på en enhet baserat på enhetens [attestering mekanism](concepts-security.md#attestation-mechanism). IoT-DevKit MXChip använder den [enhetens identitet sammansättning motorn](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) från den [Trusted Computing Group](https://trustedcomputinggroup.org). En *unikt enhets hemlighet* (UDS) som sparas i en säkerhetsgrupp för STSAFE krets på DevKit används för att generera enheten har unika [X.509-certifikat](concepts-security.md#x509-certificates). Certifikatet används senare för registreringen i enheten etableras och under registreringen vid körning.

En typisk unikt enhets-hemlighet är en sträng med 64 tecken, som visas i följande exempel:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Var och en av två tecken används som hexadecimalt värde i beräkningen säkerhet. The preceding sample UDS is resolved to: `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

Att spara en hemlighet som unikt enhets på DevKit:

1. Öppna serial övervakaren med hjälp av ett verktyg, till exempel Putty. Se [Använd konfigurationsläge](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) mer information.

2. Med DevKit ansluten till datorn, håll ned den **A** knappen, och tryck sedan på och släpper den **återställa** att ange konfigurationsläge. Skärmen visar DevKit ID och konfiguration.

3. Ta prov UDS sträng och ändra en eller flera tecken till andra värden mellan `0` och `f` för egna UDS.

4. I fönstret seriella övervakaren anger *set_dps_uds [your_own_uds_value]* och välj RETUR.
  > [!NOTE]
  > Om du ställer in en egen UDS genom att ändra de två sista tecknen till exempelvis `f`, måste du ange kommandot som detta: `set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff`.

5. Utan att stänga fönstret seriella trycker du på den **återställa** på DevKit-knappen.

6. Notera den **DevKit MAC-adress** och **DevKit Version på inbyggd programvara** värden.
  ![Version på inbyggd programvara](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>Generera ett X.509-certifikat

### <a name="windows"></a>Windows

1. Öppna Utforskaren och gå till den mapp som innehåller enheten etablering exempelkod för tjänsten som du tidigare har klonat. I den **.build** mapp, hitta och kopiera **DPS.ino.bin** och **DPS.ino.map** till den mapp som innehåller koden.
  ![Genererade filer](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Om du har ändrat den `built.path` konfigurationen för Arduino till en annan mapp som du vill söka efter dessa filer i mappen som du har konfigurerat.

2. Klistra in de här två filerna i den **verktyg** mappen på samma nivå med den **.build** mapp.

3. Kör **dps_cert_gen.exe**. Följ anvisningarna för att ange din **UDS**, **MAC-adress** för DevKit, och **version på inbyggd programvara** att generera X.509-certifikat.
  ![Kör DP-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. När X.509-certifikatet har skapats en **.pem** certifikat sparas i samma mapp.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Skapa en post för registrering av enheten i enheten etablering av tjänst

1. Gå till din enhet etablering tjänstinstansen i Azure-portalen. Välj **hantera registreringar**, och välj sedan den **enskilda registreringar** fliken. ![Enskilda registreringar](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Välj **Lägg till**.

3. På panelen ”Lägg till registrering”:
   - Välj **X.509** under **mekanism**
   - Klicka på ”Välj en fil” under **certifikatet för primär .pem eller .cer-fil**
   - i dialogrutan Öppna går du till och ladda upp den **.pem** certifikat som du just genererade
   - lämna resten som standard och klicka på **spara**

   ![Överför certifikat](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

  > [!NOTE]
  > Om du har ett fel med meddelandet:
  >
  > `{"message":"BadRequest:{\r\n \"errorCode\": 400004,\r\n \"trackingId\": \"1b82d826-ccb4-4e54-91d3-0b25daee8974\",\r\n \"message\": \"The certificate is not a valid base64 string value\",\r\n \"timestampUtc\": \"2018-05-09T13:52:42.7122256Z\"\r\n}"}`
  >
  > Öppna certifikatfilen **.pem** som text (Öppna med anteckningar eller valfri textredigerare), och ta bort rader:
  >
  > `"-----BEGIN CERTIFICATE-----"` och `"-----END CERTIFICATE-----"`.
  >


## <a name="start-the-devkit"></a>Starta DevKit

1. Öppna VS-kod och seriella övervakaren.

2. Tryck på den **återställa** din DevKit-knappen.

Du kan se DevKit starta registreringen med din tjänst för etablering.

![VS felkoden utdata](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>Kontrollera att DevKit har registrerats med Azure IoT-hubb

Efter att enheten startar utföras följande åtgärder:

1. Enheten skickar en begäran om registrering till din enhet etableras.
2. Enheten som etablerar tjänsten skickar tillbaka en registrering utmaning som enheten svarar.
3. På lyckad registrering skickar enheten etableras IoT-hubb URI, enhets-ID och den krypterade nyckeln till enheten.
4. IoT-hubb-klientprogrammet på enheten ansluter till din hubb.
5. Anslutningen till hubben visas enheten visas i IoT Hub-enhet Explorer.
  ![Enheten har registrerats](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-the-device-id"></a>Ändra enhets-ID

Standard-enhets-ID som registrerats med Azure IoT-hubben är *AZ3166*. Om du vill ändra ID, följ instruktionerna i [anpassa enhets-ID](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, läser du Iot DevKit [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), eller nå ut till följande kanaler för support:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

I kursen får du lärt dig att registrera en enhet på ett säkert sätt till enheten etablering tjänsten med hjälp av enhetens identitet sammansättning motorn, så att enheten kan registreras automatiskt med Azure IoT-hubben. 

Sammanfattningsvis: du har lärt dig hur du:

> [!div class="checklist"]
> * Konfigurera globala slutpunkten för enheten etableras på en enhet.
> * Använd ett unikt enhets-hemlighet för att generera ett X.509-certifikat.
> * Registrera en enskild enhet.
> * Kontrollera att enheten är registrerad.

Lär dig hur du [skapa och etablera en simulerad enhet](./quick-create-simulated-device.md).

