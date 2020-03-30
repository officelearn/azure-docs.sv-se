---
title: Självstudiekurs - Utveckla modul för Windows-enheter med Azure IoT Edge
description: Den här självstudien går igenom att konfigurera utvecklingsmaskinen och molnresurserna för att utveckla IoT Edge-moduler med Windows-behållare för Windows-enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 58a63c9e11cf86318f0e9f051d034cbbaf7c40a9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76772255"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Självstudiekurs: Utveckla IoT Edge-moduler för Windows-enheter

Använd Visual Studio för att utveckla och distribuera kod till Windows-enheter som kör IoT Edge.

I snabbstarten skapade du en IoT Edge-enhet med en virtuell Windows-dator och distribuerade en färdigmodul från Azure Marketplace. Den här självstudien går igenom vad som krävs för att utveckla och distribuera din egen kod till en IoT Edge-enhet. Den här självstudien är en användbar förutsättning för andra självstudier, som går in mer i detalj om specifika programmeringsspråk eller Azure-tjänster.

Den här självstudien använder exemplet med att distribuera en **C#-modul till en Windows-enhet**. Det här exemplet valdes eftersom det är det vanligaste utvecklingsscenariot. Om du är intresserad av att utveckla på ett annat språk, eller planerar att distribuera Azure-tjänster som moduler, kommer den här självstudien fortfarande att vara användbar för att lära dig mer om utvecklingsverktygen. När du förstår utvecklingsbegreppen kan du välja önskat språk eller Azure-tjänst för att fördjupa dig i informationen.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Ställ in din utvecklingsmaskin.
> * Använd IoT Edge-verktygen för Visual Studio för att skapa ett nytt projekt.
> * Skapa ditt projekt som en behållare och lagra det i ett Azure-behållarregister.
> * Distribuera koden till en IoT Edge-enhet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Viktiga begrepp

Den här självstudien går igenom utvecklingen av en IoT Edge-modul. En *IoT Edge-modul*, eller ibland bara *modul* för kort, är en behållare som innehåller körbar kod. Du kan distribuera en eller flera moduler till en IoT Edge-enhet. Moduler utför specifika uppgifter som att inta data från sensorer, utföra dataanalys eller datarensning, eller skicka meddelanden till en IoT-hubb. Mer information finns i [Förstå Azure IoT Edge-moduler](iot-edge-modules.md).

När du utvecklar IoT Edge-moduler är det viktigt att förstå skillnaden mellan utvecklingsmaskinen och mål-IoT Edge-enheten där modulen så småningom kommer att distribueras. Behållaren som du skapar för att hålla modulkoden måste matcha operativsystemet (OS) för *målenheten*. För utveckling av Windows-behållare är det här konceptet enklare eftersom Windows-behållare endast körs på Windows-operativsystem. Men du kan till exempel använda din Windows-utvecklingsmaskin för att skapa moduler för Linux IoT Edge-enheter. I det scenariot måste du se till att din utvecklingsdator körde Linux-behållare. När du går igenom den här guiden, tänk på skillnaden mellan *utveckling maskin OS* och *behållaren OS*.

Den här självstudien riktar sig till Windows-enheter som kör IoT Edge. Windows IoT Edge-enheter använder Windows-behållare. Vi rekommenderar att du använder Visual Studio för att utveckla för Windows-enheter, så det är vad den här självstudien kommer att använda. Du kan också använda Visual Studio Code, även om det finns skillnader i stöd mellan de två verktygen.

I följande tabell visas de utvecklingsscenarier som stöds för **Windows-behållare** i Visual Studio Code och Visual Studio.

|   | Visual Studio-koden | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure-tjänster** | Azure Functions <br> Azure Stream Analytics |   |
| **Språk** | C# (felsökning stöds inte) | C <br> C# |
| **Läs mer** | [Azure IoT Edge för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-verktyg för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge-verktyg för Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Krav

En utvecklingsmaskin:

* Windows 10 med 1809 uppdatering eller nyare.
* Du kan använda din egen dator eller en virtuell dator, beroende på dina utvecklingsinställningar.
  * Kontrollera att utvecklingsdatorn stöder kapslad virtualisering. Den här funktionen är nödvändig för att köra en behållarmotor, som du installerar i nästa avsnitt.
* Installera [Git](https://git-scm.com/).

En Azure IoT Edge-enhet i Windows:

* Vi rekommenderar att du inte kör IoT Edge på utvecklingsdatorn, utan i stället använder en separat enhet. Den här skillnaden mellan utvecklingsmaskin och IoT Edge-enhet speglar mer exakt ett verkligt distributionsscenario och hjälper till att hålla de olika begreppen raka.
* Om du inte har en andra enhet tillgänglig använder du snabbstartsartikeln för att skapa en IoT Edge-enhet i Azure med en [virtuell Windows-dator](quickstart.md).

Molnresurser:

* En kostnadsfri [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) på eller på standardnivå i Azure.

## <a name="install-container-engine"></a>Installera behållarmotor

IoT Edge-moduler är förpackade som behållare, så du behöver en behållarmotor på din utvecklingsmaskin för att bygga och hantera behållarna. Vi rekommenderar att du använder Docker Desktop för utveckling på grund av dess många funktioner och popularitet som en containermotor. Med Docker Desktop på en Windows-dator kan du växla mellan Linux-behållare och Windows-behållare så att du enkelt kan utveckla moduler för olika typer av IoT Edge-enheter.

Använd Docker-dokumentationen för att installera på utvecklingsmaskinen:

* [Installera Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)

  * När du installerar Docker Desktop för Windows tillfrågas du om du vill använda Linux- eller Windows-behållare. För den här självstudien använder du **Windows-behållare**. Mer information finns i [Växla mellan Windows- och Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="set-up-visual-studio-and-tools"></a>Konfigurera Visual Studio och verktyg

IoT-tilläggen för Visual Studio hjälper dig att utveckla IoT Edge-moduler. Dessa tillägg innehåller projektmallar, automatiserar skapandet av distributionsmanifestet och gör att du kan övervaka och hantera IoT Edge-enheter. I det här avsnittet installerar du Visual Studio och IoT Edge-tillägget och konfigurerar sedan ditt Azure-konto för att hantera IoT Hub-resurser inifrån Visual Studio.

Den här självstudien lär ut utvecklingsstegen för Visual Studio 2019. Om du använder Visual Studio 2017 (version 15.7 eller senare) är stegen likartade. Om du hellre vill använda Visual Studio-kod läser du instruktionerna i [Använd Visual Studio-kod för att utveckla och felsöka moduler för Azure IoT Edge](how-to-vs-code-develop-module.md).

1. Förbered Visual Studio 2019 på din utvecklingsmaskin.

   * Om du inte redan har Visual Studio på utvecklingsdatorn [installerar du Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) med följande arbetsbelastningar:

      * Azure Development
      * Skrivbordsutveckling med C++
      * .NET Core plattformsoberoende utveckling

   * Om du redan har Visual Studio 2019 på utvecklingsdatorn följer du stegen i [Ändra Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) för att lägga till nödvändiga arbetsbelastningar.

2. Ladda ned och installera [Azure IoT Edge Tools-tillägget](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) för Visual Studio 2019.

   Om du använder Visual Studio 2017 (version 15.7 eller senare) laddar du ned och installerar [Azure IoT Edge Tools för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. När installationerna är klara öppnar du Visual Studio 2019 och väljer **Fortsätt utan kod**.

4. Välj **Visa** > **Cloud Explorer**.

5. Välj profilikonen i molnutforskaren och logga in på ditt Azure-konto om du inte redan är inloggad.

6. När du har loggat in visas dina Azure-prenumerationer. Expandera prenumerationen som har din IoT-hubb.

7. Under din prenumeration expanderar **du IoT Hubs** och sedan din IoT-hubb. Du bör se en lista över dina IoT-enheter och använda den här utforskaren för att hantera dem.

   ![Komma åt IoT-hubbresurser i Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Skapa ett nytt modulprojekt

Azure IoT Edge Tools-tillägget innehåller projektmallar för alla IoT Edge-modulspråk som stöds i Visual Studio. Dessa mallar har alla filer och kod som du behöver för att distribuera en fungerande modul för att testa IoT Edge, eller ge dig en utgångspunkt för att anpassa mallen med din egen affärslogik.

1. Välj **Arkiv** > **Nytt** > **projekt...**

2. I det nya projektfönstret söker du efter **IoT Edge** och väljer **Azure IoT Edge (Windows amd64)** projektet. Klicka på **Nästa**.

   ![Skapa ett nytt Azure IoT Edge-projekt](./media/tutorial-develop-for-windows/new-project.png)

3. Byt namn på projektet och lösningen till något beskrivande som **CSharpTutorialApp**i det nya projektfönstret . Klicka på **Skapa** om du vill skapa projektet.

   ![Konfigurera ett nytt Azure IoT Edge-projekt](./media/tutorial-develop-for-windows/configure-project.png)

4. Konfigurera projektet med följande värden i fönstret Lägg till modul:

   | Field | Värde |
   | ----- | ----- |
   | Visual Studio-mall | Välj **C#-modul**. |
   | Modulnamn | Acceptera standard **IotEdgeModule1**. |
   | Url till databas | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållaravbildningen är förifylld från modulens projektnamnsvärde. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta **värdet för inloggningsservern** från sidan **Översikt** för behållarregistret i Azure-portalen. <br><br> Den slutliga avbildningsdatabasen ser ut som \<registernamnet\>.azurecr.io/iotedgemodule1. |

      ![Konfigurera projektet för målenhet, modultyp och behållarregister](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Välj **Lägg till** om du vill skapa modulen.

När ditt nya projekt har läses in i Visual Studio-fönstret kan du bekanta dig med de filer som det har skapat:

* Ett IoT Edge-projekt som heter **CSharpTutorialApp**.
  * Mappen **Moduler** innehåller pekare till de moduler som ingår i projektet. I det här fallet borde det bara vara IotEdgeModule1.
  * Den dolda **.env-filen** innehåller autentiseringsuppgifterna till behållarregistret. Dessa autentiseringsuppgifter delas med din IoT Edge-enhet så att den har åtkomst för att hämta behållaravbildningarna.
  * **Filen deployment.template.json** är en mall som hjälper dig att skapa ett distributionsmanifest. Ett *distributionsmanifest* är en fil som definierar exakt vilka moduler du vill ha distribueras på en enhet, hur de ska konfigureras och hur de kan kommunicera med varandra och molnet.
    > [!TIP]
    > I avsnittet registerautentiseringsuppgifter fylls adressen automatiskt från den information du angav när du skapade lösningen. De referensvariabler för användarnamn och lösenord som lagras i .env-filen. Detta är för säkerhet, eftersom .env-filen ignoreras, men distributionsmallen är det inte.
* Ett IoT Edge-modulprojekt som heter **IotEdgeModule1**.
  * Den **program.cs** filen innehåller standardkoden för C#-modulen som medföljer projektmallen. Standardmodulen tar indata från en källa och skickar den vidare till IoT Hub.
  * **Module.json-filen** innehåller information om modulen, inklusive hela bilddatabasen, avbildningsversionen och vilken Dockerfile som ska användas för varje plattform som stöds.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Ange registerautentiseringsuppgifter till IoT Edge-agenten

IoT Edge-körningen behöver dina registerautentiseringsuppgifter för att hämta behållaravbildningarna till IoT Edge-enheten. IoT Edge-tillägget försöker hämta information om behållarregister från Azure och fylla i den i distributionsmallen.

1. Öppna **filen deployment.template.json** i modullösningen.

1. Leta reda på egenskapen **Credentials** i de $edgeAgent önskade egenskaperna. Den bör ha registeradressen automatiskt ifylld från den information du angav när du skapade projektet, och sedan ska användarnamns- och lösenordsfält innehålla variabelnamn. Ett exempel:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Öppna **.env-filen** i modullösningen. (Den är dold som standard i Lösningsutforskaren, så du kan behöva välja knappen **Visa alla filer** för att visa den.)

1. Lägg till de **värden för användarnamn** och **lösenord** som du kopierade från ditt Azure-behållarregister.

1. Spara ändringarna i .env-filen.

### <a name="review-the-sample-code"></a>Granska exempelkoden

Lösningsmallen som du skapade innehåller exempelkod för en IoT Edge-modul. Den här exempelmodulen tar helt enkelt emot meddelanden och skickar dem sedan vidare. Pipeline-funktionen visar ett viktigt koncept i IoT Edge, vilket är hur moduler kommunicerar med varandra.

Varje modul kan ha flera *in-* och *utdataköer* deklarerade i sin kod. IoT Edge-hubben som körs på enheten dirigerar meddelanden från utdata från en modul till indata från en eller flera moduler. Det specifika språket för att deklarera indata och utdata varierar mellan språk, men konceptet är detsamma för alla moduler. Mer information om routning mellan moduler finns i [Deklarera vägar](module-composition.md#declare-routes).

Exempelkoden C#som medföljer projektmallen använder [klassen ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) från IoT Hub SDK för .NET.

1. Leta reda på metoden **SetInputMessageHandlerAsync** i **filen program.cs.**

2. Metoden [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) ställer in en indatakö för att ta emot inkommande meddelanden. Granska den här metoden och se hur den initierar en indatakö som kallas **input1**.

   ![Hitta indatanamnet i Konstruktorn SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Leta sedan reda på metoden **SendEventAsync.**

4. [Metoden SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) bearbetar mottagna meddelanden och ställer in en utdatakö för att skicka dem vidare. Granska den här metoden och se till att den initierar en utdatakö som kallas **output1**.

   ![Hitta utdatanamnet i Konstruktorn SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Öppna **filen deployment.template.json.**

6. Leta reda på egenskapen **moduler** för de $edgeAgent önskade egenskaperna.

   Det bör finnas två moduler som anges här. Den första är **SimulatedTemperatureSensor**, som ingår i alla mallar som standard för att tillhandahålla simulerade temperaturdata som du kan använda för att testa dina moduler. Den andra är **IotEdgeModule1-modulen** som du skapade som en del av detta projekt.

   Den här moduler egenskapen deklarerar vilka moduler som ska ingå i distributionen till din enhet eller enheter.

7. Leta reda på egenskapen **routes** för de $edgeHub önskade egenskaperna.

   En av funktionerna i IoT Edge-hubbmodulen är att dirigera meddelanden mellan alla moduler i en distribution. Granska värdena i egenskapen routes. Den första vägen, **IotEdgeModule1ToIoTHub**, använder**\*** ett jokertecken ( ) för att inkludera alla meddelanden som kommer från alla utdataköer i modulen IotEdgeModule1. Dessa meddelanden går till *$upstream*, vilket är ett reserverat namn som anger IoT Hub. Den andra vägen, **sensorToIotEdgeModule1**, tar meddelanden som kommer från modulen SimuleradTemperatureSensor och dirigerar dem till *indata1* inmatningskön för modulen IotEdgeModule1.

   ![Granska vägar i deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Skapa och push-överföra lösningen

Du har granskat modulkoden och distributionsmallen för att förstå några viktiga distributionsbegrepp. Nu är du redo att bygga iotEdgeModule1-behållaravbildningen och skicka den till behållarregistret. Med IoT-verktygstillägget för Visual Studio genererar det här steget också distributionsmanifestet baserat på informationen i mallfilen och modulinformationen från lösningsfilerna.

### <a name="sign-in-to-docker"></a>Logga in på Docker

Ange behållarregisterautentiseringsuppgifterna till Docker på utvecklingsmaskinen så att den kan skicka behållaravbildningen som ska lagras i registret.

1. Öppna PowerShell eller en kommandotolk.

2. Logga in på Docker med azure-behållarregisterautentiseringsuppgifter som du sparade när du skapade registret.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhetsvarning `--password-stdin`som rekommenderar användning av . Även om bästa praxis rekommenderas för produktionsscenarier, ligger den utanför den här självstudiens omfattning. Mer information finns i [inloggningsreferensen för docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Bygg och tryck

Din utvecklingsmaskin har nu tillgång till ditt behållarregister, och dina IoT Edge-enheter kommer också. Det är dags att omvandla projektkoden till en behållaravbildning.

1. Högerklicka på **projektmappen CSharpTutorialApp** och välj **Bygg och pushA IoT-edgemoduler**.

   ![Skapa och pushA IoT Edge-moduler](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Kommandot Bygg och push startar tre åtgärder. Först skapas en ny mapp i lösningen som kallas **config** som innehåller hela distributionsmanifestet, inbyggt av information i distributionsmallen och andra lösningsfiler. För det `docker build` andra körs den för att skapa behållaravbildningen baserat på lämplig dockerfile för din målarkitektur. Sedan körs `docker push` det för att skicka avbildningsdatabasen till behållarregistret.

   Den här processen kan ta flera minuter första gången, men är snabbare nästa gång du kör kommandona.

2. Öppna **filen deployment.windows-amd64.json** i den nyskapade config-mappen. (Konfigurationsmappen visas kanske inte i Lösningsutforskaren i Visual Studio. Om så är fallet väljer du ikonen **Visa alla filer** i Aktivitetsfältet i Lösningsutforskaren.)

3. Leta **image** reda på bildparametern för avsnittet IotEdgeModule1. Observera att bilden innehåller hela bilddatabasen med namn-, versions- och arkitekturtaggen från module.json-filen.

4. Öppna **module.json-filen** i mappen IotEdgeModule1.

5. Ändra versionsnumret för modulbilden. (Versionen, inte $schema-versionen.) Till exempel öka korrigeringsnumret till **0.0.2** som om vi hade gjort en liten fix i modulkoden.

   >[!TIP]
   >Modulversioner aktiverar versionskontroll och låter dig testa ändringar på en liten uppsättning enheter innan du distribuerar uppdateringar till produktion. Om du inte ökar modulversionen innan du skapar och trycker, skriver du över databasen i behållarregistret.

6. Spara ändringarna i module.json-filen.

7. Högerklicka på **projektmappen CSharpTutorialApp** igen och välj **Bygg och Push IoT Edge-moduler** igen.

8. Öppna **filen deployment.windows-amd64.json** igen. Observera att en ny fil inte skapades när du körde kommandot build and push igen. I stället uppdaterades samma fil för att återspegla ändringarna. IotEdgeModule1-bilden pekar nu på 0.0.2-versionen av behållaren. Den här ändringen i distributionsmanifestet är hur du talar om för IoT Edge-enheten att det finns en ny version av en modul att hämta.

9. Om du vill ytterligare kontrollera vad bygg- och push-kommandot gjorde går du till [Azure-portalen](https://portal.azure.com) och navigerar till behållarregistret.

10. I behållarregistret väljer du **Databaser** och sedan **iotedgemodul1**. Kontrollera att båda versionerna av avbildningen har pressats till registret.

    ![Visa båda avbildningsversionerna i behållarregistret](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Felsöka

Om du stöter på fel när du skapar och trycker på modulavbildningen har det ofta att göra med Docker-konfigurationen på utvecklingsmaskinen. Använd följande kontroller för att granska konfigurationen:

* Körde du `docker login` kommandot med de autentiseringsuppgifter som du kopierade från behållarregistret? Dessa autentiseringsuppgifter skiljer sig från de som du använder för att logga in på Azure.
* Stämmer containerlagringsplatsen? Har den rätt container registernamn och rätt modulnamn? Öppna **module.json-filen** i mappen IotEdgeModule1 för att kontrollera. Databasvärdet ska se ut som ** \<\>registernamnet .azurecr.io/iotedgemodule1**.
* Om du använde ett annat namn än **IotEdgeModule1** för din modul, är det namnet konsekvent i hela lösningen?
* Kör din maskin samma typ av behållare som du bygger? Den här självstudien är för Windows IoT Edge-enheter, så dina Visual Studio-filer bör ha **windows-amd64-tillägget** och Docker Desktop ska köra Windows-behållare.

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enheten

Du har verifierat att de inbyggda behållaravbildningarna lagras i behållarregistret, så det är dags att distribuera dem till en enhet. Kontrollera att din IoT Edge-enhet är igång.

1. Öppna Cloud Explorer i Visual Studio och expandera information om din IoT-hubb.

2. Välj namnet på den enhet som du vill distribuera till. Välj **Skapa distribution**i listan **Åtgärder** .

   ![Skapa distribution för en enskild enhet](./media/tutorial-develop-for-windows/create-deployment.png)

3. I utforskaren navigerar du till konfigurationsmappen i projektet och väljer filen **deployment.windows-amd64.json.** Den här filen finns ofta på`C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Använd inte filen deployment.template.json, som inte har de fullständiga modulavbildningsvärdena i den.

4. Expandera informationen för din IoT Edge-enhet i Cloud Explorer för att se modulerna på din enhet.

5. Använd knappen **Uppdatera** för att uppdatera enhetsstatusen för att se att modulerna SimulatedTemperatureSensor och IotEdgeModule1 har distribuerats till enheten.

   ![Visa moduler som körs på din IoT Edge-enhet](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Visa meddelanden från enheten

IotEdgeModule1-koden tar emot meddelanden via indatakön och skickar dem vidare genom utdatakön. Distributionsmanifestet deklarerade vägar som skickade meddelanden från SimulatedTemperatureSensor till IotEdgeModule1 och vidarebefordrade sedan meddelanden från IotEdgeModule1 till IoT Hub. Azure IoT Edge-verktygen för Visual Studio gör att du kan se meddelanden när de anländer till IoT Hub från dina enskilda enheter.

1. I Visual Studio-molnutforskaren väljer du namnet på den IoT Edge-enhet som du distribuerade till.

2. Välj Börja övervaka **inbyggt händelseslutpunkt på Åtgärds-menyn**. **Actions**

3. Titta på avsnittet **Utdata** i Visual Studio för att se meddelanden som kommer till din IoT-hubb.

   Det kan ta några minuter för båda modulerna att starta. IoT Edge-körningen måste ta emot sitt nya distributionsmanifest, dra ner modulavbildningarna från behållarkörningen och starta sedan varje ny modul.

   ![Visa inkommande enhet till molnmeddelanden](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Visa ändringar på enheten

Om du vill se vad som händer på själva enheten använder du kommandona i det här avsnittet för att granska IoT Edge-körningen och moduler som körs på enheten.

Kommandona i det här avsnittet gäller för din IoT Edge-enhet, inte din utvecklingsmaskin. Om du använder en virtuell dator för din IoT Edge-enhet ansluter du till den nu. I Azure går du till den virtuella datorns översiktssida och väljer **Anslut** för att komma åt fjärrskrivbordsanslutningen. Öppna ett kommando eller ett PowerShell-fönster `iotedge` på enheten för att köra kommandona.

* Visa alla moduler som har distribuerats till enheten och kontrollera deras status:

   ```cmd
   iotedge list
   ```

   Du bör se fyra moduler: de två IoT Edge runtime-modulerna, SimulatedTemperatureSensor och IotEdgeModule1. Alla fyra ska anges som igång.

* Kontrollera loggarna för en viss modul:

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge-moduler är skiftlägeskänsliga.

   Loggarna SimulatedTemperatureSensor och IotEdgeModule1 ska visa de meddelanden som de bearbetar. EdgeAgent-modulen ansvarar för att starta de andra modulerna, så dess loggar kommer att ha information om hur du implementerar distributionsmanifestet. Om någon modul inte finns med i listan eller inte körs har edgeAgent-loggarna förmodligen felen. EdgeHub-modulen ansvarar för kommunikation mellan modulerna och IoT Hub. Om modulerna är igång, men meddelandena inte kommer till din IoT-hubb, kommer edgeHub-loggarna förmodligen att ha felen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du Visual Studio 2019 på utvecklingsmaskinen och distribuerade din första IoT Edge-modul från den. Nu när du känner till de grundläggande begreppen kan du prova att lägga till funktioner i en modul så att den kan analysera data som passerar genom den. Välj önskat språk:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md)
> [C.](tutorial-csharp-module-windows.md)
