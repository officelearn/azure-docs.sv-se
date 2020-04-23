---
title: Enhetsanslutning i Azure IoT Central | Microsoft-dokument
description: Den här artikeln introducerar viktiga begrepp som rör enhetsanslutning i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 1398169c44dadcd11ad037e4e3a1cc0132e21f13
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024701"
---
# <a name="get-connected-to-azure-iot-central"></a>Ansluta till Azure IoT Central

*Den här artikeln gäller operatörer och enhetsutvecklare.*

I den här artikeln beskrivs alternativen för att ansluta dina enheter till ett Azure IoT Central-program.

Vanligtvis måste du registrera en enhet i programmet innan den kan ansluta. IoT Central stöder dock scenarier där [enheter kan ansluta utan att först registreras](#connect-without-registering-devices).

IoT Central använder [AZURE IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) för att hantera anslutningsprocessen. En enhet ansluter först till en DPS-slutpunkt för att hämta den information den behöver för att ansluta till ditt program. Internt använder ditt IoT Central-program en IoT-hubb för att hantera enhetsanslutning. Med HJÄLP AV DPS kan:

- IoT Central för att stödja introduktions- och anslutningsenheter i stor skala.
- Du kan generera enhetsautentiseringsuppgifter och konfigurera enheterna offline utan att registrera enheterna via IoT Central UI.
- Du använder dina egna enhets-ID:er för att registrera enheter i IoT Central. Genom att använda dina egna enhets-ID:er förenklas integreringen med befintliga backoffice-system.
- Ett enda konsekvent sätt att ansluta enheter till IoT Central.

För att skydda kommunikationen mellan en enhet och ditt program stöder IoT Central både SAS-certifikat (Shared Access Signatures) och X.509. X.509-certifikat rekommenderas i produktionsmiljöer.

I den här artikeln beskrivs följande användningsfall:

- [Ansluta en enda enhet med SAS](#connect-a-single-device)
- [Ansluta enheter i stor skala med SAS](#connect-devices-at-scale-using-sas)
- [Anslut enheter i stor skala med X.509-certifikat](#connect-devices-using-x509-certificates) – den rekommenderade metoden för produktionsmiljöer.
- [Anslut enheter utan att först registrera dem](#connect-without-registering-devices)
- [Ansluta enheter som använder enskilda DPS-registreringar](#individual-enrollment-based-device-connectivity)
- [Ansluta enheter med hjälp av IoT Plug and Play (preview)-funktioner](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>Ansluta en enda enhet

Den här metoden är användbar när du experimenterar med IoT Central eller testar enheter. Du kan använda SAS-tangenterna för enhetsanslutning från ditt IoT Central-program för att ansluta en enhet till ditt IoT Central-program. Kopiera _enhetens SAS-nyckel_ från anslutningsinformationen för en registrerad enhet:

![SAS-nycklar för en enskild enhet](./media/concepts-get-connected/single-device-sas.png)

Mer information finns i create [and connect a Node.js-klientprogrammet till din Azure IoT Central-programdator.](./tutorial-connect-device-nodejs.md)

## <a name="connect-devices-at-scale-using-sas"></a>Ansluta enheter i stor skala med SAS

Om du vill ansluta enheter till IoT Central i stor skala med SAS-nycklar måste du registrera dig och sedan konfigurera enheterna:

### <a name="register-devices-in-bulk"></a>Registrera enheter i grupp

Om du vill registrera ett stort antal enheter med ditt IoT Central-program använder du en CSV-fil för att [importera enhets-ID och enhetsnamn](howto-manage-devices.md#import-devices).

Om du vill hämta anslutningsinformationen för de importerade enheterna [exporterar du en CSV-fil från ioT Central-programmet](howto-manage-devices.md#export-devices). Den exporterade CSV-filen innehåller enhets-ID:n och SAS-nycklarna.

### <a name="set-up-your-devices"></a>Konfigurera dina enheter

Använd anslutningsinformationen från exportfilen i enhetskoden för att göra det möjligt för dina enheter att ansluta och skicka data till IoT till ditt IoT Central-program. Du behöver också DPS **ID-scopet** för ditt program. Du hittar det här värdet i **> Enhetsanslutning**.

> [!NOTE]
> Mer information om hur du kan ansluta enheter utan att först registrera dem i IoT Central finns i [Anslut utan att först registrera enheter](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Ansluta enheter med X.509-certifikat

I en produktionsmiljö är X.509-certifikat den rekommenderade enhetsautentiseringsmekanismen för IoT Central. Mer information finns i [Enhetsautentisering med X.509 CA-certifikat](../../iot-hub/iot-hub-x509ca-overview.md).

Innan du ansluter en enhet med ett X.509-certifikat lägger du till och verifierar ett mellanliggande X.509-certifikat i programmet. Enheter måste använda blad X.509-certifikat som genereras från rot- eller mellancertifikatet.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Lägga till och verifiera ett rot- eller mellanliggande certifikat

Navigera till **Administration > Enhetsanslutning > Hantera primärt certifikat** och lägg till X.509-rot- eller mellanliggande certifikat som du använder för att generera enhetscertifikaten.

![Inställningar för anslutning](media/concepts-get-connected/manage-x509-certificate.png)

Verifiera certifikatägarskap säkerställer att den person som överför certifikatet har certifikatets privata nyckel. Så här verifierar du certifikatet:

  1. Välj knappen **bredvid Verifieringskod** för att generera en kod.
  1. Skapa ett X.509-verifieringscertifikat med den verifieringskod som du skapade i föregående steg. Spara certifikatet som en CER-fil.
  1. Ladda upp det signerade verifieringscertifikatet och välj **Verifiera**. Certifikatet markeras som **Verifierat** när verifieringen lyckas.

Om du har en säkerhetsöverträdelse eller om ditt primära certifikat har angetts för att upphöra att gälla använder du det sekundära certifikatet för att minska stilleståndstiden. Du kan fortsätta att etablera enheter med det sekundära certifikatet medan du uppdaterar det primära certifikatet.

### <a name="register-and-connect-devices"></a>Registrera och ansluta enheter

Om du vill ansluta enheter med X.509-certifikat för massanslutning registrerar du först enheterna i programmet genom att använda en CSV-fil för att [importera enhets-ID:n och enhetsnamnen](howto-manage-devices.md#import-devices). Enhets-ID:erna ska alla vara i gemener.

Generera X.509 bladcertifikat för dina enheter med hjälp av det uppladdade rot- eller mellanliggande certifikatet. Använd **enhets-ID:et** `CNAME` som värde i lövcertifikaten. Enhetskoden behöver **ID-scopevärdet** för ditt program, **enhets-ID**och motsvarande enhetscertifikat.

### <a name="for-testing-purposes-only"></a>Endast i testsyfte

Endast för testning kan du använda följande verktyg för att generera rot-, mellan- och enhetscertifikat:

- [Verktyg för Azure IoT Device Provisioning Device SDK:](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)en samling node.js-verktyg som du kan använda för att generera och verifiera X.509-certifikat och nycklar.
- Om du använder en DevKit-enhet genererar det här [kommandoradsverktyget ett CA-certifikat](https://aka.ms/iotcentral-docs-dicetool) som du kan lägga till i ditt IoT Central-program för att verifiera certifikaten.
- [Hantera certifikatutfärdarcertifikat för testcertifikat för exempel och självstudier:](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)en samling PowerShell- och Bash-skript till:
  - Skapa en certifikatkedja.
  - Spara certifikaten som .cer-filer för att ladda upp till ditt IoT Central-program.
  - Använd verifieringskoden från IoT Central-programmet för att generera verifieringscertifikatet.
  - Skapa lövcertifikat för dina enheter med enhets-ID:erna som en parameter till verktyget.

### <a name="further-reference"></a>Ytterligare referens

- [Exempel implementering för RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)
- [Exempel på enhetsklient i C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>Anslut utan att registrera enheter

De tidigare beskrivna scenarierna kräver att du registrerar enheter i ditt program innan de ansluter. IoT Central gör det också möjligt för OEM-tillverkare att masstillverkningsenheter som kan ansluta utan att först registreras. En OEM-tillverkare genererar lämpliga enhetsautentiseringsuppgifter och konfigurerar enheterna i fabriken. När en kund slår på en enhet för första gången ansluter den till DPS, som sedan automatiskt ansluter enheten till rätt IoT Central-program. En IoT Central-operatör måste godkänna enheten innan den börjar skicka data till programmet.

Flödet är något annorlunda beroende på om enheterna använder SAS-token eller X.509-certifikat:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Ansluta enheter som använder SAS-token utan att registrera

1. Kopiera IoT Central-programmets gruppprimernyckel:

    ![Primär SAS-nyckel för programgrupp](media/concepts-get-connected/group-sas-keys.png)

1. Använd [dps-keygen-verktyget](https://www.npmjs.com/package/dps-keygen) för att generera enheten SAS-nycklar. Använd gruppens primärnyckel från föregående steg. Enhets-ID:erna måste vara gemener:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. OEM-tillverkaren blinkar varje enhet med ett enhets-ID, en genererad sas-nyckel för enheten och **program-ID-scopevärdet.**

1. När du slår på en enhet ansluter den först till DPS för att hämta sin IoT Central-registreringsinformation.

    Enheten har inledningsvis en **enhetsstatus Oassocierad** på sidan **Enheter** och har inte tilldelats en enhetsmall. På sidan **Enheter** **migrerar du** enheten till rätt enhetsmall. Enhetsetablering är nu klar, **enhetsstatusen**är nu etablerad och enheten kan börja skicka data.

    På **sidan Administration > Enhetsanslutning** styr alternativet **Automatiskt om** du behöver godkänna enheten manuellt innan den kan börja skicka data.

    > [!NOTE]
    > Mer information om hur du automatiskt associerar en enhet med en enhetsmall finns i [Ansluta enheter med IoT Plug and Play (förhandsgranskning)](#connect-devices-with-iot-plug-and-play-preview).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Anslut enheter som använder X.509-certifikat utan att registrera dig

1. [Lägg till och verifiera ett rot- eller mellanliggande X.509-certifikat](#connect-devices-using-x509-certificates) i ditt IoT Central-program. (#connect-enheter-använda-x509-certifikat)

1. Generera lövcertifikat för dina enheter med hjälp av rot- eller mellanliggande certifikat som du har lagt till i ioT Central-programmet. Använd tilläggsenhets-ID:er som `CNAME` i lövcertifikaten.

1. OEM-tillverkaren blinkar varje enhet med ett enhets-ID, ett genererat vänster X.509-certifikat och **program-ID-scopevärdet.**

1. När du slår på en enhet ansluter den först till DPS för att hämta sin IoT Central-registreringsinformation.

    Enheten har inledningsvis en **enhetsstatus Oassocierad** på sidan **Enheter** och har inte tilldelats en enhetsmall. På sidan **Enheter** **migrerar du** enheten till rätt enhetsmall. Enhetsetablering är nu klar, **enhetsstatusen**är nu etablerad och enheten kan börja skicka data.

    På **sidan Administration > Enhetsanslutning** styr alternativet **Automatiskt om** du behöver godkänna enheten manuellt innan den kan börja skicka data.

    > [!NOTE]
    > Mer information om hur du automatiskt associerar en enhet med en enhetsmall finns i [Ansluta enheter med IoT Plug and Play (förhandsgranskning)](#connect-devices-with-iot-plug-and-play-preview).

## <a name="individual-enrollment-based-device-connectivity"></a>Individuell registreringsbaserad enhetsanslutning

För kunder som ansluter enheter som var och en har sina egna autentiseringsuppgifter använder du enskilda registreringar. En enskild registrering är en post för en enda enhet som tillåts ansluta. Enskilda registreringar kan använda antingen X.509 leaf certifikat eller SAS-token (från en fysisk eller virtuell betrodd plattform modul) som attestering mekanismer. Enhets-ID:t (kallas även registrerings-ID) i en enskild registrering är alfanumeriskt, gemener och kan innehålla bindestreck. Mer information finns i [DPS-enskilda registrering](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> När du skapar en enskild registrering för en enhet har den företräde framför standardalternativen för gruppregistrering i ditt IoT Central-program.

### <a name="creating-individual-enrollments"></a>Skapa enskilda registreringar

IoT Central stöder följande attestationmekanismer för enskilda registreringar:

- **Symmetrisk nyckeltörsstation:** Symmetrisk nyckeltysststation är en enkel metod för att autentisera en enhet med DPS-instansen. Om du vill skapa en enskild registrering som använder symmetriska nycklar öppnar du sidan **Enhetsanslutning,** väljer **Individuell registrering** som anslutningsmetod och **SAS (Shared Access signature)** som mekanism. Ange bas64-kodade primära och sekundära nycklar och spara ändringarna. Använd **ID-scopet,** **enhets-ID**och antingen den primära eller sekundära nyckeln för att ansluta enheten.

    > [!TIP]
    > För testning kan du använda **OpenSSL** för att generera base64-kodade nycklar:`openssl rand -base64 64`

- **X.509 certifikat:** Om du vill skapa en enskild registrering med X.509-certifikat öppnar du sidan **Enhetsanslutning,** väljer **Individuell registrering** som anslutningsmetod och **Certifikat (X.509)** som mekanism. Enhetscertifikat som används med en enskild registreringspost har ett krav på att utfärdaren och ämnes-CN är inställda på enhets-ID.

    > [!TIP]
    > För testning kan du använda [Verktyg för Azure IoT Device Provisioning Device SDK for Node.js för](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) att generera ett självsignerat certifikat:`node create_test_cert.js device "mytestdevice"`

- **TPM-intyg (Trusted Platform Module):** En [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) är en typ av maskinvarusäkerhetsmodul. Att använda en TPM är ett av de säkraste sätten att ansluta en enhet. Den här artikeln förutsätter att du använder en diskret, firmware eller integrerad TPM. Programvarureglerade TPM:er är väl lämpade för prototyper eller testning, men de ger inte samma säkerhetsnivå som diskreta, inbyggda eller integrerade TPM:er. Använd inte program-TPM i produktion. Om du vill skapa en enskild registrering som använder en TPM öppnar du sidan **Enhetsanslutning,** väljer **Individuell registrering** som anslutningsmetod och **TPM** som mekanism. Ange TPM-bekräftelsenyckeln och spara enhetsanslutningsinformationen.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>Ansluta enheter med IoT Plug and Play (förhandsgranskning)

En av de viktigaste funktionerna i IoT Plug and Play (preview) med IoT Central är möjligheten att associera enhetsmallar automatiskt på enhetsanslutning. Tillsammans med enhetsautentiseringsuppgifter kan enheter nu skicka **CapabilityModelId** som en del av enhetsregistreringsanropet. Med den här funktionen kan IoT Central identifiera och associera enhetsmallen med enheten. Identifieringsprocessen fungerar på följande sätt:

1. Associerar med enhetsmallen om den redan har publicerats i IoT Central-programmet.
1. Hämtar från den offentliga lagringsplatsen för publicerade och certifierade kapacitetsmodeller.

Nedan är formatet på den extra nyttolast enheten skulle skicka under DPS registreringssamtal

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Observera att alternativet **Automatisk godkännande** på administration **> Enhetsanslutning** måste vara aktiverat för enheter som automatiskt ska ansluta, identifiera enhetsmallen och börja skicka data.

## <a name="device-status-values"></a>Värden för enhetsstatus

När en riktig enhet ansluter till ditt IoT Central-program ändras enhetens status på följande sätt:

1. Enhetsstatusen är först **registrerad**. Den här statusen innebär att enheten skapas i IoT Central och har ett enhets-ID. En enhet registreras när:
    - En ny riktig enhet läggs till på sidan **Enheter.**
    - En uppsättning enheter läggs till med **sidan Importera** på sidan **Enheter.**

1. Enhetsstatusen **ändras** till Etablerad när enheten som är ansluten till ditt IoT Central-program med giltiga autentiseringsuppgifter slutför etableringssteget. I det här steget använder enheten DPS för att automatiskt hämta en anslutningssträng från IoT Hub som används av ditt IoT Central-program. Enheten kan nu ansluta till IoT Central och börja skicka data.

1. En operatör kan blockera en enhet. När en enhet är blockerad kan den inte skicka data till ditt IoT Central-program. Blockerade enheter har **statusen Blockerad**. En operatör måste återställa enheten innan den kan fortsätta att skicka data. När en operatör avblockerar en enhet återgår statusen till sitt tidigare värde, **Registrerat** eller **etablerat**.

1. Om enhetsstatusen väntar **på godkännande**betyder det att alternativet **Automatisk godkännande** är inaktiverat. En operatör måste uttryckligen godkänna en enhet innan den börjar skicka data. Enheter som inte registreras manuellt på sidan **Enheter,** men som är anslutna till giltiga autentiseringsuppgifter, har enhetsstatusen **Väntar på godkännande**. Operatörer kan godkänna dessa enheter från sidan **Enheter** med knappen **Godkänn.**

1. Om **enhetsstatusen**inte är associerad betyder det att enheten som ansluter till IoT Central inte har någon associerad enhetsmall. Den här situationen inträffar vanligtvis i följande scenarier:

    - En uppsättning enheter läggs till med hjälp av **Importera** på sidan **Enheter** utan att enhetsmallen anges.
    - En enhet registrerades manuellt på sidan **Enheter** utan att enhetsmallen angavs. Enheten som sedan är ansluten till giltiga autentiseringsuppgifter.  

    Operatören kan associera en enhet till en enhetsmall från sidan **Enheter** med knappen **Migrera.**

## <a name="best-practices"></a>Bästa praxis

Spara eller cachelagra inte enhetens anslutningssträng som DPS returnerar när du ansluter enheten. Om du vill återansluta en enhet går du igenom standardregistreringsflödet för enheten för att få rätt enhetsanslutningssträng. Om enheten cachelagrar anslutningssträngen riskerar enhetsprogrammet att ha en inaktuell anslutningssträng om IoT Central uppdaterar den underliggande Azure IoT-hubben som används.

## <a name="sdk-support"></a>SDK-support

Azure Device SDK:er är det enklaste sättet för dig att implementera enhetskoden. Följande enhetSDK:er är tillgängliga:

- [Azure IoT SDK för C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK för Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK för Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK för Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK för .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funktioner och IoT Hub-anslutning

All enhetskommunikation med IoT Hub använder följande IoT Hub-anslutningsalternativ:

- [Meddelanden från enhet till moln](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Enhetstvillingar](../../iot-hub/iot-hub-devguide-device-twins.md)

I följande tabell sammanfattas hur Azure IoT Central-enheten har mappas till IoT Hub-funktioner:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetri | Meddelanden från enhet till moln |
| Egenskap | Enhetstvilling rapporterade egenskaper |
| Egenskap (skrivbar) | Enhetstvilling önskade och rapporterade egenskaper |
| Kommando | Direkta metoder |

Mer information om hur du använder EnhetSDK:erna finns i [Ansluta en DevDiv-kitenhet till ditt Azure IoT Central-program,](howto-connect-devkit.md) till exempel kod.

### <a name="protocols"></a>Protokoll

Enhets-SDK:er stöder följande nätverksprotokoll för anslutning till en IoT-hubb:

- MQTT
- AMQP
- HTTPS

Information om dessa differensprotokoll och vägledning om hur du väljer ett finns i [Välj ett kommunikationsprotokoll](../../iot-hub/iot-hub-devguide-protocols.md).

Om enheten inte kan använda något av de protokoll som stöds kan du använda Azure IoT Edge för att göra protokollkonvertering. IoT Edge stöder andra intelligens-on-the-edge-scenarier för att avlasta bearbetning till kanten från Azure IoT Central-programmet.

## <a name="security"></a>Säkerhet

Alla data som utbyts mellan enheter och din Azure IoT Central är krypterad. IoT Hub autentiserar varje begäran från en enhet som ansluter till någon av de enhetsvända IoT Hub-slutpunkterna. För att undvika att byta autentiseringsuppgifter över kabeln använder en enhet signerade token för att autentisera. Mer information finns i [Kontrollera åtkomst till IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Nästa steg

Om du är enhetsutvecklare är några föreslagna nästa steg att:

- Lär dig hur du [övervakar enhetsanslutning med Azure CLI](./howto-monitor-devices-azure-cli.md)
- Lär dig hur du [definierar en ny IoT-enhetstyp i ditt Azure IoT Central-program](./howto-set-up-template.md)
- Läs om [Azure IoT Edge-enheter och Azure IoT Central](./concepts-iot-edge.md)
