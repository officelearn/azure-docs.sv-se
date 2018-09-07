---
title: Felsöka Java-moduler för Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio Code för att utveckla och felsöka Java-moduler för Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/04/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6e3bbe996a2f0c37905aa990b39d324fd540952b
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057171"
---
# <a name="use-visual-studio-code-to-develop-and-debug-java-modules-for-azure-iot-edge"></a>Använd Visual Studio Code för att utveckla och felsöka Java-moduler för Azure IoT Edge

Du kan skicka din affärslogik som ska användas vid gränsen genom att aktivera det i moduler för Azure IoT Edge. Den här artikeln innehåller detaljerade anvisningar för att använda Visual Studio Code (VS Code) som det huvudsakliga utvecklingsverktyg för att utveckla Java-moduler.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows, macOS eller Linux som din utvecklingsdator. IoT Edge-enhet kan vara en annan fysisk enhet.

> [!NOTE]
> Den här felsökning artikeln visar två vanliga sätt att felsöka din Java-modul i VS Code. Ett sätt är att koppla en process i en modul-behållare, medan den andra är att lanuch modulen koden i felsökningsläge. Om du inte är bekant med felsökning funktionerna i Visual Studio Code, Läs om [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

Eftersom den här artikeln används Visual Studio Code som det huvudsakliga utvecklingsverktyg, installera VS Code och Lägg sedan till tillägg som behövs:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Java-tilläggspaketet för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
* [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Du behöver för att skapa en modul, Java och Maven att skapa och köra modulen kod, Docker för att skapa modulen avbildningen och ett behållarregister för att lagra avbildningen modulen:
* [Java SE Development Kit 10](http://www.oracle.com/technetwork/java/javase/downloads/index.html), och [ange den `JAVA_HOME` miljövariabeln](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) så att den pekar till den JDK-installationen.
* [Maven 3.](https://maven.apache.org/)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Du kan använda en lokal Docker-register för prototyper och testning i stället för ett register i molnet. 

Konfigurera lokal utvecklingsmiljö för att felsöka, köra och testa din IoT Edge-lösning måste du [utvecklingsverktyg för Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Installera [Python (2.7/3.6) och Pip](https://www.python.org/). Installera sedan **iotedgehubdev** genom att köra nedanstående kommando i terminalen.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Testa din modul på en enhet, behöver du en aktiv IoT-hubb med minst en IoT Edge enhets-ID som skapats. Om du kör IoT Edge-daemon på utvecklingsdator, kan du behöva stoppa EdgeHub och EdgeAgent innan du går vidare till nästa steg. 


## <a name="create-a-new-solution-template"></a>Skapa en ny lösningsmall

Följande steg visar hur du skapar en IoT Edge-modul som baseras på Java med hjälp av Visual Studio Code och Azure IoT Edge-tillägget. Du startar genom att skapa en lösning och sedan skapa den första modulen i lösningen. Varje lösning kan innehålla flera moduler. 

1. I Visual Studio Code, Välj **visa** > **integrerade terminalen**.

3. Gå till Visual Studio Code och välj **Visa** > **Kommandopalett**. 
4. Skriv i kommandopaletten, och kör kommandot **Azure IoT Edge: nya IoT Edge-lösning**.

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

5. Bläddra till mappen där du vill skapa den nya lösningen och klicka på **Välj mapp**. 
6. Ange ett namn för din lösning. 
7. Välj **Java-modulen** som mall för den första modulen i lösningen.
8. Ange ett namn för din modul. Välj ett namn som är unikt i ditt behållarregister. 
8. Ange ett värde för grupp-ID eller acceptera standardinställningen **com.edgemodule**.
9. Ange avbildningslagringsplatsen för modulen. VS Code autopopulates modulen namn, så du behöver bara ersätta **localhost:5000** med din egen information i registret. Om du använder en lokal Docker-register för testning, är det bra med localhost. Om du använder Azure Container Registry kan du sedan använda inloggningsserver från din registerinställningar. Det ser ut som inloggningsserver  **\<registernamn\>. azurecr.io**. Ersätt endast localhost-delen av strängen; ta inte bort modulens namn.

   ![Ange lagringsplatsen för Docker-avbildningen](./media/how-to-develop-node-module/repository.png)

VS Code tar den information du tillhandahålls, skapar en IoT Edge-lösning och läser in den i ett nytt fönster.

I lösningen har du tre objekt: 
* En **.vscode** mappen innehåller konfigurationer för felsökning.
* En **moduler** mappen innehåller undermappar för varje modul. Just nu du bara har en, men du kan lägga till fler i kommandopaletten med kommandot **Azure IoT Edge: Lägg till IoT Edge-modul**. 
* En **.env** filen visar en lista över dina miljövariabler. Om Azure Container Registry är ditt register, har du ett Azure Container Registry användarnamn och lösenord i den.

   >[!NOTE]
   >Miljö-filen skapas endast om du anger en avbildningslagringsplatsen för modulen. Om du har godkänt localhost för att testa och felsöka lokalt, behöver du inte deklarera miljövariabler. 

* En **deployment.template.json** filen visar en lista över dina nya modulen tillsammans med ett exempel **tempSensor** modul som simulerar data som du kan använda för testning. Mer information om hur distribution manifest work finns i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).

## <a name="develop-your-module"></a>Utveckla din modell

Standard Java-kod som medföljer lösningen finns i **moduler > [Modulnamn] > src > huvudsakliga > java > com > edgemodulemodules > App.java**. Modulen och filen deployment.template.json ställs in så att du kan skapa lösningen, push-överföra den till behållarregistret och distribuera den till en enhet för att börja testa utan att röra kod. Modulen är utformat för att helt enkelt ta indata från en källa (i det här fallet modulen tempSensor som simulerar data) och skicka det till IoT Hub. 

När du är redo att anpassa Java-mall med din egen kod kan använda den [Azure IoT Hub SDK: er](../iot-hub/iot-hub-devguide-sdks.md) att skapa moduler adressen nyckeln måste för IoT-lösningar som säkerhet, hantering av enheter och tillförlitlighet. 

Visual Studio Code har stöd för Java. Läs mer om [hur du arbetar med Java i VS Code](https://code.visualstudio.com/docs/java/java-tutorial).

## <a name="launch-and-debug-module-code-without-container"></a>Starta och felsöka modulen kod utan behållare
IoT Edge-Java-modulen beror på Azure IoT-Java enhets-SDK. I modul standardkoden du initiera en **ModuleClient** miljöinställningar Indatanamnet, vilket innebär att IoT Edge-Java-modulen kräver miljöinställningar att starta och köra och du måste också skicka eller dirigera meddelanden till inkommande kanaler. Din standard Java-modulen innehåller endast en indatakanal och namnet är **indata1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Konfigurera IoT Edge-simulator för enkel modulen app

1. För att konfigurera och starta simulatorn i VS Code kommandopalett skriver och välj **Azure IoT Edge: starta IoT Edge Hub Simulator för enkel modulen**. Du måste också ange Indatanamn för din enda modulen program skriver **indata1** och tryck på RETUR. Kommandot utlöser **iotedgehubdev** CLI och starta IoT Edge-simulatorn och en testning verktyget modulen behållare. Du kan se utdata nedan i den integrerade terminalen om simulatorn startades i läget för enskild modulen. Du kan också se en `curl` kommando för att skicka meddelande via. Du ska använda det senare.

   ![Konfigurera IoT Edge-simulator för enkel modulen app](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Du kan flytta till Docker-Utforskaren och finns i modulen Körningsstatus.

   ![Simulatorn modulens status](media/how-to-develop-csharp-module/simulator-status.png)

   Den **edgeHubDev** behållare utgör kärnan i lokal IoT Edge-simulatorn. Det kan köras på utvecklingsdatorn utan IoT Edge security daemon och ange miljöinställningar för ursprunglig modul appen eller modulen behållare. Den **inkommande** behållare exponeras restAPIs för att överbrygga meddelanden att rikta indatakanal på din modul.

2. I VS Code kommandopaletten, skriver du och väljer **Azure IoT Edge: ange autentiseringsuppgifter för modulen användarinställningar** att ställa in modulen miljöinställningar i `azure-iot-edge.EdgeHubConnectionString` och `azure-iot-edge.EdgeModuleCACertificateFile` i användarinställningarna. Du hittar dessa miljöinställningar refereras till i **.vscode** > **launch.json** och [användarinställningar för VS Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="debug-java-module-in-launch-mode"></a>Felsöka Java-modulen i Start-läge

2. Navigera till `App.java`. Lägg till en brytpunkt i den här filen.

3. Gå till felsökningsvyn för VS Code. Välj debug konfigurationen **ModuleName lokala felsöka (java)**. 

4. Klicka på **Starta felsökning** eller tryck på **F5**. Felsökningssessionen startas.

5. I VS Code-integrerade terminalen, kör du följande kommando för att skicka en **Hello World** meddelande till din modul. Det här är kommandot visade i föregående steg när har installationen IoT Edge-simulatorn. Du kan behöva skapa eller växla till en annan integrerade terminalen om aktuella blockeras.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Om du använder Windows kontrollerat att gränssnittet för den integrerade VS Code-terminalen är **Git Bash** eller **WSL Bash**. Du kan inte köra `curl` i PowerShell eller Kommandotolken. 
   
   > [!TIP]
   > Du kan också använda [PostMan](https://www.getpostman.com/) eller andra API-verktyg för att skicka meddelanden via i stället för `curl`.

6. I VS Code Felsöka vyn visas variabler i den vänstra panelen. 

7. Stoppa felsökningssessionen genom klicka på stoppknappen eller tryck på **SKIFT + F5**. I VS Code kommandopaletten, och Skriv Välj **Azure IoT Edge: stoppa IoT Edge-simulatorn** att stoppa och rensa simulatorn.


## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Skapa modulen behållare för felsökning och felsökning i Koppla läge

Standardlösningen innehåller två moduler, en är en sensor modul för simulerad temperatur och den andra är Java pipe-modulen. Simulerade temperatursensorn ser till att skicka meddelanden till Java pipe-modulen och sedan meddelandena som skickas till IoT Hub. Det finns flera Docker-filer för olika behållartyper i modulmappen som du skapade. Använd någon av dessa filer som slutar med tillägget **.debug** att skapa din modul för testning. Java-moduler stöder för närvarande endast felsökning i linux-amd64- och linux-arm32v7 behållare.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurera IoT Edge-simulator för IoT Edge-lösning

I en utvecklingsdator måste starta du IoT Edge-simulator istället för att installera IoT Edge-daemon för säkerhet för att köra din IoT Edge-lösning. 

1. I enhetsutforskare till vänster, högerklickar du på din IoT Edge enhets-ID, Välj **installationsprogrammet IoT Edge-simulatorn** att starta simulatorn med enhetens anslutningssträng.

2. Du kan se IoT Edge simulatorn har har konfigurerats i integrerade terminalen.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Skapa och köra behållare för felsökning och felsökning i Koppla läge

1. I VS Code, navigerar du till den `deployment.template.json` filen. Uppdatera din modulen bild-URL genom att lägga till **.debug** i slutet.

2. Ersätt createOptions för Java-modulen i **deployment.template.json** med nedan innehåll och spara den här filen: 
    ```json
    "createOptions":"{\"HostConfig\":{\"PortBindings\":{\"5005/tcp\":[{\"HostPort\":\"5005\"}]}}}"
    ```

5. Gå till felsökningsvyn VS Code. Välj debug-konfigurationsfil för. Alternativnamn debug bör likna **ModuleName fjärrfelsökning (Java)**.

6. Välj **Starta felsökning** eller välj **F5**. Välj processen för att ansluta till.

7. I VS Code Felsöka vyn visas variabler i den vänstra panelen.

8. Stoppa felsökningssessionen genom klicka på stoppknappen eller tryck på **SKIFT + F5**. I VS Code kommandopaletten, och Skriv Välj **Azure IoT Edge: stoppa IoT Edge-simulatorn**.

> [!NOTE]
> I föregående exempel visar hur du felsöker Java IoT Edge-moduler i behållare. Det har lagts till portar i din modul behållare createOptions. När du är klar med felsökning av Java-moduler, rekommenderar vi att du tar bort dessa portar för produktionsklara IoT Edge-moduler.

## <a name="next-steps"></a>Nästa steg

När du har din modul bygger lär du dig hur du [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md)

Att utveckla moduler för dina IoT Edge-enheter, [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
