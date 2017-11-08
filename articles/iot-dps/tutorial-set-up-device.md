---
title: "Konfigurera enheten för Azure IoT-hubb enheten Etableringstjänsten | Microsoft Docs"
description: "Konfigurera enheten för att etablera via IoT Hub-enhet etablering Service under enheten tillverkar process"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 7031409aa63f5d64d5bb7a1b9dcac50a97718630
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Konfigurera en enhet för att etablera med hjälp av Azure IoT-hubb enheten Etableringstjänsten

Du lärt dig hur du ställer in Azure IoT-hubb Device etablering Service att automatiskt etablera dina enheter till din IoT-hubb i föregående självstudierna. Den här självstudiekursen innehåller anvisningar för att ställa in enheten under produktionsprocessen, så att du kan konfigurera enheten Etableringstjänsten för enheten baserat på dess [maskinvarusäkerhetsmodul (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security), och enheten kan ansluta till tjänsten Enhetsetableringen när den startar för första gången. Den här självstudiekursen beskrivs processer till:

> [!div class="checklist"]
> * Välj en maskinvarusäkerhetsmodul
> * Skapa enheten etablering klient-SDK för valda HSM
> * Extrahera artefakter för säkerhet
> * Ställ in konfigurationen för etablering av tjänst på enheten

## <a name="prerequisites"></a>Krav

Innan du fortsätter, skapa din enhet etablering tjänstinstansen och en IoT-hubb med instruktioner som nämns i kursen [konfigurera molnet för enhetsetableringen](./tutorial-set-up-cloud.md).


## <a name="select-a-hardware-security-module"></a>Välj en maskinvarusäkerhetsmodul

Den [enhet Etableringstjänsten klient-SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) har stöd för två typer av Maskinvarusäkerhetsmoduler (eller HSM: er): 

- [Trusted Platform Module (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module).
    - TPM är en etablerad standard för de flesta plattformar för Windows-baserade enheter, samt några Linux/Ubuntu-baserade enheter. Du kan välja denna HSM om du har något av dessa operativsystem som körs på dina enheter, och om du söker efter en etablerad standard för HSM som en enhetstillverkare. Du kan bara registrera varje enhet individuellt för att enheten Etableringstjänsten med TPM-chip. För utveckling, kan du använda TPM-simulatorn på utvecklingsdatorn Windows eller Linux.

- [X.509](https://cryptography.io/en/latest/x509/) baserat maskinvarusäkerhetsmoduler. 
    - X.509 baserat HSM är relativt nyare chip med arbetet som för närvarande framsteg inom Microsoft i RIoT eller RAPPORTANVÄNDARNA kretsar som implementerar X.509-certifikat. Du kan göra med X.509-kretsar massregistrering i portalen. Det stöder också vissa icke - Windows operativsystem som embedOS. För utveckling ändamål stöder enheten Etableringstjänsten klient-SDK ett X.509 enheten simulatorn. 

Du måste välja maskinvara säkerhet moduler/modulerna som är baserade på någon av de föregående typerna som en enhetstillverkare. Andra typer av HSM: er stöds inte för närvarande i enheten Etableringstjänsten klient-SDK.   


## <a name="build-device-provisioning-client-sdk-for-the-selected-hsm"></a>Skapa enheten etablering klient-SDK för valda HSM

Enheten etablering tjänsten Client SDK kan implementera valda säkerhetsmekanism i programvaran. Följande steg visar hur du använder SDK för de valda TPM-chip HSM:

1. Om du har följt den [Snabbstart för att skapa simulerade enheten](./quick-create-simulated-device.md), du har konfigurerat redo att skapa SDK. Annars följer du de första fyra steg från avsnittet [förbereda utvecklingsmiljön](./quick-create-simulated-device.md#setupdevbox). De här stegen klona GitHub-lagringsplatsen för enheten etablering tjänsten Client SDK samt installera den `cmake` skapa verktyget. 

1. Skapa SDK för typ av HSM som du har valt för din enhet med hjälp av någon av följande kommandon i Kommandotolken:
    - För TPM-enheter:
        ```cmd/sh
        cmake -Ddps_auth_type=tpm ..
        ```

    - För TPM simulator:
        ```cmd/sh
        cmake -Ddps_auth_type=tpm_simulator ..
        ```

    - För X.509-enheter och simulator:
        ```cmd/sh
        cmake -Ddps_auth_type=x509 ..
        ```

1. SDK stöder standard för enheter som kör Windows eller Ubuntu implementeringar för TPM och X.509 HSM: er. För dessa stöds HSM, fortsätter du till avsnittet [extrahera säkerhet artefakter](#extractsecurity) nedan. 
 
## <a name="support-custom-tpm-and-x509-devices"></a>Stöd för anpassade TPM och X.509-enheter

Enheten etablering System Client SDK tillhandahåller inte standardstöd för alla TPM och X.509-enheter som inte kör Windows eller Ubuntu. För sådana enheter måste du skriva anpassade koden för en viss HSM-chip som visas i följande steg:

### <a name="develop-your-custom-repository"></a>Utveckla anpassade databasen

1. Utveckla en GitHub-databas för att komma åt din HSM. Det här projektet måste skapa ett statiskt bibliotek för enheten etablering SDK, för att använda.
1. Biblioteket måste implementera funktionerna som definierats i följande huvudfilen: en. För anpassade TPM implementera funktioner som är definierade i `\azure-iot-sdk-c\dps_client\adapters\custom_hsm_tpm_impl.h`.
    b. För anpassade X.509 implementera funktioner som är definierade i `\azure-iot-sdk-c\dps_client\adapters\custom_hsm_x509_impl.h`. 
1. HSM-databasen måste också innehålla en `CMakeLists.txt` filen i roten för databasen som ska byggas.

### <a name="integrate-with-the-device-provisioning-service-client"></a>Integrera med enheten etablering-klienten

När biblioteket har bygger på sin egen, kan du flytta till IoThub C-SDK och hämta i databasen:

1. Ange anpassade HSM GitHub-lagringsplatsen, bibliotekssökvägen och dess namn i kommandot cmake:
    ```cmd/sh
    cmake -Ddps_auth_type=<custom_hsm> -Ddps_hsm_custom_repo=<github_repo_name> -Ddps_hsm_custom_lib=<path_and_name_of library> <PATH_TO_AZURE_IOT_SDK>
    ```
   Ersätt den `<custom_hsm>` i det här kommandot med antingen `tpm` eller `x509`. Det här kommandot skapar en markör för din anpassade HSM-databas i den `cmake` directory. Observera att anpassade HSM fortfarande ska baseras på TPM eller X.509 säkerhetsmekanismer.

1. Öppna SDK i visual studio och skapa den. 

    - Skapar klonar anpassade databasen och skapar i biblioteket.
    - SDK försöker länka mot anpassade HSM som definierats i kommandot cmake.

1. Kör den `\azure-iot-sdk-c\dps_client\samples\dps_client_sample\dps_client_sample.c` exempel för att kontrollera om din HSM har implementerats korrekt.

<a id="extractsecurity"></a>
## <a name="extract-the-security-artifacts"></a>Extrahera artefakter för säkerhet

Nästa steg är att extrahera säkerhet artefakter för HSM på enheten.

1. För en TPM-enhet behöver du ta reda på **bekräftelsenyckel** associerad från tillverkaren av TPM-chip. Du kan härleda ett unikt **registrerings-ID** för din TPM-enhet av hashning bekräftelsenyckeln. 
2. För ett X.509-enhet behöver du skaffa certifikat som utfärdats till din enhet(er) - certifikat för slutanvändare för enskilda enhetsregistrering när rotcertifikat för gruppen registreringar av enheter.

Dessa artefakter säkerhet krävs för att registrera dina enheter till tjänsten etablering av enheten. Tjänsten etablering väntar sedan någon av dessa enheter för att starta och ansluta till den vid en senare tidpunkt. Se [hantera enhetsregistreringar](how-to-manage-enrollments.md) information om hur du skapar registreringar med hjälp av dessa artefakter säkerhet. 

När enheten startas för första gången, klient-SDK interagerar med din TPM-chip att extrahera säkerhet artefakter från enheten och verifierar registrering med Enhetsetableringen-tjänsten. 


## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Ställ in konfigurationen för etablering av tjänst på enheten

Det sista steget i enheten tillverkar processen är att skriva ett program som använder enheten Etableringstjänsten klient-SDK för att registrera enheten med tjänsten. Detta SDK innehåller följande API: er för ditt program att använda:

```C
typedef void(*DPS_REGISTER_DEVICE_CALLBACK)(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* user_context); // Callback to notify user of device registration results.
DPS_CLIENT_LL_HANDLE DPS_Client_LL_Create (const char* dps_uri, const char* scope_id, DPS_TRANSPORT_PROVIDER_FUNCTION protocol, DPS_CLIENT_ON_ERROR_CALLBACK on_error_callback, void* user_ctx); // Creates the IOTHUB_DPS_LL_HANDLE to be used in subsequent calls.
void DPS_Client_LL_Destroy(DPS_CLIENT_LL_HANDLE handle); // Frees any resources created by the IoTHub Device Provisioning Service module.
DPS_RESULT DPS_LL_Register_Device(DPS_LL_HANDLE handle, DPS_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, DPS_CLIENT_REGISTER_STATUS_CALLBACK status_cb, void* status_ctx); // Registers a device that has been previously registered with Device Provisioning Service
void DPS_Client_LL_DoWork(DPS_LL_HANDLE handle); // Processes the communications with the Device Provisioning Service and calls any user callbacks that are required.
```

Kom ihåg att initiera variablerna `dps_uri` och `dps_scope_id` som anges i den [simulera första startsekvens för enheten avsnitt i den här snabbstartsguide](./quick-create-simulated-device.md#firstbootsequence), innan du använder dem. Klientregistrering Enhetsetableringen API `DPS_Client_LL_Create` ansluter till globala enheten Etableringstjänsten. Den *ID Scope* genereras av tjänsten och garanterar unikhet. Det är oföränderlig och används för att unikt identifiera registrering ID: N. Den `iothub_uri` kan IoT-hubb klientregistrering API `IoTHubClient_LL_CreateFromDeviceAuth` att ansluta till rätt IoT-hubben. 


Dessa API: er med hjälp av din enhet för att ansluta och registrera med tjänsten etablering enheten när den startas upp, hämta information om din IoT-hubb och anslut sedan till den. Filen `dps_client/samples/dps_client_sample/dps_client_sample.c` visar hur du använder dessa API: er. I allmänhet måste du skapa följande ramverket för klientregistrering:

```C
static const char* dps_uri = "global.azure-devices-provisioning.net";
static const char* dps_scope_id = "[ID scope for your provisioning service]";
...
static void register_callback(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* context)
{
    USER_DEFINED_INFO* user_info = (USER_DEFINED_INFO *)user_context;
    ...
    user_info. reg_complete = 1;
}
static void registation_status(DPS_REGISTRATION_STATUS reg_status, void* user_context)
{
}
int main()
{
    ...    
    security_device_init(); // initialize your HSM 

    DPS_CLIENT_LL_HANDLE handle = DPS_Client_LL_Create(dps_uri, dps_scope_id, dps_transport, on_dps_error_callback, &user_info); // Create your DPS client

    if (DPS_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
        do {
            // The dps_register_callback is called when registration is complete or fails
            DPS_Client_LL_DoWork(handle);
        } while (user_info.reg_complete == 0);
    }
    DPS_Client_LL_Destroy(handle); // Clean up the DPS client
    ...
    iothub_client = IoTHubClient_LL_CreateFromDeviceAuth(user_info.iothub_uri, user_info.device_id, transport); // Create your IoT hub client and connect to your hub
    ...
}
```

Du kan förfina enheten Etableringstjänsten registrering klientprogrammet använder en simulerad enhet först med hjälp av en inställningar för tjänsten. När ditt program fungerar i testmiljön måste du skapa för enheten och kopiera den körbara filen i enheten avbildningen. Starta inte enheten, men du behöver [registrera enheten med enheten Etableringstjänsten](./tutorial-provision-device-to-hub.md#enrolldevice) innan du startar enheten. Se nästa steg nedan för att se den här processen. 

## <a name="clean-up-resources"></a>Rensa resurser

Du kan nu har lagt upp tjänsterna Enhetsetableringen och IoT-hubb i portalen. Om du vill avbryta enheten etablering och/eller fördröjning med någon av dessa tjänster rekommenderar vi att de stängs av att undvika onödiga kostnader.

1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din Device Provisioning-tjänst. Klicka på **Ta bort** överst på bladet **Alla resurser**.  
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Klicka på **Ta bort** överst på bladet **Alla resurser**.  


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Välj en maskinvarusäkerhetsmodul
> * Skapa enheten etablering klient-SDK för valda HSM
> * Extrahera artefakter för säkerhet
> * Ställ in konfigurationen för etablering av tjänst på enheten

Gå vidare till nästa kursen lär dig hur du etablerar du enheten till din IoT-hubb genom att registrera till Azure IoT Hub-enhet Etableringstjänsten för automatisk etablering.

> [!div class="nextstepaction"]
> [Etablerar du enheten till din IoT-hubb](tutorial-provision-device-to-hub.md)

