---
title: Snabbstart – Använd symmetrisk nyckel för att etablera simulerad enhet till Azure IoT Hub med Java
description: I den här snabbstarten kommer du att använda Java-enheten SDK för att skapa en simulerad enhet som använder symmetrisk nyckel med Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: aaa1a4423363255536db7d53a1f8f8fa9ba686ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76941404"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Snabbstart: Etablera en simulerad enhet med symmetriska nycklar

I den här snabbstarten får lära dig att skapa och köra en enhetssimulator på en Windows-utvecklingsdator. Du konfigurerar den här simulerade enheten så att den använder en symmetrisk nyckel för att autentisera med en DPS-instans (Device Provisioning Service) och tilldelas en IoT-hubb. Exempelkod från [Microsoft Azure IoT SDK:er för Java](https://github.com/Azure/azure-iot-sdk-java) används för att simulera en startsekvens för enheten som initierar etablering. Enheten identifieras baserat på en enskild registrering med en DPS-tjänstinstans och tilldelas en IoT-hubb.

Även om den här artikeln visar etablering med en enskild registrering kan du använda registreringsgrupper. Det finns vissa skillnader när du använder registreringsgrupper. Du måste till exempel använda en härledd enhetsnyckel med ett unikt registrerings-ID för enheten. Även om registreringsgrupper för symmetrisk nyckel inte är begränsade till äldre enheter finns det ett exempel för registreringsgrupper i avsnittet om [hur du etablerar äldre enheter med symmetrisk nyckelattestering](how-to-legacy-device-symm-key.md). Mer information finns i avsnittet om [gruppregistreringar för symmetrisk nyckelattestering](concepts-symmetric-key-attestation.md#group-enrollments).

Om du inte känner till processen för automatisk etablering bör du gå igenom [Begrepp inom automatisk etablering](concepts-auto-provisioning.md). 

Se även till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub-enhetsetableringstjänsten med Azure-portalen) innan du fortsätter med den här snabbstarten. Den här snabbstarten kräver att du redan har skapat en Device Provisioning Service-instans.

Den här artikeln riktar sig till en Windows-arbetsstation. Du kan dock utföra procedurerna i Linux. Ett Linux-exempel finns i informationen om att [etablera för flera innehavare](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Krav

* Se till att du har [Java SE Development Kit 8](https://aka.ms/azure-jdks) eller senare installerat på din maskin.

* Ladda ned och installera [Maven](https://maven.apache.org/install.html).

* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>Förbereda Java SDK-miljön 

1. Kontrollera att Git är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

2. Öppna en kommandotolk. Klona GitHub-lagringsplatsen för enhetssimuleringens kodexempel:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Navigera till `azure-iot-sdk-java` rotkatalogen och bygg projektet för att hämta alla nödvändiga paket.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Skapa en enhetsregistrering

1. Logga in på [Azure-portalen,](https://portal.azure.com)välj knappen **Alla resurser** på menyn till vänster och öppna DPS-instansen (Device Provisioning Service).

2. Välj fliken **Hantera registreringar** och välj sedan knappen **Lägg till individuell registrering** högst upp. 

3. Ange följande information på panelen **Lägg till registrering** och tryck på knappen **Spara.**

   - **Mekanism:** välj **Symmetrisk nyckel** som identitetsattesterings*mekanism*.

   - **Generera nycklar automatiskt:** Markera den här rutan.

   - **Registrerings-ID**: Ange ett registrerings-ID för att identifiera registreringen. Använd endast alfanumeriska gemener och bindestreck (”-”). Till exempel **symm-key-java-device-007**.

   - **Enhets-ID för IoT Hub:** Ange en enhetsidentifierare. Till exempel **java-device-007**.

     ![Lägga till en enskild registrering för symmetrisk nyckelattestering i portalen](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. När du har sparat registreringen genereras **primärnyckeln** och **sekundärnyckeln** och läggs till i registreringsposten. Registreringen av den symmetriska nyckelenheten visas som **symm-key-java-device-007** under kolumnen *Registrerings-ID* på fliken *Enskilda registreringar.* 

    Öppna registreringen och kopiera värdet för din genererade **primärnyckel**. Du kommer att använda det här nyckelvärdet och **registrerings-ID** senare när du uppdaterar Java-koden för enheten.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Simulera startsekvens för enheten

I det här avsnittet uppdaterar du enhetsexemplingskoden för att skicka enhetens startsekvens till DPS-instansen. Den här startsekvensen gör att enheten identifieras, autentiseras och tilldelas en IoT-hubb som är länkad till DPS-instansen.

1. På menyn Tjänst för enhetsetablering väljer du **Översikt** och noterar din globala slutpunkt för _ID-scope_ _och etableringstjänst_.

    ![Tjänstinformation](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Öppna exempelkoden för Java-enheten för redigering. Den fullständiga sökvägen till enhetens exempelkod är:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Lägg till _ID-scopet_ och _etableringstjänstens globala slutpunkt_ för din DPS-instans. Inkludera även den primära symmetriska nyckeln och registrerings-ID:et som du valde för din individuella registrering. Spara ändringarna. 

      ```java
        private static final String SCOPE_ID = "[Your scope ID here]";
        private static final String GLOBAL_ENDPOINT = "[Your Provisioning Service Global Endpoint here]";
        private static final String SYMMETRIC_KEY = "[Enter your Symmetric Key here]";
        private static final String REGISTRATION_ID = "[Enter your Registration ID here]";
      ```

3. Öppna en kommandotolk för att skapa. Navigera till exempelprojektmappen för etablerandet i Java SDK-databasen.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample
    ```

4. Skapa exemplet och navigera `target` sedan till mappen för att köra den skapade JAR-filen.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. Den förväntade produktionen bör se ut ungefär så här:

    ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

6. I Azure Portal går du till den IoT-hubb som är kopplad till din etableringstjänst och öppnar bladet **Device Explorer**. När den simulerade symmetriska nyckelenheten har etablerats till navet visas dess enhets-ID på **bladet Device Explorer,** med *STATUS* som **aktiverat**.  Du kan behöva trycka på **knappen Uppdatera** högst upp om du redan har öppnat bladet innan du kör exempelenhetsprogrammet. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).
>


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som skapas i den här snabbstarten. Om du inte planerar att fortsätta använder du följande steg för att ta bort alla resurser som skapats av den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. På menyn till vänster i Azure-portalen väljer du **Alla resurser** och väljer sedan din enhetsetableringstjänst. Öppna **Hantera registreringar** för tjänsten och markera sedan fliken **Enskilda registreringar.** *REGISTRATION ID* **Delete** 
1. På menyn till vänster i Azure-portalen väljer du **Alla resurser** och väljer sedan din IoT-hubb. Öppna **IoT-enheter** för din hubb, markera kryssrutan bredvid *DEVICE-ID för* den enhet som du registrerade i den här snabbstarten och tryck sedan på knappen **Ta bort** högst upp i fönstret.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en simulerad enhet på din Windows-dator och etablerat den i din IoT-hubb med symmetrisk nyckel med Azure IoT Hub Device Provisioning Service på portalen. Om du vill veta hur du registrerar enheten programmässigt fortsätter du till snabbstarten för programmatisk registrering av X.509-enheter. 

> [!div class="nextstepaction"]
> [Snabbstart i Azure – Registrera X.509-enheter till Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
