---
title: Etablera en simulerad X.509-enhet på Azure IoT Hub med Java och registreringsgrupper | Microsoft Docs
description: Azure-självstudie – Skapa och etablera en simulerad X.509-enhet med Java-enhets-SDK och registreringsgrupper för IoT Hub Device Provisioning-tjänsten
author: bryanla
ms.author: bryanla
ms.date: 01/04/2018
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 02cc3f861864d3bfa878be6501b8563715f88dac
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971327"
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>Skapa och etablera en simulerad X.509-enhet med Java-enhets-SDK och tjänst-SDK och gruppregistreringar för IoT Hub Device Provisioning-tjänsten

Dessa steg visar hur du simulerar en X.509-enhet på utvecklingsdatorn som kör Windows OS och använder kodexemplet för att ansluta till denna simulerade enhet med Device Provisioning-tjänsten och IoT-hubben med registreringsgrupper. 

Se till att slutföra stegen i [Konfigurera IoT Hub Device Provisioning-tjänsten med Azure Portal](./quick-setup-auto-provision.md) innan du fortsätter.


## <a name="prepare-the-environment"></a>Förbereda miljön 

1. Kontrollera att [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) är installerat på datorn.

1. Ladda ned och installera [Maven](https://maven.apache.org/install.html).

1. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

1. Använd följande [certifikatöversikt](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) för att skapa dina testcertifikat. En mer grundlig genomgång av att skapa certifikat finns i [PowerShell scripts to manage CA-signed X.509 certificates](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates) (PowerShell-skript för att hantera CA-signerade X.509-certifikat).

    > [!NOTE]
    > Det är steget kräver [OpenSSL](https://www.openssl.org/), vilket antingen kan byggas och installeras från källkoden eller laddas ned och installeras från en [tredje part](https://wiki.openssl.org/index.php/Binaries) som [det här](https://sourceforge.net/projects/openssl/). Om du redan har skapat dina _rot-_, _mellanliggande_ och _enhets_-certifikat kan du hoppa över det här steget.
    >

    1. Gå igenom de två första stegen för att skapa dina _rot-_ och _mellanliggande_ certifikat.

    1. Logga in på Azure-portalen, klicka på knappen **Alla resurser** i den vänstra menyn och öppna din distributionstjänst.

        1. På sammanfattningsbladet för Device Provisioning-tjänsten väljer du **Certifikat** och klickar på knappen **Lägg till** högst upp.

        1. Under **Lägg till certifikat** anger du följande information:
            - Ange ett unikt certifikatnamn.
            - Välj filen **_RootCA.pem_** som du nyss skapade.
            - Klicka på knappen **Spara** när det är klart.

        ![Lägg till certifikat](./media/tutorial-group-enrollments/add-certificate.png)

        1. Välj det nya certifikatet:
            - Klicka på **Skapa verifieringskod**. Kopiera den skapade koden.
            - Kör verifieringsstegen. Ange _verifieringskoden_ eller högerklicka för att klistra in PowerShell-fönstret som körs.  Tryck på **RETUR**.
            - Välj den nyligen skapade filen **_verifyCert4.pem_** i Azure-portalen. Klicka på **Verifiera**.

            ![Validera certifikat](./media/tutorial-group-enrollments/validate-certificate.png)

    1. Avsluta genom att köra stegen för att skapa dina enhetscertifikat och rensa resurser.

    > [!NOTE]
    > När du skapar enhetscertifikat ska du se till att endast använda gemena alfanumeriska tecken och bindestreck i enhetens namn.
    >


## <a name="create-a-device-enrollment-entry"></a>Skapa en post för enhetsregistrering

1. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för att hämta Java SDK-exempelkoden:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. I den nedladdade källkoden går du till exempelmappen **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**. Öppna filen **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** i valfritt redigeringsprogram och lägg till följande information:

    1. Lägg till `[Provisioning Connection String]` för etableringstjänsten från portalen enligt följande:

        1. Navigera till etableringstjänsten i [Azure Portal](https://portal.azure.com). 

        1. Öppna **Policyer för delad åtkomst** och välj en princip med behörigheten *EnrollmentWrite*.
    
        1. Kopiera **Anslutningssträng – primär nyckel**. 

            ![Hämta etableringsanslutningssträngen från portalen](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. I exempelkodfilen **_ServiceEnrollmentGroupSample.java_** ersätter du `[Provisioning Connection String]` med **Anslutningssträng – primär nyckel**.

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. Öppna filen **_RootCA.pem_** i ett redigeringsprogram. Tilldela **rotcertifikatets** värde till parametern **PUBLIC_KEY_CERTIFICATE_STRING** enligt nedan:

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```
 
    1. Navigera till IoT-hubben som är länkad till din etableringstjänst i [Azure Portal](https://portal.azure.com). Öppna fliken **Översikt** för hubben och kopiera **värdnamnet**. Tilldela **värdnamnet** till parametern *IOTHUB_HOST_NAME*.

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. Studera exempelkoden. Den skapar, uppdaterar, frågar och tar bort en gruppregistrering för X.509-enheter. Du kan verifiera att registreringen har lyckats i portalen genom att tillfälligt kommentera ut följande rader med kod i filen _ServiceEnrollmentGroupSample.java_:

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. Spara filen _ServiceEnrollmentGroupSample.java_. 

1. Öppna ett kommandofönster och navigera till mappen **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**.

1. Skapa exempelkoden genom att använda följande kommando:

    ```cmd\sh
    mvn install -DskipTests
    ```

1. Kör exemplet genom att använda de här kommandona i kommandofönstret:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. Se utdatafönstret om registreringen har lyckats.

    ![Lyckad registrering](./media/tutorial-group-enrollments/enrollment.png) 

1. Navigera till etableringstjänsten i Azure Portal. Klicka på **Hantera registreringar**. Observera att gruppen med X.509-enheter visas under fliken **Registreringsgrupper**, med ett automatiskt genererat *GRUPPNAMN*. 


## <a name="simulate-the-device"></a>Simulera enheten

1. På sammanfattningsbladet för etableringstjänsten väljer du **Översikt** och noterar _Id Scope_ (ID-omfång) och _Provisioning Service Global Endpoint_ (Global slutpunkt för etableringstjänsten).

    ![Tjänstinformation](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. Öppna en kommandotolk. Navigera till exempelprojektmappen.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Ange registreringsinformation för gruppen på följande sätt:

    - Redigera `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` så att _ID-omfång_ och _Provisioning Service Global Endpoint_ (Global slutpunkt för etableringstjänsten) inkluderas enligt tidigare notering. Öppna filen **_{deviceName}-public.pem_** och inkludera värdet som ditt _klientcertifikat_. Öppna filen **_{deviceName}-all.pem_** och kopiera texten från _-----BEGIN PRIVATE KEY-----_ (PÅBÖRJA PRIVAT NYCKEL) till _-----END PRIVATE KEY-----_ (AVSLUTA PRIVAT NYCKEL).  Använd det som din _privata klientcertifikatnyckel_.

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

1. Skapa exemplet. Navigera till målmappen och kör den skapade jar-filen.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![Lyckad registrering](./media/tutorial-group-enrollments/registration.png)

1. I portalen går du till den IoT-hubb som är kopplad till din etableringstjänst och öppnar bladet **Device Explorer**. Vid lyckad etablering av den simulerade X.509-enheten till hubben visas dess enhets-ID på bladet **Device Explorer** med *STATUS* **aktiverad**. Observera att du kan behöva klicka på **uppdateringsknappen** högst upp om du redan har öppnat bladet innan du kör programmet på exempelenheten. 

    ![Enheten är registrerad på IoT-hubben](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta att arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som har skapats i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din Device Provisioning-tjänst. Öppna bladet **Hantera registreringar** för tjänsten och klicka på fliken **Enskilda registreringar**. Välj *REGISTRERINGS-ID* för enheten du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp. 
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Öppna bladet **IoT-enheter** för din hubb, välj *ENHETS-ID* för enheten du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en X.509-simulerad enhet på Windows-datorn och etablerat den på IoT-hubben med hjälp av Azure IoT Hub Device Provisioning-tjänsten och registreringsgrupper. Om du vill läsa mer om X.509-enheten fortsätter du till enhetsbegreppen. 

> [!div class="nextstepaction"]
> [Självstudier om IoT Hub Device Provisioning-tjänstens begrepp](concepts-device.md)
