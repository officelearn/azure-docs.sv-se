---
title: Hur du använder Azure IoT Hub Device Provisioning-tjänsten Automatisk etablering för att registrera MXChip IoT DevKit med IoT Hub | Microsoft Docs
description: Hur du använder Azure IoT Hub Device Provisioning-tjänsten Automatisk etablering för att registrera MXChip IoT DevKit med IoT Hub.
author: liydu
ms.author: liydu
ms.date: 12/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: a7131a66e9d722265282ac98b67b52db8de086e2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551909"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Använd Azure IoT Hub Device Provisioning-tjänsten Automatisk etablering för att registrera MXChip IoT DevKit med IoT Hub

Den här artikeln beskriver hur du använder Azure IoT Hub Device Provisioning Service [Automatisk etablering](concepts-auto-provisioning.md), för att registrera MXChip IoT DevKit med Azure IoT Hub. I den här guiden får du lära dig att:

* Konfigurera globala slutpunkten för Device Provisioning-tjänsten på en enhet.
* Använd ett unikt enhets-hemlighet (ud) för att generera ett X.509-certifikat.
* Registrera en enskild enhet.
* Kontrollera att enheten är registrerad.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino-kompatibelt tavla med omfattande kringutrustning och sensorer. Du kan utveckla för den med hjälp av [Azure IoT Device Workbench](https://aka.ms/iot-workbench) eller [Azure IoT Tools](https://aka.ms/azure-iot-tools) tilläggspaketet i Visual Studio Code. DevKit levereras med en växande [projekt catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att vägleda dina prototyp Internet of Things (IoT)-lösningar som utnyttjar Azure-tjänster.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här självstudien måste du först göra följande uppgifter:

* Förbereda din DevKit genom att följa stegen i [ansluta IoT DevKit AZ3166 på Azure IoT Hub i molnet](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Uppgradera till den senaste inbyggda programvaran (1.3.0 eller senare) med den [DevKit för uppdatering av inbyggd programvara](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) självstudien.
* Skapa och länka en IoT-hubb med en Device Provisioning-tjänstinstans genom att följa stegen i [konfigurera IoT Hub Device Provisioning-tjänsten med Azure portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Öppna exempelprojektet

1. Kontrollera att din IoT DevKit är **inte ansluten** till datorn. Starta VS Code först och sedan ansluta DevKit till din dator.

1. Klicka på `F1` för att öppna kommandopaletten, skriver du och väljer **Azure IoT Device Workbench: Öppna exempel...** . Välj sedan **IoT DevKit** som tavla.

1. På sidan IoT Workbench exempel hitta **Enhetsregistrering med DPS** och klicka på **öppna exemplet**. Sedan väljer standardsökvägen för att hämta exempelkoden.
    ![Öppna exemplet](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Spara en unik enhet-hemlighet på security enhetslagring

Automatisk etablering kan konfigureras på en enhet baserat på enhetens [attesteringsmetod](concepts-security.md#attestation-mechanism). MXChip IoT DevKit använder den [enheten Identity Composition Engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) från den [Trusted Computing Group](https://trustedcomputinggroup.org). En **unika enheten hemlighet** (ud) som sparas i en STSAFE security-chip ([STSAFE A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) på DevKit används för att generera enheten användarens unika [X.509-certifikat](concepts-security.md#x509-certificates). Certifikatet används senare under registreringsprocessen i Device Provisioning-tjänsten och under registreringen vid körning.

En typisk ud är en 64 tecken lång sträng, som visas i följande exempel:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Spara en ud på DevKit:

1. Klicka på statusfältet för att välja den COM-porten för DevKit i VS Code.
  ![Välj COM-Port](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. På DevKit, håll ned **knappen A**, push och släpp den **återställa** knappen och släpp **knappen A**. Din DevKit försätts konfigurationsläge.

1. Klicka på `F1` för att öppna kommandopaletten, skriver du och väljer **Azure IoT Device Workbench: Konfigurera Enhetsinställningar... > Config unikt enhets-sträng (ud)**.
  ![Konfigurera ud](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Anteckna den genererade ud-strängen. Du behöver den och generera X.509-certifikat. Tryck på `Enter`.
  ![Kopiera ud](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Bekräfta från meddelandet att ud har konfigurerats på STSAFE.
  ![Konfigurera ud lyckades](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Du kan också konfigurera ud via seriell port med hjälp av verktyg, till exempel Putty. Följ [Använd konfigurationsläge](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) att göra detta.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Uppdatera Global Enhetsslutpunkt och ID-omfång

I koden för enheten, måste du ange den [Device provisioning-slutpunkten](/azure/iot-dps/concepts-service#device-provisioning-endpoint) och ID-omfång så klientisolering.

1. I Azure-portalen väljer du den **översikt** fönstret Device Provisioning-tjänsten och noterar den **Global enhetsslutpunkt** och **ID-omfång** värden.
  ![Device Provisioning Service Global Endpoint och ID-omfång](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Open **DeKitDPS.ino**. Sök och Ersätt `[Global Device Endpoint]` och `[ID Scope]` med de värden som du just skrev ned.
  ![Enheten etablering tjänstslutpunkt](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Fyll i `registrationId` variabel i koden. Endast alfanumeriska tecken, gemener, och bindestreck kombination med högst 128 tecken tillåts. Även antecknade värdet.
  ![Registrerings-ID](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Klicka på `F1`skriver du och väljer **Azure IoT Device Workbench: Ladda upp enheten**. Den startar när koden kompileras och ladda upp koden till DevKit.
  ![Ladda upp enheten](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Generera X.509-certifikat

Den [attesteringsmetod](/azure/iot-dps/concepts-device#attestation-mechanism) används av det här exemplet är X.509-certifikat. Du måste använda ett verktyg för att skapa den.

> [!NOTE]
> X.509 certificate generator stöder endast Windows nu.

1. I VS Code, klickar du på `F1`skriver du och väljer **Öppna ny Terminal** att öppna terminalfönstret.

1. Kör `dps_cert_gen.exe` i `tool` mapp.

1. Ange platsen för kompilerade binärfil som `..\.build\DevKitDPS`. Klistra in den **ud** och **registrationId** bara du antecknade. 
  ![Generera X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. En `.pem` X.509-certifikat som genereras i samma mapp.
  ![X.509-fil](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Skapa en post för enhetsregistrering

1. Öppna enheten etablera tjänsten i Azure-portalen, navigera för att hantera registreringar avsnittet och klicka på **Lägg till enskild registrering**.
  ![Lägg till enskild registrering](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Klicka på filikonen bredvid **primära .pem- eller .cer-certifikatfilen** att ladda upp den `.pem` -filen som genereras.
  ![Ladda upp .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Kontrollera DevKit har registrerats med Azure IoT Hub

Tryck på den **återställa** knappen på din DevKit. Du bör se **DPS ansluten!** on DevKit screen. När enheten om sker de följande:

1. Enheten skickar en registreringsbegäran till enhetsetableringstjänsten.
1. Device Provisioning-tjänsten skickar tillbaka en registreringskontroll som enheten svarar.
1. Om registringen har lyckats skickar Device Provisioning-tjänsten IoT Hub-URI, enhets-ID och den krypterade nyckeln till enheten.
1. IoT Hub-klientprogrammet på enheten ansluter till hubben.
1. På lyckad anslutning till hubben visas enheten i IoT Hub Device Explorer.
  ![Enhet har registrerats](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan referera till Iot DevKit [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), eller kontakta följande kanaler för support:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att registrera en enhet på ett säkert sätt till Device Provisioning-tjänsten genom att använda enheten Identity Composition Engine, så att enheten kan automatiskt registrera med Azure IoT Hub. 

Sammanfattningsvis ska du lärt dig hur du:

> [!div class="checklist"]
> * Konfigurera globala slutpunkten för Device Provisioning-tjänsten på en enhet.
> * Använd ett unikt enhets-hemlighet för att generera ett X.509-certifikat.
> * Registrera en enskild enhet.
> * Kontrollera att enheten är registrerad.

Lär dig hur du [skapa och etablera en simulerad enhet](./quick-create-simulated-device.md).

