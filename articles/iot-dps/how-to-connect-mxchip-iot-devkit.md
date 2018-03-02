---
title: "Hur du använder MXChip IoT DevKit för att ansluta till Azure IoT Hub etablering av tjänst | Microsoft Docs"
description: "Hur du använder MXChip IoT DevKit för att ansluta till Azure IoT Hub etablering av tjänst"
services: iot-dps
keywords: 
author: liydu
ms.author: liydu
ms.date: 02/20/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
<<<<<<< HEAD
ms.openlocfilehash: 697196f725f0cb8ad3054bd8336b588c17dddc3d
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
=======
ms.openlocfilehash: 77c8a6a5e384d27dca2582cc0fedc2bd23c0592e
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
>>>>>>> d7b96017f3c2d57f5135571d8789582901649dd0
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-hub-device-provisioning-service"></a>Ansluta MXChip IoT DevKit till Azure IoT-hubb enheten etablering av tjänst

Den här artikeln beskriver hur du konfigurerar DevKit för att göra det automatiskt registrera IoT-hubb med hjälp av tjänsten enhet etablering. I den här självstudien får du lära dig hur man:

* Konfigurera globala slutpunkten för DP på enhet
* Använd unika enheten hemligheten (UDS) för att generera X.509-certifikat
* Registrera enskilda enhet
* Kontrollera att enheten är registrerad

<<<<<<< HEAD
=======
Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibel skiva med sensorer och omfattande kringutrustning. Du kan utveckla för den med hjälp av [Visual Studio Code-tillägget för Arduino](https://aka.ms/arduino). Och det ingår en växande [projekt katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att hjälpa dig prototyp Sakernas Internet (IoT) lösningar som utnyttjar Microsoft Azure-tjänster.

>>>>>>> d7b96017f3c2d57f5135571d8789582901649dd0
## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här kursen behöver du följande:

* Förbered din DevKit med [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Uppgradera till den senaste versionen av programvaran (> = 1.3.0) med [Firmware uppgraderar](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) kursen.
* Skapa och länka IoT-hubb med enheten etablering tjänstinstansen med [ställa in automatisk etablering](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision).

## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Ställ in konfigurationen för etablering av tjänst på enheten

Aktivera DevKit att ansluta till enheten etablering tjänstinstansen du skapade:

1. I Azure portal, väljer du den **översikt** tjänst för etablering och Skriv ner den **globala enheten endpoint** och **ID Scope** värde.
  ![DP globala slutpunkt och Scope-ID](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [programvara frihet naturvårdande Git-klientverktyg](https://git-scm.com/download/) har den senaste versionen installerad.

3. Öppna en kommandotolk. Klona GitHub-repo-DP-kodexempel:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Starta VS-kod och ansluta DevKit till datorn, öppna mappen som innehåller den kod som du har klonat.

5. Öppna **DevKitDPS.ino**, Sök och Ersätt `[Global Device Endpoint]` och `[ID Scope]` med värden som du precis notera.
  ![DP Endpoint](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) du kan lämna den **registrationId** som tom, genereras ett automatiskt baserat på MAC-adress och inbyggd programvara version. Om du vill anpassa den har registrerings-ID du använder alfanumeriska, gemener och bindestreck kombinationer endast med maximalt 128 tecken långt. Mer information finns i [hantera enhetsregistrering med Azure-portalen](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments).

6. Använd **snabbt öppna** i VS-kod (Windows: `Ctrl+P`, macOS: `Cmd+P`) och skriv **uppgift enheten överför** skapa och ladda upp koden till DevKit.

7. Se genomförandet av uppgiften i utdatafönstret.

## <a name="save-unique-device-secret-on-stsafe-security-chip"></a>Spara unika enheten hemligheten på STSAFE säkerhetschip

Etablering av tjänst kan konfigureras på enhet baserat på dess [maskinvarusäkerhetsmodul (HSM)](https://azure.microsoft.com/en-us/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). DevKit använder [enhetens identitet sammansättning motorn (RAPPORTANVÄNDARNA)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) från den [Trusted Computing Group (TCG)](https://trustedcomputinggroup.org). A **unika enheten hemligheten (UDS)** sparas i STSAFE säkerhet krets på DevKit används för att generera enheten unika [X.509](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device#select-a-hardware-security-module) certifikat. Certifikatet kan användas senare för registreringen i tjänsten enhet för etablering.

En typisk **unika enheten hemligheten (UDS)** är 64 tecken lång sträng. Ett exempel på en UDS är som nedan:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Var och en av två tecken används som hexadecimalt värde i beräkningen säkerhet. Så i exemplet ovan UDS har lösts till ”:`0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16` , `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`".

Att spara unika enheten hemligheten på DevKit:

1. Öppnar seriella övervakaren med hjälp av verktyg som Putty [Använd konfigurationsläge](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) mer information.

2. Håll ned knappen A, DevKit anslutna till datorn, och sedan push och släpper återställningsknappen om du vill ange konfigurationsläge. Skärmen ska visa DevKit-ID och **”Configuration”**.

3. Ta lång exempel UDS strängen ovan och ändra ett eller flera tecken till andra värden mellan `0` och `f`. Detta används som en egen UDS.

4. Skriv i seriella övervakningsfönstret **set_dps_uds [your_own_uds_value]** och tryck på RETUR för att spara den.
  > [!NOTE]
  > Om du ställer in en egen UDS genom att ändra de två sista tecknen till exempelvis `f`, måste du ange kommandot som **set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff**.

5. Utan att stänga fönstret serial tryck på Återställ DevKit-knappen.

6. Notera **DevKit MAC-adress** och **DevKit Version på inbyggd programvara** värde.
  ![Version på inbyggd programvara](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-x509-certificate"></a>Generera X.509-certifikat

### <a name="windows"></a>Windows

1. Öppna Utforskaren och gå till mappen innehåller DSP exempelkod som du har klonat, finns det en **.build** mapp, hitta och kopiera **DPS.ino.bin** och **DPS.ino.map** i den.
  ![Genererade filer](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Om du har ändrat den `built.path` konfigurationen för Arduino till andra mappen. Du behöver leta upp filerna i mappen som du har konfigurerat.

2. Klistra in de här två filerna i **verktyg** mappen på samma nivå med **.build** mapp.

3. Kör **dps_cert_gen.exe**, följer du anvisningarna för att ange din **UDS**, **MAC-adress** för DevKit och **version på inbyggd programvara** att generera X.509-certifikat.
  ![Kör DP-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Observera att generation, en **.pem** certifikat sparas i samma mapp.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Skapa en post för enhetsregistrering i Device Provisioning-tjänsten

1. Navigera till din etablering tjänst i Azure-portalen. Klicka på **Hantera registreringar** och välj fliken **Enskilda registreringar**. ![Enskilda registreringar](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Klicka på **Lägg till**.

3. I **mekanism**, Välj **X.509**.
  ![Överför certifikat](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

4. I **.pem eller .cer certifikatfilen**, ladda upp den **.pem** certifikat som du precis har.

5. Lämna resten som standard och klicka på **spara**.

## <a name="start-the-devkit"></a>Starta DevKit

1. Starta VS-kod och öppna serial övervakaren.

2. Tryck på den **återställa** din DevKit-knappen.

Du bör se DevKit starta registreringen med din enhet etablering av tjänst.

![VS felkoden utdata](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-the-devkit-is-registered-on-iot-hub"></a>Kontrollera DevKit är registrerad i IoT-hubb

När din enhet Starter följande åtgärder ska utföras:

1. Enheten skickar en begäran om registrering till din enhet etablering av tjänst.
2. Etablering av tjänst skickar tillbaka en registrering utmaning som enheten svarar.
3. På lyckad registrering skickar enheten Etableringstjänsten IoT-hubb URI, enhets-ID och den krypterade nyckeln tillbaka till enheten.
4. IoT-hubb klientprogrammet på enheten sedan ansluter till din hubb.
5. Du bör se enheten visas i IoT-hubben enheten Explorer på anslutningen till hubben.
  ![Enheten har registrerats](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-device-id"></a>Ändra enhets-ID

Standard-enhets-ID som registrerats i Azure IoT Hub är **AZ3166**. Om du vill ändra den följer [anvisningarna här](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, läser du [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller nå oss från följande kanaler:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att förbereda DevKit att registrera en enhet på ett säkert sätt till DP med RAPPORTANVÄNDARNA, så att den automatiskt registrera till IoT-hubb med zero touch. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera globala slutpunkten för DP på enhet
> * Använd unika enheten hemligheten (UDS) för att generera X.509-certifikat
> * Registrera enskilda enhet
> * Kontrollera att enheten är registrerad

Gå vidare till andra självstudiekurser att lära dig:

> [!div class="nextstepaction"]
> [Skapa och etablera en simulerad enhet](./quick-create-simulated-device.md)

