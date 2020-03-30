---
title: Självstudiekurs - Utveckla modul för Linux-enheter med Azure IoT Edge
description: Den här självstudien går igenom att konfigurera din utvecklingsmaskin och molnresurser för att utveckla IoT Edge-moduler med Linux-behållare för Linux-enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 478d9c0485125870f8d5ffb4132f46476b4bb4ef
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384372"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Självstudiekurs: Utveckla IoT Edge-moduler för Linux-enheter

Använd Visual Studio Code för att utveckla och distribuera kod till Linux-enheter som kör IoT Edge.

I snabbstarten skapade du en IoT Edge-enhet med en virtuell Linux-dator och distribuerade en modul från Azure Marketplace. Den här självstudien går igenom att utveckla och distribuera din egen kod till en IoT Edge-enhet. Den här artikeln är en användbar förutsättning för andra självstudier, som går in mer i detalj om specifika programmeringsspråk eller Azure-tjänster.

Den här självstudien använder exemplet med att distribuera en **C#-modul till en Linux-enhet**. Det här exemplet valdes eftersom det är det vanligaste utvecklarscenariot för IoT Edge-lösningar. Även om du planerar att använda ett annat språk eller distribuera en Azure-tjänst, är den här självstudien fortfarande användbar för att lära dig mer om utvecklingsverktyg och koncept. Slutför den här introduktionen till utvecklingsprocessen och välj sedan önskat språk eller Azure-tjänst för att fördjupa dig i informationen.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Ställ in din utvecklingsmaskin.
> * Använd IoT Edge-verktygen för Visual Studio Code för att skapa ett nytt projekt.
> * Skapa ditt projekt som en behållare och lagra det i ett Azure-behållarregister.
> * Distribuera koden till en IoT Edge-enhet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Viktiga begrepp

Den här självstudien går igenom utvecklingen av en IoT Edge-modul. En *IoT Edge-modul*, eller ibland bara *modul* för kort, är en behållare med körbar kod. Du kan distribuera en eller flera moduler till en IoT Edge-enhet. Moduler utför specifika uppgifter som att inta data från sensorer, rensa och analysera data eller skicka meddelanden till en IoT-hubb. Mer information finns i [Förstå Azure IoT Edge-moduler](iot-edge-modules.md).

När du utvecklar IoT Edge-moduler är det viktigt att förstå skillnaden mellan utvecklingsmaskinen och mål-IoT Edge-enheten där modulen så småningom kommer att distribueras. Behållaren som du skapar för att hålla modulkoden måste matcha operativsystemet (OS) för *målenheten*. Det vanligaste scenariot är till exempel att någon utvecklar en modul på en Windows-dator som avser att rikta in sig på en Linux-enhet som kör IoT Edge. I så fall skulle behållaroperativsystemet vara Linux. När du går igenom den här guiden, tänk på skillnaden mellan *utveckling maskin OS* och *behållaren OS*.

Den här självstudien riktar sig till Linux-enheter som kör IoT Edge. Du kan använda ditt önskade operativsystem så länge din utvecklingsdator kör Linux-behållare. Vi rekommenderar att du använder Visual Studio Code för att utveckla för Linux-enheter, så det är vad den här självstudien kommer att använda. Du kan också använda Visual Studio, även om det finns skillnader i stöd mellan de två verktygen.

I följande tabell visas de utvecklingsscenarier som stöds för **Linux-behållare** i Visual Studio Code och Visual Studio.

|   | Visual Studio-koden | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux-enhetsarkitektur** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure-tjänster** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Språk** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Läs mer** | [Azure IoT Edge för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-verktyg för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge-verktyg för Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Stöd för Linux ARM64-enheter är tillgängligt i [offentlig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Mer information finns i [Utveckla och felsöka ARM64 IoT Edge-moduler i Visual Studio Code (förhandsversion).](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)

Den här självstudien lär ut utvecklingsstegen för Visual Studio Code. Om du hellre vill använda Visual Studio läser du instruktionerna i [Använd Visual Studio 2019 för att utveckla och felsöka moduler för Azure IoT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Krav

En utvecklingsmaskin:

* Du kan använda din egen dator eller en virtuell dator, beroende på dina utvecklingsinställningar.
  * Kontrollera att utvecklingsdatorn stöder kapslad virtualisering. Den här funktionen är nödvändig för att köra en behållarmotor, som du installerar i nästa avsnitt.
* De flesta operativsystem som kan köra en behållarmotor kan användas för att utveckla IoT Edge-moduler för Linux-enheter. Den här självstudien använder en Windows-dator, men påpekar kända skillnader på MacOS eller Linux.
* Installera [Git](https://git-scm.com/)för att hämta modulmallspaket senare i den här självstudien.  
* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

En Azure IoT Edge-enhet på Linux:

* Vi rekommenderar att du inte kör IoT Edge på utvecklingsdatorn, utan i stället använder en separat enhet. Den här skillnaden mellan utvecklingsmaskin och IoT Edge-enhet speglar mer exakt ett verkligt distributionsscenario och hjälper till att hålla de olika begreppen raka.
* Om du inte har en andra enhet tillgänglig använder du snabbstartsartikeln för att skapa en IoT Edge-enhet i Azure med en [virtuell Linux-dator](quickstart-linux.md).

Molnresurser:

* En kostnadsfri [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) på eller på standardnivå i Azure.

## <a name="install-container-engine"></a>Installera behållarmotor

IoT Edge-moduler är förpackade som behållare, så du behöver en behållarmotor på din utvecklingsmaskin för att bygga och hantera dem. Vi rekommenderar Docker Desktop för utveckling på grund av dess funktionsstöd och popularitet. Med Docker Desktop i Windows kan du växla mellan Linux-behållare och Windows-behållare så att du enkelt kan utveckla moduler för olika typer av IoT Edge-enheter.

Använd Docker-dokumentationen för att installera på utvecklingsmaskinen:

* [Installera Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)

  * När du installerar Docker Desktop för Windows tillfrågas du om du vill använda Linux- eller Windows-behållare. Detta beslut kan ändras när som helst med hjälp av en enkel switch. För den här guiden använder vi Linux-behållare eftersom våra moduler riktar sig till Linux-enheter. Mer information finns i [Växla mellan Windows- och Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Installera Docker Desktop för Mac](https://docs.docker.com/docker-for-mac/install/)

* Läs [Om Docker CE](https://docs.docker.com/install/) för installationsinformation på flera Linux-plattformar.
  * För Windows Subsystem for Linux (WSL) installerar du Docker Desktop för Windows.

## <a name="set-up-vs-code-and-tools"></a>Konfigurera VS-kod och verktyg

Använd IoT-tilläggen för Visual Studio Code för att utveckla IoT Edge-moduler. Dessa tillägg innehåller projektmallar, automatiserar skapandet av distributionsmanifestet och gör att du kan övervaka och hantera IoT Edge-enheter. I det här avsnittet installerar du Visual Studio Code och IoT-tillägget och konfigurerar sedan ditt Azure-konto för att hantera IoT Hub-resurser från Visual Studio Code.

1. Installera [Visual Studio-kod](https://code.visualstudio.com/) på din utvecklingsmaskin.

2. När installationen är klar väljer du **Visa** > **tillägg**.

3. Sök efter **Azure IoT Tools**, som faktiskt är en samling tillägg som hjälper dig att interagera med IoT Hub och IoT-enheter, samt utveckla IoT Edge-moduler.

4. Välj **Installera**. Varje inkluderad tillägg installeras individuellt.

5. När tilläggen är klara med installationen öppnar du kommandopaletten genom att välja **Visa** > **kommandopalett**.

6. Sök efter och välj **Azure: Logga in i**kommandopaletten. Följ anvisningarna för att logga in på ditt Azure-konto.

7. Sök efter och välj **Azure IoT Hub**i kommandopaletten igen. Följ anvisningarna för att välja din Azure-prenumeration och IoT-hubb.

8. Öppna utforskaravsnittet i Visual Studio-kod genom att antingen välja ikonen i aktivitetsfältet till vänster eller genom att välja **Visa** > **Utforskaren**.

9. Expandera menyn **Azure IoT Hub Devices** längst ned i utforskaren. Du bör se de enheter och IoT Edge-enheter som är associerade med IoT-hubben som du valde via kommandopaletten.

   ![Visa enheter i din IoT-hubb](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Skapa ett nytt modulprojekt

Azure IoT Tools-tillägget innehåller projektmallar för alla IoT Edge-modulspråk som stöds i Visual Studio Code. Dessa mallar har alla filer och kod som du behöver för att distribuera en fungerande modul för att testa IoT Edge, eller ge dig en utgångspunkt för att anpassa mallen med din egen affärslogik.

För den här självstudien använder vi C#-modulmallen eftersom det är den vanligaste mallen.

### <a name="create-a-project-template"></a>Skapa en projektmall

Sök efter och välj **Azure IoT Edge: Ny IoT Edge-lösning**i kommandopaletten Visual Studio-kod . Följ anvisningarna och använd följande värden för att skapa din lösning:

   | Field | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **C#-modul**. |
   | Ange ett modulnamn | Acceptera standard **samplemodule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br> Den slutliga avbildningsdatabasen ser ut som \<registernamnet\>.azurecr.io/samplemodule. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-develop-for-linux/image-repository.png)

När den nya lösningen har läses in i Visual Studio-kodfönstret kan du bekanta dig med de filer som skapas:

* **.vscode-mappen** innehåller en fil som heter **launch.json**, som används för felsökning av moduler.
* **Modulens** mapp innehåller en mapp för varje modul i din lösning. Just nu bör det bara vara **SampleModule**, eller vad namn du gav till modulen. Mappen SampleModule innehåller huvudprogramkoden, modulens metadata och flera Docker-filer.
* **.env-filen** innehåller autentiseringsuppgifterna till behållarregistret. Dessa autentiseringsuppgifter delas med din IoT Edge-enhet så att den har åtkomst för att hämta behållaravbildningarna.
* **Filen deployment.debug.template.json** och **deployment.template.json** är mallar som hjälper dig att skapa ett distributionsmanifest. Ett *distributionsmanifest* är en fil som definierar exakt vilka moduler du vill ha distribueras på en enhet, hur de ska konfigureras och hur de kan kommunicera med varandra och molnet. Mallfilerna använder pekare för vissa värden. När du omvandlar mallen till ett riktigt distributionsmanifest ersätts pekarna med värden som hämtas från andra lösningsfiler. Leta upp de två vanliga platshållarna i distributionsmallen:

  * I avsnittet registerautentiseringsuppgifter fylls adressen automatiskt från den information du angav när du skapade lösningen. Användarnamnet och lösenordet refererar dock till de variabler som lagras i .env-filen. Den här konfigurationen är avsedd för säkerhet, eftersom .env-filen ignoreras, men distributionsmallen är det inte.
  * I avsnittet SampleModule fylls inte behållaravbildningen i även om du angav avbildningsdatabasen när du skapade lösningen. Den här platshållaren pekar på **module.json-filen** i mappen SampleModule. Om du går till filen ser du att bildfältet innehåller databasen, men också ett taggvärde som består av versionen och plattformen för behållaren. Du kan iterera versionen manuellt som en del av din utvecklingscykel, och du väljer behållarplattformen med hjälp av en växlare som vi introducerar senare i det här avsnittet.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Ange registerautentiseringsuppgifter till IoT Edge-agenten

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta behållaravbildningarna till IoT Edge-enheten.

IoT Edge-tillägget försöker hämta dina autentiseringsuppgifter för behållarregister från Azure och fylla dem i miljöfilen. Kontrollera om dina autentiseringsuppgifter redan finns med. Om inte, lägg till dem nu:

1. Öppna **.env-filen** i modullösningen.
2. Lägg till de **användarnamn** och **lösenordsvärden** som du kopierade från ditt Azure-behållarregister.
3. Spara ändringarna i .env-filen.

### <a name="select-your-target-architecture"></a>Välj målarkitektur

För närvarande kan Visual Studio Code utveckla C#-moduler för Linux AMD64- och ARM32v7-enheter. Du måste välja vilken arkitektur du riktar dig till med varje lösning, eftersom det påverkar hur behållaren byggs och körs. Standard är Linux AMD64.

1. Öppna kommandopaletten och sök efter **Azure IoT Edge: Ange standardmålplattform för edge-lösning**eller välj genvägsikonen i sidofältet längst ned i fönstret.

   ![Välj arkitekturikon i sidofältet](./media/tutorial-develop-for-linux/select-architecture.png)

2. I kommandopaletten väljer du målarkitekturen i listan med alternativ. För den här guiden använder vi en Ubuntu virtuell maskin som IoT Edge-enhet, så kommer att behålla standard **amd64**.

### <a name="review-the-sample-code"></a>Granska exempelkoden

Lösningsmallen som du skapade innehåller exempelkod för en IoT Edge-modul. Den här exempelmodulen tar helt enkelt emot meddelanden och skickar dem sedan vidare. Pipeline-funktionen visar ett viktigt koncept i IoT Edge, vilket är hur moduler kommunicerar med varandra.

Varje modul kan ha flera *in-* och *utdataköer* deklarerade i sin kod. IoT Edge-hubben som körs på enheten dirigerar meddelanden från utdata från en modul till indata från en eller flera moduler. Det specifika språket för att deklarera indata och utdata varierar mellan språk, men konceptet är detsamma för alla moduler. Mer information om routning mellan moduler finns i [Deklarera vägar](module-composition.md#declare-routes).

Exempelkoden C#som medföljer projektmallen använder [klassen ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) från IoT Hub SDK för .NET.

1. Öppna **Program.cs-filen,** som finns i **mappen modulen/SampleModule/.**

2. Leta reda på metoden **SetInputMessageHandlerAsync** i program.cs.

3. Metoden [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) ställer in en indatakö för att ta emot inkommande meddelanden. Granska den här metoden och se hur den initierar en indatakö som kallas **input1**.

   ![Hitta indatanamnet i SetInputMessageCallback-konstruktorn](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Leta sedan reda på metoden **SendEventAsync.**

5. [Metoden SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) bearbetar mottagna meddelanden och ställer in en utdatakö för att skicka dem vidare. Granska den här metoden och se till att den initierar en utdatakö som kallas **output1**.

   ![Hitta utdatanamnet i SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Öppna **filen deployment.template.json.**

7. Leta reda på egenskapen **moduler** för de $edgeAgent önskade egenskaperna.

   Det bör finnas två moduler som anges här. Den första är **SimulatedTemperatureSensor**, som ingår i alla mallar som standard för att tillhandahålla simulerade temperaturdata som du kan använda för att testa dina moduler. Den andra är **den SampleModule-modul** som du skapade som en del av den här lösningen.

8. Leta reda på de önskade egenskaperna för **modulen $edgeHub** längst ned i filen.

   En av funktionerna i IoT Edge-hubbmodulen är att dirigera meddelanden mellan alla moduler i en distribution. Granska värdena i egenskapen **routes.** Den första vägen, **SampleModuleToIoTHub**, använder**\*** ett jokertecken ( ) för att ange alla meddelanden som kommer från alla utdataköer i SampleModule-modulen. Dessa meddelanden går till *$upstream*, vilket är ett reserverat namn som anger IoT Hub. Den andra vägen, sensorToSampleModule, tar meddelanden som kommer från modulen SimuleradTemperatureSensor och dirigerar dem till *indata1-indatakön* som du såg initieras i SampleModule-koden.

   ![Granska vägar i deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Skapa och push-överföra lösningen

Du har granskat modulkoden och distributionsmallen för att förstå några viktiga distributionsbegrepp. Nu är du redo att skapa SampleModule-behållaravbildningen och skicka den till behållarregistret. Med IoT-verktygstillägget för Visual Studio Code genererar det här steget också distributionsmanifestet baserat på informationen i mallfilen och modulinformationen från lösningsfilerna.

### <a name="sign-in-to-docker"></a>Logga in på Docker

Ange autentiseringsuppgifterna för behållarregistret till Docker så att den kan skicka behållaravbildningen så att den lagras i registret.

1. Öppna den integrerade terminalen för Visual Studio-kod genom att välja **Visa** > **terminal**.

2. Logga in på Docker med azure-behållarregisterautentiseringsuppgifter som du sparade när du skapade registret.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhetsvarning `--password-stdin`som rekommenderar användning av . Även om bästa praxis rekommenderas för produktionsscenarier, ligger den utanför den här självstudiens omfattning. Mer information finns i [inloggningsreferensen för docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Bygg och tryck

Visual Studio Code har nu åtkomst till behållarregistret, så det är dags att omvandla lösningskoden till en behållaravbildning.

1. Högerklicka på filen **deployment.template.json** i Utforskaren för Visual Studio-kod och välj **Bygg och Push IoT Edge-lösning**.

   ![Skapa och pushA IoT Edge-moduler](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Kommandot Bygg och push startar tre åtgärder. Först skapas en ny mapp i lösningen som kallas **config** som innehåller hela distributionsmanifestet, inbyggt av information i distributionsmallen och andra lösningsfiler. För det `docker build` andra körs den för att skapa behållaravbildningen baserat på lämplig dockerfile för din målarkitektur. Sedan körs `docker push` det för att skicka avbildningsdatabasen till behållarregistret.

   Den här processen kan ta flera minuter första gången, men är snabbare nästa gång du kör kommandona.

2. Öppna **filen deployment.amd64.json** i den nyskapade config-mappen. Filnamnet återspeglar målarkitekturen, så det blir annorlunda om du väljer en annan arkitektur.

3. Observera att de två parametrar som hade platshållare nu är ifyllda med sina rätta värden. **Avsnittet RegistryCredentials** har ditt register användarnamn och lösenord dras från .env-filen. **SampleModule** har hela bilddatabasen med namn-, versions- och arkitekturtaggen från module.json-filen.

4. Öppna **module.json-filen** i mappen SampleModule.

5. Ändra versionsnumret för modulbilden. (Versionen, inte $schema-versionen.) Till exempel öka korrigeringsnumret till **0.0.2** som om vi hade gjort en liten fix i modulkoden.

   >[!TIP]
   >Modulversioner aktiverar versionskontroll och låter dig testa ändringar på en liten uppsättning enheter innan du distribuerar uppdateringar till produktion. Om du inte ökar modulversionen innan du skapar och trycker, skriver du över databasen i behållarregistret.

6. Spara ändringarna i module.json-filen.

7. Högerklicka på **filen deployment.template.json** igen och välj återigen **Bygg och push IoT Edge-lösning**.

8. Öppna **filen deployment.amd64.json** igen. Observera att en ny fil inte skapades när du körde kommandot build and push igen. I stället uppdaterades samma fil för att återspegla ändringarna. SampleModule-avbildningen pekar nu på 0.0.2-versionen av behållaren.

9. Om du vill ytterligare kontrollera vad bygg- och push-kommandot gjorde går du till [Azure-portalen](https://portal.azure.com) och navigerar till behållarregistret.

10. I behållarregistret väljer du **Databaser** och sedan **samplemodule**. Kontrollera att båda versionerna av avbildningen har pressats till registret.

    ![Visa båda avbildningsversionerna i behållarregistret](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Felsöka

Om du stöter på fel när du skapar och trycker på modulavbildningen har det ofta att göra med Docker-konfigurationen på utvecklingsmaskinen. Använd följande kontroller för att granska konfigurationen:

* Körde du `docker login` kommandot med de autentiseringsuppgifter som du kopierade från behållarregistret? Dessa autentiseringsuppgifter skiljer sig från de som du använder för att logga in på Azure.
* Stämmer containerlagringsplatsen? Har den rätt container registernamn och rätt modulnamn? Öppna **module.json-filen** i mappen SampleModule för att kontrollera. Databasvärdet ska se ut som ** \<\>registernamnet .azurecr.io/samplemodule**.
* Om du har använt ett annat namn än **SampleModule** för din modul, är det namnet konsekvent i hela lösningen?
* Kör din maskin samma typ av behållare som du bygger? Denna handledning är för Linux IoT Edge-enheter, så Visual Studio Code bör säga **amd64** eller **arm32v7** i sidofältet, och Docker Desktop bör köra Linux-behållare.  

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enheten

Du har verifierat att de inbyggda behållaravbildningarna lagras i behållarregistret, så det är dags att distribuera dem till en enhet. Kontrollera att din IoT Edge-enhet är igång.

1. Expandera avsnittet Azure IoT Hub Devices i Utforskaren för Visual Studio-kod.

2. Högerklicka på den IoT Edge-enhet som du vill distribuera till och välj sedan **Skapa distribution för en enhet**.

   ![Skapa distribution för en enskild enhet](./media/tutorial-develop-for-linux/create-deployment.png)

3. I utforskaren navigerar du till **konfigurationsmappen** och väljer sedan filen **deployment.amd64.json.**

   Använd inte filen deployment.template.json, som inte har behållarregisterautentiseringsuppgifterna eller modulens avbildningsvärden i den. Om du riktar in dig på en Linux ARM32-enhet kommer distributionsmanifestet att heta deployment.arm32v7.json.

4. Expandera informationen för din IoT Edge-enhet och expandera sedan listan **Moduler** för enheten.

5. Använd uppdateringsknappen för att uppdatera enhetsvyn tills modulerna SimulatedTemperatureSensor och SampleModule körs på enheten.

   Det kan ta några minuter för båda modulerna att starta. IoT Edge-körningen måste ta emot sitt nya distributionsmanifest, dra ner modulavbildningarna från behållarkörningen och starta sedan varje ny modul.

   ![Visa moduler som körs på din IoT Edge-enhet](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Visa meddelanden från enheten

SampleModule-koden tar emot meddelanden via indatakön och skickar dem vidare via utdatakön. Distributionsmanifestet deklarerade vägar som skickade meddelanden till SampleModule från SimulatedTemperatureSensor och vidarebefordrade sedan meddelanden från SampleModule till IoT Hub. Azure IoT-verktygen för Visual Studio-kod gör att du kan se meddelanden när de anländer till IoT Hub från dina enskilda enheter.

1. Högerklicka på den IoT Edge-enhet som du vill övervaka i Utforskaren av Visual Studio-kod och välj sedan **Starta övervakningsenad händelseslutpunkt**.

2. Titta på utdatafönstret i Visual Studio-koden för att se meddelanden som kommer till din IoT-hubb.

   ![Visa inkommande enhet till molnmeddelanden](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Visa ändringar på enheten

Om du vill se vad som händer på själva enheten använder du kommandona i det här avsnittet för att granska IoT Edge-körningen och moduler som körs på enheten.

Kommandona i det här avsnittet gäller för din IoT Edge-enhet, inte din utvecklingsmaskin. Om du använder en virtuell dator för din IoT Edge-enhet ansluter du till den nu. I Azure går du till den virtuella datorns översiktssida och väljer **Anslut** för att komma åt den säkra skalanslutningen.

* Visa alla moduler som har distribuerats till enheten och kontrollera deras status:

   ```bash
   iotedge list
   ```

   Du bör se fyra moduler: de två IoT Edge-runtimemodulerna, SimulatedTemperatureSensor och SampleModule. Alla fyra ska anges som igång.

* Kontrollera loggarna för en viss modul:

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge-moduler är skiftlägeskänsliga.

   Loggarna SimulatedTemperatureSensor och SampleModule ska visa de meddelanden som de bearbetar. EdgeAgent-modulen ansvarar för att starta de andra modulerna, så dess loggar kommer att ha information om hur du implementerar distributionsmanifestet. Om någon modul inte finns med i listan eller inte körs har edgeAgent-loggarna förmodligen felen. EdgeHub-modulen ansvarar för kommunikation mellan modulerna och IoT Hub. Om modulerna är igång, men meddelandena inte kommer till din IoT-hubb, kommer edgeHub-loggarna förmodligen att ha felen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du Visual Studio-kod på utvecklingsmaskinen och distribuerade din första IoT Edge-modul från den. Nu när du känner till de grundläggande begreppen kan du prova att lägga till funktioner i en modul så att den kan analysera data som passerar genom den. Välj önskat språk:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)
