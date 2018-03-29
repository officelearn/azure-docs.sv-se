---
title: Hur du använder MXChip IoT DevKit för att ansluta till Azure IoT-hubb enheten Etableringstjänsten | Microsoft Docs
description: Hur du använder MXChip IoT DevKit för att ansluta till Azure IoT-hubb Device etablering Service
services: iot-dps
keywords: ''
author: liydu
ms.author: liydu
ms.date: 02/20/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 502f22a39622e9a8341e1daca8c9899fd8b7d7d1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="connect-the-mxchip-iot-devkit-to-the-azure-iot-hub-device-provisioning-service"></a>Ansluta MXChip IoT DevKit till Azure IoT-hubb enheten etablering av tjänst

Den här artikeln beskriver hur du konfigurerar MXChip IoT DevKit så att den registreras automatiskt med Azure IoT-hubben med hjälp av Azure IoT-enhet etablering av tjänsten. I den här guiden får du lära dig hur man:

* Konfigurera globala slutpunkten för enheten etableras på en enhet.
* Använd ett unikt enhets-hemlighet (UDS) för att generera ett X.509-certifikat.
* Registrera en enskild enhet.
* Kontrollera att enheten är registrerad.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino-kompatibel skiva med sensorer och omfattande kringutrustning. Du kan utveckla för den med hjälp av den [Visual Studio Code-tillägget för Arduino](https://aka.ms/arduino). DevKit medföljer en växande [projekt katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att hjälpa dina prototyp Sakernas Internet (IoT) lösningar som utnyttjar Azure-tjänster.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här självstudiekursen måste du först göra följande uppgifter:

* Förbered din DevKit genom att följa stegen i [ansluta IoT DevKit AZ3166 till Azure IoT-hubben i molnet](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Uppgradera till den senaste inbyggda (1.3.0 eller senare) med den [uppdatering DevKit firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) kursen.
* Skapa och länka en IoT-hubb med en enhet som etablerar tjänstinstans genom att följa stegen i [konfigurera IoT Hub-enhet Etableringstjänsten med Azure-portalen](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision).

## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Ställa in enheten etablering tjänstkonfiguration på enheten

Att ansluta DevKit till enheten etablering service-instans som du skapade:

1. I Azure portal, väljer du den **översikt** rutan av din enhet för etablering av tjänst och Observera ned den **globala enheten endpoint** och **ID Scope** värden.
  ![DP globala slutpunkt och Scope-ID](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Kontrollera att du har `git` installerat på datorn och att den har lagts till miljövariabler som är tillgängliga för kommandofönstret. Se [programvara frihet naturvårdande Git-klientverktyg](https://git-scm.com/download/) har den senaste versionen installerad.

3. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för enheten etablering exempelkod för tjänsten:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Öppna Visual Studio Code ansluta DevKit till din dator och öppna sedan den mapp som innehåller den kod som du har klonat.

5. Öppna **DevKitDPS.ino**. Sök och Ersätt `[Global Device Endpoint]` och `[ID Scope]` med värden som du antecknade bara ned.
  ![DP Endpoint](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) du kan lämna den **registrationId** tomt. Genereras ett automatiskt baserat på MAC-adress och inbyggd programvara version. Om du vill anpassa registrerings-ID måste du bara använda alfanumeriska, gemener och bindestreck kombinationer med högst 128 tecken. Mer information finns i [hantera enhetsregistrering med Azure-portalen](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments).

6. Använda snabb öppna i VS-kod (Windows: `Ctrl+P`, macOS: `Cmd+P`) och skriv *uppgift enheten överför* skapa och ladda upp koden till DevKit.

7. Utdatafönstret visar om aktiviteten har slutförts.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Spara en hemlighet som unikt enhets på en STSAFE säkerhetschip

Enheten som etablerar tjänsten kan konfigureras på en enhet baserat på dess [maskinvarusäkerhetsmodul](https://azure.microsoft.com/en-us/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). IoT-DevKit MXChip använder den [enhetens identitet sammansättning motorn](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) från den [Trusted Computing Group](https://trustedcomputinggroup.org). En *unikt enhets hemlighet* (UDS) som sparas i en säkerhetsgrupp för STSAFE krets på DevKit används för att generera enheten har unika [X.509](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device#select-a-hardware-security-module) certifikat. Certifikatet kan användas för registrering i enheten Etablerar tjänsten senare.

En typisk unikt enhets-hemlighet är en sträng med 64 tecken, som visas i följande exempel:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Var och en av två tecken används som hexadecimalt värde i beräkningen säkerhet. Föregående exempel UDS har lösts till: `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16` , `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

Att spara en hemlighet som unikt enhets på DevKit:

1. Öppna serial övervakaren med hjälp av ett verktyg, till exempel Putty. Se [Använd konfigurationsläge](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) mer information.

2. Med DevKit ansluten till datorn, håll ned den **A** knappen, och tryck sedan på och släpper den **återställa** att ange konfigurationsläge. Skärmen visar DevKit ID och konfiguration.

3. Ta prov UDS sträng och ändra en eller flera tecken till andra värden mellan `0` och `f` för egna UDS.

4. I fönstret seriella övervakaren anger *set_dps_uds [your_own_uds_value]* och välj RETUR.
  > [!NOTE]
  > Om du ställer in en egen UDS genom att ändra de två sista tecknen till exempelvis `f`, måste du ange kommandot som detta: set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff.

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

1. Gå till din etablering tjänst i Azure-portalen. Välj **hantera registreringar**, och välj sedan den **enskilda registreringar** fliken. ![Enskilda registreringar](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Välj **Lägg till**.

3. I **mekanism**väljer **X.509**.
  ![Ladda upp certifikat](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

4. I **.pem eller .cer certifikatfilen**, ladda upp den **.pem** certifikat som du just genererade.

5. Lämna resten som standard och välj **spara**.

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

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
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

