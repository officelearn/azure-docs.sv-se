---
title: Enhets anslutning i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp som rör enhets anslutning i Azure IoT Central
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
ms.openlocfilehash: ddbb1c6fd705e658867c0d594981e87bc8cd6afe
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930496"
---
# <a name="get-connected-to-azure-iot-central"></a>Anslut till Azure IoT Central

*Den här artikeln gäller operatörer och enhets utvecklare.*

I den här artikeln beskrivs alternativen för att ansluta dina enheter till ett Azure IoT Central-program.

Normalt måste du registrera en enhet i programmet innan den kan ansluta. IoT Central har dock stöd för scenarier där [enheter kan ansluta utan att först registreras](#connect-without-registering-devices).

IoT Central använder [Azure IoT Hub Device Provisioning-tjänsten (DPS)](../../iot-dps/about-iot-dps.md) för att hantera anslutnings processen. En enhet ansluter först till en DPS-slutpunkt för att hämta den information som krävs för att ansluta till ditt program. Internt använder ditt IoT Central-program en IoT-hubb för att hantera enhets anslutningar. Genom att använda DPS kan du:

- IoT Central för att stödja onboarding och ansluta enheter i stor skala.
- Du genererar autentiseringsuppgifter för enheten och konfigurerar enheterna offline utan att registrera enheterna via IoT Central användar gränssnitt.
- Du kan använda dina egna enhets-ID: n för att registrera enheter i IoT Central. Genom att använda dina egna enhets-ID: er blir det enklare att integrera med befintliga Back Office-system.
- Ett enda, konsekvent sätt att ansluta enheter till IoT Central.

För att skydda kommunikationen mellan en enhet och ditt program stöder IoT Central både SAS-(Shared Access Signatures) och X. 509-certifikat. X. 509-certifikat rekommenderas i produktions miljöer.

I den här artikeln beskrivs följande användnings fall:

- [Anslut en enskild enhet med SAS](#connect-a-single-device)
- [Ansluta enheter i stor skala med SAS](#connect-devices-at-scale-using-sas)
- [Anslut enheter i skala med X. 509-certifikat](#connect-devices-using-x509-certificates) – den rekommenderade metoden för produktions miljöer.
- [Anslut enheter utan att först registrera dem](#connect-without-registering-devices)
- [Ansluta enheter som använder DPS enskilda registreringar](#individual-enrollment-based-device-connectivity)
- [Associera automatiskt en enhet med en enhets mall](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>Anslut en enskild enhet

Den här metoden är användbar när du experimenterar med IoT Central eller testning av enheter. Du kan använda enhets anslutningens SAS-nycklar från IoT Central programmet för att ansluta en enhet till ditt IoT Central-program. Kopiera _enhetens SAS-nyckel_ från anslutnings informationen för en registrerad enhet:

![SAS-nycklar för en enskild enhet](./media/concepts-get-connected/single-device-sas.png)

Mer information finns i själv studie kursen [skapa och ansluta ett Node. js-klientprogram till Azure IoT Central Application](./tutorial-connect-device-nodejs.md) .

## <a name="connect-devices-at-scale-using-sas"></a>Ansluta enheter i stor skala med SAS

Om du vill ansluta enheter till IoT Central i skala med SAS-nycklar måste du registrera och sedan konfigurera enheterna:

### <a name="register-devices-in-bulk"></a>Registrera enheter i bulk

Om du vill registrera ett stort antal enheter med IoT Central programmet använder du en CSV-fil för att [Importera enhets-ID: n och enhets namn](howto-manage-devices.md#import-devices).

Om du vill hämta anslutnings informationen för de importerade enheterna [exporterar du en CSV-fil från IoT Central-programmet](howto-manage-devices.md#export-devices). Den exporterade CSV-filen innehåller enhets-ID: n och SAS-nycklar.

### <a name="set-up-your-devices"></a>Konfigurera dina enheter

Använd anslutnings informationen från export filen i enhets koden för att göra det möjligt för enheterna att ansluta och skicka data till IoT till ditt IoT Central-program. Du behöver också DPS **-ID-omfånget** för ditt program. Du kan hitta det här värdet i **Administration > enhets anslutning**.

> [!NOTE]
> Om du vill lära dig hur du kan ansluta enheter utan att först registrera dem i IoT Central, se [Anslut utan att först registrera enheter](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Ansluta enheter med X. 509-certifikat

I en produktions miljö är användningen av X. 509-certifikat den rekommenderade mekanismen för enhets autentisering för IoT Central. Mer information finns i [enhets autentisering med X. 509 CA-certifikat](../../iot-hub/iot-hub-x509ca-overview.md).

Innan du ansluter en enhet med ett X. 509-certifikat lägger du till och verifierar ett mellanliggande eller rot-X. 509-certifikat i ditt program. Enheter måste använda löv-X. 509-certifikat som genereras från rot-eller mellanliggande certifikat.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Lägga till och verifiera ett rot-eller mellanliggande certifikat

Gå till **Administration > enhets anslutning > hantera primärt certifikat** och Lägg till X. 509-roten eller mellanliggande certifikat som du använder för att generera enhets certifikaten.

![Inställningar för anslutning](media/concepts-get-connected/manage-x509-certificate.png)

Genom att verifiera certifikatets ägarskap ser du till att den person som laddar upp certifikatet har certifikatets privata nyckel. Verifiera certifikatet:

  1. Klicka på knappen bredvid **verifierings koden** för att generera en kod.
  1. Skapa ett verifierings certifikat för X. 509 med den verifierings kod som du skapade i föregående steg. Spara certifikatet som en CER-fil.
  1. Ladda upp det signerade verifierings certifikatet och välj **Verifiera**. Certifikatet har marker ATS som **verifierat** när verifieringen lyckades.

Om du har en säkerhets överträdelse eller om ditt primära certifikat är inställt på upphör ande använder du det sekundära certifikatet för att minska stillestånds tiden. Du kan fortsätta att etablera enheter med hjälp av det sekundära certifikatet när du uppdaterar det primära certifikatet.

### <a name="register-and-connect-devices"></a>Registrera och Anslut enheter

Om du vill massredigera enheter med X. 509-certifikat, registrera först enheterna i ditt program genom att använda en CSV-fil för att [Importera enhets-ID: n och enhets namnen](howto-manage-devices.md#import-devices). Enhets-ID bör alltid vara i gemener.

Generera löv certifikat för X. 509 för dina enheter med hjälp av det överförda rot-eller mellanliggande certifikatet. Använd **enhets-ID: t** som `CNAME` värde i löv certifikaten. Enhets koden behöver **ID-** värdet för ditt program, **enhets-ID**och motsvarande enhets certifikat.

#### <a name="sample-device-code"></a>Exempel på enhets kod

Följande exempel från [Azure IoT Node. js SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js) visar hur en Node. js-enhet använder ett X. 509 löv certifikat och DPS för att registrera sig för ett IoT Central program:

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

Ett motsvarande C-exempel finns i [prov_dev_client_sample. C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c) i [Azure IoT C Provisioning Device client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md).

### <a name="for-testing-purposes-only"></a>Endast i testnings syfte

För testning kan du använda följande verktyg för att generera rot-, mellanliggande och enhets certifikat:

- [Verktyg för Azure IoT Device Provisioning-enhetens SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): en samling Node. js-verktyg som du kan använda för att generera och verifiera X. 509-certifikat och nycklar.
- Om du använder en DevKit-enhet genererar detta [kommando rads verktyg](https://aka.ms/iotcentral-docs-dicetool) ett CA-certifikat som du kan lägga till i ditt IoT Central program för att verifiera certifikaten.
- [Hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): en samling PowerShell-och bash-skript för att:
  - Skapa en certifikat kedja.
  - Spara certifikaten som CER-filer som ska överföras till ditt IoT Central-program.
  - Använd verifierings koden från IoT Central programmet för att generera verifierings certifikatet.
  - Skapa löv certifikat för dina enheter med dina enhets-ID: n som en parameter till verktyget.

## <a name="connect-without-registering-devices"></a>Anslut utan att registrera enheter

De tidigare beskrivna scenarierna kräver att du registrerar enheter i ditt program innan de kan ansluta. IoT Central aktiverar också OEM-tillverkare till Mass framställning av enheter som kan ansluta utan att först registreras. En OEM genererar lämpliga autentiseringsuppgifter för enheten och konfigurerar enheterna i fabriken. När en kund vänder sig till en enhet för första gången ansluter den till DPS, som sedan ansluter enheten automatiskt till rätt IoT Central-program. En IoT Central operatör måste godkänna enheten innan den börjar skicka data till programmet.

Flödet skiljer sig något beroende på om enheterna använder SAS-token eller X. 509-certifikat:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Anslut enheter som använder SAS-token utan registrering

1. Kopiera IoT Central programmets primära grupp primär nyckel:

    ![Primär SAS-nyckel för program grupp](media/concepts-get-connected/group-sas-keys.png)

1. Använd verktyget [DPS-keygen](https://www.npmjs.com/package/dps-keygen) för att generera SAS-nycklar för enheten. Använd gruppens primära nyckel från föregående steg. Enhets-ID: n måste vara gemener:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. OEM: en blinkar varje enhet med ett enhets-ID, en genererad SAS-nyckel för enheten och värdet för programmets **ID-omfång** .

1. När du växlar på en enhet ansluter den först till DPS för att hämta IoT Central registrerings information.

    Enheten har en enhets status **som inte är kopplad** till sidan **enheter** och som inte har tilldelats någon enhets mall. **Migrera** enheten till lämplig enhets mall på sidan **enheter** . Enhets etableringen har slutförts, enhets statusen har nu **tillhandahållits**och enheten kan börja skicka data.

    På sidan **Administration > enhets anslutning** styr alternativet för **automatiskt godkännande** om du måste godkänna enheten manuellt innan du kan börja skicka data.

    > [!NOTE]
    > Information om hur du automatiskt associerar en enhet med en enhets mall finns i [associera automatiskt en enhet med en enhets mall](#automatically-associate-with-a-device-template).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Anslut enheter som använder X. 509-certifikat utan registrering

1. [Lägg till och verifiera ett rot-eller mellanliggande X. 509-certifikat](#connect-devices-using-x509-certificates) till ditt IoT Central-program.

1. Generera löv certifikat för dina enheter med hjälp av rot-eller mellanliggande certifikat som du har lagt till i ditt IoT Central-program. Använd lägre enhets-ID: n som `CNAME` i blad certifikaten.

1. OEM: en blinkar varje enhet med ett enhets-ID, ett genererat löv-X. 509-certifikat och värdet för programmets **ID-omfång** .

1. När du växlar på en enhet ansluter den först till DPS för att hämta IoT Central registrerings information.

    Enheten har en enhets status **som inte är kopplad** till sidan **enheter** och som inte har tilldelats någon enhets mall. **Migrera** enheten till lämplig enhets mall på sidan **enheter** . Enhets etableringen har slutförts, enhets statusen har nu **tillhandahållits**och enheten kan börja skicka data.

    På sidan **Administration > enhets anslutning** styr alternativet för **automatiskt godkännande** om du måste godkänna enheten manuellt innan du kan börja skicka data.

    > [!NOTE]
    > Information om hur du automatiskt associerar en enhet med en enhets mall finns i [associera automatiskt en enhet med en enhets mall](#automatically-associate-with-a-device-template).

## <a name="individual-enrollment-based-device-connectivity"></a>Enskild registrering baserad på enhets anslutning

För kunder som ansluter enheter som var och en har sina egna autentiseringsuppgifter, använder du enskilda registreringar. En enskild registrering är en post för en enskild enhet som tillåts ansluta. Enskilda registreringar kan använda antingen X. 509-löv certifikat eller SAS-token (från en fysisk eller virtuell Trusted Platform Module) som attesterings metoder. Enhets-ID: t (även kallat registrerings-ID) i en enskild registrering är alfanumeriskt, gement och får innehålla bindestreck. Mer information finns i [DPS individuell registrering](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> När du skapar en enskild registrering för en enhet har den företräde framför standard alternativen för grupp registrering i IoT Central programmet.

### <a name="create-individual-enrollments"></a>Skapa enskilda registreringar

IoT Central stöder följande mekanismer för attestering för enskilda registreringar:

- **Attestering av symmetrisk nyckel:** Symmetrisk nyckel attestering är en enkel metod för att autentisera en enhet med DPS-instansen. Om du vill skapa en enskild registrering som använder symmetriska nycklar öppnar du sidan **enhets anslutning** , väljer **individuell registrering** som anslutnings metod och **signaturen för delad åtkomst (SAS)** som mekanismen. Ange base64-kodade primära och sekundära nycklar och spara ändringarna. Använd **ID-omfånget**, **enhets-ID**: t och antingen den primära eller sekundära nyckeln för att ansluta din enhet.

    > [!TIP]
    > För testning kan du använda **openssl** för att generera base64-kodade nycklar:`openssl rand -base64 64`

- **X. 509-certifikat:** Om du vill skapa en enskild registrering med X. 509-certifikat, öppnar du sidan **enhets anslutning** , väljer **individuell registrering** som anslutnings metod och **certifikat (X. 509)** som mekanism. Enhets certifikat som används med en enskild registrerings post har ett krav på att utfärdaren och ämnet CN anges till enhets-ID: t.

    > [!TIP]
    > För testning kan du använda [verktyg för Azure IoT Device Provisioning-enheten SDK för Node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) för att skapa ett självsignerat certifikat:`node create_test_cert.js device "mytestdevice"`

- **Trusted Platform Module (TPM) attestering:** En [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) är en typ av modul för maskin varu säkerhet. Att använda en TPM är ett av de säkraste sätten att ansluta en enhet. I den här artikeln förutsätter vi att du använder en diskret, inbyggd program vara eller integrerad TPM. Programvarubaserade TPM: er lämpar sig väl för prototyper eller testning, men de ger inte samma säkerhets nivå som diskreta, inbyggda program eller integrerade TPM: er. Använd inte programvaru-TPM: er i produktion. Om du vill skapa en enskild registrering som använder en TPM öppnar du sidan **enhets anslutning** , väljer **individuell registrering** som anslutnings metod och **TPM** som mekanism. Ange TPM-bekräftelse nyckeln och spara anslutnings informationen för enheten.

## <a name="automatically-associate-with-a-device-template"></a>Associera automatiskt med en enhets mall

En av de viktigaste funktionerna i IoT Central är möjligheten att associera enhets mallar automatiskt på enhets anslutningen. Tillsammans med autentiseringsuppgifter för enheten kan enheter skicka en **CapabilityModelId** som en del av enhetens registrerings anrop. **CapabilityModelID** är en URN som identifierar den kapacitets modell enheten implementerar. IoT Central programmet kan använda **CapabilityModelID** för att identifiera den enhets mall som ska användas och sedan automatiskt associera enheten med enhets mal len. Identifierings processen fungerar på följande sätt:

1. Om enhets mal len redan har publicerats i IoT Central-programmet är enheten kopplad till enhets mal len.
1. För förauktoriserade IoT Plug and Play-enheter hämtas enhets mal len från den offentliga lagrings platsen, om enhets mal len inte redan har publicerats i IoT Central programmet.

Följande kodfragment visar formatet för den extra nytto last som enheten måste skicka under DPS-registreringen för att automatisk Association ska fungera.

Detta är formatet för enheter som använder allmänt tillgängliga enhets-SDK: er som inte stöder IoT Plug and Play:

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

Det här är formatet för enheter som använder den offentliga för hands versionen av enhets-SDK: n som stöder IoT Plug and Play:

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Alternativet **Godkänn automatiskt** på **administrations > enhets anslutning** måste vara aktiverat för att enheter ska ansluta automatiskt, identifiera enhets mal len och börja skicka data.

## <a name="device-status-values"></a>Enhets status värden

När en riktig enhet ansluter till ditt IoT Central-program ändras enhetens status enligt följande:

1. Enhetens status **registreras**först. Den här statusen innebär att enheten skapas i IoT Central och har ett enhets-ID. En enhet registreras när:
    - En ny riktig enhet läggs till på sidan **enheter** .
    - En uppsättning enheter läggs till med hjälp av **Importera** på sidan **enheter** .

1. Enhetens status ändras till **etablerad** när enheten som anslöt till ditt IoT Central program med giltiga autentiseringsuppgifter Slutför etablerings steget. I det här steget använder enheten DPS för att automatiskt hämta en anslutnings sträng från den IoT Hub som används av ditt IoT Central-program. Enheten kan nu ansluta till IoT Central och börja skicka data.

1. En operatör kan blockera en enhet. När en enhet blockeras kan den inte skicka data till ditt IoT Central-program. Blockerade enheter har statusen **blockerad**. En operatör måste återställa enheten innan den kan återuppta sändning av data. När en operatör avblockerar en enhet återgår statusen till sitt tidigare värde, **registrerad** eller **etablerad**.

1. Om enhetens status **väntar på godkännande**innebär det att alternativet **Godkänn automatiskt** är inaktiverat. En operatör måste explicit godkänna en enhet innan den börjar skicka data. Enheter som inte är registrerade manuellt på sidan **enheter** , men som är anslutna med giltiga autentiseringsuppgifter, har enhets statusen **väntar på godkännande**. Operatörer kan godkänna dessa enheter från sidan **enheter** med knappen **Godkänn** .

1. Om enhetens status är **associerad**innebär det att enheten som ansluter till IoT Central saknar en associerad enhets mall. Den här situationen inträffar vanligt vis i följande scenarier:

    - En uppsättning enheter läggs till med hjälp av **Importera** på sidan **enheter** utan att ange enhets mal len.
    - En enhet registrerades manuellt på sidan **enheter** utan att ange enhets mal len. Enheten anslöt sedan med giltiga autentiseringsuppgifter.  

    Operatören kan koppla en enhet till en enhets mall från sidan **enheter** med knappen **migrera** .

## <a name="best-practices"></a>Metodtips

Behåll eller cachelagra inte enhets anslutnings strängen som DPS returnerar när du först ansluter enheten. Om du vill återansluta en enhet går du igenom standard enhets registrerings flödet för att få rätt anslutnings sträng för enheten. Om enheten cachelagrar anslutnings strängen, körs enhetens program vara i risken att en inaktuell anslutnings sträng används, om IoT Central uppdaterar den underliggande Azure IoT-hubb som används.

## <a name="sdk-support"></a>SDK-support

Azure-enhetens SDK: er erbjuder det enklaste sättet för dig att implementera enhets koden. Följande enhets-SDK: er är tillgängliga:

- [Azure IoT SDK för C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK för Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK för Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK för Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK för .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funktioner och IoT Hub anslutning

All enhets kommunikation med IoT Hub använder följande IoT Hub anslutnings alternativ:

- [Meddelanden från enhet till moln](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Enhets dubbla](../../iot-hub/iot-hub-devguide-device-twins.md)

I följande tabell sammanfattas hur Azure IoT Central enhets funktioner mappas till IoT Hub funktioner:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetri | Meddelanden från enhet till moln |
| Egenskap | Enhetens dubbla rapporterade egenskaper |
| Egenskap (skrivbar) | Enhetens dubbla önskade och rapporterade egenskaper |
| Kommando | Direkta metoder |

Mer information om hur du använder enhets-SDK: er finns i [ansluta en DevDiv-paket-enhet till ditt Azure IoT Central-program](howto-connect-devkit.md) till exempel kod.

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

Om du är enhets utvecklare är några förslag på nästa steg att:

- Lär dig hur du [övervakar enhets anslutningar med hjälp av Azure CLI](./howto-monitor-devices-azure-cli.md)
- Lär dig hur du [definierar en ny IoT-enhets typ i ditt Azure IoT Central-program](./howto-set-up-template.md)
- Läs om [Azure IoT Edge enheter och Azure IoT Central](./concepts-iot-edge.md)
