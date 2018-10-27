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
ms.openlocfilehash: ad77f1f0a99fbdb355163e1bc83461c8c4eb75fa
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157708"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Enhetsanslutning i Azure IoT Central

Den här artikeln beskrivs viktiga begrepp som rör enhetsanslutning i Microsoft Azure IoT Central.

Azure IoT Central använder [Azure IoT Hub Device Provisioning-tjänsten (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps), aktiveras IoT Central för onboarding och ansluta enheter i stor skala.

-   Kunder kan nu skapa enhetens autentiseringsuppgifter och konfigurera enheterna som är offline utan att först registrera enheter i IoT Central
-   IoT Central har stöd för enhetsanslutning med branschens rekommenderas X509 cert - baserade anslutningen, samtidigt som du fortsätter att stödja och förbättra anslutningsmöjligheter för signaturer för delad åtkomst (SAS)
-   IoT Central-kunder kan nu använda sina egna enhets-ID för att registrera enheter i IoT Central, aktiverar enkel integrering med befintliga BackOffice-system
-   Det finns ett konsekvent sätt att ansluta enheter till IoT Central 

>[!NOTE]
>IoT Central använder Azure IoT Device Provisioning-tjänsten (DPS) under för all enhetsregistrering och anslutning, [mer information om DPS](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

Baserat på din användning fallet Följ instruktionerna för att ansluta enheter till IoT Central
1. [Anslut en enhet (med signaturer för delad åtkomst)](#connect-a-single-device)
1. [Anslut enheter i stor skala med signaturer för delad åtkomst (SAS)](#connect-devices-at-scale-using-shared-access-signatures)
1. [Anslut enheter i stor skala med X509 certifikat](#connect-devices-using-x509-certificates) **rekommenderas för produktionsarbetsbelastningar**
1. [Ansluta utan att första registrera enheter](#connect-without-first-registering-devices) 


>[!NOTE]
>Här är global slutpunkt för enheter för att ansluta och etablera **global.azure-enheter – provisioning.net**.

## <a name="connect-a-single-device"></a>Ansluta en enskild enhet
Ansluter en enhet till IoT Central med hjälp av SAS är enkelt och tar bara några få steg 
1. Lägg till en **riktig enhet** från Device Explorer klickar du på **+ Ny > verkliga** att lägga till en riktig enhet.
    * Ange enhets-Id **<span style="color:Red">(bör vara gemener)</span>** eller använder den föreslagna enhets-Id.
    * Ange namnet på enheten eller använda det föreslagna namnet   
    ![Lägg till enhet](media\concepts-connectivity\add-device.png)
1. Hämta anslutningsinformationen som **Scope-ID, enhets-ID och primära nyckel** för tillagd enhet genom att klicka på **Connect** på enhetssidan.
    * **[Scope-ID](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)**  per IoT Central-App och genereras av DPS som används för att se till att unikt enhets-ID i en App.
    * **Enhets-Id** är unikt enhets-ID per App, enheten behöver skicka enhets-Id som en del av anropet för registrering.   
    * **Primär nyckel** är en SAS-token genereras av IoT Central för den här specifika enheten. 
    ![Anslutningsinformation](media\concepts-connectivity\device-connect.PNG)
1. Använd de här anslutningsinformationen **enhetsidentitet, enhetsnamn och den primärnyckeln för enheten** i din kod för enheten att etablera och ansluta din enhet och börja ser de data som flödar genom omedelbart. Om du använder den enhet Följ MxChip [de stegvisa anvisningarna här](howto-connect-devkit.md#add-a-real-device), starta från avsnittet **Förbered enheten DevKit**.   

    Nedan finns referenser till andra språk som du kanske vill använda.

    *   **Språk som C:** om du använder C följer [C exempel enhetsklienten](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md) att ansluta en exempel-enhet. Använd följande inställningar i det här exemplet.   

         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
         
         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";

         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```

    *   **Node.js:** om du vill använda Node.js [använda de stegvisa anvisningarna här](tutorial-add-device.md#prepare-the-client-code), starta från avsnittet **förbereda klientkoden**.



## <a name="connect-devices-at-scale-using-shared-access-signatures"></a>Anslut enheter i stor skala med signaturer för delad åtkomst

För att ansluta enheter i stor skala med IoT Central med hjälp av SAS, finns det två steg som ingår 
1. **Registrera enheter** genom att importera dem till IoT Central via en CSV-filen och exportera enheter med enhetsinformation för anslutning för att ansluta dina enheter
1. **Enhetsinställningar** enheten är programmerade med anslutningsinformationen ( **Scope-ID, enhets-ID och primära nyckel**), så att systemet kan anropa etableringstjänsten för att få dess anslutning info/IoT Central apptilldelning när det är påslaget.

>[!NOTE]
>Ett avancerat alternativ är också tillgängliga där du kan ansluta enheter utan att först registrera enheter i IoT Central [Läs mer här](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

**Registrera enheter**

Om du vill ansluta stort antal enheter i ditt program, Azure IoT Central massetablerings erbjuder-importera enheter via en CSV-fil. 

Krav för CSV-fil: The CSV-filen ska ha följande kolumner (och rubriker)
1.  IOTC_DeviceID  **<span style="color:Red">(bör vara gemener)</span>**
1.  IOTC_DeviceName (valfritt)



Importera enheter att registrera dem i ditt program
1.  Välj **Explorer** på den vänstra navigeringsmenyn.
1.  Välj enhet mallen som du vill massregistrera skapar enheterna på den vänstra panelen. 
1.  Klicka på **Import**, Välj den CSV-fil som innehåller listan över enhets-ID som ska importeras.
CSV-filen ska innehålla följande kolumner (och rubriker)
    *   IOTC_DeviceID  **<span style="color:Red">(bör vara gemener)</span>**
    *   IOTC_DeviceName (valfritt)
1.  När importen är klar visas ett meddelande på enheten rutnätet.

Exportera enheter för att hämta anslutningsinformation, Export skapar en CSV-fil med enhets-Id, enhetsnamn och enhetsnyckeln. Använd följande information för att ansluta enheten till IoT Central.
Massregistrera enheter för export från ditt program:
1.  Välj **Explorer** på den vänstra navigeringsmenyn.
1.  Markera de enheter som du vill exportera och klicka sedan på den **exportera** åtgärd.
1.  När exporten är klar visas ett meddelande tillsammans med en länk till ladda ned den genererade filen.
1.  Klicka på meddelande att ladda ned filen till en lokal mapp på disken.
1.  Den exporterade CSV-filen innehåller följande kolumner: **enhets-Id, enhetsnamn, primär/sekundär enhetsnycklar och primär/sekundär certifikattumavtryck**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY


**Enhetsinställningar**

Använd de här anslutningsinformationen **enhetens identitet (IOTC_DEVICEID), enheten primär nyckel (IOTC_SASKEY_PRIMARY) och Scope-ID** i din kod för enheten du etablerar och ansluter din enhet. Om du inte redan har den **ScopeID** från din IoT Central-App **Administration > enhetsanslutning > Scope-ID**.
Om du använder den **MxChip** enheten ansluter Följ [de stegvisa anvisningarna här](howto-connect-devkit.md#add-a-real-device), starta från avsnittet **Förbered enheten DevKit**.   

Nedan finns referenser till andra språk som du kanske vill använda.

   *   **Språk som C:** om du använder C Följ [C exempel enhetsklienten](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md) att ansluta en exempel-enhet. Använd följande inställningar i det här exemplet.   
         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";
         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```
    * **Node.js:** om du vill använda Node.js [använda de stegvisa anvisningarna här](tutorial-add-device.md#prepare-the-client-code), starta från avsnittet **förbereda klientkoden**.


## <a name="connect-devices-using-x509-certificates"></a>Anslut enheter med hjälp av X509 certifikat

Med hjälp av X.509-certifikat som en attesteringsmetod är ett utmärkt sätt att skala **produktion** och förenkla enhetsetablering. X.509-certifikat som är normalt ordnade i en certifikatkedja med förtroenden där varje certifikat i kedjan är signerat av den privata nyckeln för nästa högre certifikatet och så vidare, avslutar i ett självsignerat rotcertifikat. Därmed skapas en delegerad certifikatkedja från rotcertifikatet som genererats av en betrodd rotcertifikatutfärdare (CA) ned via varje mellanliggande Certifikatutfärdare till slutanvändare ”löv” certifikatet på enheten. Mer information finns i [autentisering med X.509 CA-certifikat](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

För att ansluta enheter till IoT Central använder X509 certifikat, det finns tre viktiga steg som ingår 
1. **Konfigurera anslutningsinställningarna** i IoT Central-app genom att lägga till/verifiera X509 rot-/ mellanliggande certifikat som används för att generera enhetscertifikat.  Det finns två steg för att konfigurera anslutningsinställningar för X509 certifikat.  

    *   **Lägg till X509 rot- eller mellanliggande certifikat** du använder för att generera enhetscertifikat lövmedlemmar. Gå till Administration > enhetsanslutning > certifikat. 
    
        ![Inställningar för anslutning](media\concepts-connectivity\connection-settings.PNG)
    *   **Verifiering av certifikat:** verifierar certifikat ägarskap säkerställer att gilgit för certifikatet är tillgång certifikatets privata nyckel. Att verifiera certifikatet
        *  Skapa Verifieringskod, klicka på knappen bredvid fältet verifiering kod för att generera verifieringskoden. 
        *  Skapa ett X.509-certifikat för verifiering med verifieringskoden spara certifikatet som en .cer-fil. 
        *  Ladda upp signerad verifieringscertifikatet och klicka på verifiera.

        ![Inställningar för anslutning](media\concepts-connectivity\verify-cert.png)
    *   **Sekundärt certifikat:** under livscykeln för din IoT-lösning, måste du distribuera certifikat. Två av de viktigaste skälen för löpande certifikat skulle vara en säkerhetsöverträdelse och förfallodatum för certifikat. Sekundärt certifikat används för att minska stopptiden för enheter som försöker etablera när du uppdaterar primära certifikatet.

    **TESTA ENDAST FÖR** 
    
    Nedan visas några commandline verktyg du kan använda för att generera CA-certifikat och certifikat på enheten.

    * Om du använder MxChip här är en [kommandoradsverktyg](http://aka.ms/iotcentral-docs-dicetool) för att generera CA-certifikat lägga till den i din IoT Central-app och verifiera certifikat. 

    *   Använd det här [kommandoradsverktyg](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) till
        * Skapa certifikatkedjan (Följ steg 2 i GitHub-dokument). 
         Spara certifikat som CER-filer och ladda upp till IoT Central (primär).   
        * Få verifieringskoden från IoT Central-App, generera certifikatet (Följ steg3 i GitHub-docs) och ladda upp för att verifiera. 
        * Skapa lövmedlemmar certifikat med din enhets-Id som en parameter för verktyget (Följ steg 4). Spara certifikatet och använda dem på din enhet.     

1. **Registrera enheter** genom att importera dem till IoT Central via en CSV-fil.

1. **Enhetsinställningar** : Generera löv-certifikat med hjälp av överförda rotcertifikatet. Kontrollera att du använder den **enhets-ID** som CNAME-post i lägsta certifikat och är i **gemen**. Här är en [kommandoradsverktyg](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) att generera löv/enheten certifikat för **TESTAR endast avsedd**.

    Programmera enheten med provisioning serviceinformation så att systemet kan hämta dess anslutningsinformation och IoT Central apptilldelning när påslaget.    

    **Ytterligare referene** 
    *   Exempel på implementering för [RaspberryPi.](http://aka.ms/iotcentral-docs-Raspi-releases)  

    *   [Exemplet enhetsklienten i C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

>[!NOTE]
>Använd den **enhets-ID** som en CNAME-post när du genererar löv-certifikat för enheter.

>[!NOTE]
>Den **enhets-ID** ska vara gemener 
 
## <a name="connect-without-first-registering-devices"></a>Ansluta utan att första registrera enheter
En av de scenarier som IoT Central kan är att generera autentiseringsuppgifter för OEM-tillverkare drivrutiner för masslagring tillverkning enheter och konfigurera dem i fabriken utan att först registrera dem i IoT Central. När enheterna är aktiverade och ansluta till IoT Central godkänner operatorn att enheten ansluter till IoT Central-app.

Nedan visas flödet för att ansluta enheter med den här funktionen

![Inställningar för anslutning](media\concepts-connectivity\device-connection-flow.PNG)


Följ stegen baserat på ditt val av enheten autentiseringsschemat (X509/SAS)

1. **Anslutningsinställningar** 
    * **X509 certifikat:** [Lägg till och verifiera rot-/ mellanliggande certifikat](#connect-devices-using-x509-certificates) och använda den för att generera enhetscertifikat i nästa steg.
    * **SAS:** kopiera den primära nyckeln (den här nyckeln är gruppen SAS-nyckeln för den här IoT Central-program) och använda den för att generera SAS-nycklar för enheten i nästa steg. 
![Anslutningsinställningar SAS](media\concepts-connectivity\connection-settings-sas.png)

1. **Skapa autentiseringsuppgifter för enhet** 
    *   **Certifikat X509:** generera löv-certifikat för dina enheter med hjälp av den rot-/ mellanliggande certifikat som du har lagt till den här appen. Kontrollera att du använder den **enhets-ID** som en CNAME-post i löv-certifikat och  **<span style="color:Red">(bör vara gemener)</span>**. Här är en [kommandoradsverktyg](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) att generera löv/enheten certifikat för testning.
    *   **SAS** enheten SAS-nycklar genereras med hjälp av detta [kommandoradsverktyget](https://www.npmjs.com/package/dps-keygen). Använd primära SAS-nyckel (grupp SAS-nyckeln) från föregående steg. Se till att enhets-ID  **<span style="color:Red">är i gemener</span>**.

        Använd den nedan anvisningar för att generera SAS-nyckel för enhet           

        ```
        npm i -g dps-keygen
        ```
    
        **Användning**
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Enhetsinställningar** 
    
     Flash-enheten med **Scope-ID, enhets-ID, enheten cert/SAS-nyckel** och sedan aktivera att enheten ansluter till IoT Central-app.

1. **Anslut enhet till IoT Central:** aktiverat när enheter ansluta till DPS/IoT Central för registrering.

1. **Kopplar enheten till en mall:** den anslutna enheten visas **olänkade enheter** i **Device Explorer**. Enheten Etableringsstatus är **registrerad**. **Associera** enheten till lämplig enhet mallen och godkänna att enheten ansluter till IoT Central-app. Enheten får anslutning information för IoT Central-app, ansluter och startar skickar data. Enheten provioning är nu klar och *Etableringsstatus* övergår i **etablerad**.

## <a name="device-provisioning-status"></a>Etablering av Enhetsstatus
Det finns ett antal steg ingår vid en riktig enhet är ansluten till Azure IoT Central 
1. **Registrerad**: enheten är den första **registrerad**, vilket innebär att enheten har skapats i IoT Central och har enhets-ID för enheten.
Enheten är Registeretd när  
    *   En ny riktig enhet läggs till på **Explorer**
    *   En uppsättning enheter läggs till med **Import** på **Explorer**
    *   En enhet som inte har registrerats men ansluter med giltiga autentiseringsuppgifter och som visas under **icke associerade** enheter. 

    I alla ovanstående fall den *Etableringsstatus* är **registrerad**

1. **Etablerade**: nästa steg är när enheten ansluter med giltiga autentiseringsuppgifter IoT Central är klar etablering steg (genom att skapa enheten i IoT Hub). Den returnerar sedan anslutningssträngen till enheten för att ansluta och börja skicka data. Enheten *Etableringsstatus* stängs nu från **registrerad** till **etablerad**.

1.  **Blockerad**: operatorn kan blockera en enhet, när en enhet blockeras den kan inte skicka data till IoT Central och måste återställas. Enheter som hindras har den *Etableringsstatus* av **blockerad**. Operatören kan också låsa upp enheten. En gång avblockerad enheten *Etableringsstatus* gå tillbaka till dess tidigare *Etableringsstatus* (registrerad eller etablerad). 

## <a name="getting-device-connection-string"></a>Hämta enhetens anslutningssträng
Du kan hämta anslutningssträngen för Iot hub-enhet på Azure IoT Hub med följande steg 
1. Hämta anslutningsinformationen som **Scope-ID, enhets-ID, enheten primärnyckel** från enhetssidan (till enhetssidan > Klicka på Anslut) 

    ![Anslutningsinformation](media\concepts-connectivity\device-connect.PNG)

1. Hämta enhetens anslutningssträng med hjälp av kommandoradsverktyget commnd nedan.
    Använd den nedan anvisningarna för att hämta enhetens anslutningssträng  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **Användning**

    För att skapa en anslutningssträng måste hitta den binära filen under bin / mapp
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    Läs mer om den [här dps-keygen-verktyget.](https://www.npmjs.com/package/dps-keygen)

## <a name="sdk-support"></a>SDK-support

SDK: er för Azure-enheter erbjudandet det enklaste sättet du implementerar kod på dina enheter som ansluter till ditt Azure IoT Central program. Följande enhet SDK: er är tillgängliga:

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
| Mått: telemetri | Enhet-till-moln-meddelanden |
| Enhetsegenskaper | Enhetstvillingen rapporterade egenskaper |
| Inställningar | Enhetstvillingen önskad och rapporterade egenskaper |

Mer information om hur du använder SDK: er för enheter finns i följande artiklar exempelkod:

- [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs.md)
- [Ansluta en Raspberry Pi-enhet till Azure IoT Central programmet](howto-connect-raspberry-pi-python.md)
- [Ansluta en DevDiv kit enhet till Azure IoT Central programmet](howto-connect-devkit.md).


## <a name="protocols"></a>Protokoll

SDK: er för enheter stöder följande nätverksprotokoll för att ansluta till en IoT-hubb:

- MQTT
- AMQP
- HTTPS

Läs om hur dessa skillnaden protokoll och vägledning om hur du väljer en [välja ett kommunikationsprotokoll](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Om enheten inte kan använda någon av protokoll som stöds, kan du använda Azure IoT Edge till protokoll konvertering. IoT Edge stöder andra intelligence-på-the-edge-scenarier för att avlasta bearbetning till gränsen från programmet Azure IoT Central.

## <a name="security"></a>Säkerhet

Krypteras alla data som utbyts mellan enheter och din Azure IoT Central. IoT Hub autentiserar varje begäran från en enhet som ansluter till någon av IoT Hub device-slutpunkter. För att undvika att byta ut autentiseringsuppgifter via kabel kan använder en enhet signera token för autentisering. Mer information finns i, [styra åtkomsten till IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Enheter som ansluter till Azure IoT Central måste för närvarande använder SAS-token. X.509-certifikat har inte stöd för enheter som ansluter till Azure IoT Central.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om enhetsanslutning i Azure IoT Central, är här nästa föreslagna steg:

- [Förbereda och ansluta en DevKit-enhet](howto-connect-devkit.md)
- [Förbereda och ansluta en Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs.md)
