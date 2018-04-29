---
title: Etablera en simulerad TPM-enhet på Azure IoT Hub med C | Microsoft Docs
description: Azure Quickstart – Skapa och etablera en simulerad TPM-enhet med C-enhets-SDK för Azure IoT Hub Device Provisioning-tjänsten
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/16/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 1d642bec50d8de2f1f9a44c33c99ba8edda040e7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Skapa och etablera en simulerad TPM-enhet med C-enhets-SDK för IoT Hub Device Provisioning-tjänsten

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Dessa steg visar hur du skapar en simulerad enhet på utvecklingsdatorn som kör Windows OS, kör Windows TPM-simulatorn som [maskinvarusäkerhetsmodul (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) för enheten och använder kodexemplet för att ansluta till denna simulerade enhet med Device Provisioning-tjänsten och IoT-hubben. 

Om du inte känner till processen för automatisk etablering, bör du också gå igenom [Begrepp inom automatisk etablering](concepts-auto-provisioning.md). Se också till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub Device Provisioning-tjänsten med Azure-portalen) innan du fortsätter. 

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön 

1. Kontrollera att du har antingen Visual Studio 2015 eller [Visual Studio 2017](https://www.visualstudio.com/vs/) installerat på datorn. Du måste ha arbetsbelastningen [”Desktop development with C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) (Skrivbordsutveckling med C++) aktiverad för installationen av Visual Studio.

2. Hämta och installera [CMake-buildsystemet](https://cmake.org/download/). Det är viktigt att Visual Studio med arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++) är installerat på datorn **före** `cmake` installationen.

3. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

4. Öppna en kommandotolk eller Git Bash. Klona GitHub-lagringsplatsen för enhetssimuleringens kodexempel:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. Skapa en mapp på den lokala kopian av denna GitHub-lagringsplats för CMake-buildprocessen. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

6. Kodexemplet använder en Windows TPM-simulator för att tillhandahålla attestering via SAS-tokenautentisering. Kör följande kommando för att skapa en version av SDK:t som är specifik för plattformen för din utvecklingsklient och din [attesteringsmetod](concepts-security.md#attestation-mechanism) (TPM-simulator). Det genererar även en Visual Studio-lösning för den simulerade enheten.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Om `cmake` inte hittar din C++-kompilerare kan du få kompileringsfel när du kör kommandot ovan. Om det händer ska du försöka köra det här kommandot i [kommandotolken i Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

7. Använd en separat kommandotolk för att navigera till GitHub-rotmappen och köra [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview)-simulatorn. Den lyssnar via en socket på portarna 2321 och 2322. Stäng inte det här kommandofönstret. Den här simulatorn måste fortsätta att köras till slutet av den här snabbstartsguiden. 

   Om du är i mappen *cmake* kör du sedan följande kommandon:

    ```cmd/sh
    cd..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

<a id="simulatetpm"></a>

## <a name="simulate-tpm-device"></a>Simulera TPM-enhet

1. Öppna den lösning som har genererats i mappen *cmake* med namnet `azure_iot_sdks.sln`, och skapa den i Visual Studio.

2. I rutan*Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Verktyg**. Högerklicka på projektet **tpm_device_provision** och markera **Set as Startup Project** (Ange som startprojekt). 

3. Kör lösningen. Utdatafönstret visar **_Registrerings-ID_** och **_Bekräftelsenyckel_** som krävs för enhetsregistrering. Anteckna dessa värden. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Skapa en post för enhetsregistrering i portalen

1. Logga in på Azure-portalen, klicka på knappen **Alla resurser** i den vänstra menyn och öppna Device Provisioning-tjänsten.

2. På sammanfattningsbladet för Device Provisioning-tjänsten väljer du **Manage enrollments** (Hantera registreringar). Välj fliken **Individual Enrollments** (Enskilda registreringar) och klicka på knappen **Lägg till** längst upp. 

3. Under posten för att **lägga till registreringslista** anger du följande information:
    - Välj **TPM** som identitet för bestyrkande *mekanism*.
    - Ange *registrerings-ID* och *bekräftelsenyckel* för din TPM-enhet.
    - Du kan även ange följande information:
        - Välj en IoT hub som är länkad till din etableringstjänst.
        - Ange ett unikt enhets-ID. Se till att undvika känsliga data när du namnger din enhet.
        - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
    - Klicka på knappen **Spara** när det är klart. 

    ![Ange information för enhetsregistrering på portalbladet](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Vid lyckad registrering visas *Registrerings-ID* för enheten i listan under fliken *Individual Enrollments* (Enskilda registreringar). 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simulera första startsekvens för enheten

1. I Azure-portalen väljer du bladet **Översikt** för Device Provisioning-tjänsten och noterar värdet för **_ID-omfång_**.

    ![Extrahera DP-slutpunktsinformation från portalbladet](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. I Visual Studio *Solution Explorer* på datorn går du till mappen **Provision (Etablera)\_Exempel**. Välj exempelprojektet som heter **prov\_dev\_client\_sample** och öppna filen **prov\_dev\_client\_sample.c**.

3. Tilldela värdet _ID Scope_ (ID-omfattning) till variabeln `id_scope`. 

    ```c
    static const char* id_scope = "[ID Scope]";
    ```

4. I funktionen **main()** i samma fil ska du kontrollera att **SECURE_DEVICE_TYPE** är inställt på TPM.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    ```

   Kommentera ut eller ta bort instruktionen `hsm_type = SECURE_DEVICE_TYPE_X509;` som finns som standard. 

5. Högerklicka på projektet **prov\_dev\_client\_sample** och välj **Set as Startup Project** (Ange som startprojekt). Kör lösningen. 

6. Lägg märke till de meddelanden som simulerar enhetsstart och anslutning till Device Provisioning-tjänsten för att hämta IoT-hubinformationen. Vid lyckad etablering av den simulerade enheten på IoT-hubben som är kopplad till etableringstjänsten visas enhets-ID på hubbens blad **IoT-enheter**. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device/hub-registration.png) 

    Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub)


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta att arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som har skapats i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Stäng fönstret för TPM-simulatorn på datorn.
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din Device Provisioning-tjänst. Öppna bladet **Hantera registreringar** för tjänsten och klicka på fliken **Enskilda registreringar**. Välj *REGISTRERINGS-ID* för enheten du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp. 
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Öppna bladet **IoT-enheter** för din hubb, välj *ENHETS-ID* för enheten du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en TPM-simulerad enhet på datorn och etablerat den på IoT-hubben med hjälp av IoT Hub Device Provisioning-tjänsten. Information om hur du registrerar TPM-enheten programmässigt får du om du fortsätter till snabbstarten för programmässig registrering av en TPM-enhet. 

> [!div class="nextstepaction"]
> [Azure snabbstart – Registrera TPM-enhet på Azure IoT Hub Device Provisioning-tjänsten](quick-enroll-device-tpm-java.md)

