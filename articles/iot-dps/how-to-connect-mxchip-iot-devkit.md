---
title: Hur du använder Azure IoT Hub Device Provisioning-tjänsten Automatisk etablering för att registrera MXChip IoT DevKit med IoT Hub | Microsoft Docs
description: Hur du använder Azure IoT Hub Device Provisioning-tjänsten Automatisk etablering för att registrera MXChip IoT DevKit med IoT Hub.
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 300bde27f956b449d1e0e73f7efb54a13df27b0c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145673"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Använd Azure IoT Hub Device Provisioning-tjänsten Automatisk etablering för att registrera MXChip IoT DevKit med IoT Hub

Den här artikeln beskriver hur du använder Azure IoT Hub Device Provisioning Service [Automatisk etablering](concepts-auto-provisioning.md), för att registrera MXChip IoT DevKit med Azure IoT Hub. I den här guiden får du lära dig att:

* Konfigurera globala slutpunkten för enhetsetableringstjänst på en enhet.
* Använd ett unikt enhets-hemlighet (ud) för att generera ett X.509-certifikat.
* Registrera en enskild enhet.
* Kontrollera att enheten är registrerad.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino-kompatibelt tavla med omfattande kringutrustning och sensorer. Du kan utveckla för den med hjälp av den [Visual Studio Code-tillägg för Arduino](https://aka.ms/arduino). DevKit levereras med en växande [projekt catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att vägleda dina prototyp Internet of Things (IoT)-lösningar som utnyttjar Azure-tjänster.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här självstudien måste du först göra följande uppgifter:

* Förbereda din DevKit genom att följa stegen i [ansluta IoT DevKit AZ3166 på Azure IoT Hub i molnet](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Uppgradera till den senaste inbyggda programvaran (1.3.0 eller senare) med den [DevKit för uppdatering av inbyggd programvara](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) självstudien.
* Skapa och länka en IoT-hubb med en device provisioning-tjänstinstans genom att följa stegen i [konfigurera IoT Hub Device Provisioning-tjänsten med Azure portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>Skapa och distribuera Automatisk etablering registreringsprogramvara till enheten

Ansluta DevKit till den device provisioning-tjänstinstansen som du skapade:

1. I Azure-portalen väljer du den **översikt** rutan i din device provisioning-tjänsten och anteckna ned den **Global enhetsslutpunkt** och **ID-omfång** värden.
  ![DPS Global slutpunkt och ID-omfång](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Kontrollera att du har `git` installerat på datorn och att den läggs till i miljövariablerna som är tillgängliga för kommandofönstret. Se [programvaran Freedom Conservancys Git-klientverktyg](https://git-scm.com/download/) ha den senaste versionen installerad.

3. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för den device provisioning-tjänsten exempelkoden:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Öppna Visual Studio Code, ansluta DevKit till din dator och öppna sedan den mapp som innehåller koden som du har klonat.

5. Öppna **DevKitDPS.ino**. Sök och Ersätt `[Global Device Endpoint]` och `[ID Scope]` med de värden som du just skrev ned.
  ![DPS Endpoint](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) du kan lämna den **registrationId** tom. Programmet skapar en åt dig baserat på MAC-adress och inbyggd programvara version. Om du vill anpassa registrerings-ID kan du bara använda alfanumeriska, gemener och bindestreck kombinationer med högst 128 tecken. Mer information finns i [hantera enhetsregistreringar med Azure-portalen](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

6. Använda snabb öppna i VS Code (Windows: `Ctrl+P`, macOS: `Cmd+P`) och skriv *uppgift enhet-överföringen* att skapa och ladda upp koden till DevKit.

7. Utdatafönstret visar om uppgiften har slutförts.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Spara en unikt enhets-hemlighet på en STSAFE security-chip

Automatisk etablering kan konfigureras på en enhet baserat på enhetens [attesteringsmetod](concepts-security.md#attestation-mechanism). MXChip IoT DevKit använder den [enheten Identity Composition Engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) från den [Trusted Computing Group](https://trustedcomputinggroup.org). En *unikt enhets-hemlighet* (ud) som sparas i en säkerhetsgrupp för STSAFE chip på DevKit används för att generera enheten användarens unika [X.509-certifikat](concepts-security.md#x509-certificates). Certifikatet används senare under registreringsprocessen i device provisioning-tjänst och under registreringen vid körning.

En typisk unikt enhets-hemlighet är en 64 tecken lång sträng, som visas i följande exempel:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Strängen har delats upp till tecken-par som används i beräkningen säkerhet. I föregående exempel ud har åtgärdats till: `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16` , `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

Så här sparar ett unikt enhets-hemlighet på DevKit:

1. Öppna seriell övervakaren med hjälp av ett verktyg, till exempel Putty. Se [Använd konfigurationsläge](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) mer information.

2. Med DevKit ansluten till datorn, håll ned den **A** knappen och tryck sedan på och släpp den **återställa** knappen för att ange konfigurationsläge. Skärmen visar DevKit ID och konfiguration.

3. Ta prov ud sträng och ändra ett eller flera tecken till andra värden mellan `0` och `f` för dina egna ud.

4. I fönstret seriell övervakaren anger *set_dps_uds [your_own_uds_value]* och välj RETUR.
  > [!NOTE]
  > Exempel: Om du ställer in din egen ud genom att ändra de två sista tecknen till `f`, måste du ange kommandot så här: `set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff`.

5. Utan att stänga fönstret seriell, trycker du på den **återställa** DevKit-knappen.

6. Anteckna den **DevKit MAC-adress** och **DevKit Version på inbyggd programvara** värden.
  ![Version av inbyggd programvara](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>Generera ett X.509-certifikat

### <a name="windows"></a>Windows

1. Öppna Utforskaren och gå till den mapp som innehåller den device provisioning-tjänsten exempelkod som du klonade tidigare. I den **.build** mapp, hitta och kopiera **DPS.ino.bin** och **DPS.ino.map**.
  ![Genererade filer](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Om du har ändrat den `built.path` konfiguration för Arduino till en annan mapp som du vill söka efter dessa filer i mappen som du har konfigurerat.

2. Klistra in dessa två filer till den **verktyg** mappen på samma nivå med den **.build** mapp.

3. Kör **dps_cert_gen.exe**. Följ anvisningarna för att ange din **ud**, **MAC-adress** för DevKit, och **version av inbyggd programvara** att generera X.509-certifikat.
  ![Kör dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. När X.509-certifikatet har skapats en **.pem** certifikatet sparas i samma mapp.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Skapa en post för enhetsregistrering i device provisioning-tjänst

1. Gå till din instans av Device Provisioning-tjänsten i Azure-portalen. Välj **hantera registreringar**, och välj sedan den **enskilda registreringar** fliken. ![Enskilda registreringar](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Välj **Lägg till**.

3. På panelen ”Lägg till registrering”:
   - Välj **X.509** under **mekanism**
   - Klicka på ”Välj en fil” under **primära .pem- eller .cer-certifikatfilen**
   - i dialogrutan Öppna går du till och ladda upp den **.pem** certifikat som du just genererade
   - lämna resten som standard och klicka på **spara**

   ![Överför certifikat](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

  > [!NOTE]
  > Om du har ett fel med det här meddelandet:
  >
  > `{"message":"BadRequest:{\r\n \"errorCode\": 400004,\r\n \"trackingId\": \"1b82d826-ccb4-4e54-91d3-0b25daee8974\",\r\n \"message\": \"The certificate is not a valid base64 string value\",\r\n \"timestampUtc\": \"2018-05-09T13:52:42.7122256Z\"\r\n}"}`
  >
  > Öppna certifikatfilen **.pem** som text (Öppna med anteckningar eller valfri textredigerare), och ta bort raderna:
  >
  > `"-----BEGIN CERTIFICATE-----"` och `"-----END CERTIFICATE-----"`.
  >


## <a name="start-the-devkit"></a>Starta DevKit

1. Öppna VS Code och seriell övervakaren.

2. Tryck på den **återställa** knappen på din DevKit.

Du kan se DevKit starta registreringen med din enhetsetableringstjänst.

![VS Code-utdata](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>Kontrollera att DevKit är registrerad med Azure IoT Hub

När enheten startar sker de följande:

1. Enheten skickar en begäran om registrering till din enhetsetableringstjänst.
2. Device provisioning-tjänst skickar tillbaka en registreringskontroll som enheten svarar.
3. Om registringen har lyckats skickar till enhetsetableringstjänsten URI: N för IoT Hub, enhets-ID och den krypterade nyckeln till enheten.
4. IoT Hub-klientprogrammet på enheten ansluter till hubben.
5. På lyckad anslutning till hubben visas enheten i IoT Hub Device Explorer.
  ![Enhet har registrerats](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-the-device-id"></a>Ändra enhets-ID

Standard-enhets-ID som registrerats med Azure IoT Hub är *AZ3166*. Om du vill ändra det ID: T, följer du anvisningarna i [anpassa enhets-ID](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan referera till Iot DevKit [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), eller kontakta följande kanaler för support:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att registrera en enhet på ett säkert sätt till device provisioning-tjänst med hjälp av enheten Identity Composition Engine, så att enheten kan automatiskt registrera med Azure IoT Hub. 

Sammanfattningsvis ska du lärt dig hur du:

> [!div class="checklist"]
> * Konfigurera globala slutpunkten för enhetsetableringstjänst på en enhet.
> * Använd ett unikt enhets-hemlighet för att generera ett X.509-certifikat.
> * Registrera en enskild enhet.
> * Kontrollera att enheten är registrerad.

Lär dig hur du [skapa och etablera en simulerad enhet](./quick-create-simulated-device.md).

