---
title: Felsöka Python-moduler för Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio Code för att utveckla, skapa och Felsök en Python-modul för Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/13/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4393d3231ca4f0a813d4586161383bd6d61f6a49
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45552460"
---
# <a name="use-visual-studio-code-to-develop-and-debug-python-modules-for-azure-iot-edge"></a>Använd Visual Studio Code för att utveckla och felsöka Python-moduler för Azure IoT Edge

Du kan aktivera din affärslogik-moduler för Azure IoT Edge. Den här artikeln visar hur du använder Visual Studio Code (VS Code) som det huvudsakliga verktyget för att utveckla och felsöka Python-moduler.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du använder en dator eller virtuell dator som kör Windows eller Linux som din utvecklingsdator. Och du simulera IoT Edge-enhet på utvecklingsdatorn med IoT Edge security daemon.

> [!NOTE]
> Den här felsökning artikeln visar hur du ansluter en process i en modul-behållare och felsöka med VS Code. Du kan bara felsöka Python-moduler i Linux amd64-behållare. Om du inte är bekant med felsökning funktionerna i Visual Studio Code, Läs om [Debugging](https://code.visualstudio.com/Docs/editor/debugging). 

Eftersom den här artikeln används Visual Studio Code som det huvudsakliga utvecklingsverktyg, installera VS Code. Lägg sedan till tillägg som behövs:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) för att installera Python-paket (ingår vanligtvis i Python-installationen).
* Installera **Cookiecutteru** med kommandot nedan
   
    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

Om du vill skapa en modul, behöver du Docker för att skapa modulen avbildningen och ett behållarregister ska lagra avbildningen modulen:
* [Docker Community Edition](https://docs.docker.com/install/) på utvecklingsdatorn. 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Du kan använda en lokal Docker-register för prototyper och testning i stället för ett register i molnet. 

Testa din modul på en enhet, behöver du en aktiv IoT-hubb med minst en IoT Edge-enhet. Om du vill använda din dator som en IoT Edge-enhet följer du stegen i snabbstarten för [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Skapa en ny lösningsmall

Gör följande för att skapa en IoT Edge-modul som baseras på Azure IoT Python SDK med hjälp av Visual Studio Code och Azure IoT Edge-tillägget. Först skapar du en lösning och sedan skapa den första modulen i lösningen. Varje lösning kan innehålla mer än en modul. 

1. I Visual Studio Code, Välj **visa** > **integrerade terminalen**.

2. Välj **visa** > **kommandot paletten**. 

3. Ange i kommandopaletten, och kör kommandot **Azure IoT Edge: nya IoT Edge-lösning**.

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

4. Bläddra till mappen där du vill skapa den nya lösningen. Välj **Välj mapp**. 

5. Ange ett namn för din lösning. 

6. Välj **Python-modulen** som mall för den första modulen i lösningen.

7. Ange ett namn för din modul. Välj ett namn som är unikt i ditt behållarregister. 

8. Ange namnet på modulens avbildningslagringsplatsen. VS Code autopopulates modulen namnet med **localhost:5000**. Ersätt den med din egen information i registret. Om du använder en lokal Docker-register för testning, sedan **localhost** är bra. Om du använder Azure Container Registry kan du sedan använda inloggningsserver från din registerinställningar. Det ser ut som inloggningsserver  **\<registernamn\>. azurecr.io**. Ersätt endast localhost-delen av strängen; ta inte bort modulens namn. 

   ![Ange lagringsplatsen för Docker-avbildningen](./media/how-to-develop-c-module/repository.png)

VS Code tar den information du tillhandahålls, skapar en IoT Edge-lösning och läser sedan in den i ett nytt fönster.

Det finns fyra objekt inne i lösningen: 
* En **.vscode** mappen innehåller konfigurationer för felsökning.
* En **moduler** mappen innehåller undermappar för varje modul. Nu kan har du bara en. Men du kan lägga till fler i kommandopaletten med kommandot **Azure IoT Edge: Lägg till IoT Edge-modul**. 
* En **.env** filen visar en lista över dina miljövariabler. Om Azure Container Registry är ditt register, har du ett Azure Container Registry användarnamn och lösenord i den. 

   > [!NOTE]
   > Miljö-filen skapas endast om du anger en avbildningslagringsplatsen för modulen. Om du har godkänt localhost för att testa och felsöka lokalt, behöver du inte deklarera miljövariabler. 

* En **deployment.template.json** filen visar en lista över dina nya modulen tillsammans med ett exempel **tempSensor** modul som simulerar data som du kan använda för testning. Mer information om hur distribution manifest work finns i [Lär dig hur du använder distribution manifest för att distribuera moduler och upprätta vägar](module-composition.md). 

## <a name="develop-your-module"></a>Utveckla din modell

Python-modul-koden som medföljer lösningen finns i **moduler** > [Modulnamn] > **main.py**. Modulen och filen deployment.template.json ställs in så att du kan skapa lösningen, push-överföra den till behållarregistret och distribuera den till en enhet för att börja testa utan att röra kod. Modulen är utformat för att helt enkelt ta indata från en källa (i det här fallet modulen tempSensor som simulerar data) och skicka det till IoT Hub. 

När du är redo att anpassa mallen Python med din egen kod kan använda den [Azure IoT Hub SDK: er](../iot-hub/iot-hub-devguide-sdks.md) att skapa moduler adressen nyckeln måste för IoT-lösningar som säkerhet, hantering av enheter och tillförlitlighet. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Skapa och distribuera din modul för felsökning

Det finns flera Docker-filer för olika behållartyper i varje modul-mapp. Använd någon av dessa filer som slutar med tillägget **.debug** att skapa din modul för testning. För närvarande stöder Python-moduler felsökning endast i Linux amd64-behållare. 

1. I VS Code, navigerar du till den `deployment.template.json` filen. Uppdatera din modulen bild-URL genom att lägga till **.debug** i slutet.

2. Ersätt createOptions för Python-modulen i **deployment.template.json** med nedan innehåll och spara den här filen: 
    
    ```json
    "createOptions": "{\"ExposedPorts\":{\"5678/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"5678/tcp\":[{\"HostPort\":\"5678\"}]}}}"
    ```

3. Gå till `main.py`, lägga till följande koder efter importavsnittet
    
    ```python
    import ptvsd
    ptvsd.enable_attach(('0.0.0.0',  5678))
    ```

4. Lägg till följande rad med kod i att det återanrop som du vill felsöka

    ```python
    ptvsd.break_into_debugger()
    ```

   Exempel: Om du vill felsöka `receive_message_callback` metod. Du kan insekter rad med kod som den här.

    ```python
    def receive_message_callback(message, hubManager):
        ptvsd.break_into_debugger()
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

2. Ange i kommandopaletten VS Code och kör kommandot **Azure IoT Edge: Build and Push IoT Edge-lösningen**.
3. Välj den `deployment.template.json` -filen för din lösning från kommandopaletten. 
4. I Azure IoT Hub Device Explorer högerklickar du på en IoT Edge-enhets-ID. Välj sedan **skapa distribution för enskild enhet**. 
5. Öppna din lösning **config** mapp. Välj sedan den `deployment.json` filen. Välj **Välj Edge-distribution Manifest**. 

Distributionen har skapats med en distributions-ID i en terminal för VS Code-integrerade visas.

Kontrollera behållarstatus för i VS Code Docker-Utforskaren eller genom att köra den `docker ps` i terminalen.

## <a name="start-debugging-python-module-in-vs-code"></a>Starta felsökning Python-modulen i VS Code
VS Code håller felsökning konfigurationsinformationen i en `launch.json` finns i en `.vscode` mapp i din arbetsyta. Detta `launch.json` filen genererades när du skapade en ny IoT Edge-lösning. Uppdateras varje gång du lägger till en ny modul som har stöd för felsökning. 

1. Gå till felsökningsvyn VS Code. Välj debug-konfigurationsfil för. Alternativnamn debug bör likna **ModuleName fjärrfelsökning (Python)**

2. Navigera till `main.c`. Lägg till en brytpunkt i motringningsmetoden som du har publicerat `ptvsd.break_into_debugger()` i den.

3. Välj **Starta felsökning** eller välj **F5**. Välj processen för att ansluta till.

4. I VS Code Felsöka vyn visas variabler i den vänstra panelen. 

I föregående exempel visar hur du felsöker Python IoT Edge-moduler i behållare. Det har lagts till portar i din modul behållare createOptions. När du är klar med felsökning Python-moduler, rekommenderar vi att du tar bort dessa portar för produktionsklara IoT Edge-moduler.

## <a name="next-steps"></a>Nästa steg

När du har skapat din modul, lär du dig hur du [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md).

Att utveckla moduler för dina IoT Edge-enheter, [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
