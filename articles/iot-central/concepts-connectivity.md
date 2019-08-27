---
title: Enhets anslutning i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp som rör enhets anslutning i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0088cc2c2bd30748a8a62217c76f962dd1b174f8
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019990"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Enhets anslutning i Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Den här artikeln beskriver viktiga begrepp som rör enhets anslutning i Microsoft Azure IoT Central.

Azure IoT Central använder [azure IoT Hub Device Provisioning service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) för att hantera all enhets registrering och anslutning.

Genom att använda DPS kan du:

- IoT Central för att stödja onboarding och ansluta enheter i stor skala.
- Du genererar autentiseringsuppgifter för enheten och konfigurerar enheterna offline utan att registrera enheterna via IoT Central användar gränssnitt.
- Enheter som ska anslutas med signaturer för delad åtkomst (SAS).
- Enheter som ska anslutas med bransch standard X. 509-certifikat.
- Du kan använda dina egna enhets-ID: n för att registrera enheter i IoT Central. Genom att använda dina egna enhets-ID: er blir det enklare att integrera med befintliga Back Office-system.
- Ett enda, konsekvent sätt att ansluta enheter till IoT Central.

I den här artikeln beskrivs följande fyra användnings fall:

1. [Anslut snabbt en enskild enhet med hjälp av SAS](#connect-a-single-device)
1. [Ansluta enheter i stor skala med SAS](#connect-devices-at-scale-using-sas)
1. [Anslut enheter i skala med X. 509-certifikat](#connect-devices-using-x509-certificates) detta är den rekommenderade metoden för produktions miljöer.
1. [Anslut utan att först registrera enheter](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Anslut en enskild enhet

Den här metoden är användbar när du experimenterar med IoT Central eller testning av enheter. Du kan använda anslutnings informationen för enheten från ditt IoT Central program för att generera anslutnings strängen för en enhet. Detaljerade anvisningar finns i [så här skapar du en enhets anslutnings sträng för att ansluta till ett Azure IoT Central-program](howto-generate-connection-string.md).

## <a name="connect-devices-at-scale-using-sas"></a>Ansluta enheter i stor skala med SAS

Om du vill ansluta enheter till IoT Central i stor skala med hjälp av SAS måste du registrera och sedan konfigurera enheterna:

### <a name="register-devices-in-bulk"></a>Registrera enheter i bulk

Om du vill registrera ett stort antal enheter med IoT Central programmet använder du en CSV-fil för att [Importera enhets-ID: n och enhets namn](howto-manage-devices.md#import-devices).

Om du vill hämta anslutnings informationen för de importerade enheterna [exporterar du en CSV-fil från IoT Central-programmet](howto-manage-devices.md#export-devices).

> [!NOTE]
> Om du vill lära dig hur du kan ansluta enheter utan att först registrera dem i IoT Central, se [Anslut utan att först registrera enheter](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Konfigurera dina enheter

Använd anslutnings informationen från export filen i enhets koden för att göra det möjligt för enheterna att ansluta och skicka data till IoT till ditt IoT Central-program. Mer information om hur du ansluter enheter finns i [Nästa steg](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Ansluta enheter med X. 509-certifikat

I en produktions miljö är användningen av X. 509-certifikat den rekommenderade mekanismen för enhets autentisering för IoT Central. Mer information finns i [enhets autentisering med X. 509 CA-certifikat](../iot-hub/iot-hub-x509ca-overview.md).

Följande steg beskriver hur du ansluter enheter till IoT Central med X. 509-certifikat:

1. I ditt IoT Central-program _lägger du till och kontrollerar det mellanliggande eller rot-X. 509-certifikat_ som du använder för att generera enhets certifikat:

    - Gå till **Administration > enhets anslutning > certifikat (X. 509)** och Lägg till X. 509-rot eller mellanliggande certifikat som du använder för att generera löv enhets certifikat.

      ![Inställningar för anslutning](media/concepts-connectivity/connection-settings.png)

      Om du har en säkerhets överträdelse eller om ditt primära certifikat är inställt på upphör ande använder du det sekundära certifikatet för att minska stillestånds tiden. Du kan fortsätta att etablera enheter med hjälp av det sekundära certifikatet när du uppdaterar det primära certifikatet.

    - Genom att verifiera certifikatets ägarskap ser du till att omladdningen av certifikatet har certifikatets privata nyckel. Verifiera certifikatet:
        - Klicka på knappen bredvid **verifierings koden** för att generera en kod.
        - Skapa ett verifierings certifikat för X. 509 med den verifierings kod som du skapade i föregående steg. Spara certifikatet som en CER-fil.
        - Ladda upp det signerade verifierings certifikatet och välj **Verifiera**.

          ![Inställningar för anslutning](media/concepts-connectivity/verify-cert.png)

1. Använd en CSV-fil för att _Importera och registrera enheter_ i ditt IoT Central-program.

1. _Konfigurera dina enheter._ Generera löv certifikaten med det överförda rot certifikatet. Använd **enhets-ID: t** som CNAME-värde i löv certifikaten. Enhets-ID: t bör bara innehålla gemener. Program mera sedan enheterna med information om etablerings tjänsten. När en enhet slås på för första gången, hämtas anslutnings informationen för ditt IoT Central program från DPS.

### <a name="further-reference"></a>Ytterligare referens

- Exempel på implementering av [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Exempel på enhets klient i C.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Endast i testnings syfte

För testning kan du använda dessa verktyg för att skapa CA-certifikat och enhets certifikat.

- Om du använder en DevKit-enhet genererar detta [kommando rads verktyg](https://aka.ms/iotcentral-docs-dicetool) ett CA-certifikat som du kan lägga till i ditt IoT Central program för att verifiera certifikaten.

- Använd det här [kommando rads verktyget](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) för att:
  - Skapa en certifikat kedja. Följ steg 2 i GitHub-artikeln.
  - Spara certifikaten som CER-filer som ska överföras till ditt IoT Central-program.
  - Använd verifierings koden från IoT Central programmet för att generera verifierings certifikatet. Följ steg 3 i GitHub-artikeln.
  - Skapa löv certifikat för dina enheter med dina enhets-ID: n som en parameter till verktyget. Följ steg 4 i GitHub-artikeln.

## <a name="connect-without-registering-devices"></a>Anslut utan att registrera enheter

Ett nyckel scenario IoT Central möjliggör för OEM-tillverkare att tillverka enheter som kan ansluta till ett IoT Central program utan att först registreras. En tillverkare måste generera lämpliga autentiseringsuppgifter och konfigurera enheterna i fabriken. När en enhet startar för första gången ansluter den automatiskt till ett IoT Central-program. En IoT Central operatör måste godkänna enheten innan den kan skicka data.

Följande diagram beskriver det här flödet:

![Inställningar för anslutning](media/concepts-connectivity/device-connection-flow1.png)

I följande steg beskrivs den här processen i detalj. Stegen skiljer sig något beroende på om du använder SAS-eller X. 509-certifikat för autentisering av enhet:

1. Konfigurera anslutnings inställningar:

    - **X. 509-certifikat:** [Lägg till och verifiera rot-/mellanliggande certifikat](#connect-devices-using-x509-certificates) och Använd det för att generera enhets certifikaten i följande steg.
    - **SAS:** Kopiera primär nyckeln. Den här nyckeln är gruppens SAS-nyckel för IoT Central-programmet. Använd nyckeln för att generera SAS-nycklar för enheten i följande steg.
    ![Anslutnings inställningar SAS](media/concepts-connectivity/connection-settings-sas.png)

1. Generera autentiseringsuppgifter för enheten
    - **Certifikat X. 509:** Generera löv certifikat för dina enheter med hjälp av rot-eller mellanliggande certifikat som du har lagt till i ditt IoT Central-program. Se till att du använder det lägsta fallet **enhets-ID** som CNAME i löv certifikaten. I test syfte kan du använda det här [kommando rads verktyget](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) för att generera enhets certifikat.
    - **SAS:** Använd det här [kommando rads verktyget](https://www.npmjs.com/package/dps-keygen) för att generera enhets-SAS-nycklar. Använd gruppens **primära nyckel** från föregående steg. Enhets-ID: t måste vara gemener.

      Kör följande kommando för att installera [verktyget för nyckel Generator](https://github.com/Azure/dps-keygen):

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Kör följande kommando för att skapa en enhets nyckel från den primära gruppen SAS-nyckel:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Om du vill konfigurera dina enheter måste du skapa Flash varje enhet med **scope-ID**, **enhets-ID**och **X. 509 enhets certifikat** eller **SAS-nyckel**.

1. Sätt sedan på enheten för att ansluta till ditt IoT Central-program. När du växlar på en enhet ansluter den först till DPS för att hämta IoT Central registrerings information.

1. Den anslutna enheten visas initialt som en **enhet** som inte tillhör på sidan **Device Explorer** . Enhetens etablerings status har **registrerats**. **Koppla** enheten till lämplig enhets mall och godkänn enheten för att ansluta till ditt IoT Central-program. Enheten kan sedan hämta en anslutnings sträng från IoT Hub och börja skicka data. Enhets etableringen är nu slutförd och etablerings statusen har nu tillhandahållits.

## <a name="provisioning-status"></a>Etableringsstatus

När en riktig enhet ansluter till ditt IoT Central-program ändras dess etablerings status enligt följande:

1. Enhetens etablerings status **registreras**först. Den här statusen innebär att enheten skapas i IoT Central och har ett enhets-ID. En enhet registreras när:
    - En ny riktig enhet läggs till på sidan **Device Explorer** .
    - En uppsättning enheter läggs till med hjälp av **import** på sidan **Device Explorer** .
    - En enhet registrerades inte manuellt på **Device Explorer** sidan, men ansluten med giltiga autentiseringsuppgifter och visas som en ej **kopplad** enhet på **Device Explorer** sidan.

1. Enhetens etablerings status ändras till **etablerad** när enheten som anslöt till ditt IoT Central program med giltiga autentiseringsuppgifter Slutför etablerings steget. I det här steget hämtar enheten en anslutnings sträng från IoT Hub. Enheten kan nu ansluta till IoT Hub och börja skicka data.

1. En operatör kan blockera en enhet. När en enhet blockeras kan den inte skicka data till ditt IoT Central-program. Blockerade enheter har en etablerings status som **blockeras**. En operatör måste återställa enheten innan den kan återuppta sändning av data. När en operatör avblockerar en enhet återgår etablerings statusen till sitt tidigare värde, **registrerad** eller **etablerad**.

## <a name="sdk-support"></a>SDK-stöd

Azure-enhetens SDK: er erbjuder det enklaste sättet för dig att implementera enhets koden. Följande enhets-SDK: er är tillgängliga:

- [Azure IoT SDK för C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK för python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK för Node. js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK för Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK för .NET](https://github.com/azure/azure-iot-sdk-csharp)

Varje enhet ansluter med en unik anslutnings sträng som identifierar enheten. En enhet kan endast ansluta till IoT-hubben där den är registrerad. När du skapar en riktig enhet i ditt Azure IoT Central-program genererar programmet den information som du behöver för att skapa en anslutnings sträng `dps-keygen`med hjälp av.

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funktioner och IoT Hub anslutning

All enhets kommunikation med IoT Hub använder följande IoT Hub anslutnings alternativ:

- [Meddelanden från enhet till moln](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Enhets dubbla](../iot-hub/iot-hub-devguide-device-twins.md)

I följande tabell sammanfattas hur Azure IoT Central enhets funktioner mappas till IoT Hub funktioner:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Värdering Telemetri | Meddelanden från enhet till moln |
| Enhetsegenskaper | Enhetens dubbla rapporterade egenskaper |
| Inställningar | Enhetens dubbla önskade och rapporterade egenskaper |

Mer information om hur du använder enhets-SDK: er finns i någon av följande artiklar för exempel kod:

- [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs.md)
- [Ansluta en Raspberry Pi-enhet till ditt Azure IoT Central-program](howto-connect-raspberry-pi-python.md)
- [Anslut en DevDiv kit-enhet till ditt Azure IoT Central-program](howto-connect-devkit.md).

### <a name="protocols"></a>Protokoll

Enhets-SDK: erna stöder följande nätverks protokoll för att ansluta till en IoT-hubb:

- MQTT
- AMQP
- HTTPS

Information om dessa skillnads protokoll och vägledning om hur du väljer ett finns i [Välj ett kommunikations protokoll](../iot-hub/iot-hub-devguide-protocols.md).

Om enheten inte kan använda något av de protokoll som stöds kan du använda Azure IoT Edge för protokoll konvertering. IoT Edge stöder andra Intelligence-scenarier för att avlasta bearbetningen till gränsen från Azure IoT Central-programmet.

## <a name="security"></a>Säkerhet

Alla data som utbyts mellan enheter och din Azure-IoT Central krypteras. IoT Hub autentiserar varje begäran från en enhet som ansluts till någon av de enhets riktade IoT Hub slut punkterna. För att undvika att utbyta autentiseringsuppgifter via kabeln använder enheten signerade token för att autentisera. Mer information finns i, [kontrol lera åtkomst till IoT Hub](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om enhets anslutningen i Azure IoT Central, så är det förslag på nästa steg:

- [Förbereda och ansluta en DevKit-enhet](howto-connect-devkit.md)
- [Förbereda och ansluta en Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs.md)
- [C SDK: Etablering av enhets klientens SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
