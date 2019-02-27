---
title: Enhetsanslutning i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp som rör enhetsanslutning i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: edffc6677609537d8a07aeae45a57c5e88449099
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882672"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Enhetsanslutning i Azure IoT Central

Den här artikeln beskriver viktiga begrepp som är relaterade till enhetsanslutning i Microsoft Azure IoT Central.

Azure IoT Central använder [Azure IoT Hub Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) att registrera och ansluta enheter i stor skala.

Med Device Provisioning-tjänsten:

- Kunder kan nu skapa enhetens autentiseringsuppgifter och konfigurera enheter offline utan att först registrera enheter i Azure IoT Central.
- Azure IoT Central stöder enhetsanslutning med X.509-certifikat, samtidigt som du fortsätter att stödja och förbättra anslutningsmöjligheter med signaturer för delad åtkomst.
- Azure IoT Central-kunder kan nu använda sina egna enhets-ID för att registrera enheter i Azure IoT Central, vilket möjliggör enkel integrering med befintliga BackOffice-system.
- Det finns ett konsekvent sätt att ansluta enheter till Azure IoT Central.

>[!NOTE]
>Azure IoT Central använder IoT Hub Device Provisioning-tjänsten för alla enhetsregistrering och anslutning. [Läs mer](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

Baserat på ditt användningsfall, följ instruktionerna för att ansluta enheter till Azure IoT Central:

- [Ansluta en enskild enhet snabbt (genom att använda signaturer för delad åtkomst)](#connect-a-single-device)
- [Anslut enheter i stor skala genom att använda signaturer för delad åtkomst](#connect-devices-at-scale-using-shared-access-signatures)
- [Anslut enheter i stor skala genom att använda X.509-certifikat](#connect-devices-using-x509-certificates) (rekommenderas för produktionsarbetsbelastningar)
- [Ansluta utan att första registrera enheter](#connect-without-first-registering-devices) 

>[!NOTE]
>Global slutpunkt för enheter för att ansluta och etablera är **global.azure-enheter – provisioning.net**.

## <a name="connect-a-single-device"></a>Ansluta en enskild enhet

Ansluta en enskild enhet till Azure IoT Central genom att använda signaturer för delad åtkomst:

1. Lägg till en **riktig enhet** från **Device Explorer**. Välj **+ ny** > **verkliga** att lägga till en riktig enhet.
    - Ange den **enhets-ID** (bör vara gemener) eller använda föreslagna enhets-ID.
    - Ange den **enhetsnamn** eller använda det föreslagna namnet.

    ![Lägg till enhet](media/concepts-connectivity/add-device.png)

1. Hämta anslutningsinformationen, som den **Scopeid**, **enhets-ID**, och **primärnyckel**, för tillagd enhet genom att välja **Connect** på den enhetens sida.

    - **[Scope-ID](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)**  per Azure IoT Central-app och genereras av Device Provisioning-tjänsten. Detta ID används för att se till att ett unikt enhets-ID i en app.
    - **Enhets-ID** är unikt enhets-ID per app. Enheten behöver skicka enhets-ID som en del av anropet för registrering.
    - **Primär nyckel** är en signaturtoken för delad åtkomst genereras av Azure IoT Central för den här specifika enheten.
 
    ![Anslutningsinformation](media/concepts-connectivity/device-connect.png)

1. Använd anslutningsinformationen **enhetsidentitet**, **enhetsnamn**, och enhetens **primärnyckel** i din kod för enheten att etablera och ansluta din enhet och börja se den data flödar genom omedelbart. Om du använder en MXChip IoT DevKit (DevKit)-enhet följer [de stegvisa anvisningarna](howto-connect-devkit-experimental.md#add-a-real-device)med start från avsnittet ”Förbereda enheten DevKit”.

    Här följer referenser för andra språk som du kanske vill använda.

    - **Språk som C:** Följ [C exempel enhetsklienten](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) att ansluta en exempel-enhet. Använd följande inställningar i det här exemplet:

         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```

    - **Node.js:**  Följ [de stegvisa anvisningarna](tutorial-add-device-experimental.md#prepare-the-client-code)med start från avsnittet ”Förbered klientkoden”.

## <a name="connect-devices-at-scale-by-using-shared-access-signatures"></a>Anslut enheter i stor skala genom att använda signaturer för delad åtkomst

För att ansluta enheter i stor skala med Azure IoT Central genom att använda signaturer för delad åtkomst, finns det två övergripande stegen:

1. **Enhetsregistrering:** Registrera enheter genom att importera dem till Azure IoT Central via en CSV-fil. Använd sedan den **exportera** åtgärder för att exportera dina enheter och få deras enhet anslutningsinformation.
1. **Enhetsinställningar:** Programmera enheter med anslutningsinformationen (**Scopeid**, **enhets-ID**, och **primärnyckel**). Varje enhet slås på, anropar Device Provisioning-tjänsten för att få dess anslutningsinformationen eller Azure IoT Central apptilldelning.

>[!NOTE]
>Ett avancerat alternativ är också tillgängliga där du kan ansluta enheter utan att först registrera enheter i Azure IoT Central. [Läs mer](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

### <a name="device-registration"></a>Enhetsregistrering

Om du vill ansluta ett stort antal enheter i ditt program stöder Azure IoT Central massimport av enheter via en CSV-fil. 

1. Importera enheter att registrera dem i ditt program:

   1. Välj **Device Explorer** på den vänstra menyn.
   1. Välj mallen enhet på den vänstra panelen Skapa enheterna som du vill massregistrera. 
   1. Välj **Import**, och välj sedan en CSV-fil med listan över enhets-ID som ska importeras. CSV-filen ska ha följande kolumner (och rubriker):

       - IOTC_DeviceID (bör vara gemener)
       - IOTC_DeviceName (valfritt)

   1. När importen är klar visas ett meddelande på enheten rutnätet.

1. Exportera enheter för att få sina anslutningsinformation:

   Den **exportera** åtgärden skapar en CSV-fil med enhets-ID, namnet på enheten och enhetsnycklar. Använd följande information för att ansluta enheten till Azure IoT Central. Att bulk-export enheter från ditt program:

   1. Välj **Device Explorer** på den vänstra menyn.
   1. Markera de enheter som du vill exportera och välj sedan den **exportera** åtgärd.
   1. När exporten är klar visas ett meddelande tillsammans med en länk till ladda ned den genererade filen.
   1. Klicka på meddelande att ladda ned filen till en lokal mapp på disken.
   1. Den exporterade CSV-filen innehåller följande kolumner som innehåller anslutningsinformationen **enhets-ID**, **enhetsnamn**, **enheten primära eller sekundära nyckeln**, och  **Primär eller sekundär certifikattumavtryck**:

       - IOTC_DEVICEID
       - IOTC_DEVICENAME
       - IOTC_SASKEY_PRIMARY
       - IOTC_SASKEY_SECONDARY
       - IOTC_X509THUMBPRINT_PRIMARY
       - IOTC_X509THUMBPRINT_SECONDARY

### <a name="device-setup"></a>Enhetskonfiguration

 Etablera och ansluta din enhet genom att använda anslutningsinformationen **enhetens identitet (IOTC_DEVICEID)**, **enheten primär nyckel (IOTC_SASKEY_PRIMARY)**, och **Scopeid** i din kod för enheten. Om du inte redan gjort det, får den **Scopeid** från din Azure IoT Central-app genom att välja **Administration** > **enhetsanslutning**  >  **Scope-ID**.

Om du ansluter en DevKit enhet följer [de stegvisa anvisningarna](howto-connect-devkit-experimental.md#add-a-real-device)med start från avsnittet ”Förbereda enheten DevKit”.

Här följer referenser för andra språk som du kanske vill använda.

- **Språk som C:** Följ [C exempel enhetsklienten](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) att ansluta en exempel-enhet. Använd följande inställningar i det här exemplet:

     ```c
     hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

     ## Enter the Device Id and Symmetric keys 
     prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
    ```

- **Node.js:** Följ [de stegvisa anvisningarna](tutorial-add-device-experimental.md#prepare-the-client-code)med start från avsnittet ”Förbered klientkoden”.

## <a name="connect-devices-by-using-x509-certificates"></a>Anslut enheter med hjälp av X.509-certifikat

Med X.509-certifikat som en attesteringsmetod är ett utmärkt sätt att skala produktion och förenkla enhetsetablering. X.509-certifikat som är normalt ordnade i en certifikatkedja med förtroenden, där varje certifikat i kedjan är signerat av den privata nyckeln för nästa högre certifikat och så vidare, slutar med ett självsignerat rotcertifikat. Den här strukturen upprättar en delegerad certifikatkedja från rotcertifikatet som genereras av en betrodd rot-certifikatutfärdare (CA) ned via varje mellanliggande Certifikatutfärdare till slutanvändare ”löv” certifikatet som är installerad på en enhet. Mer information finns i [autentisering med X.509 CA-certifikat](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

För att ansluta enheter till Azure IoT Central genom att använda X.509-certifikat, finns det tre viktiga steg som ingår:
 
1. **Konfigurera anslutningsinställningarna** i Azure IoT Central appen genom att lägga till eller verifiera X.509 rot- eller mellanliggande certifikat som används för att generera enhetscertifikat. Det finns tre steg för att konfigurera anslutningsinställningar för X.509-certifikat:  

    - **Lägg till X.509 rot- eller mellanliggande certifikat** att du använder för att generera enhetscertifikat lövmedlemmar. Gå till **Administration** > **enhetsanslutning** > **certifikat**. 
    
       ![Inställningar för anslutning](media/concepts-connectivity/connection-settings.png)

    - **Verifiera certifikatet.** Verifiera att du äger certifikat säkerställer att överföring för certifikatet har certifikatets privata nyckel. För att verifiera certifikatet:

        - Generera verifieringskoden. Välj knappen bredvid den **Verifieringskod** fält för att generera den här koden. 
        - Skapa ett X.509-certifikat för verifiering med verifieringskoden. Spara certifikatet som en .cer-fil. 
        - Ladda upp signerad verifieringscertifikatet och välj **Kontrollera**.

        ![Inställningar för anslutning](media/concepts-connectivity/verify-cert.png)

    - **Lägg till ett sekundärt certifikat.** Under livscykeln för din IoT-lösning måste du distribuera certifikat. Två av de huvudsakliga skälen för att rulla certifikat är säkerhetsintrång och förfallodatum för certifikat. Sekundära minska stopptiden för enheter som försöker att etablera när du uppdaterar primära certifikatet.

      **Endast avsett för testning:**
    
      Här följer vissa verktyget kommandoradsverktyg som du kan använda för att generera CA-certifikat och enhetscertifikat.

      - Om du använder en DevKit-enhet, här är en [kommandoradsverktyget](https://aka.ms/iotcentral-docs-dicetool) att generera CA-certifikat. Lägga till den i din Azure IoT Central-app och kontrollera certifikaten. 

      - Använd [det här kommandoradsverktyget](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) till:

          - Skapa certifikatkedjan (Följ steg 2 i GitHub-dokument). Spara certifikat som CER-filer och överföra dem till Azure IoT Central (som primär certifikat).
          - Få verifieringskoden från Azure IoT Central-app, generera certifikatet (Följ steg 3 i GitHub-docs) och överför certifikat för att kontrollera att den. 
          - Skapa lövmedlemmar certifikat med enhets-ID som en parameter för verktyget (Följ steg 4). Spara certifikatet och använda dem på din enhet.

1. **Registrera enheter** genom att importera dem till Azure IoT Central via en CSV-fil.

1. **Enhetsinställningar**: Generera löv-certifikat med hjälp av överförda rotcertifikatet. Kontrollera att du använder enhets-ID som CNAME-post i löv-certifikat och se till att den är i gemener. Här är en [kommandoradsverktyget](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) att generera löv/enheten certifikat för **testningssyfte endast**.

1. **Programmera enheten med tjänsten Etableringsinformation**, att få dess anslutningsinformationen och dess Azure IoT Central apptilldelning när den är påslagen.

    Mer information finns i dessa artiklar:

    - [Exempel på implementering för Raspberry Pi](https://aka.ms/iotcentral-docs-Raspi-releases)  

    - [Exemplet enhetsklienten i C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-first-registering-devices"></a>Ansluta utan att första registrera enheter

En av de scenarier som stöd för Azure IoT Central är för OEM-tillverkare lagringsenheter-tillverkning enheter, generera autentiseringsuppgifter och konfigurera dem i fabriken utan att först registrera enheter i Azure IoT Central. När enheterna är aktiverade och de försöker ansluta till Azure IoT Central, godkänner operatorn enheterna ska ansluta till Azure IoT Central-app.

Här är flödet för att ansluta enheter med den här funktionen:

![Inställningar för anslutning](media/concepts-connectivity/device-connection-flow.png)

Följ stegen baserat på ditt val av enhetsautentisering schema (X.509-certifikat eller signaturer för delad åtkomst):

1. **Konfigurera eller hämta anslutningsinställningarna:**

    - **X.509-certifikat:** [Lägga till och verifiera rot- eller mellanliggande certifikat](#connect-devices-using-x509-certificates) och använda den för att generera enhetscertifikat i nästa steg.
    - **Signaturer för delad åtkomst:** Kopiera den primära nyckeln (den här nyckeln är gruppnyckel för delad åtkomst som signaturen för det här Azure IoT Central programmet) och använda den för att generera signatur för delad enhet åtkomstnycklar i nästa steg.

       ![Anslutningsinställningar för signaturer för delad åtkomst](media/concepts-connectivity/connection-settings-sas.png)

1. **Skapa autentiseringsuppgifter för enheten:**

    - **X.509-certifikat:** Generera löv-certifikat för dina enheter med hjälp av rot- eller mellanliggande certifikat som du har lagt till den här appen. Kontrollera att du använder enhets-ID som en CNAME-post i löv-certifikat och se till att den är gemen. Här är en [kommandoradsverktyget](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) att generera löv/enheten certifikat för testning.
    - **Signaturer för delad åtkomst:** Använd det här alternativet för att generera signatur för enhet som delas åtkomstnycklar [kommandoradsverktyget](https://www.npmjs.com/package/dps-keygen). Använd signaturnyckel för primära delad åtkomst (signaturnyckeln för gruppen delad åtkomst) från föregående steg. Kontrollera att enheten ID: T är i gemener.

        Använd följande kommando för att hämta enhetens anslutningssträng: 

        ```
        npm i -g dps-keygen
        ```
    
        Använd följande kommando för att generera en signaturnyckel för delad åtkomst av enheten:
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Ställ in enheterna:** Flash varje enhet med den **Scopeid**, **enhets-ID**, och **certifikat/SAS-nyckel för enheten**, och sedan aktivera att enheten ansluter till Azure IoT Central-app.

1. **Anslut enheter till Azure IoT Central:** När enheterna är påslaget ansluta de till Device Provisioning Service/Azure IoT Central för registrering.

1. **Associera enheterna till en mall:** Anslutna enheter som visas **icke associerade enheter** i **Device Explorer**. Enheten Etableringsstatus är **registrerad**. **Associera** enheterna till lämplig enhet mallen och godkänna enheterna ska ansluta till Azure IoT Central-app. Enheter att hämta anslutningsinformationen för Azure IoT Central-app, och sedan de ansluta och börja skicka data. Enhetsetablering är nu klar och **Etableringsstatus** för enheter övergår i **etablerad**.

## <a name="device-provisioning-status"></a>Etableringsstatus för enhet

När en riktig enhet är ansluten till Azure IoT Central, genomförs följande steg:

1. **Registrerad**: Enheten registreras först, vilket innebär att enheten har skapats i Azure IoT Central och den har enhets-ID för enheten. En enhet registreras när:

    * En ny riktig enhet läggs till i **Device Explorer**
    * En uppsättning enheter har lagts till med hjälp av **Import** i **Device Explorer**
    * En enhet som inte har registrerats ansluter med giltiga autentiseringsuppgifter och som visas under **icke associerade enheter**

    I samtliga fall ovan, den **Etableringsstatus** är **registrerad**.

1. **Etablerade**: När enheten ansluter med giltiga autentiseringsuppgifter, slutförs Azure IoT Central steget etablering (genom att skapa enheten i IoT Hub). Azure IoT Central returnerar sedan anslutningssträngen till enheten så att den kan ansluta och börja skicka data. Enhetens **Etableringsstatus** ändras från **registrerad** till **etablerad**.

1. **Blockerad**: Operatören kan blockera en enhet. När en enhet har blockerats visas det går inte att skicka data till Azure IoT Central och den måste återställas. Enheter som hindras har den **Etableringsstatus** av **blockerad**. Operatören kan också låsa upp enheten. När den har avblockerad, enhetens **Etableringsstatus** återgår till dess tidigare status (**registrerad** eller **etablerad**). 

## <a name="get-the-device-connection-string"></a>Hämta enhetens anslutningssträng

Du kan hämta anslutningssträngen för Iot Hub-enhet på Azure IoT Hub med hjälp av följande steg:

1. Hämta anslutningsinformationen för enhetens, som den **Scope-ID**, **enhets-ID**, och **primärnyckel** från den **enhetsanslutning** sidan. Dessa uppgifter, gå till den **enhet** och välj **Connect**. 

    ![Anslutningsinformation](media/concepts-connectivity/device-connect.png)

1. Hämta enhetens anslutningssträng med hjälp av kommandoradsverktyget dps-keygen. Använd följande kommando för att hämta enhetens anslutningssträng:  

    ```cmd/sh
    npm i -g dps-keygen
    ```

    Om du vill skapa en anslutningssträng, hitta den binära filen under den *bin /* mapp:

    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```

    [Mer information om dps-keygen-verktyget](https://www.npmjs.com/package/dps-keygen).

## <a name="sdk-support"></a>SDK-support

Azure IoT SDK: er erbjuder det enklaste sättet att använda koden på enheter som ansluter till ditt Azure IoT Central program. Följande SDK: er är tillgängliga:

- [Azure IoT SDK för C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK för Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK för Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK för Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK för .NET](https://github.com/azure/azure-iot-sdk-csharp)

Varje enhet ansluter med en unik anslutningssträng som identifierar enheten. En enhet kan endast ansluta till IoT hub där den är registrerad. När du skapar en riktig enhet i Azure IoT Central programmet skapar en anslutningssträng som du kan använda programmet.

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funktioner och IoT Hub-anslutning

All kommunikation mellan enheten med IoT Hub använder följande anslutningsalternativ för IoT Hub:

- [Enhet-till-moln-meddelanden](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Enhetstvillingar](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

I följande tabell sammanfattas hur Azure IoT Central enhetsfunktioner mappas till IoT Hub funktioner:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mätning: Telemetri | Enhet-till-moln-meddelanden |
| Enhetsegenskaper | Enhetstvillingen rapporterade egenskaper |
| Inställningar | Enhetstvillingen önskad och rapporterade egenskaper |

Om du vill veta mer om hur du använder Azure IoT SDK: er, finns i följande artiklar för exempelkoden:

- [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs-experimental.md)
- [Ansluta en Raspberry Pi-enhet till Azure IoT Central programmet](howto-connect-raspberry-pi-python.md)
- [En ansluts MXChip IoT DevKit till programmet Azure IoT Central](howto-connect-devkit-experimental.md)


## <a name="protocols"></a>Protokoll

Azure IoT SDK stöder följande nätverksprotokoll för att ansluta till en IoT-hubb:

- MQTT
- AMQP
- HTTPS

Läs om hur dessa protokoll och vägledning om hur du väljer en [välja ett kommunikationsprotokoll](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Om enheten inte kan använda någon av protokoll som stöds, kan du använda Azure IoT Edge till protokoll konvertering. IoT Edge stöder andra intelligence-på-the-edge-scenarier för att avlasta bearbetning till gränsen från programmet Azure IoT Central.

## <a name="security"></a>Säkerhet

Alla data som utbyts mellan enheter och Azure IoT Central appen krypteras. IoT Hub autentiserar varje begäran från en enhet som ansluter till någon av IoT Hub device-slutpunkter. För att undvika att byta ut autentiseringsuppgifter via kabel kan använder en enhet signera token för autentisering. Mer information finns i [Control access to IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security) (Kontrollera åtkomsten till IoT Hub).

## <a name="next-steps"></a>Nästa steg

- [Förbereda och ansluta MXChip IoT DevKit enheter](howto-connect-devkit-experimental.md)
- [Förbereda och ansluta en Raspberry Pi-enhet](howto-connect-raspberry-pi-python.md)
- [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs-experimental.md)
