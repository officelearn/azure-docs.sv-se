---
title: Enhetsanslutning i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp som rör enhetsanslutning i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 9e1e85d1ab1c5e7ce0cbd96c64137309c2e2916a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60887510"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Enhetsanslutning i Azure IoT Central

Den här artikeln beskrivs viktiga begrepp som rör enhetsanslutning i Microsoft Azure IoT Central.

Azure IoT Central använder den [Azure IoT Hub Device Provisioning-tjänsten (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) att hantera alla enhetsregistrering och anslutning.

Med hjälp av DPS kan:

- IoT Central för onboarding och anslutande enheterna i stor skala.
- Du kan generera enheten autentiseringsuppgifter och konfigurera enheterna som är offline utan att registrera enheter via IoT Central Användargränssnittet.
- Enheter att ansluta med signaturer för delad åtkomst (SAS).
- Enheter att ansluta med branschstandard X.509-certifikat.
- Du kan använda din egen enhet ID: N för att registrera enheter i IoT Central. Med ditt eget enhets-ID: N som förenklar integrering med befintliga BackOffice-system.
- Ett konsekvent sätt att ansluta enheter till IoT Central.

Den här artikeln beskrivs följande fyra användningsfall:

1. [Anslut snabbt en enskild enhet med hjälp av SAS](#connect-a-single-device)
1. [Anslut enheter i stor skala med hjälp av SAS](#connect-devices-at-scale-using-sas)
1. [Anslut enheter i stor skala med X.509-certifikat](#connect-devices-using-x509-certificates) detta är den rekommendera metoden för produktionsmiljöer.
1. [Ansluta utan att första registrera enheter](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Ansluta en enskild enhet

Den här metoden är användbar när du experimentera med IoT Central eller testa enheter. Du kan använda informationen i anslutning från ditt IoT Central-program för att generera anslutningssträngen för en enhet. Detaljerade anvisningar finns i [hur du skapar en anslutningssträng för enheten för att ansluta till ett Azure IoT Central program](howto-generate-connection-string.md).

## <a name="connect-devices-at-scale-using-sas"></a>Anslut enheter i stor skala med hjälp av SAS

För att ansluta enheter till IoT Central i stor skala med hjälp av SAS, måste du registrera dig och sedan konfigurera enheterna:

### <a name="register-devices-in-bulk"></a>Registrera enheter i grupp

Registrera ett stort antal enheter med ditt IoT Central-program genom att använda en CSV-fil till [importera enhets-ID och namn på enheter](howto-manage-devices.md#import-devices).

Att hämta anslutningsinformationen för de importera enheterna, [exportera en CSV-fil från programmets IoT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Information om hur du kan ansluta enheter utan att första registrera dem i IoT Central finns [Anslut utan första registrera enheter](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Konfigurera dina enheter

Använd anslutningsinformationen från exportfilen i din kod för enheten för att aktivera dina enheter att ansluta och skicka data till IoT till din IoT Central-App. Mer information om hur du ansluter enheter finns i [nästa steg](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Anslut enheter med X.509-certifikat

I en produktionsmiljö är med X.509-certifikat rekommenderade enheten autentiseringsmekanismen för IoT Central. Mer information finns i [autentisering med X.509 CA-certifikat](../iot-hub/iot-hub-x509ca-overview.md).

Följande steg beskriver hur du ansluter enheter till IoT Central med X.509-certifikat:

1. I programmets IoT Central _lägga till och verifiera mellanliggande eller X.509-certifikat för rotmappen_ du använder för att generera enhetscertifikat:

    - Gå till **Administration > enhetsanslutning > certifikat (X.509)** och Lägg till X.509 rot- eller mellanliggande certifikat som du använder för att generera enhetscertifikat lövmedlemmar.

      ![Inställningar för anslutning](media/concepts-connectivity/connection-settings.png)

      Om du har en säkerhetsöverträdelse eller primära certifikatet upphör att gälla, kan du använda sekundärt certifikat för att minska driftstopp. Du kan fortsätta att etablera enheter med hjälp av sekundärt certifikat när du uppdaterar primära certifikatet.

    - Verifiera att du äger certifikat säkerställer att överföring för certifikatet har certifikatets privata nyckel. För att verifiera certifikatet:
        - Välj knappen bredvid **Verifieringskod** att generera en kod.
        - Skapa ett X.509-certifikat för verifiering med verifieringskoden som du genererade i föregående steg. Spara certifikatet som en .cer-fil.
        - Ladda upp signerad verifieringscertifikatet och välj **Kontrollera**.

          ![Inställningar för anslutning](media/concepts-connectivity/verify-cert.png)

1. Använda en CSV-fil till _importera och registrera enheter_ i ditt IoT Central-program.

1. _Konfigurera dina enheter._ Generera löv-certifikat med hjälp av överförda rotcertifikatet. Använd den **enhets-ID** som CNAME-värde i löv-certifikat. Enhets-ID ska vara gemener. Sedan Programmera dina enheter med tjänsten Etableringsinformation. När en enhet är påslaget för först, hämtar dess anslutningsinformationen för din IoT Central-programmet från DPS.

### <a name="further-reference"></a>Ytterligare referens

- Exempel på implementering för [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Exemplet enhetsklienten i C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Endast avsett för testning

Endast för testning, kan du använda dessa verktyg för att generera CA-certifikat och enhetscertifikat.

- Om du använder en DevKit enhet detta [kommandoradsverktyget](https://aka.ms/iotcentral-docs-dicetool) genererar ett CA-certifikat som du kan lägga till din IoT Central-programmet för att kontrollera certifikaten.

- Använd det här [kommandoradsverktyget](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) till:
  - Skapa en certifikatkedja. Följ steg 2 i GitHub-artikeln.
  - Spara certifikat som CER-filer att ladda upp till programmets IoT Central.
  - Använd Verifieringskod från IoT Central-programmet för att generera verifieringscertifikatet. Följ steg3 i GitHub-artikeln.
  - Skapa lövmedlemmar certifikat för dina enheter med hjälp av din enhets-ID som en parameter för verktyget. Följ steg 4 i GitHub-artikeln.

## <a name="connect-without-registering-devices"></a>Ansluta utan att registrera enheter

Ett scenario med viktiga IoT Central kan är OEM-tillverkare kan vikt tillverkar enheter som kan ansluta till ett IoT Central-program utan att först registreras. En tillverkare måste generera lämpliga autentiseringsuppgifter och konfigurera enheterna i fabriken. När en enhet startar för första gången, ansluter den automatiskt till ett IoT Central-program. En IoT Central-operator måste godkänna enheten innan den kan stat skickar data.

Följande diagram illustrerar det här flödet:

![Inställningar för anslutning](media/concepts-connectivity/device-connection-flow.png)

Följande steg beskriver den här processen i större detalj. Stegen skiljer sig något beroende på om du använder SAS- eller X.509-certifikat för autentisering:

1. Konfigurera anslutningsinställningar:

    - **X.509-certifikat:** [Lägga till och verifiera rot-/ mellanliggande certifikat](#connect-devices-using-x509-certificates) och använda den för att generera enhetens certifikat i följande steg.
    - **SAS:** Kopiera den primära nyckeln. Den här nyckeln är gruppen SAS-nyckel för IoT Central-programmet. Använd nyckeln för att generera SAS-nycklar för enheten i följande steg.
    ![Anslutningsinställningar SAS](media/concepts-connectivity/connection-settings-sas.png)

1. Skapa dina autentiseringsuppgifter för enhet
    - **X.509-certifikat:** Generera löv-certifikat för dina enheter med hjälp av rot- eller mellanliggande certifikat som du har lagt till i ditt IoT Central-program. Kontrollera att du använder den gemena **enhets-ID** som CNAME-post i löv-certifikat. För testning, Använd det här [kommandoradsverktyget](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) att generera enhetscertifikat.
    - **SAS:** Använd det här [kommandoradsverktyget](https://www.npmjs.com/package/dps-keygen) att generera SAS-nycklar för enheten. Använd gruppen **primärnyckel** från föregående steg. Enhets-ID måste vara gemener.

      Installera den [nyckeln generator verktyget](https://github.com/Azure/dps-keygen), kör du följande kommando:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Kör följande kommando för att generera en enhetsnyckel från den primära nyckeln för grupp-SAS:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Om du vill konfigurera dina enheter, flash varje enhet med den **Scopeid**, **enhets-ID**, och **X.509-enhetscertifikat** eller **SAS-nyckel för**.

1. Aktivera enheten för att den kan ansluta till ditt IoT Central-program. När du växlar på en enhet först ansluter den till DPS för att hämta registreringsinformation IoT Central.

1. Den anslutna enheten inledningsvis visas som en **olänkade enheten** på den **Device Explorer** sidan. Enheten Etableringsstatus är **registrerad**. **Associera** enheten till lämplig enhet mallen och godkänna att enheten ansluter till din IoT Central-program. Enheten kan sedan hämta en anslutningssträng från IoT Hub och börja skicka data. Enhetsetablering är nu klar och etableringsstatusen är nu **etablerad**.

## <a name="provisioning-status"></a>Etableringsstatus

När: en riktig enhet som ansluter till ditt IoT Central-program, dess etablering statusändringar på följande sätt:

1. Status för enhetsetablering är den första **registrerad**. Denna status innebär att enheten har skapats i IoT Central och har ett enhets-ID. En enhet registreras när:
    - En ny riktig enhet läggs till på den **Device Explorer** sidan.
    - En uppsättning enheter läggs till med **Import** på den **Device Explorer** sidan.
    - En enhet inte har registrerats manuellt på den **Device Explorer** sida, men kontakten med giltiga autentiseringsuppgifter och visas som en **Unassociated** enhet på den **Device Explorer**sidan.

1. Enhetsetablering status ändras till **etablerad** när enheten är ansluten till din IoT Central-program med giltiga autentiseringsuppgifter har slutförts etablering steg. I det här steget hämtar enheten en anslutningssträng från IoT Hub. Enheten kan nu ansluta till IoT Hub och börja skicka data.

1. En operatör kan blockera en enhet. När en enhet har blockerats, kan inte det skicka data till programmets IoT Central. Blockerade enheter har statusen etablering av **blockerad**. En operatör måste återställa enheten innan den kan fortsätta att skicka data. När en operatör häver blockeringen för en enhet som etableringsstatusen återgår till det tidigare värdet **registrerad** eller **etablerad**.

## <a name="sdk-support"></a>SDK-support

SDK: er för Azure-enheter erbjudandet det enklaste sättet för dig implementera enhetskoden. Följande enhet SDK: er är tillgängliga:

- [Azure IoT SDK för C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK för Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK för Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK för Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK för .NET](https://github.com/azure/azure-iot-sdk-csharp)

Varje enhet ansluter med en unik anslutningssträng som identifierar enheten. En enhet kan endast ansluta till IoT hub där den är registrerad. När du skapar en riktig enhet i Azure IoT Central programmet skapar programmet den information du behöver att skapa en anslutningssträng genom att använda `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funktioner och IoT Hub-anslutning

All kommunikation mellan enheten med IoT Hub använder följande anslutningsalternativ för IoT Hub:

- [Enhet-till-moln-meddelanden](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Enhetstvillingar](../iot-hub/iot-hub-devguide-device-twins.md)

I följande tabell sammanfattas hur Azure IoT Central enhetsfunktioner mappas till IoT Hub funktioner:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mätning: Telemetri | Enhet-till-moln-meddelanden |
| Enhetsegenskaper | Enhetstvillingen rapporterade egenskaper |
| Inställningar | Enhetstvillingen önskad och rapporterade egenskaper |

Mer information om hur du använder SDK: er för enheter finns i följande artiklar exempelkod:

- [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs.md)
- [Ansluta en Raspberry Pi-enhet till Azure IoT Central programmet](howto-connect-raspberry-pi-python.md)
- [Ansluta en DevDiv kit enhet till Azure IoT Central programmet](howto-connect-devkit.md).

### <a name="protocols"></a>Protokoll

SDK: er för enheter stöder följande nätverksprotokoll för att ansluta till en IoT-hubb:

- MQTT
- AMQP
- HTTPS

Läs om hur dessa skillnaden protokoll och vägledning om hur du väljer en [välja ett kommunikationsprotokoll](../iot-hub/iot-hub-devguide-protocols.md).

Om enheten inte kan använda någon av protokoll som stöds, kan du använda Azure IoT Edge till protokoll konvertering. IoT Edge stöder andra intelligence-på-the-edge-scenarier för att avlasta bearbetning till gränsen från programmet Azure IoT Central.

## <a name="security"></a>Säkerhet

Krypteras alla data som utbyts mellan enheter och din Azure IoT Central. IoT Hub autentiserar varje begäran från en enhet som ansluter till någon av IoT Hub device-slutpunkter. För att undvika att byta ut autentiseringsuppgifter via kabel kan använder en enhet signera token för autentisering. Mer information finns i, [styra åtkomsten till IoT Hub](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om enhetsanslutning i Azure IoT Central, är här nästa föreslagna steg:

- [Förbereda och ansluta en DevKit-enhet](howto-connect-devkit.md)
- [Förbereda och ansluta en Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs.md)
- [C SDK: Etablering Enhetsklienten SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
