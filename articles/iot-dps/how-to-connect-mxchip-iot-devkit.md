---
title: Använda Azure IoT Hub Device Provisioning Service automatisk etablering för att registrera IoT-DevKit för MXChip med IoT Hub | Microsoft Docs
description: Hur du använder Azure IoT Hub Device Provisioning Service automatisk etablering för att registrera MXChip IoT-DevKit med IoT Hub.
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 2731bbcd6a6b0c8f7d82334c022c017d5eae35f0
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677009"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Använd Azure IoT Hub Device Provisioning Service automatisk etablering för att registrera MXChip IoT-DevKit med IoT Hub

Den här artikeln beskriver hur du använder Azure IoT Hub Device Provisioning Service [Automatisk etablering](concepts-auto-provisioning.md), för att registrera MXChip IoT-DevKit med Azure IoT Hub. I den här guiden får du lära dig att:

* Konfigurera den globala slut punkten för enhets etablerings tjänsten på en enhet.
* Använd en unik enhets hemlighet (UDS) för att generera ett X. 509-certifikat.
* Registrera en enskild enhet.
* Kontrol lera att enheten är registrerad.

[MXChip IoT-DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino-kompatibelt kort med omfattande kring utrustning och sensorer. Du kan utveckla för IT-enheter med hjälp av [Azure IoT Device Workbench](https://aka.ms/iot-workbench) eller [Azure IoT tools](https://aka.ms/azure-iot-tools) Extension Pack i Visual Studio Code. DevKit levereras med en växande [projekt katalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) som vägleder dina prototyp Sakernas Internet-lösningar (IoT) som utnyttjar Azure-tjänster.

## <a name="before-you-begin"></a>Innan du börjar

Utför stegen i den här självstudien genom att först utföra följande uppgifter:

* Konfigurera din DevKit Wi-Fi och Förbered utvecklings miljön genom att följa anvisningarna i avsnittet "förbereda utvecklings miljön" i [ansluta IoT DEVKIT AZ3166 till Azure IoT Hub i molnet](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment).
* Uppgradera till den senaste inbyggda program varan (1.3.0 eller senare) med själv studie kursen [Uppdatera DevKit-programvaran](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) .
* Skapa och länka en IoT Hub med en enhets etablerings tjänst instans genom att följa stegen i [konfigurera IoT Hub Device Provisioning service med Azure Portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Öppna exempel projekt

1. Se till att din IoT-DevKit **inte är ansluten** till din dator. Starta VS Code först och Anslut sedan DevKit till datorn.

1. Klicka `F1` för att öppna kommando-paletten, Skriv **och välj Azure IoT Device Workbench: Öppna exempel...** . Välj sedan **IoT DevKit** som tavla.

1. På sidan IoT Workbench-exempel hittar du **enhets registrering med DPS** och klickar på **Öppna exempel**. Sedan väljer du standard Sök vägen för att ladda ned exempel koden.
    ![Open-exempel @ no__t-1

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Spara en unik enhets hemlighet på enhetens säkerhets lagring

Automatisk etablering kan konfigureras på en enhet baserat på enhetens [mekanism för attestering](concepts-security.md#attestation-mechanism). MXChip IoT-DevKit använder [enhetens identitets sammansättnings motor](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) från [Trusted Computing Group](https://trustedcomputinggroup.org). En **unik enhets hemlighet** (UDS) som sparas i en STSAFE Security chip ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) på DevKit används för att generera enhetens unika [X. 509-certifikat](concepts-security.md#x509-certificates). Certifikatet används senare för registrerings processen i Device Provisioning-tjänsten och under registreringen vid körning.

En typisk UDS är en sträng på 64 tecken, som visas i följande exempel:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Så här sparar du en UDS på DevKit:

1. I VS Code, klickar du på statusfältet för att välja COM-porten för DevKit.
  ![Select COM-port @ no__t-1

1. På DevKit trycker du på **knappen a**, push och släpper knappen **Återställ** och släpper sedan **knappen A**. Din DevKit går in i konfigurations läge.

1. Klicka `F1` för att öppna kommando-paletten, Skriv **och välj Azure IoT Device Workbench: Konfigurera enhets inställningar... > Config unik enhets sträng (UDS)** .
  ![Configure UDS @ no__t-1

1. Anteckna den genererade UDS-strängen. Du behöver den för att generera X. 509-certifikatet. Tryck sedan på `Enter`.
  ![Copy UDS @ no__t-1

1. Bekräfta från meddelandet att UDS har kon figurer ATS på STSAFE.
  @no__t – 0Configure UDS lyckades @ no__t-1

> [!NOTE]
> Du kan också konfigurera UDS via seriell port genom att använda verktyg som till exempel SparaTillFil. Följ [konfigurations läget](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) för att göra det.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Uppdatera den globala enhetens slut punkt och ID-omfång

I enhets kod måste du ange [enhets etablerings slut punkt](/azure/iot-dps/concepts-service#device-provisioning-endpoint) och ID-omfång för att säkerställa klient isoleringen.

1. I Azure Portal väljer du fönstret **Översikt** i enhets etablerings tjänsten och noterar värdena för **Global enhets slut punkt** och **ID-omfång** .
  @no__t global slut punkt och ID för 0Device-etablerings tjänsten @ no__t-1

1. Öppna **DevKitDPS. INO**. Sök och ersätt `[Global Device Endpoint]` och `[ID Scope]` med de värden som du nyss skrev ned.
  ![Device Provisioning service-slutpunkt @ no__t-1

1. Fyll `registrationId`-variabeln i koden. Endast alfanumeriska tecken, gemener och bindestreck med högst 128 tecken tillåts. Notera också värdet.
  ![Registration-ID @ no__t-1

1. Klicka på `F1`, skriv och välj **Azure IoT Device Workbench: Ladda upp enhets kod**. Den börjar kompilera och laddar upp koden till DevKit.
  ![Device upload @ no__t-1

## <a name="generate-x509-certificate"></a>Generera X. 509-certifikat

[Mekanismen för attestering](/azure/iot-dps/concepts-device#attestation-mechanism) som används i det här exemplet är X. 509-certifikat. Du måste använda ett verktyg för att generera det.

1. I VS Code, klickar du på `F1`, skriver och väljer **Öppna ny terminal** för att öppna terminalfönstret.

1. Kör `dps_cert_gen.exe` i `tool`-mappen.

1. Ange den kompilerade binära fil platsen som `..\.build\DevKitDPS`. Klistra sedan in **UDS** -och **registrationId** som du precis har nämnt. 
  ![Generate X. 509 @ no__t-1

1. Ett `.pem` X. 509-certifikat genereras i samma mapp.
  ![X. 509-fil @ no__t-1

## <a name="create-a-device-enrollment-entry"></a>Skapa en post för enhetsregistrering

1. I Azure Portal öppnar du enhets etablerings tjänsten, navigerar till avsnittet hantera registreringar och klickar på **Lägg till enskild registrering**.
  ![Add individuell registrering @ no__t-1

1. Klicka på fil ikonen bredvid **primärt Certificate. pem eller. cer-fil** för att överföra `.pem`-filen som genereras.
  ![Upload. pem @ no__t-1

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Kontrol lera att DevKit har registrerats med Azure IoT Hub

Tryck på **återställnings** knappen på din DevKit. Du bör se **DPS Connected!** på DevKit-skärmen. När enheten har startats om utförs följande åtgärder:

1. Enheten skickar en registreringsbegäran till enhetsetableringstjänsten.
1. Enhets etablerings tjänsten skickar tillbaka en registrerings utmaning som enheten svarar på.
1. Vid lyckad registrering skickar enhets etablerings tjänsten IoT Hub-URI, enhets-ID och den krypterade nyckeln tillbaka till enheten.
1. IoT Hub klient programmet på enheten ansluter till din hubb.
1. Vid lyckad anslutning till hubben visas enheten i IoT Hub Device Explorer.
  ![Device registrerad @ no__t-1

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem kan du läsa [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)om IoT-DevKit, eller kontakta följande kanaler för support:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att registrera en enhet säkert på enhets etablerings tjänsten med hjälp av motorn för enhets identitets sammansättning, så att enheten kan registreras automatiskt med Azure IoT Hub. 

Sammanfattnings vis har du lärt dig att:

> [!div class="checklist"]
> * Konfigurera den globala slut punkten för enhets etablerings tjänsten på en enhet.
> * Använd en unik enhets hemlighet för att generera ett X. 509-certifikat.
> * Registrera en enskild enhet.
> * Kontrol lera att enheten är registrerad.

Lär dig hur du [skapar och etablerar en simulerad enhet](./quick-create-simulated-device.md).

