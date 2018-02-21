---
title: "Etablera en simulerad X.509-enhet på Azure IoT Hub med Java | Microsoft Docs"
description: "Azure snabbstart – Skapa och etablera en simulerad X.509-enhet med Java-enhets-SDK för IoT Hub Device Provisioning-tjänsten"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: d966a1ce5f30531668c05e68bfe709057c6dee35
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Skapa och etablera en simulerad X.509-enhet med Java-enhets-SDK för IoT Hub Device Provisioning-tjänsten
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Dessa steg visar hur du simulerar en X.509-enhet på utvecklingsdatorn som kör Windows OS och använder kodexemplet för att ansluta till denna simulerade enhet med Device Provisioning-tjänsten och IoT-hubben. 

Se till att slutföra stegen i [Konfigurera IoT Hub Device Provisioning-tjänsten med Azure Portal](./quick-setup-auto-provision.md) innan du fortsätter.


## <a name="prepare-the-environment"></a>Förbereda miljön 

1. Kontrollera att [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) är installerat på datorn.

1. Ladda ned och installera [Maven](https://maven.apache.org/install.html).

1. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

1. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för enhetssimuleringens kodexempel:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Navigera till certifikatgeneratorprojektet och skapa projektet. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

1. Navigera till målmappen och kör den skapade jar-filen.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

1. Skapa registreringsinformationen på något av följande sätt, enligt inställningarna:

    - **Enskild registrering**:

        1. Ange **N** för _Do you want to input common name_  (Vill du ange ett eget namn). Kopiera utdata för `Client Cert` från *-----BEGIN CERTIFICATE-----* och till *-----END CERTIFICATE-----* till Urklipp.

            ![Generator för enskilt certifikat](./media/java-quick-create-simulated-device-x509/individual.png)

        1. Skapa en fil med namnet **_X509individual.pem_** på din Windows-dator, öppna den i valfritt redigeringsprogram och kopiera urklippsinnehållet till filen. Spara filen.

        1. Ange **N** för _Do you want to input Verification Code_ (Vill du ange verifieringskod) och ha programutdata öppna för senare referens i snabbstarten. Notera värdena för _Client Cert_ (Klientcertifikat) och _Client Cert Private Key_ (Privat nyckel för klientcertifikat).
    
    - **Registreringsgrupper**:

        1. Ange **N** för _Do you want to input common name_  (Vill du ange ett eget namn). Kopiera utdata för `Root Cert` från *-----BEGIN CERTIFICATE-----* och till *-----END CERTIFICATE-----* till Urklipp.

            ![Generator för gruppcertifikat](./media/java-quick-create-simulated-device-x509/group.png)

        1. Skapa en fil med namnet **_X509group.pem_** på din Windows-dator, öppna den i valfritt redigeringsprogram och kopiera urklippsinnehållet till filen. Spara filen.

        1. Ange **Y** för _Do you want to input Verification Code_ (Vill du ange verifieringskod) och ha programutdata öppna för senare referens i snabbstarten. Notera värdena för _Client Cert_ (Klientcertifikat), _Client Cert Private Key_ (Privat nyckel för klientcertifikat), _Signer Cert_ (Undertecknarcertifikat) och _Root Cert_ (Rotcertifikat).


## <a name="create-a-device-enrollment-entry"></a>Skapa en post för enhetsregistrering

1. Logga in på Azure-portalen, klicka på knappen **Alla resurser** i den vänstra menyn och öppna din distributionstjänst.

1. Ange registreringsinformationen på något av följande sätt, enligt inställningarna:

    - **Enskild registrering**: 

        1. På sammanfattningsbladet för Device Provisioning-tjänsten väljer du **Manage enrollments** (Hantera registreringar). Välj fliken **Individual Enrollments** (Enskilda registreringar) och klicka på knappen **Lägg till** längst upp. 

        1. Under posten för att **lägga till registreringslista** anger du följande information:
            - Välj **X.509** som identitet för bestyrkande *mekanism*.
            - Under filen *Certificate .pem eller .cer* väljer du certifikatfilen **_X509individual.pem_** som skapades i föregående steg med widgeten *Utforskaren*.
            - Du kan även ange följande information:
                - Välj en IoT hub som är länkad till din etableringstjänst.
                - Ange ett unikt enhets-ID. Se till att undvika känsliga data när du namnger din enhet. 
                - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
            - Klicka på knappen **Spara** när det är klart. 

        ![Ange information för X.509-enhetsregistrering på portalbladet](./media/java-quick-create-simulated-device-x509/enter-device-enrollment.png)  

       Om registreringen har lyckats visas din X.509-enhet som **microsoftriotcore** under kolumnen *Registrerings-ID* på fliken *Enskilda registreringar*. 

    - **Registreringsgrupper**: 

        1. På sammanfattningsbladet för Device Provisioning-tjänsten väljer du **Certifikat** och klickar på knappen **Lägg till** högst upp.

        1. Under **Lägg till certifikat** anger du följande information:
            - Ange ett unikt certifikatnamn.
            - Välj den **_X509group.pem_**-fil som du skapade tidigare.
            - Klicka på knappen **Spara** när det är klart.

        ![Lägg till certifikat](./media/java-quick-create-simulated-device-x509/add-certificate.png)

        1. Välj det nya certifikatet:
            - Klicka på **Skapa verifieringskod**. Kopiera den skapade koden.
            - Ange _verifieringskoden_ eller högerklicka för att klistra in i fönstret _provisioning-x509-cert-generator_ som körs.  Tryck på **RETUR**.
            - Kopiera utdata för `Verification Cert` från *-----BEGIN CERTIFICATE-----* och till *-----END CERTIFICATE-----* till Urklipp.
            
                ![Valideringsgenerator](./media/java-quick-create-simulated-device-x509/validation-generator.png)

            - Skapa en fil med namnet **_X509validation.pem_** på din Windows-dator, öppna den i valfritt redigeringsprogram och kopiera urklippsinnehållet till filen. Spara filen.
            - Välj **_X509validation.pem_**-filen i Azure Portal. Klicka på **Verifiera**.

            ![Validera certifikat](./media/java-quick-create-simulated-device-x509/validate-certificate.png)

        1. Välj **Hantera registreringar**. Välj fliken **Registreringsgrupper** och klicka på knappen **Lägg till** längst upp.
            - Ange ett unikt gruppnamn.
            - Välj det unika certifikatnamnet som du skapade tidigare
            - Du kan även ange följande information:
                - Välj en IoT hub som är länkad till din etableringstjänst.
                - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.

        ![Ange information för X.509-gruppregistrering på portalbladet](./media/java-quick-create-simulated-device-x509/enter-group-enrollment.png)

        Vid lyckad registrering visas din X.509-enhetsgrupp under kolumnen *Gruppnamn* på fliken *Registreringsgrupper*.


## <a name="simulate-the-device"></a>Simulera enheten

1. På sammanfattningsbladet för etableringstjänsten väljer du **Översikt** och noterar _Id Scope_ (ID-omfång) och _Provisioning Service Global Endpoint_ (Global slutpunkt för etableringstjänsten).

    ![Tjänstinformation](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. Öppna en kommandotolk. Navigera till exempelprojektmappen.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Ange registreringsinformationen på något av följande sätt, enligt inställningarna:

    - **Enskild registrering**: 

        1. Redigera `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` så att _Id Scope_ (ID-omfång) och _Provisioning Service Global Endpoint_ (Global slutpunkt för etableringstjänsten) inkluderas enligt tidigare notering. Inkludera även _Client Cert_ (Klientcertifikat) och _Client Cert Private Key_ (Privat nyckel för klientcertifikat) enligt tidigare notering.

            ```java
            private static final String idScope = "[Your ID scope here]";
            private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
            private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
            private static final String leafPublicPem = "<Your Public PEM Certificate here>";
            private static final String leafPrivateKey = "<Your Private PEM Key here>";
            ```

            - Inkludera certifikatet och nyckeln med hjälp av följande format:
            
                ```java
                private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXX\n" +
                    "-----END PRIVATE KEY-----\n";
                ```

    - **Registreringsgrupper**: 

        1. Följ instruktionerna för **enskild registrering ovan**.

        1. Lägg till följande rader kod i början av funktionen `main`.
        
            ```java
            String intermediatePem = "<Your Signer Certificate here>";          
            String rootPem = "<Your Root Certificate here>";
                
            signerCertificates.add(intermediatePem);
            signerCertificates.add(root);
            ```
    
            - Inkludera certifikatet med hjälp av följande format:
        
                ```java
                String intermediatePem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                String rootPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                ```

1. Skapa exemplet. Navigera till målmappen och kör den skapade jar-filen.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

1. I portalen går du till den IoT-hubb som är kopplad till din etableringstjänst och öppnar bladet **Device Explorer**. Vid lyckad etablering av den simulerade X.509-enheten till hubben visas dess enhets-ID på bladet **Device Explorer** med *STATUS* **aktiverad**. Observera att du kan behöva klicka på **uppdateringsknappen** högst upp om du redan har öppnat bladet innan du kör programmet på exempelenheten. 

    ![Enheten är registrerad på IoT-hubben](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).
>


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta att arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som har skapats i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din Device Provisioning-tjänst. Öppna bladet **Hantera registreringar** för tjänsten och klicka på fliken **Enskilda registreringar**. Välj *REGISTRERINGS-ID* för enheten du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp. 
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Öppna bladet **IoT-enheter** för din hubb, välj *ENHETS-ID* för enheten du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp.


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en X.509-simulerad enhet på Windows-datorn och etablerat den på IoT-hubben med hjälp av Azure IoT Hub Device Provisioning-tjänsten på portalen. Information om hur du registrerar X.509-enheten programmässigt får du om du fortsätter till snabbstarten för programmässig registrering av X.509-enheter. 

> [!div class="nextstepaction"]
> [Azure snabbstart – Registrera X.509-enheter på Azure IoT Hub Device Provisioning-tjänsten](quick-enroll-device-x509-java.md)
