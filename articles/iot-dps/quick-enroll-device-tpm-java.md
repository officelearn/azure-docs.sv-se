---
title: "Registrera TPM-enhet på Azure Device Provisioning Service-tjänsten med Java | Microsoft Docs"
description: "Azure snabbstart – Registrera TPM-enhet på Azure IoT Hub Device Provisioning-tjänsten med Java-tjänst-SDK"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 12/20/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 8b871c28c2d666bebcc192403cf0c8ef238fef4f
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Registrera TPM-enhet på IoT Hub Device Provisioning-tjänsten med Java-tjänst-SDK
> [!div class="op_single_selector"]
> * [Java](quick-enroll-device-tpm-java.md)
> * [C#](quick-enroll-device-tpm-csharp.md)
> * [Node.js](quick-enroll-device-tpm-node.md)

De här stegen visar hur du registrerar en simulerad TPM-enhet programmässigt på Azure IoT Hub Device Provisioning-tjänsterna, med [Java-tjänst-SDK](https://azure.github.io/azure-iot-sdk-java/service/) med hjälp av ett Java-exempelprogram. Java-tjänst-SDK fungerar på både Windows- och Linux-datorer men i den här artikeln används en Windows-utvecklingsdator för att gå igenom registreringsprocessen.

Se till att [konfigurera IoT Hub Device Provisioning-tjänsten med Azure Portal](./quick-setup-auto-provision.md) samt [simulera en TPM device](quick-create-simulated-device.md#simulatetpm) innan du fortsätter.

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön 

1. Kontrollera att [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) är installerat på datorn. 

2. Konfigurera miljövariabler för Java-installationen. Variabeln `PATH` ska innehålla den fullständiga sökvägen till katalogen *jdk1.8.x\bin*. Om det här är datorns första Java-installation skapar du en ny miljövariabeln med namnet `JAVA_HOME` och gör så att den pekar på den fullständiga sökvägen till katalogen *jdk1.8.x*. På Windows-datorer finns den här katalogen normalt i mappen *C:\\Program\\Java\\* och du kan skapa eller redigera miljövariabler genom att söka efter **Redigera systemets miljövariabler** i **Kontrollpanelen** på Windows-datorn. 

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

1. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för enhetsregistreringens kodexempel med Java-tjänst-SDK:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. I den nedladdade källkoden går du till exempelmappen **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**. Öppna filen **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** i valfritt redigeringsprogram och lägg till följande information:

    1. Lägg till `[Provisioning Connection String]` för etableringstjänsten från portalen enligt följande:
        1. Navigera till etableringstjänsten i [Azure Portal](https://portal.azure.com). 
        2. Öppna **Policyer för delad åtkomst** och välj en princip med behörigheten *EnrollmentWrite*.
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

   Kommandot laddar ned Maven-paketet [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) på datorn. Paketet innehåller binärfilerna för Java-tjänst-SDK, som exempelkoden behöver skapa. 

3. Kör exemplet genom att använda de här kommandona i kommandofönstret:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Se utdatafönstret om registreringen har lyckats. 

5. Navigera till etableringstjänsten i Azure Portal. Klicka på **Hantera registreringar** och välj fliken **Enskilda registreringar**. Obs! *Registrerings-ID* för den simulerade TPM visas nu. 

    ![Verifiera lyckad TPM-registrering i portalen](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Rensa resurser
Om du tänker utforska Java-tjänstexemplet ska du inte rensa resurserna som har skapats i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

1. Stäng utdatafönstret för Java-exemplet på datorn.
1. Stäng TPM-simulatorfönstret som du kan ha skapat för att simulera TPM-enheten.
1. Navigera till Device Provisioning-tjänsten i Azure Portal, klicka på **Hantera registreringar** och välj sedan fliken **Enskilda registreringar**. Välj *Registrerings-ID* för enheten du har registrerat med den här snabbstarten och klickar på knappen **Ta bort** högst upp på bladet. 

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du registrerat en simulerad TPM-enhet i Device Provisioning-tjänsten. Om du vill ha mer djupgående information om enhetsetablering kan du fortsätta till självstudien om konfiguration av Device Provisioning-tjänsten i Azure-portalen. 

> [!div class="nextstepaction"]
> [Självstudier om Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-cloud.md)
