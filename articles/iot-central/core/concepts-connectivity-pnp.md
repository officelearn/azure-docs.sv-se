---
title: Enhets anslutning i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp som rör enhets anslutning i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: dd38ba8feb2d424eb95e74df73100367ad49090c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954843"
---
# <a name="device-connectivity-in-azure-iot-central-preview-features"></a>Enhets anslutning i Azure IoT Central (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här artikeln beskriver viktiga begrepp som rör enhets anslutning i Microsoft Azure IoT Central.

Azure IoT Central använder [azure IoT Hub Device Provisioning service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) för att hantera all enhets registrering och anslutning.

Genom att använda DPS kan du:

- IoT Central för att stödja onboarding och ansluta enheter i stor skala.
- Du genererar autentiseringsuppgifter för enheten och konfigurerar enheterna offline utan att registrera enheterna via IoT Central användar gränssnitt.
- Enheter som ska anslutas med signaturer för delad åtkomst (SAS).
- Enheter som ska anslutas med bransch standard X. 509-certifikat.
- Du kan använda dina egna enhets-ID: n för att registrera enheter i IoT Central. Genom att använda dina egna enhets-ID: er blir det enklare att integrera med befintliga Back Office-system.
- Ett enda, konsekvent sätt att ansluta enheter till IoT Central.

I den här artikeln beskrivs följande användnings fall:

- [Anslut snabbt en enskild enhet med hjälp av SAS](#connect-a-single-device)
- [Ansluta enheter i stor skala med SAS](#connect-devices-at-scale-using-sas)
- [Anslut enheter i skala med X. 509-certifikat](#connect-devices-using-x509-certificates) detta är den rekommenderade metoden för produktions miljöer.
- [Anslut utan att först registrera enheter](#connect-without-registering-devices)
- [Ansluta enheter med IoT Plug and Play-funktioner](#connect-devices-with-iot-plug-and-play)

## <a name="connect-a-single-device"></a>Anslut en enskild enhet

Den här metoden är användbar när du experimenterar med IoT Central eller testning av enheter. Du kan använda enhets anslutnings informationen från ditt IoT Central-program för att ansluta en enhet till ditt IoT Central-program med Device Provisioning-tjänsten (DPS). Du hittar exempel på DPS-enhetens klient kod för följande språk:

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

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

I en produktions miljö är användningen av X. 509-certifikat den rekommenderade mekanismen för enhets autentisering för IoT Central. Mer information finns i [enhets autentisering med X. 509 CA-certifikat](../../iot-hub/iot-hub-x509ca-overview.md).

Följande steg beskriver hur du ansluter enheter till IoT Central med X. 509-certifikat:

1. I ditt IoT Central-program _lägger du till och kontrollerar det mellanliggande eller rot-X. 509-certifikat_ som du använder för att generera enhets certifikat:

    - Gå till **Administration > enhets anslutning > certifikat (X. 509)** och Lägg till X. 509-rot eller mellanliggande certifikat som du använder för att generera löv enhets certifikat.

      ![Inställningar för anslutning](media/concepts-connectivity-pnp/connection-settings.png)

      Om du har en säkerhets överträdelse eller om ditt primära certifikat är inställt på upphör ande använder du det sekundära certifikatet för att minska stillestånds tiden. Du kan fortsätta att etablera enheter med hjälp av det sekundära certifikatet när du uppdaterar det primära certifikatet.

    - Genom att verifiera certifikatets ägarskap ser du till att omladdningen av certifikatet har certifikatets privata nyckel. Verifiera certifikatet:
        - Klicka på knappen bredvid **verifierings koden** för att generera en kod.
        - Skapa ett verifierings certifikat för X. 509 med den verifierings kod som du skapade i föregående steg. Spara certifikatet som en CER-fil.
        - Ladda upp det signerade verifierings certifikatet och välj **Verifiera**.

          ![Inställningar för anslutning](media/concepts-connectivity-pnp/verify-cert.png)

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

![Inställningar för anslutning](media/concepts-connectivity-pnp/device-connection-flow1.png)

I följande steg beskrivs den här processen i detalj. Stegen skiljer sig något beroende på om du använder SAS-eller X. 509-certifikat för autentisering av enhet:

1. Konfigurera anslutnings inställningar:

    - **X. 509-certifikat:** [Lägg till och verifiera rot-/mellanliggande certifikat](#connect-devices-using-x509-certificates) och Använd det för att generera enhets certifikaten i följande steg.
    - **SAS:** Kopiera primär nyckeln. Den här nyckeln är gruppens SAS-nyckel för IoT Central-programmet. Använd nyckeln för att generera SAS-nycklar för enheten i följande steg.
    ![anslutnings inställningar SAS](media/concepts-connectivity-pnp/connection-settings-sas.png)

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

1. Den anslutna enheten visas initialt som inte **kopplad** på sidan **enheter** . Enhetens etablerings status har **registrerats**. **Migrera** enheten till lämplig enhets mall och godkänn enheten för att ansluta till ditt IoT Central-program. Enheten kan sedan hämta en anslutnings sträng från IoT Hub och börja skicka data. Enhets etableringen är nu slutförd och etablerings statusen har nu **tillhandahållits**.

## <a name="individual-enrollment-based-device-connectivity"></a>Enskild registrering baserad på enhets anslutning

För kunder som ansluter enheter med autentiserade autentiseringsuppgifter per enhet är enskilda registreringar alternativet. En enskild registrering är en post för en enskild enhet som kan ansluta. Enskilda registreringar kan använda antingen X. 509-löv certifikat eller SAS-token (från en fysisk eller virtuell TPM) som attesterings metoder. Enhets-ID: t (aka-registrerings-ID) i en enskild registrering är alfanumeriskt, gement och får innehålla bindestreck. Läs mer om enskilda registreringar [här](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> När du skapar en enskild registrering för en enhet får den företräde framför standard grupp registrering baserad på certifikaten (SAS, X509) i din app.

### <a name="creating-individual-enrollments"></a>Skapa enskilda registreringar
IoT Central stöder följande mekanismer för attestering

1. **Attestering av symmetrisk nyckel:** Symmetrisk nyckel attestering är en enkel metod för att autentisera en enhet med en enhets etablerings tjänst instans. Så här skapar du en enskild registrering med symmetriska nycklar. öppna dialog rutan Anslut, Välj enskild registrering och mekanismen "SAS" och ange de primära och sekundära nycklarna. SAS-nycklar måste vara base64-kodade. Här är en [länk](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) till kod exempel som hjälper dig att skriva enhets koden för att etablera enheter med hjälp av symmetriska nycklar och enskilda registreringar.
1. **X. 509-certifikat:** X. 509-certifikat som rubrik föreslår är en cert-baserad mekanism för attestering, ett utmärkt sätt att skala produktionen. Om du vill skapa en enskild registrering med symmetriska nycklar väljer du individuell registrering och mekanismen "X. 509" och överför de primära och sekundära certifikaten och sparar för att skapa registreringen. Här är en [länk](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) till kod exempel som hjälper dig att skriva enhets koden för att etablera enheter med hjälp av X509. Enhets certifikat som används med en [enskild registrerings](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment) post har ett krav på att ämnes namnet måste anges till enhets-ID: t (aka registrerings-ID) för enskilda registrerings poster.
1. **TPM-attestering:** TPM står för Trusted Platform Module och är en typ av maskinvaru-säkerhetsmodul (HSM) och är ett av de säkraste sätten att ansluta dina enheter.  Den här artikeln förutsätter att du använder en diskret, inbyggd program vara eller integrerad TPM. Programvarubaserade TPM: er lämpar sig väl för prototyper eller testning, men de ger inte samma säkerhets nivå som diskret, inbyggd program vara eller integrerade TPM: er. Vi rekommenderar inte att du använder programvaru-TPM: er i produktion. Om du vill skapa en enskild registrering med symmetriska nycklar väljer du individuell registrering och mekanismen "TPM" och anger bekräftelse nycklar för att skapa registreringen. Om du vill ha mer information om typer av TPM kan du läsa mer om TPM-attestering [här](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation). Här är en [länk](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/TpmSample) till kod exempel som hjälper dig att skriva enhets koden för att etablera enheter med TPM. Om du vill skapa en TPM-baserad attestering skriver du bekräftelse nyckeln och sparar.

## <a name="connect-devices-with-iot-plug-and-play"></a>Ansluta enheter med IoT Plug and Play

En av de viktigaste funktionerna i IoT Plug and Play med IoT Central är möjligheten att koppla enhetsspecifika automatiskt på enhets anslutningen. Tillsammans med autentiseringsuppgifter kan enheter nu skicka **CapabilityModelId** som en del av enhetens registrerings anrop och IoT Central identifierar och associerar enhets mal len. Identifierings processen följer följande ordning:

1. Associeras med enhets mal len om den redan har publicerats i IoT Central-programmet.
1. Hämtar från den offentliga lagrings platsen för publicerade och certifierade kapacitets modeller.

Nedan visas formatet på den extra nytto last som enheten skickar under DPS-registreringen

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

> [!NOTE]
> Observera att alternativet för automatiskt godkännande ska vara aktiverat för att enheter ska ansluta automatiskt, identifiera modellen och börja skicka data.

## <a name="device-status"></a>Enhets status

När en riktig enhet ansluter till ditt IoT Central-program ändras enhetens status enligt följande:

1. Enhetens status **registreras**först. Den här statusen innebär att enheten skapas i IoT Central och har ett enhets-ID. En enhet registreras när:
    - En ny riktig enhet läggs till på sidan **enheter** .
    - En uppsättning enheter läggs till med hjälp av **Importera** på sidan **enheter** .

1. Enhetens status ändras till **etablerad** när enheten som anslöt till ditt IoT Central program med giltiga autentiseringsuppgifter Slutför etablerings steget. I det här steget hämtar enheten en anslutnings sträng från IoT Hub. Enheten kan nu ansluta till IoT Hub och börja skicka data.

1. En operatör kan blockera en enhet. När en enhet blockeras kan den inte skicka data till ditt IoT Central-program. Blockerade enheter har statusen **blockerad**. En operatör måste återställa enheten innan den kan återuppta sändning av data. När en operatör avblockerar en enhet återgår statusen till sitt tidigare värde, **registrerad** eller **etablerad**.

1. Enhetens status **väntar på godkännande** vilket innebär att alternativet **Godkänn automatiskt** är inaktiverat och kräver att alla enheter som ansluter till IoT Central uttryckligen godkänns av en operatör. Enheter som inte är registrerade manuellt på sidan **enheter** , men som är anslutna med giltiga autentiseringsuppgifter, har enhets statusen **väntar på godkännande**. Operatörer kan godkänna dessa enheter från sidan **enheter** med knappen **Godkänn** .

1. Enhetens status är inte **associerad** , vilket innebär att enheter som ansluter till IoT Central inte har någon kopplad enhets mall. Detta inträffar vanligt vis i följande scenarier:
    - En uppsättning enheter läggs till med hjälp av **Importera** på sidan **enheter** utan att ange enhets mal len
    - Enheter som inte registrerats manuellt på sidan **enheter** är anslutna till giltiga autentiseringsuppgifter men utan att ange mall-ID vid registreringen.  
Operatören kan koppla en enhet till en mall från sidan **enheter** med knappen **migrera** .

## <a name="sdk-support"></a>SDK-stöd

Azure-enhetens SDK: er erbjuder det enklaste sättet för dig att implementera enhets koden. Följande enhets-SDK: er är tillgängliga:

- [Azure IoT SDK för C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK för python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK för Node. js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK för Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK för .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funktioner och IoT Hub anslutning

All enhets kommunikation med IoT Hub använder följande IoT Hub anslutnings alternativ:

- [Meddelanden från enhet till moln](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Enhets dubbla](../../iot-hub/iot-hub-devguide-device-twins.md)

I följande tabell sammanfattas hur Azure IoT Central enhets funktioner mappas till IoT Hub funktioner:

| Utforska IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mått: telemetri | Meddelanden från enhet till moln |
| Enhetsegenskaper | Enhetens dubbla rapporterade egenskaper |
| Inställningar | Enhetens dubbla önskade och rapporterade egenskaper |

Mer information om hur du använder enhets-SDK: er finns i [ansluta en DevDiv-paket-enhet till ditt Azure IoT Central-program](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) till exempel kod.

### <a name="protocols"></a>Protokoll

Enhets-SDK: erna stöder följande nätverks protokoll för att ansluta till en IoT-hubb:

- MQTT
- AMQP
- HTTPS

Information om dessa skillnads protokoll och vägledning om hur du väljer ett finns i [Välj ett kommunikations protokoll](../../iot-hub/iot-hub-devguide-protocols.md).

Om enheten inte kan använda något av de protokoll som stöds kan du använda Azure IoT Edge för protokoll konvertering. IoT Edge stöder andra Intelligence-scenarier för att avlasta bearbetningen till gränsen från Azure IoT Central-programmet.

## <a name="security"></a>Säkerhet

Alla data som utbyts mellan enheter och din Azure-IoT Central krypteras. IoT Hub autentiserar varje begäran från en enhet som ansluts till någon av de enhets riktade IoT Hub slut punkterna. För att undvika att utbyta autentiseringsuppgifter via kabeln använder enheten signerade token för att autentisera. Mer information finns i, [kontrol lera åtkomst till IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om enhets anslutningen i Azure IoT Central, så är det förslag på nästa steg:

- [Förbereda och ansluta en DevKit-enhet](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [C SDK: etablering av enhets klientens SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
