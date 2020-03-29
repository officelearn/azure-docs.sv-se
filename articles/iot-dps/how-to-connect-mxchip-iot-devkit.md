---
title: Så här använder du automatisk etablering av Azure IoT Hub Device Provisioning Service för automatisk etablering av MXChip IoT DevKit med IoT Hub | Microsoft-dokument
description: Så här använder du DPS (Azure IoT Hub Device Provisioning Service) för automatisk etablering för att registrera MXChip IoT DevKit med IoT Hub.
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: f05e92f0452b1cfff23e2094354203fd7eaea48b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975660"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Använda automatisk etablering av Azure IoT Hub-enhetsetableringstjänst för automatisk etablering av MXChip IoT DevKit med IoT Hub

I den här artikeln beskrivs hur du använder [autoetableringstjänsten](concepts-auto-provisioning.md)för Azure IoT Hub Device Provisioning Service för automatisk etablering av MXChip IoT DevKit med Azure IoT Hub. I den här självstudiekursen får du lära du dig att:

* Konfigurera den globala slutpunkten för tjänsten Enhetsetablering på en enhet.
* Använd en unik enhetshemlighet (UDS) för att generera ett X.509-certifikat.
* Registrera en enskild enhet.
* Kontrollera att enheten är registrerad.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino-kompatibel styrelse med rik kringutrustning och sensorer. Du kan utveckla för det med Azure [IoT Device Workbench](https://aka.ms/iot-workbench) eller [Azure IoT Tools](https://aka.ms/azure-iot-tools) tilläggspaket i Visual Studio Code. DevKit levereras med en växande [projektkatalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) för att styra dina prototyplösningar för Sakernas Internet (IoT) som utnyttjar Azure-tjänster.

## <a name="before-you-begin"></a>Innan du börjar

Gör så här för att slutföra stegen i den här självstudien:

* Konfigurera DevKits Wi-Fi och förbered utvecklingsmiljön genom att följa avsnitten "Förbered utvecklingsmiljön" i [Connect IoT DevKit AZ3166 till Azure IoT Hub i molnet](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment).
* Uppgradera till den senaste firmware (1.3.0 eller senare) med [Update DevKit firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) tutorial.
* Skapa och länka en IoT-hubb med en tjänstinstans för enhetsetablering genom att följa stegen i [Konfigurera IoT Hub Device Provisioning Service med Azure-portalen](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Öppna exempelprojekt

1. Kontrollera att din IoT DevKit inte är **ansluten** till datorn. Starta VS-koden först och anslut sedan DevKit till datorn.

1. Klicka `F1` här om du vill öppna kommandopaletten, skriv och välj **Azure IoT Device Workbench: Öppna exempel...**. Välj sedan **IoT DevKit** som anslagstavla.

1. På sidan IoT Workbench Examples hittar du **Enhetsregistrering med DPS** och klickar på **Öppna exempel**. Väljer sedan standardsökvägen för att hämta exempelkoden.
    ![Öppna exempel](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Spara en unik enhetshemlighet på enhetens säkerhetslagring

Automatisk etablering kan konfigureras på en enhet baserat på enhetens [attestationmekanism](concepts-security.md#attestation-mechanism). MXChip IoT DevKit använder [device identity composition engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) från Trusted Computing [Group](https://trustedcomputinggroup.org). En **unik enhetshemlighet** (UDS) som sparats i ett STSAFE-säkerhetschip[(STSAFE-A100)](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)på DevKit används för att generera enhetens unika [X.509-certifikat](concepts-security.md#x509-certificates). Certifikatet används senare för registreringsprocessen i tjänsten Enhetsetablering och under registreringen vid körning.

En typisk UDS är en sträng med 64 tecken, vilket visas i följande exempel:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Så här sparar du en UDS på DevKit:

1. I VS-kod klickar du på statusfältet för att välja COM-porten för DevKit.
  ![Välj COM-port](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. På DevKit håller du ned **knapp A,** trycker och släpper **återställningsknappen** och släpper sedan **knappen A**. DevKit går in i konfigurationsläge.

1. Klicka `F1` här om du vill öppna kommandopaletten skriver du och väljer **Azure IoT Device Workbench: Konfigurera enhetsinställningar... > Config Unique Device String (UDS)**.
  ![Konfigurera UDS](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Anteckna den genererade UDS-strängen. Du behöver det för att generera X.509-certifikatet. Tryck `Enter`sedan på .
  ![Kopiera UDS](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Bekräfta från meddelandet att UDS har konfigurerats på STSAFE.
  ![Konfigurera UDS-framgång](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Alternativt kan du konfigurera UDS via seriell port med hjälp av verktyg som Putty. Följ [Använd konfigurationsläge](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) för att göra det.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Uppdatera slutpunkten för den globala enheten och ID-scopet

I enhetskoden måste du ange [slutpunkten för enhetsetablering](/azure/iot-dps/concepts-service#device-provisioning-endpoint) och ID för att säkerställa klientisolering.

1. I Azure-portalen väljer du **fönstret Översikt för** tjänsten Enhetsetablering och noterar värdena för den globala **enhetens slutpunkt** och **ID-scope.**
  ![Global slutpunkt och ID-scope för enhetsetableringstjänst](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Öppna **DevKitDPS.ino**. Hitta och `[Global Device Endpoint]` `[ID Scope]` ersätt och med de värden du just noterade ner.
  ![Slutpunkt för enhetsetableringstjänsten](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Fyll `registrationId` variabeln i koden. Endast alfanumerisk kombination, gemener och bindestreck med högst 128 tecken är tillåten. Noterade också ner värdet.
  ![Registrerings-ID](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Klicka `F1`på , skriv och välj **Azure IoT Device Workbench: Ladda upp enhetskod**. Det börjar sammanställa och ladda upp koden till DevKit.
  ![Enhetsuppladdning](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Generera X.509-certifikat

Den [attestation mekanism](/azure/iot-dps/concepts-device#attestation-mechanism) som används av detta exempel är X.509 certifikat. Du måste använda ett verktyg för att generera det.

1. I VS-kod `F1`klickar du på , skriver och väljer **Öppna ny terminal** för att öppna terminalfönstret.

1. Kör `dps_cert_gen.exe` `tool` i mapp.

1. Ange den kompilerade `..\.build\DevKitDPS`binära filplatsen som . Klistra sedan in **UDS** och **registrationId** du just noterat ner. 
  ![Generera X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Ett `.pem` X.509-certifikat genereras i samma mapp.
  ![X.509-fil](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Skapa en post för enhetsregistrering

1. Öppna tjänsten Enhetsetablering i Azure-portalen, navigera till avsnittet Hantera registreringar och klicka på **Lägg till enskild registrering**.
  ![Lägga till individuell registrering](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Klicka på filikonen **bredvid Pem eller .cer-fil** för att ladda upp `.pem` filen som genereras.
  ![Ladda upp .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Kontrollera att DevKit har registrerats med Azure IoT Hub

Tryck på **återställningsknappen** på din DevKit. Du bör se **DPS Connected!** på DevKit-skärmen. När enheten har startats om sker följande åtgärder:

1. Enheten skickar en registreringsbegäran till enhetsetableringstjänsten.
1. Tjänsten Enhetsetablering skickar tillbaka en registreringsutmaning som enheten svarar på.
1. Vid lyckad registrering skickar tjänsten Enhetsetablering IoT Hub URI, enhets-ID och den krypterade nyckeln tillbaka till enheten.
1. IoT Hub-klientprogrammet på enheten ansluter till din hubb.
1. Vid lyckad anslutning till navet visas enheten i Utforskaren för IoT Hub Device Explorer.
  ![Enhet registrerad](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, se vanliga [frågor](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)om Iot DevKit eller nå ut till följande kanaler för support:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackspill](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att registrera en enhet på ett säkert sätt till enhetsetableringstjänsten med hjälp av device identity composition engine, så att enheten automatiskt kan registrera sig hos Azure IoT Hub. 

Sammanfattningsvis har du lärt dig hur du:

> [!div class="checklist"]
> * Konfigurera den globala slutpunkten för tjänsten Enhetsetablering på en enhet.
> * Använd en unik enhetshemlighet för att generera ett X.509-certifikat.
> * Registrera en enskild enhet.
> * Kontrollera att enheten är registrerad.

Lär dig hur du [skapar och etablerar en simulerad enhet](./quick-create-simulated-device.md).

