---
title: Konfigurera enheten för Azure IoT Hub Device Provisioning-tjänsten
description: Konfigurera enheten för etablering via IoT Hub Device Provisioning-tjänsten under tillverkningsprocessen
author: dsk-2015
ms.author: dkshir
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1e4e93c276fe62caae17c85bf9ac92282dfdfb88
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631276"
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Konfigurera enheten för etablering med Azure IoT Hub Device Provisioning-tjänsten

I den tidigare självstudiekursen fick du lära dig att konfigurera Azure IoT Hub Device Provisioning-tjänsten för att automatiskt etablera dina enheter till din IoT-hubb. I den här kursen visar vi hur du konfigurerar din enhet under tillverkningsprocessen och gör så att den kan etableras automatiskt med IoT-hubben. Enheten etableras baserat på dess [attesteringsmetod](concepts-device.md#attestation-mechanism) vid den första starten och anslutningen till etableringstjänsten. I den här självstudiekursen beskrivs processerna för att:

> [!div class="checklist"]
> * Bygga ett plattformsspecifikt klient-SDK för enhetsetableringstjänster
> * Extrahera säkerhetsartefakterna
> * Skapa programvara för enhetsregistrering

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du fortsätter ska du skapa en enhetsetableringstjänstinstans och en IoT-hubb enligt instruktionerna i den tidigare kursen [1 – Konfigurera molnresurser](./tutorial-set-up-cloud.md).

I den här kursen används [lagringsplatsen för Azure IoT SDK:er och bibliotek för C](https://github.com/Azure/azure-iot-sdk-c), som innehåller klient-SDK:t för enhetsetableringstjänsten för C. Detta SDK har för närvarande stöd för TPM och X.509 för enheter som körs på Windows- eller Ubuntu-implementeringar. I den här kursen används en Windows-utvecklingsklient, vilket även förutsätter grundläggande kunskaper i Visual Studio 2017. 

Om du inte är bekant med processen för automatisk etablering ska du läsa avsnittet om [begrepp inom automatisk etablering](concepts-auto-provisioning.md) innan du fortsätter. 

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Skapa en plattformsspecifik version av SDK

Klient-SDK:t för enhetsetableringstjänsten hjälper dig att implementera din programvara för enhetsregistrering. Men innan du kan använda det måste du skapa en version av SDK:t som är specifik för plattformen för din utvecklingsklient och din attesteringsmetod. I den här självstudiekursen skapar du ett SDK som använder Visual Studio 2017 på en Windows-utvecklingsplattform för en typ av attestering som stöds:

1. Installera nödvändiga verktyg och klona GitHub-lagringsplatsen som innehåller klient-SDK:t för etableringstjänsten för C:

   a. Kontrollera att du har antingen Visual Studio 2015 eller [Visual Studio 2017](https://www.visualstudio.com/vs/) installerat på datorn. Du måste ha arbetsbelastningen [”Desktop development with C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) (Skrivbordsutveckling med C++) aktiverad för installationen av Visual Studio.

   b. Hämta och installera [CMake-buildsystemet](https://cmake.org/download/). Det är viktigt att Visual Studio med arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++) är installerat på datorn **före** installationen.

   c. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) för de senaste `git` verktygen, inklusive  **Git Bash**, ett Bash-kommandoradsgränssnitt för att interagera med din lokala Git-lagringsplats. 

   d. Öppna Git Bash och klona lagringsplatsen ”Azure IoT SDKs and libraries for C”. Kloningskommandot kan ta ett par minuter att slutföra eftersom det även hämtar flera beroende undermoduler:
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   e. Skapa en ny `cmake`-underkatalog i den nyligen skapade underkatalogen för lagringsplatsen:

   ```cmd/sh
   mkdir azure-iot-sdk-c/cmake
   ``` 

2. Byt till azure-iot-sdk-c-lagringsplatsens `cmake`-underkatalog från Git Bash-kommandotolken:

   ```cmd/sh
   cd azure-iot-sdk-c/cmake
   ```

3. Bygg SDK för din utvecklingsplattform och någon av de attesteringsmetoder som stöds med hjälp av något av följande kommandon (observera de två avslutande punkterna). När det är klart bygger CMake `/cmake`-underkatalogen med innehåll som är specifikt för din enhet:
    - För enheter som använder en fysisk TPM/HSM eller ett simulerat X.509-certifikat för attestering:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - För enheter som använder TPM-simulatorn för attestering:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

Nu är du redo att använda SDK för att bygga din enhetsregistreringskod. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Extrahera säkerhetsartefakterna 

Nästa steg är att extrahera säkerhetsartefakterna för den attesteringsmetod som används av din enhet. 

### <a name="physical-device"></a>Fysisk enhet 

Om du har byggt SDK för att använda attestering från en fysisk TPM/HSM:

- För en TPM-enhet måste du bestämma **bekräftelsenyckeln** som är associerad med enheten från TPM-kretstillverkaren. Du kan härleda ett unikt **registrerings-ID** för din TPM-enhet genom att hasha bekräftelsenyckeln.  

- För X.509-enheter måste du erhålla de certifikat som utfärdats till dina enheter: slutentitetscertifikat för enskilda enhetsregistreringar och rotcertifikat för gruppregistreringar av enheter. 

### <a name="simulated-device"></a>Simulerad enhet

Om du har byggt SDK för att använda attestering från ett simulerat TPM- eller X.509-certifikat:

- För en simulerad TPM-enhet:
   1. Använd en separat/ny kommandotolk för att navigera till `azure-iot-sdk-c`-underkatalogen och köra TPM-simulatorn. Den lyssnar via en socket på portarna 2321 och 2322. Stäng inte det här kommandofönstret. Den här simulatorn måste fortsätta att köras till slutet av den här snabbstarten. 

      Kör följande kommando från `azure-iot-sdk-c`-underkatalogen för att starta simulatorn:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

   2. I Visual Studio öppnar du lösningen som genererats i *cmake*-mappen med namnet `azure_iot_sdks.sln`, och bygger den med kommandot "Build solution" (Bygg lösning) på menyn "Build" (Bygg).

   3. I rutan*Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Verktyg**. Högerklicka på projektet **tpm_device_provision** och markera **Set as Startup Project** (Ange som startprojekt). 

   4. Kör lösningen med något av startkommandona på felsökningsmenyn. I utdatafönstret visas TPM-simulatorns **_registrerings-ID_** och **_bekräftelsenyckeln_** som behövs för enhetsregistrering och registrering. Kopiera dessa värden för senare bruk. Du kan stänga det här fönstret (med registrerings-ID:t och bekräftelsenyckeln), men lämna TPM-simulatorfönstret från steg 1 öppet.

- För en simulerad X.509-enhet:
  1. I Visual Studio öppnar du lösningen som genererats i *cmake*-mappen med namnet `azure_iot_sdks.sln`, och bygger den med kommandot "Build solution" (Bygg lösning) på menyn "Build" (Bygg).

  2. I rutan*Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Verktyg**. Högerklicka på projektet **dice\_device\_enrollment** (dice-enhetsregistrering) och markera **Set as Startup Project** (Ange som startprojekt). 
  
  3. Kör lösningen med något av startkommandona på felsökningsmenyn. I utdatafönstret anger du **i** för individuell registrering när du blir uppmanad till det. I utdatafönstret visas ett lokalt genererat X.509-certifikat för din simulerade enhet. Kopiera utdata till Urklipp som börjar på *-----BEGIN CERTIFICATE-----* och slutar på den första *-----END CERTIFICATE-----*, och se till att du får med båda raderna. Observera att du behöver bara det första certifikatet från fönstret Utmatning.
 
  4. Skapa en fil med namnet **_X509testcert.pem_**, öppna den i valfritt textredigeringsprogram och kopiera urklippsinnehållet till filen. Spara filen. Du kommer att använda den senare för enhetsregistrering. När din registreringsprogramvara körs använder den samma certifikat vid automatisk etablering.    

Dessa säkerhetsuppgifter krävs vid registrering av enheten till enhetsetableringstjänsten. Etableringstjänsten väntar tills enheten har startats och ansluter till den vid en senare tidpunkt. När enheten startar för första gången interagerar logiken hos klient-SDK:t med din krets (eller simulator) för att extrahera säkerhetsartefakterna från enheten och verifierar registreringen med enhetsetableringstjänsten. 

## <a name="create-the-device-registration-software"></a>Skapa programvara för enhetsregistrering

Det sista steget är att skriva ett registreringsprogram som använder klient-SDK:t för enhetsetableringstjänsten för att registrera enheten med IoT Hub-tjänsten. 

> [!NOTE]
> För det här steget förutsätter vi att en simulerad enhet används genom att du kör ett SDK-exempelregistreringsprogram från din arbetsstation. Men samma koncept gäller om du skapar ett registreringsprogram för distribution till en fysisk enhet. 

1. Välj bladet **Översikt** i Azure Portal för enhetsetableringstjänsten och kopiera värdet för **_ID-omfång_**. *ID-omfång* genereras av tjänsten och garanterar unikhet. Det är oföränderligt och används för att unikt identifiera registrerings-ID:n.

    ![Extrahera DP-slutpunktsinformation från portalbladet](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

2. I Visual Studio *Solution Explorer* på datorn går du till mappen **Provision (Etablera)\_Exempel**. Välj exempelprojektet med namnet **prov\_dev\_client\_sample** och öppna källfilen **prov\_dev\_client\_sample.c**.

3. Tilldela värdet för _ID-omfång_ som erhölls i steg 1 till variabeln `id_scope` (tar bort vänster /`[` och höger/`]` hakparentes): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    `global_prov_uri`-variabeln gör att klientregistrerings-API:t för IoT-hubb `IoTHubClient_LL_CreateFromDeviceAuth` kan ansluta till den avsedda instansen för enhetsetableringstjänsten.

4. I **main()**-funktionen i samma fil kommenterar/avkommenterar du `hsm_type`-variabeln som matchar den attesteringsmetod som används av enhetens registreringsprogramvara (TPM eller X.509): 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Spara dina ändringar och bygg om exemplet **prov\_dev\_client\_sample** genom att välja "Build solution" (Bygg lösning) på menyn"Build" (Bygg). 

6. Högerklicka på projektet **prov\_dev\_client\_sample** i mappen **Provision\_Samples** och välj **Set as Startup Project** (Ange som startprojekt). Kör INTE exempelprogrammet ännu.

> [!IMPORTANT]
> Kör/starta inte enheten ännu! Du måste slutföra processen genom att registrera enheten med enhetsetableringstjänsten innan du startar enheten. I avsnittet Nästa steg nedan vägleds du till nästa artikel.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>SDK-API:er används under registreringen (endast som referens)

Som referens tillhandahåller SDK följande API:er som du kan använda i ditt program vid registrering. Med dessa API:er kan enheten ansluta till och registrera med enhetsetableringstjänsten när den startas. Som svar får enheten den information som krävs för att etablera en anslutning till din IoT-hubbinstans:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Du kanske också behöver förfina klientregistreringsprogrammet för enhetsetableringstjänsten först med hjälp av en simulerad enhet och en testtjänstinstallation. När ditt program fungerar i testmiljön kan du bygga det för din specifika enhet och kopiera den körbara filen till din enhetsavbildning. 

## <a name="clean-up-resources"></a>Rensa resurser

I det här läget kanske enhetsetablerings- och IoT-hubbtjänsterna körs i portalen. Om du vill avbryta enhetsetableringsinstallationen och/eller vänta med att slutföra den här kursserien rekommenderar vi att du stänger av dem för att undvika onödiga kostnader.

1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din Device Provisioning-tjänst. Klicka på **Ta bort** överst på bladet **Alla resurser**.  
2. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Klicka på **Ta bort** överst på bladet **Alla resurser**.  

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Bygga ett plattformsspecifikt klient-SDK för enhetsetableringstjänsten
> * Extrahera säkerhetsartefakterna
> * Skapa programvara för enhetsregistrering

Gå vidare till nästa kurs om du vill lära dig hur du etablerar enheten i din IoT-hubb genom att registrera den till Azure IoT Hub Device Provisioning-tjänsten för automatisk etablering.

> [!div class="nextstepaction"]
> [Etablera enheten till din IoT-hubb](tutorial-provision-device-to-hub.md)

