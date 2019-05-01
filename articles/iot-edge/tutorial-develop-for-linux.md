---
title: Utveckla modell för Linux-enheter – Azure IoT Edge | Microsoft Docs
description: Den här självstudiekursen beskriver hur du konfigurerar din Utvecklingsresurser för dator och molnet att utveckla IoT Edge-moduler med Linux-behållare för Linux-enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: e5b34171af53a72ef231bb8beb758b7ff97cf5d9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576784"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Självstudier: Utveckla IoT Edge-moduler för Linux-enheter

Använd Visual Studio Code för att utveckla och distribuera kod till Linux-enheter som kör IoT Edge. 

I artiklarna i snabbstarten skapade du en IoT Edge-enhet med en Linux-dator och distribuerat en färdiga modul från Azure Marketplace. Den här självstudiekursen beskriver vad som krävs för att utveckla och distribuera din egen kod till en IoT Edge-enhet. Den här självstudien är en användbar förutsättning för alla andra självstudier, som anger att mer information om specifika programmeringsspråk eller Azure-tjänster. 

Den här självstudien används ett exempel på distribution av en **C-modul till en Linux-enhet**. Det här exemplet valdes eftersom den har minst antal förutsättningar, så att du kan lära dig om utvecklingsverktygen utan att behöva bekymra dig om du har rätt bibliotek som är installerade. När du förstår begreppen som utveckling, kan du välja din det språk du föredrar eller Azure-tjänst som fördjupar. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ställ in din utvecklingsdator.
> * Använd IoT Edge-verktyg för Visual Studio Code för att skapa ett nytt projekt.
> * Skapa ditt projekt som en behållare och lagra den i ett Azure container registry.
> * Distribuera din kod till en IoT Edge-enhet. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Viktiga begrepp

Den här självstudiekursen beskriver utvecklingen av IoT Edge-modul. En *IoT Edge-modul*, eller bara ibland *modulen* för kort, är en behållare som innehåller körbar kod. Du kan distribuera en eller flera moduler till en IoT Edge-enhet. Moduler utför specifika uppgifter, t.ex. mata in data från sensorer, utföra dataanalys eller data rengöringen eller skicka meddelanden till en IoT-hubb. Mer information finns i [förstå Azure IoT Edge-moduler](iot-edge-modules.md).

När du utvecklar IoT Edge-moduler, är det viktigt att förstå skillnaden mellan utvecklingssystemet och målet IoT Edge-enhet där modulen så småningom ska distribueras. Behållaren som du skapar för att lagra din modul-kod måste matcha operativsystemet (OS) på den *målenheten*. Till exempel är det vanligaste scenariot någon utveckla en modul på en Windows-dator som har för avsikt att fokusera på en Linux-enhet som kör IoT Edge. I så fall är behållare operativsystemet Linux. När du går igenom den här kursen, Tänk på skillnaden mellan den *utvecklingsdator OS* och *behållare OS*.

Den här kursen riktar sig till Linux-enheter som kör IoT Edge. Du kan använda operativsystemet lämpliga utvecklings datorn så länge utvecklingsdatorn kan köra Linux-behållare. Vi rekommenderar att du använder Visual Studio Code för att utveckla för Linux enheter, så det är den här självstudien använder. Du kan använda Visual Studio 2017, även om det finns skillnader i stöd mellan de två verktyg.

I följande tabell visas stöds utvecklingsscenarier för **Linux-behållare** i Visual Studio Code och Visual Studio 2017.

|   | Visual Studio-koden | Visual Studio 2017 |
| - | ------------------ | ------------------ |
| **Arkitektur för Linux-enhet** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure-tjänster** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Språk** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Mer information** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/itemdetails?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-verktyg för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) |

Den här självstudien Lär hur utveckling för Visual Studio Code. Om du föredrar att använda Visual Studio 2017, läser du anvisningarna i [använda Visual Studio 2017 du utvecklar och felsöker moduler för Azure IoT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

En utvecklingsdator:

* Du kan använda din egen dator eller en virtuell dator, beroende på dina inställningar för utveckling.
* De flesta operativsystem som kan köra en behållare motor kan användas för att utveckla IoT Edge-moduler för Linux-enheter. Den här självstudien använder en Windows-dator, men den pekar på kända skillnader på MacOS eller Linux. 
* Installera [Git](https://git-scm.com/), för att hämta modulen mall paket senare i den här självstudien.  

En Azure IoT Edge-enhet i Linux:

* Vi rekommenderar att du inte kör IoT Edge på utvecklingsdatorn, men i stället använda en separat enhet. Denna skillnad mellan utvecklingssystemet och IoT Edge-enhet mer korrekt speglar en SANT distributionsscenariot och hjälper till att hålla de olika begreppen direkt.
* Om du inte har en andra enhet som är tillgängliga kan använda snabbstartsartikeln för att skapa en IoT Edge-enhet i Azure med en [Linux-dator](quickstart-linux.md).

Molnresurser:

* En kostnadsfri eller standard-nivån [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure. 

## <a name="install-container-engine"></a>Installationsprogram för behållare

IoT Edge-moduler är paketerade som behållare, så du behöver en motor för behållare på en utvecklingsdator för att skapa och hantera behållare. Vi rekommenderar att du använder Docker Desktop för utveckling på grund av dess många funktioner och dess popularitet som en behållare. Med Docker Desktop på en Windows-enhet kan växla du mellan Linux-behållare och Windows-behållare så att du enkelt kan utveckla moduler för olika typer av IoT Edge-enheter. 

Använd Docker-dokumentation för att installera på utvecklingsdatorn: 

* [Installera Docker Desktop för Windows](https://docs.docker.com/docker-for-windows/install/)

  * När du installerar Docker Desktop för Windows, blir du tillfrågad om du vill använda Linux eller Windows-behållare. Det här beslutet kan ändras när som helst med hjälp av en enkel växel. I den här självstudien använder vi Linux-behållare eftersom vår moduler riktar in sig på Linux-enheter. Mer information finns i [växla mellan Windows och Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Installera Docker Desktop för Mac](https://docs.docker.com/docker-for-mac/install/)

* Läs [om Docker CE](https://docs.docker.com/install/) installationsinformation på flera Linux-plattformar.

## <a name="set-up-vs-code-and-tools"></a>Konfigurera VS Code och verktyg

Använd IoT-tillägg för Visual Studio Code för att utveckla IoT Edge-moduler. Dessa tillägg ger projektmallar, automatisera skapandet av manifestet distributionen och gör att du kan övervaka och hantera IoT Edge-enheter. I det här avsnittet ska du installera Visual Studio Code och IoT-tillägget och sedan ställa in ditt Azure-konto för att hantera IoT Hub-resurser från Visual Studio Code. 

1. Installera [Visual Studio Code](https://code.visualstudio.com/) på utvecklingsdatorn. 

2. När installationen är klar, väljer **visa** > **tillägg**. 

3. Sök efter **Azure IoT Tools**, vilket är faktiskt en uppsättning tillägg som hjälper dig att interagera med IoT Hub och IoT-enheter, samt utveckla IoT Edge-moduler. 

4. Välj **Installera**. Varje inkluderade tillägget installeras separat. 

5. När tillägg som är klar installerar, öppna kommandopaletten genom att välja **visa** > **Kommandopaletten**. 

6. I kommandopaletten, Sök efter och välj **Azure: Logga in**. Följ anvisningarna för att logga in på ditt Azure-konto. 

7. I kommandopaletten igen och Sök efter och välj **Azure IoT Hub: Välj IoT-hubb**. Följ anvisningarna för att välja din Azure-prenumeration och IoT hub. 

7. Öppna i explorer-avsnittet i Visual Studio Code genom att antingen välja ikonen i Aktivitetsfältet till vänster eller genom att **visa** > **Explorer**. 

8. Längst ned i avsnittet explorer expanderar du den komprimerade **Azure IoT Hub-enheter** menyn. Du bör se vilka enheter och IoT Edge-enheter som är associerade med IoT-hubben som du har valt via kommandopaletten. 

   ![Visa enheter i IoT hub](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Skapa ett nytt projekt i modulen

Verktyg för Azure IoT-tillägget innehåller projektmallar för alla stöds IoT Edge modulen språk i Visual Studio Code. Dessa mallar kan alla filer och kod som du behöver distribuera en fungerande-modul för att testa IoT Edge, eller ge dig en utgångspunkt för att anpassa mallen med egen affärslogik. 

Den här självstudien används mallen C-modulen eftersom den har minst antal krav för att installera. 

### <a name="create-a-project-template"></a>Skapa en projektmall

Sök efter i kommandopaletten Visual Studio Code och välj **Azure IoT Edge: Ny IoT Edge-lösning**. Följ anvisningarna och Använd följande värden för att skapa din lösning: 

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **C-modul**. |
   | Ange ett modulnamn | Acceptera standardvärdet **SampleModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br> Sista avbildningslagringsplatsen ut \<registernamn\>.azurecr.io/samplemodule. |
 
   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-develop-for-linux/image-repository.png)

När den nya lösningen har lästs in i Visual Studio Code-fönstret kan du ta en stund att bekanta dig med de filer som den skapats: 

* Den **.vscode** mappen innehåller en fil med namnet **launch.json**, som används för felsökning av moduler.
* Den **moduler** mappen innehåller en mapp för varje modul i din lösning. Just nu är som bara ska vara **SampleModule**, eller det namn du gav till modulen. Mappen SampleModule innehåller huvudsakliga programkoden, modul-metadata och flera Docker-filer. 
* Den **.env** filen innehåller autentiseringsuppgifterna på ditt behållarregister. Dessa autentiseringsuppgifter delas med din IoT Edge-enhet så att den har åtkomst till hämta behållaravbildningarna. 
* Den **deployment.debug.template.json** fil och **deployment.template.json** fil finns mallar som hjälper dig att skapa ett manifest för distribution. En *distribution manifest* är en fil som definierar exakt vilka moduler som du vill distribuerad på en enhet, hur de ska konfigureras och hur de kan kommunicera med varandra och molnet. Mallfilerna använder pekare för vissa värden. När du omvandla mallen till ett sant distribution manifest ersätts till någon med värden från andra lösningsfiler. Leta upp de två vanliga platshållarna i mallen för distribution: 

  * Adressen är autofilled utifrån den information som du angav när du skapade lösningen under registry autentiseringsuppgifter. Men referera det användarnamn och lösenord till variabler som lagrats i .env-fil. Detta är för säkerhet, eftersom .env-fil är git ignoreras, men distributionsmallen för är inte. 
  * I avsnittet SampleModule är inte behållaravbildningen ifyllt även om du har angett avbildningslagringsplatsen när du har skapat lösningen. Den här platshållaren pekar på den **module.json** fil i mappen SampleModule. Om du går till filen visas att fältet avbildningen innehåller lagringsplatsen, utan även ett Taggvärde som består av versionen och plattformen för behållaren. Du kan iterera versionen manuellt som en del av din utvecklingscykeln och väljer du den behållarplattform som använder en mellan aktiviteter som vi introducerar senare i det här avsnittet. 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Ange dina autentiseringsuppgifter för registret till IoT Edge-agenten

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina behållaravbildningar till IoT Edge-enhet. 

IoT Edge-tillägget försöker hämta autentiseringsuppgifter för registret från Azure för din behållare och fylla dem i filen miljö. Kontrollera om dina autentiseringsuppgifter redan ingår. Annars kan du lägga till dem nu:

1. Öppna den **.env** fil i din modul-lösning. 
2. Lägg till den **användarnamn** och **lösenord** värden som du kopierade från Azure container registry.
3. Spara dina ändringar i .env-fil. 

### <a name="select-your-target-architecture"></a>Välj din mål-arkitektur

Visual Studio Code kan för närvarande kan utveckla C-moduler för Linux AMD64- och Linux ARM32v7-enheter. Du måste välja vilken arkitektur som du med med varje lösning, eftersom det påverkar hur behållaren har skapats och körs. Standardvärdet är Linux AMD64. 

1. Öppna kommandopaletten och Sök efter **Azure IoT Edge: Ange standard målplattform för lösning**, eller klicka på genvägsikonen i Sidopanel längst ned i fönstret. 

   ![Välj ikonen för arkitekturen i Sidopanel](./media/tutorial-develop-for-linux/select-architecture.png)

2. Välj mål-arkitektur i kommandopaletten, från listan med alternativ. Den här självstudien använder vi en Ubuntu-dator som IoT Edge-enhet, så kommer behåller du standardvärdet **amd64**. 

### <a name="review-the-sample-code"></a>Granska exempelkoden

Den mall som du skapade innehåller exempelkod för en IoT Edge-modul. Det här exemplet modulen tar bara emot meddelanden och skickar dem på. Pipeline-funktionen visar ett viktigt begrepp i IoT Edge, vilket är hur moduler kommunicerar med varandra.

Varje modul kan ha flera *inkommande* och *utdata* köer som har deklarerats i sin kod. IoT Edge hub som körs på enheten skickar meddelanden från utdata från en modul i indata för en eller flera moduler. Det aktuella språket för deklarerar indata och utdata varierar mellan olika språk, men konceptet är detsamma för både alla moduler. Mer information om routning mellan moduler finns i [deklarera vägar](module-composition.md#declare-routes).

1. Öppna den **main.c** -fil som har tillgång till den **moduler/SampleModules/** mapp. 

2. IoT Hub C SDK använder funktionen [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback) att initiera modulen inkommande köer. Sök i main.c-filen för den funktionen.

3. Granska konstruktorn SetInputMessageCallback funktion och se att en indatakö heter **indata1** initieras i koden. 

   ![Hitta Indatanamnet i SetInputMessageCallback konstruktor](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Modulen utgående köer initieras på ett liknande sätt. Sök efter den [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync) funktion i filen main.c. 

5. Granska konstruktorn SendEventToOutputAsync funktion och se att en utgående kö heter **output1** initieras i koden. 

   ![Hitta utdatanamnet i SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Öppna den **deployment.template.json** fil.

7. Hitta den **moduler** egenskapen för $edgeAgent önskade egenskaper. 

   Det bör finnas två moduler som anges här. Först är **tempSensor**, som ingår i alla mallar som standard att tillhandahålla simulerade temperaturdata som du kan använda för att testa dina moduler. Andra är den **SampleModule** modul som du har skapat som en del av den här lösningen.

7. Längst ned i filen hittar du de önskade egenskaperna för den **$edgeHub** modulen. 

   En av funktionerna i IoT Edge hub-modulen är att dirigera meddelanden mellan alla moduler i en distribution. Granska värdena i den **vägar** egenskapen. Den första rutten **SampleModuleToIoTHub**, använder jokertecken (**\***) att visa alla meddelanden som kommer från alla utgående köer i modulen SampleModule. Dessa meddelanden går du till *$uppströms*, vilket är ett reserverat namn som anger IoT Hub. Den andra vägen sensorToSampleModule, tar meddelanden som kommer från modulen tempSensor och skickar dem till den *indata1* inkommande kö som du såg initieras i SampleModule koden. 

   ![Granska vägar i deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Skapa och push-överföra lösningen

Du har granskat kod som modulen och Distributionsmall att förstå vissa begrepp i samband med distributionen. Nu är du redo att skapa SampleModule behållaravbildning och push-överföra den till behållarregistret. Det här steget genererar även distribution manifestet baserat på informationen i mallfilen och modulinformation från lösningsfiler med tillägget för IoT-verktyg för Visual Studio Code. 

### <a name="sign-in-to-docker"></a>Logga in på Docker

Ange din behållare autentiseringsuppgifter för registret på Docker så att den kan skicka en behållaravbildning som ska lagras i registret. 

1. Öppna den integrerade Visual Studio Code-terminalen genom att välja **visa** > **Terminal**.

2. Logga in på Docker med Azure container registry-autentiseringsuppgifter som du sparade när du har skapat registret. 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du får en säkerhetsvarning som rekommenderar användning av `--password-stdin`. Även om den bästa metoden rekommenderas för produktionsscenarier, ligger utanför omfånget för den här självstudien. Mer information finns i den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referens.

### <a name="build-and-push"></a>Skapa och skicka 

Visual Studio Code har nu åtkomst till behållarregistret, så det är dags att aktivera lösningen koden i en behållaravbildning. 

1. I Visual Studio Code-Utforskaren högerklickar du på den **deployment.template.json** och väljer **Build and Push IoT Edge-lösning**. 

   ![Skapa och skicka IoT Edge-moduler](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Versions- och push-kommando startar tre åtgärder. Först skapar den en ny mapp i lösningen kallas **config** som innehåller fullständig distribution manifest, byggas ut information i distributionsmallen och andra lösningsfiler. Dessutom körs `docker build` att skapa behållaravbildningen baserat på dockerfile som är lämpliga för din mål-arkitektur. Sedan körs `docker push` att skicka lagringsplatsen för avbildningen till behållarregistret. 

   Den här processen kan ta flera minuter första gången, men är snabbare nästa gång du kör kommandona. 

2. Öppna den **deployment.amd64.json** fil i nyligen skapade config-mappen. Filnamnet återspeglar den mål-arkitekturen, så att den påverkas om du väljer en annan arkitektur.

3. Observera att de två parametrarna som hade platshållare nu fylls i med sina rätt värden. Den **registryCredentials** avsnittet har ditt registreringsanvändarnamn och lösenord som hämtats från .env-fil. Den **SampleModule** har fullständig avbildningslagringsplatsen med taggen namn, version och arkitektur från filen module.json. 

4. Öppna den **module.json** filen i mappen SampleModule. 

5. Ändra det lägre versionsnumret för avbildningen modulen. (Versionen, inte $schema-version.) Till exempel öka versionsnumret patch till **0.0.2** som om vi hade gjort en liten korrigering i modulen koden. 

   >[!TIP]
   >Modulversioner aktivera versionskontroll och gör att du kan testa ändringar på en liten uppsättning enheter innan du distribuerar uppdateringar till produktion. Om du inte öka Modulversion innan du skapar och push-överföring, över du lagringsplatsen i ditt behållarregister. 

6. Spara dina ändringar i filen module.json.

7. Högerklicka på den **deployment.template.json** filen igen och väljer du återigen **Build and Push IoT Edge-lösning**.

8. Öppna den **deployment.amd64.json** filen igen. Observera att en ny fil inte har skapats när du körde versions- och push-kommandot igen. I stället har samma fil uppdaterats för att återspegla ändringarna. SampleModule bild pekar nu på 0.0.2 version av behållaren. 

9. Att ytterligare kontrollera det kommandot versions- och push gjorde, gå till den [Azure-portalen](https://portal.azure.com) och navigera till ditt behållarregister. 

10. Välj i ditt behållarregister **databaser** sedan **samplemodule**. Kontrollera att båda versionerna av avbildningen pushats till registret.

   ![Visa båda bild-versioner i container registry](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Felsöka

Om det uppstår problem när du bygger och push-överföra avbildningen modulen har den ofta att göra med Docker-konfigurationen på utvecklingsdatorn. Använd följande kontroller för att granska konfigurationen: 

* Stötte du den `docker login` kommando med hjälp av de autentiseringsuppgifter som du kopierade från ditt behållarregister? Dessa autentiseringsuppgifter skiljer sig från de som används för att logga in på Azure. 
* Stämmer containerlagringsplatsen? Har den din rätt namnet på behållarregistret och din rätt Modulnamn? Öppna den **module.json** filen i mappen SampleModule att kontrollera. Värdet för databasen bör se ut så  **\<registernamn\>.azurecr.io/samplemodule**. 
* Om du har använt ett annat namn än **SampleModule** för, är det namnet konsekvent i hela lösningen?
* Din dator kör samma typ av behållare som du skapar? Den här självstudien är för Linux IoT Edge-enheter så att Visual Studio Code ska stå **amd64** eller **arm32v7** i sida-fältet och Docker Desktop bör köra Linux-behållare. C-moduler i Visual Studio Code har inte stöd för Windows-behållare. 

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Du har verifierat att de inbyggda behållaravbildningarna lagras på ditt behållarregister, så är det dags att distribuera dem till en enhet. Se till att din IoT Edge-enhet är igång. 

1. I Visual Studio Code-Utforskaren expanderar du avsnittet Azure IoT Hub-enheter. 

2. Högerklicka på IoT Edge-enhet som du vill distribuera till och välj sedan **skapa distribution för enskild enhet**. 

   ![Skapa distribution för en enskild enhet](./media/tutorial-develop-for-linux/create-deployment.png)

3. I Utforskaren, går du till den **config** mapp väljer sedan den **deployment.amd64.json** fil. 

   Använd inte filen deployment.template.json, som inte har autentiseringsuppgifter för container-registret eller modulen värden i den. Om en enhet med Linux ARM32, namnges distribution manifestet deployment.arm32v7.json. 

4. Visa information för din IoT Edge-enhet och sedan expandera den **moduler** lista för din enhet. 

5. Använd uppdateringsknappen för att uppdatera i enhetsvyn tills du ser tempSensor och SampleModule moduler som körs på din enhet. 

   Det kan ta några minuter för båda modulerna att starta. IoT Edge-körningen behöver att ta emot nya distribution manifestet, hämta modulen bilder från behållaren runtime och starta sedan varje ny modul. 

   ![Visa moduler som körs på din IoT Edge-enhet](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Visa meddelanden från enhet

SampleModule koden tar emot meddelanden via dess inkommande kö och skickar dem via den utgående kön. Distribution av manifestet deklarerats vägar som skickas meddelanden till SampleModule från tempSensor och sedan vidarebefordras meddelanden från SampleModule till IoT Hub. Azure IoT-verktyg för Visual Studio Code kan du se meddelanden när de anländer till IoT Hub från dina enskilda enheter. 

1. I Visual Studio Code-Utforskaren högerklickar du på IoT Edge-enheten som du vill övervaka och väljer sedan **börja övervaka D2C-meddelande**. 

2. Se utdatafönstret i Visual Studio Code för att se meddelanden som inkommer på din IoT-hubb. 

   ![Visa inkommande enhet till moln-meddelanden](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Visa ändringar på enheten

Om du vill se vad som händer på din enhet själva kan du använda kommandon i det här avsnittet för att granska IoT Edge-körningen och moduler som körs på din enhet. 

Kommandona i det här avsnittet är för din IoT Edge-enhet, inte på din utvecklingsdator. Om du använder en virtuell dator för din IoT Edge-enhet kan ansluta till den nu. Gå till den virtuella datorns översiktssidan i Azure, och välj **Connect** att få åtkomst till SSH-anslutningen. 

* Visa alla moduler som har distribuerats till enheten och kontrollera deras status:

   ```bash
   iotedge list
   ```

   Du bör se fyra moduler: de två moduler för IoT Edge-körning och tempSensor SampleModule. Alla fyra bör anges som körs.

* Granska loggarna för en specifik modul:

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge-moduler är skiftlägeskänsliga. 

   TempSensor och SamplModule loggar ska visa meddelanden som de bearbetning. Modulen edgeAgent ansvarar för att starta andra moduler så att loggar har information om hur du implementerar distribution manifestet. Om alla moduler visas inte i listan eller inte körs, har förmodligen edgeAgent loggarna felen. Modulen edgeHub ansvarar för kommunikationen mellan moduler och IoT Hub. Om modulerna som är igång och körs, men meddelanden inte anländer till din IoT-hubb, har förmodligen edgeHub loggarna felen. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du ställer in Visual Studio Code på utvecklingsdatorn och distribuerat din första IoT Edge-modul från den. Nu när du vet de grundläggande begreppen kan du prova att lägga till funktioner till en modul så att den kan analysera informationen som passerar genom den. Välj det språk du föredrar: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)