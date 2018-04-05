---
title: Etablera en simulerad X.509-enhet på Azure IoT Hub med Node.js | Microsoft Docs
description: Azure snabbstart – Skapa och etablera en simulerad X.509-enhet med Node.js-enhets-SDK för Azure IoT Hub Device Provisioning-tjänsten
services: iot-dps
keywords: ''
author: msebolt
ms.author: v-masebo
ms.date: 01/29/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 8295ffe14446423ae2cc3de31616aa375a91e96c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Skapa och etablera en simulerad X.509-enhet med Node.js-enhets-SDK för IoT Hub Device Provisioning-tjänsten
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

IoT Hub Device Provisioning-tjänsten är en hjälptjänst för IoT Hub som tillhandahåller zero-touch-enhetsetablering för IoT Hub. Med enhetsetableringstjänsten kan du etablera miljontals enheter på ett säkert och skalbart sätt.

Enhetsetablering består av två steg. Det första steget är att skapa en lämplig registreringspost i enhetsetableringstjänsten utifrån lösningens specifika krav.  Det andra steget är att upprätta en koppling mellan enheten och enhetsetableringstjänsten och registrera enheten i IoT Hub. När båda stegen utförts anses enheten vara helt etablerad. Enhetsetableringstjänsten automatiserar båda stegen och ger sömlös etablering av enheten. Mer information finns i [Koncept för enhetsetableringstjänsten](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-service).

Dessa steg visar hur du skapar en registreringspost i enhetsetableringstjänsten, simulerar en X.509-enhet på utvecklingsmaskinen, ansluter den simulerade enheten med enhetsetableringstjänsten och registrerar enheten i din IoT Hub med [Azure IoT Hub Node.js-enhets-SDK](https://github.com/Azure/azure-iot-sdk-node).

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Förbereda miljön 

1. Slutför stegen i [Konfigurera IoT Hub Device Provisioning-tjänsten med Azure Portal](./quick-setup-auto-provision.md) innan du fortsätter.

1. Kontrollera att [Node.js v4.0 eller senare](https://nodejs.org) är installerat på din dator.

1. Kontrollera att [Git](https://git-scm.com/download/) är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. 

1. Kontrollera att [OpenSSL](https://www.openssl.org/) är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Biblioteket kan antingen byggas och installeras från källkoden eller laddas ned och installeras från en [tredje part](https://wiki.openssl.org/index.php/Binaries) som [det här](https://sourceforge.net/projects/openssl/). 

    > [!NOTE]
    > Om du redan skapat _rotcertifikat_,  _mellanliggande certifikat_ och/eller X.509-_lövcertifikat_ kan du hoppa över det här steget och alla efterföljande steg som rör skapande av certifikat.
    >


## <a name="create-an-enrollment-entry"></a>Skapa en registreringspost

En registrering är en post för enheter eller grupper av enheter som någon gång kan ha registrerats i enhetsetableringstjänsten. Registreringsposten innehåller information om enheten eller gruppen med enheter, inklusive information om X.509-certifikat och ytterligare registreringsinformation. Två typer av registrering stöds av enhetsetableringstjänsten, _enskild registrering_ och _registreringsgrupp_. Mer information finns i [Registreringskoncept](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-service#enrollment).

Om du skapar dina egna X.509-testcertifikat läser du i [Säkerhetskoncept](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#x509-certificates) om vilka certifikat som krävs för lösningen och i [Verktyg för Azure IoT enhetsetableringstjänst-SDK för Node.js](https://github.com/azure/azure-iot-sdk-node/tree/master/provisioning/tools) för information om implementering.

1. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för att hämta exempelkoden:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

1. Navigera till certifikatgeneratorskriptet och skapa projektet. 

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Skapa registreringsinformationen på något av följande sätt, enligt inställningarna:

    - **Enskild registrering**:

        1. Skapa _lövcertifikatet_ genom att köra skriptet med ditt eget _certifikatenamn_. Lövcertifikatets namn blir [Registrerings-ID](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-device#registration-id) så var noga med att bara använda små bokstäver, siffror och bindestreck.

        ```cmd/sh
        node create_test_cert.js device {certificate-name}
        ```
         
        1. I **Azure Portal** öppnar du sammanfattningsbladet för **enhetsetableringstjänsten**. Välj **Hantera registreringar** och sedan fliken **Enskilda registreringar** och klicka på knappen **Lägg till** längst upp. 

        1. Under posten för att **lägga till registreringslista** anger du följande information:
            - Välj **X.509** som identitet för bestyrkande *mekanism*.
            - Under *certifikatets .pem- eller .cer-fil* väljer du certifikatfilen **_{certifikatnamn}\_cert.pem_** som har skapats i de föregående stegen med widgeten *Utforskaren*.
            - Du kan även ange följande information:
                - Välj en IoT hub som är länkad till din etableringstjänst.
                - Ange ett unikt enhets-ID. Se till att undvika känsliga data när du namnger din enhet. 
                - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
            - Klicka på knappen **Spara** när det är klart. 

        ![Ange information för X.509-enhetsregistrering på portalbladet](./media/quick-create-simulated-device-x509-node/enter-device-enrollment.png)  

    Om registreringen har lyckats visas din X.509-enhet som **{certifikatnamn}** under kolumnen *Registrerings-ID* på fliken *Enskilda registreringar*. Anteckna det här värdet för senare användning.

    - **Registreringsgrupper**: 

        1. Skapa _rotcertifikatet_ genom att köra skriptet med ditt eget _rotnamn_.

        ```cmd/sh
        node create_test_cert.js root {root-name}
        ```

        1. I **Azure Portal** öppnar du sammanfattningsbladet för **enhetsetableringstjänsten**. Välj **Certifikat** och klicka på knappen **Lägg till** längst upp.

        1. Under **Lägg till certifikat** anger du följande information:
            - Ange ett unikt certifikatnamn.
            - Välj den **_{rotnamn}\_cert.pem_**-fil som du skapade tidigare.
            - Klicka på knappen **Spara** när det är klart.

        ![Lägg till certifikat](./media/quick-create-simulated-device-x509-node/add-certificate.png)

        1. Välj det nya certifikatet:
            - Klicka på **Skapa verifieringskod**. Kopiera den skapade koden.
            - Skapa _verifieringscertifikatet_. Ange _verifieringskoden_ eller högerklicka för att klistra in följande kommando i det aktiva nodskriptfönstret:

                ```cmd/sh
                node create_test_cert.js verification {rootname_cert} {verification code}
                ```

            - Under *verifieringscertifikatets .pem- eller .cer*-fil väljer du certifikatfilen **_verification_cert.pem_** som skapades i föregående steg med widgeten *Utforskaren*. Klicka på **Verifiera**.

            ![Validera certifikat](./media/quick-create-simulated-device-x509-node/validate-certificate.png)

        1. Välj **Hantera registreringar**. Välj fliken **Registreringsgrupper** och klicka på knappen **Lägg till** längst upp.
            - Ange ett unikt gruppnamn.
            - Välj det unika certifikatnamnet som du skapade tidigare
            - Du kan även ange följande information:
                - Välj en IoT hub som är länkad till din etableringstjänst.
                - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.

        ![Ange information för X.509-gruppregistrering på portalbladet](./media/quick-create-simulated-device-x509-node/enter-group-enrollment.png)

        Vid lyckad registrering visas din X.509-enhetsgrupp under kolumnen *Gruppnamn* på fliken *Registreringsgrupper*. Anteckna det här värdet för senare användning.

        1. Skapa _lövcertifikatet_ genom att köra skriptet med ditt eget _certifikatnamn_ följt av det _rotnamn_ som använts tidigare. Lövcertifikatets namn blir [Registrerings-ID](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-device#registration-id) så var noga med att bara använda små bokstäver, siffror och bindestreck.

            ```cmd/sh
            node create_test_cert.js device {certificate-name} {root-name}
            ```

        > [!NOTE]
        > Du kan även skapa _mellanliggande_ certifikat med `node create_test_cert.js intermediate {certificate-name} {parent-name}`. Var noga med att skapa _lövcertifikatet_ som det sista steget med det sista _mellanliggande_ certifikatet som rot eller överordnat certifikat. Mer information finns i [Styra enhetsåtkomst](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#controlling-device-access-to-the-provisioning-service-with-x509-certificates).
        >


## <a name="simulate-the-device"></a>Simulera enheten

[Azure IoT Hub Node.js-enhets-SDK](https://github.com/Azure/azure-iot-sdk-node) gör det enkelt att simulera en enhet. Läs mer i [Enhetskoncept](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-device).

1. I Azure-portalen väljer du bladet **Översikt** för Device Provisioning-tjänsten och noterar värdena för **_Global enhetsslutpunkt_** och **_ID-omfång_**.

    ![Extrahera DP-slutpunktsinformation från portalbladet](./media/quick-create-simulated-device-x509-node/extract-dps-endpoints.png) 

1. Kopiera ditt _certifikat_ och din _nyckel_ till exempelmappen.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

1. Navigera till skriptet för enhetstest och skapa projektet. 

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

1. Redigera **register\_x509.js**-filen. Spara filen när du gjort följande ändringar.
    - Ersätt `provisioning host` med den **_globala enhetsslutpunkt_** du antecknade i **steg 1** ovan.
    - Ersätt `id scope` med det **_ID-omfång_** du antecknade i **steg 1** ovan. 
    - Ersätt `reigstration id` med det **_Registrerings-ID_** eller **_Gruppnamn_** du antecknade i föregående avsnitt.
    - Ersätt `cert filename` och `key filename` med de filer du kopierade i **steg 2** ovan. 

1. Kör skriptet och verifiera att enheten etablerats korrekt.

    ```cmd/sh
    node register_x509.js
    ```   

1. I portalen går du till den IoT-hubb som är kopplad till din etableringstjänst och öppnar bladet **IoT-enheter**. Vid lyckad etablering av den simulerade X.509-enheten till hubben visas dess enhets-ID på bladet **IoT-enheter** med *STATUS* **aktiverad**. Du kan behöva klicka på **uppdateringsknappen** högst upp om du redan har öppnat bladet innan du kör programmet på exempelenheten. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-x509-node/hub-registration.png) 

    Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta att arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som har skapats i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din Device Provisioning-tjänst. Öppna bladet **Hantera registreringar** för tjänsten och klicka på fliken **Enskilda registreringar** eller **Registreringsgrupper**. Välj *REGISTRERINGS-ID* eller *GRUPPNAMN* för enheten du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp. 
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Öppna bladet **IoT-enheter** för din hubb, välj *ENHETS-ID* för enheten du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp.


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en simulerad X.509-enhet och etablerat den på IoT-hubben med hjälp av Azure IoT Hub Device Provisioning-tjänsten i portalen. Information om hur du registrerar X.509-enheten programmässigt får du om du fortsätter till snabbstarten för programmässig registrering av X.509-enheter. 

> [!div class="nextstepaction"]
> [Azure snabbstart – Registrera X.509-enheter på Azure IoT Hub Device Provisioning-tjänsten](quick-enroll-device-x509-node.md)
