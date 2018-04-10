---
title: Så här gör du med Azure – Använda olika säkerhetsmoduler för maskinvara med klient-SDK:t för enhetsetableringstjänsten i Azure
description: Så här gör du med Azure – Använda olika säkerhetsmoduler för maskinvara med klient-SDK:t för enhetsetableringstjänsten i Azure
services: iot-dps
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 03/28/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: ''
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 0d392f4a8d935cb37b6f4cfcd69826de58b33880
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk-for-c"></a>Använda olika maskinvarusäkerhetsmoduler med klient-SDK:t för enhetsetableringstjänsten för C

Den här artikeln beskriver hur du använder olika [maskinvarusäkerhetsmoduler (HSM:er)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) med Device Provisioning Services Client SDK (klient-SDK för enhetsetableringstjänster) för C. Du kan antingen använda en fysisk enhet eller en simulator. Etableringstjänsten stöder autentisering för två typer av attesteringsmetoder: X**.**509 och TPM (Trusted Platform Module).

## <a name="prerequisites"></a>Nödvändiga komponenter

Förbered din utvecklingsmiljö enligt avsnittet "Prepare the development environment" (förbereda utvecklingsmiljön) i guiden [Create and provision simulated device](./quick-create-simulated-device.md) (Skapa och etablera en simulerad enhet).

### <a name="choose-a-hardware-security-module"></a>Välja en maskinvarusäkerhetsmodul

Som enhetens tillverkare måste du först välja maskinvarusäkerhetsmoduler (eller HSM:er) som baseras på någon av typerna som stöds. För närvarande erbjuder [klient-SDK:t för enhetsetableringstjänsten för C](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) stöd för följande HSM:er: 

- [Trusted Platform Module (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): TPM är en etablerad standard för de flesta plattformar för Windows-baserade enheter, samt några Linux-/Ubuntu-baserade enheter. Som enhetens tillverkare kan du välja denna HSM om du kör något av dessa operativsystem på dina enheter och letar efter en etablerad standard för HSM:er. Med TPM-kretsar kan du endast registrera varje enhet individuellt till enhetsetableringstjänsten. I utvecklingssyfte kan du använda TPM-simulatorn på din Windows- eller Linux-utvecklingsdator.

- [X.509](https://cryptography.io/en/latest/x509/): X.509-baserade HSM:er är relativt nyare kretsar. Arbetet fortlöper också inom Microsoft, på RIoT- eller DICE-kretsar, som implementerar X.509-certifikaten. Med X.509-kretsar kan du göra massregistrering av enheter i portalen. De stöder även vissa icke-Windows-operativsystem som embedOS. I utvecklingssyfte stöder klient-SDK:er för enhetsetableringstjänster en X.509-enhetssimulator. 

Läs mer i informationen om [säkerhetsbegrepp för IoT Hub Device Provisioning-tjänsten](concepts-security.md). 

## <a name="enable-authentication-for-supported-hsms"></a>Aktivera autentisering för HSM:er som stöds

Autentiseringsläge (X**.**509 eller TPM) måste aktiveras för den fysiska enheten eller simulatorn innan den kan registreras i Azure Portal. Navigera först till rotmappen för azure-iot-sdk-c. Kör sedan det angivna kommandot beroende på vilket autentiseringsläge du väljer:

### <a name="use-x509-with-simulator"></a>Använd X**.**509 med simulator

Etableringstjänsten levereras med en DICE-emulator (Device Identity Composition Engine) som genererar ett X**.**509-certifikat för autentisering av enheten. Kör följande kommando för att aktivera X**.**509-autentisering: 

```
cmake -Ddps_auth_type=x509 ..
```

Information om maskinvara med DICE finns [här](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Använd X**.**509 med maskinvara

Etableringstjänsten kan användas med X**.**509 på annan maskinvara. Det behövs ett gränssnitt mellan maskinvara och SDK:t för att upprätta anslutningen. Prata med tillverkaren av din maskinvarusäkerhetsmodul för mer information om gränssnittet.

### <a name="use-tpm"></a>Använd TPM

Etableringstjänsten kan ansluta till TPM-kretsar i Windows- och Linux-maskinvara med SAS-Token. Kör följande kommando för att aktivera TPM-autentisering:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Använd TPM med simulator

Om du inte har en enhet med TPM-krets, kan du använda en simulator för utveckling i Windows-operativsystemet. Kör följande kommando för att aktivera TPM-autentisering och köra TPM-simulatorn:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Bygga SDK:t
Bygga SDK:t innan du skapar enhetsregistreringen.

### <a name="linux"></a>Linux
- Bygga SDK:t i Linux:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Om du vill bygga binärfiler för felsökning lägger du till motsvarande CMake-alternativ i kommandot för projektgenerering, till exempel:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Det finns många [CMake-konfigurationsalternativ](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) för att bygga SDK:t. Du kan till exempel inaktivera en av de tillgängliga protokollstackarna genom att lägga till ett argument i CMake-kommandot för projektgenerering:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- Du kan också bygga och köra enhetstest:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Vidta följande steg för generering av projektfiler för att bygga SDK:t i Windows:
    - Öppna kommandotolken för utvecklare för VS2015
    - Kör följande CMake-kommandon från rotkatalogen för källkoden:
      ```
      cd azure-iot-sdk-c
      mkdir cmake
      cd cmake
      cmake -G "Visual Studio 14 2015" ..
      ```
    Det här kommandot bygger x86-bibliotek. Om du vill bygga för x64 ändrar du cmake generator-argumentet: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Om projektgenereringen slutförs utan fel, kan du se en Visual Studio-lösningsfil (.sln) under `cmake`-mappen. Så här bygger du SDK:t:
    - Öppna **cmake\azure_iot_sdks.sln** i Visual Studio och bygg den, **ELLER**
    - Kör följande kommando på kommandotolken du använde för att generera projektfilerna:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Använd motsvarande MSBuild-argument för att bygga binära filer för felsökning: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Det finns många CMake-konfigurationsalternativ för att bygga SDK:t. Du kan till exempel inaktivera en av de tillgängliga protokollstackarna genom att lägga till ett argument i CMake-kommandot för projektgenerering:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- Du kan också bygga och köra enhetstester:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Bibliotek att inkludera
- Dessa bibliotek ska ingå i SDK:t:
    - Etableringstjänsten: dps_http_transport dps_client, dps_security_client
    - IoTHub-säkerhet: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Skapa en post för enhetsregistrering i Device Provisioning-tjänsten

### <a name="tpm"></a>TPM
Om du använder TPM, följer du anvisningarna i ["Create and provision a simulated device using IoT Hub Device Provisioning Service"](./quick-create-simulated-device.md) (skapa och etablera en simulerad enhet med enhetsetableringstjänsten för IoT-hubb) för att skapa en post för registrering av enheten i din enhetsetableringstjänst och simulering av den första starten.

### <a name="x509"></a>X**.**509
1. Om du vill registrera en enhet i etableringstjänsten behöver du anteckna bekräftelsenyckel och registrerings-ID för varje enhet. Dessa visas i etableringsverktyget som tillhandahålls av klient-SDK:t. Kör följande kommando för att skriva ut rotcertifikatutfärdarens certifikat (för gruppregistrering) och signeringscertifikatet (för enskild registrering):
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Logga in på Azure-portalen, klicka på knappen **Alla resurser** i den vänstra menyn och öppna DPS-tjänsten.
   - Enskild X**.**509-registrering: välj **Hantera registreringar** på etableringstjänstens sammanfattningsblad. Välj fliken **Individual Enrollments** (Enskilda registreringar) och klicka på knappen **Lägg till** längst upp. Välj **X**.**509** som *mekanism* för styrkande av identitet och överför det signeringscertifikat som krävs av bladet. Klicka på knappen **Spara** när det är klart. 
   - X**.**509-gruppregistrering: välj **Hantera registreringar** på etableringstjänstens sammanfattningsblad. Välj fliken **Group Enrollments** (gruppregistreringar) och klicka på knappen **Lägg till** längst upp. Välj **X**.**509** som *mekanism* för styrkande av identitet, ange ett gruppnamn och ett certifikatnamn, ladda upp det certifikat för rotcertifikatutfärdaren som krävs av bladet. Klicka på knappen **Spara** när det är klart. 

## <a name="enable-authentication-for-custom-tpm-and-x509-devices-optional"></a>Aktivera autentisering för anpassade TPM- och X.509-enheter (valfritt)

> [!NOTE]
> Det här avsnittet gäller endast enheter som kräver stöd för en anpassad plattform eller HSM, som för närvarande inte stöds av klient-SDK för enhetsetableringstjänster för C.

Först måste du utveckla din egen HSM-databas och -bibliotek:

1. Utveckla ett bibliotek för att få åtkomst till HSM. Det här projektet måste skapa ett statiskt bibliotek som ska användas av enhetsetableringens SDK.

2. Implementera funktionerna som definieras i följande huvudfil i ditt bibliotek: 

    - För en anpassad TPM: implementera de anpassade HSM-funktionerna under [HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api).  
    - För en anpassad X.509: implementera de anpassade HSM-funktionerna under [HSM X509 API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api). 

När biblioteket skapas på egen hand måste du integrera det med klient-SDK för enhetsetableringstjänster genom att länka till ditt bibliotek. :

1. Ange den anpassade HSM GitHub-lagringsplatsen, bibliotekets sökväg och dess namn i följande `cmake`-kommando:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Öppna Visual Studio-lösningsfilen som skapats av CMake (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`) och skapa den. 

    - Skapandeprocessen kompilerar SDK-biblioteket.
    - SDK:n försöker länka mot anpassade HSM:er enligt definitionen i kommandot `cmake`.

3. Kör exempelappen ”prov_dev_client_ll_sample” under ”Provision_Samples” (under `\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample`) för att verifiera att din HSM har implementerats korrekt.

## <a name="connecting-to-iot-hub-after-provisioning"></a>Ansluta till IoT Hub efter etablering

När enheten har etablerats med etableringstjänsten använder det här API:t HSM-autentiseringsläget för anslutning med IoT Hub: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```

