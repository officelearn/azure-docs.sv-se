---
title: Registrera TPM-enhet till Azure Device Provisioning Service med Java
description: Snabbstart – Registrera TPM-enhet till Azure IoT Hub Device Provisioning Service (DPS) med Java-tjänst SDK. Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: c199d5be4c103c80a6fcc126af70f48367909f64
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241698"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Snabbstart: Registrera TPM-enhet till IoT Hub Device Provisioning Service med Java Service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

I den här snabbstarten skapar du programmässigt en individuell registrering för en simulerad TPM-enhet i Azure IoT Hub Device Provisioning Service med hjälp av Java Service SDK med hjälp av ett exempel Java-program.

## <a name="prerequisites"></a>Krav

- Slutförd av [Konfigurera IoT Hub Device Provisioning Service med Azure-portalen](./quick-setup-auto-provision.md).
- Slutförande av [Läs kryptografiska nycklar från TPM-enheten](quick-create-simulated-device.md#simulatetpm).
- Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Utveckling Kit 8](https://aka.ms/azure-jdks). Den här snabbstarten installerar [Java-tjänsten SDK](https://azure.github.io/azure-iot-sdk-java/service/) nedan. Det fungerar på både Windows och Linux. Den här snabbstarten använder Windows.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön 

1. Kontrollera att [Java SE Development Kit 8](https://aka.ms/azure-jdks) är installerat på datorn. 

2. Konfigurera miljövariabler för Java-installationen. Variabeln `PATH` ska innehålla den fullständiga sökvägen till katalogen *jdk1.8.x\bin*. Om det här är datorns första Java-installation skapar du en ny miljövariabeln med namnet `JAVA_HOME` och gör så att den pekar på den fullständiga sökvägen till katalogen *jdk1.8.x*. På Windows-datorer finns den här katalogen i mappen *C:\\Program\\Java\\* och du kan skapa eller redigera miljövariabler genom att söka efter **Redigera systemets miljövariabler** i **Kontrollpanelen** på Windows-datorn. 

   Du kan kontrollera om Java har konfigurerats på datorn genom att köra följande kommando i kommandofönstret:

    ```cmd\sh
    java -version
    ```

3. Ladda ned och extrahera [Maven 3](https://maven.apache.org/download.cgi) på datorn. 

4. Redigera miljövariabeln `PATH` så att den pekar på mappen *apache-maven-3.x.x\\bin* i mappen där Maven har extraherats. Du kan bekräfta att Maven har installerats genom att köra följande kommando i kommandofönstret:

    ```cmd\sh
    mvn --version
    ```

5. Kontrollera att [git](https://git-scm.com/download/) är installerad på datorn och har lagts till i miljövariabeln `PATH`. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Ladda ned och ändra Java-exempelkoden

I det här avsnittet visas hur du lägger till etableringsinformationen för TPM-enheten i exempelkoden. 

1. Öppna en kommandotolk. Klona GitHub-repo för enhetsregistreringskodexempel med [Java-tjänsten SDK:](https://azure.github.io/azure-iot-sdk-java/service/)
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. I den nedladdade källkoden går du till exempelmappen **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**. Öppna filen **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** i valfritt redigeringsprogram och lägg till följande information:

   1. Lägg till `[Provisioning Connection String]` för etableringstjänsten från portalen enligt följande:
       1. Navigera till etableringstjänsten i [Azure-portalen](https://portal.azure.com). 
       2. Öppna **Policyer för delad åtkomst** och välj en princip som har behörigheten *EnrollmentWrite*.
       3. Kopiera **Anslutningssträng – primär nyckel**. 

           ![Hämta etableringsanslutningssträngen från portalen](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. I exempelkodfilen **_ServiceEnrollmentSample.java_** ersätter du `[Provisioning Connection String]` med **Anslutningssträng – primär nyckel**.
    
           ```Java
           private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
           ```

   2. Lägg till TPM-enhetsinformation:
       1. Hämta *registrerings-ID* och *TPM-bekräftelsenyckeln* för en TPM-enhetssimulering genom att följa stegen som leder till avsnittet [Simulera TPM-enhet](quick-create-simulated-device.md#simulatetpm).
       2. Använd **_registrerings-ID_** och **_bekräftelsenyckeln_** från utdata i det föregående steget för att ersätta `[RegistrationId]` och `[TPM Endorsement Key]` i exempelkodfilen **_ServiceEnrollmentSample.java_**:
        
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

   3. Alternativt kan du konfigurera etableringstjänsten genom exempelkoden:
      - Om du vill lägga till konfigurationen i exemplet gör du följande:
        1. Navigera till IoT-hubben som är länkad till din etableringstjänst i [Azure Portal](https://portal.azure.com). Öppna fliken **Översikt** för hubben och kopiera **värdnamnet**. Tilldela **värdnamnet** till parametern *IOTHUB_HOST_NAME*.
            ```Java
            private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
            ```
        2. Tilldela ett eget namn till parametern *DEVICE_ID* och behåll standardvärdet för *PROVISIONING_STATUS*, *ENABLED*. 
    
      - Eller: om du väljer att inte konfigurera etableringstjänsten kommentera ut eller ta bort följande instruktioner i filen _ServiceEnrollmentSample.java_:
          ```Java
          // The following parameters are optional. Remove it if you don't need.
          individualEnrollment.setDeviceId(DEVICE_ID);
          individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
          individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
          ```

   4. Studera exempelkoden. Den skapar, uppdaterar, frågar och tar bort en enskild TPM-enhetsregistrering. Du kan verifiera att registreringen har lyckats i portalen genom att tillfälligt kommentera ut följande rader med kod i filen _ServiceEnrollmentSample.java_:
    
       ```Java
       // *********************************** Delete info of individualEnrollment ************************************
       System.out.println("\nDelete the individualEnrollment...");
       provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
       ```

   5. Spara filen _ServiceEnrollmentSample.java_.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>Skapa och kör Java-exempelkoden

1. Öppna ett kommandofönster och navigera till mappen **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**.

2. Skapa exempelkoden genom att använda följande kommando:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Det här kommandot hämtar [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) Maven-paketet till din dator. Det här paketet innehåller binärfilerna för [Java-tjänsten SDK](https://azure.github.io/azure-iot-sdk-java/service/), som exempelkoden behöver skapa. 

3. Kör exemplet genom att använda de här kommandona i kommandofönstret:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Se utdatafönstret om registreringen har lyckats. 

5. Navigera till etableringstjänsten i Azure Portal. Välj **Hantera registreringar**och välj fliken **Enskilda registreringar.** *Registration ID* 

    ![Verifiera lyckad TPM-registrering i portalen](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att utforska java-tjänstexemplet ska du inte rensa de resurser som skapas i den här snabbstarten. Om du inte planerar att fortsätta använder du följande steg för att ta bort alla resurser som skapats av den här snabbstarten.

1. Stäng utdatafönstret för Java-exemplet på datorn.
1. Stäng TPM-simulatorfönstret som du kan ha skapat för att simulera TPM-enheten.
1. Navigera till tjänsten Enhetsetablering i Azure-portalen, välj **Hantera registreringar**och välj sedan *Registration ID* fliken **Enskilda registreringar.** **Delete**

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten registrerade du en simulerad TPM-enhet till enhetens etableringstjänst. Om du vill ha mer djupgående information om enhetsetablering kan du fortsätta till självstudien om konfiguration av Device Provisioning-tjänsten i Azure-portalen. 

> [!div class="nextstepaction"]
> [Självstudier om Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-cloud.md)
