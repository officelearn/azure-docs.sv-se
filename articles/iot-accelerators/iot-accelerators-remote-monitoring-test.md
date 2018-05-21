---
title: Enheten simuleringen i fjärranslutna övervakningslösning - Azure | Microsoft Docs
description: Den här kursen visar hur du använder enheten simulatorn med fjärråtkomst övervakning solution accelerator.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c10d983ea6b864d21f4589a3cbfdd5def39ac753
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="create-a-new-simulated-device"></a>Skapa en ny simulerad enhet

Den här kursen visar hur du anpassar enheten simulatorn mikrotjänster i fjärråtkomst övervakning solution accelerator. Den här kursen använder två scenarier i Contoso IoT-programmet för att visa funktionerna i enheten simulatorn.

Följande videoklipp visar en översikt över alternativ för att anpassa enheten simulatorn mikrotjänster:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

Contoso vill testa en ny enhet för smart av glödlampa i det första scenariot. Om du vill utföra testerna måste skapa du en ny simulerade enhet med följande egenskaper:

*Egenskaper*

| Namn                     | Värden                      |
| ------------------------ | --------------------------- |
| Färg                    | Vit, röd, blå            |
| Ljusstyrkan               | 0 till 100                    |
| Uppskattad återstående livslängd | Nedräkning 10 000 timmar |

*Telemetri*

Följande tabell visar data i av glödlampa rapporterar till molnet som en dataström:

| Namn   | Värden      |
| ------ | ----------- |
| Status | ”på”, ”av” |
| Temperatur | Grader F |
| online | SANT, FALSKT |

> [!NOTE]
> Den **online** telemetri värdet är obligatoriskt för alla simulerade typer.

*Metoder*

I följande tabell visas de åtgärder som har stöd för den nya enheten:

| Namn        |
| ----------- |
| Aktivera   |
| Stäng av  |

*Ursprungligt tillstånd*

I följande tabell visas enhetens inledande status:

| Namn                     | Värden |
| ------------------------ | -------|
| Inledande färg            | Vit  |
| Inledande intensitet       | 75     |
| Inledande återstående livslängd   | 10 000 |
| Inledande telemetri status | ”on”   |
| Inledande telemetri temperatur | 200   |

I det andra scenariot lägger du till en ny typ av telemetri till Contoso befintliga **kylaggregat** enhet.

Den här kursen visar hur du använder enheten simulatorn med fjärråtkomst övervakning solution accelerator:

I den här guiden får du lära dig att:

>[!div class="checklist"]
> * Skapa en ny enhetstyp av
> * Simulera anpassade beteende
> * Lägga till en ny enhet på instrumentpanelen
> * Skicka telemetri om anpassade från en befintlig typ av enhet

Följande videoklipp visar en genomgång av simulerade och verkliga enheter som ansluter till den fjärranslutna övervakningslösning:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här kursen behöver du:

* En distribuerad instans av den fjärranslutna övervakningslösning i din Azure-prenumeration. Om du inte har distribuerat remote övervakningslösning ännu, bör du genomföra den [Distribuera fjärråtkomst övervakning solution accelerator](../iot-accelerators/iot-accelerators-remote-monitoring-deploy.md) kursen.

* Visual Studio 2017. Om du inte har Visual Studio 2017 installerat kan du hämta den kostnadsfria [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/) edition.

* [Cloud Explorer för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) Visual Studio-tillägget.

* Ett konto på [Docker-hubb](https://hub.docker.com/). Du kan registrera dig gratis att komma igång.

* [Git](https://git-scm.com/downloads) installerad på den stationära datorn.

## <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Utför följande uppgifter för att förbereda din utvecklingsmiljö för att lägga till en ny simulerade enhet till din fjärranslutna övervakningslösning:

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>Konfigurera SSH-åtkomst till den virtuella datorn på lösningen i Azure

När du skapade din fjärranslutna övervakningslösning på [www.azureiotsuite.com](https://www.azureiotsuite.com), du har valt en lösningens namn. Lösningens namn blir namnet på Azure-resursgrupp som innehåller de olika distribuerade resurser som används i lösningen. Använder följande kommandon en resursgrupp med namnet **Contoso-01**, bör du ersätta **Contoso-01** med namnet på resursgruppen.

Följande kommandon för användning av `az` från [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest). Du kan installera Azure CLI 2.0 på utvecklingsdatorn eller använda den [moln Shell](https://docs.microsoft.com/azure/cloud-shell/overview) i den [Azure-portalen](http://portal.azure.com). Azure CLI 2.0 är förinstallerade i molnet-gränssnittet.

1. Kontrollera namnet på resursgruppen som innehåller dina fjärresurser övervakning genom att köra följande kommando:

    ```sh
    az group list | grep "name"
    ```

    Det här kommandot visar alla resursgrupper i din prenumeration. I listan ska innehålla en resursgrupp med samma namn som din fjärranslutna övervakningslösning.

1. Kör följande kommando med din resursgruppens namn i stället för att göra din resurs gruppera standardgruppen för efterföljande kommandon **Contoso-01**:

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. Kör följande kommando för att lista resurserna i resursgruppen:

    ```sh
    az resource list -o table
    ```

    Anteckna namnen på den virtuella datorn och dina nätverkssäkerhetsgruppen. Du kan använda dessa värden i senare steg.

1. Om du vill aktivera SSH-åtkomst till den virtuella datorn, kör du följande kommando med namnet på din nätverkssäkerhetsgruppen från föregående steg:

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    Om du vill visa listan över regler för inkommande trafik i nätverket, kör du följande kommando:

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

1. Om du vill ändra lösenordet för virtuell dator till ett lösenord som du vet att köra följande kommando. Använd namnet på den virtuella datorn som du antecknade tidigare och ett lösenord som du väljer:

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. Att hitta IP-adressen för den virtuella datorn, använder du följande kommando och anteckna den offentliga IP-adressen:

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. Du kan nu använda SSH för att ansluta till den virtuella datorn. Den `ssh` kommandot är förinstallerade i molnet-gränssnittet. Använd den offentliga IP-adressen från föregående steg, och när du uppmanas att göra lösenordet som du konfigurerade för den virtuella datorn:

    ```sh
    ssh azureuser@public-ip-address
    ```

    Nu har du åtkomst till gränssnittet på den virtuella datorn som kör Docker-behållare i den fjärranslutna övervakningslösning. Om du vill visa behållarna som körs, använder du följande kommando:

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>Hitta anslutningssträngar för tjänsten

I självstudierna kan du arbeta med Visual Studio-lösning som ansluter till lösningens Cosmos-DB- och IoT-hubb. Följande steg visar ett sätt att hitta anslutningen strängvärden som du behöver:

1. Kör följande kommando för att hitta anslutningssträngen Cosmos-DB i SSH-session som är ansluten till den virtuella datorn:

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    Anteckna anslutningssträngen. Du kan använda det här värdet senare under kursen.

1. Kör följande kommando för att hitta anslutningssträngen IoT-hubb i SSH-session som är ansluten till den virtuella datorn:

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    Anteckna anslutningssträngen. Du kan använda det här värdet senare under kursen.

> [!NOTE]
> Du kan också hitta dessa anslutningssträngar i Azure-portalen eller genom att använda den `az` kommando.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>Stoppa tjänsten enhet simuleringen på den virtuella datorn

När du ändrar simuleringen tjänst kan köra du det lokalt för att testa dina ändringar. Innan du kör tjänsten enhet simuleringen lokalt, måste du stoppa den instans som körs på den virtuella datorn på följande sätt:

1. Att hitta den **BEHÅLLAR-ID** av den **enheten simuleringen** tjänsten, kör följande kommando i SSH-session som är ansluten till den virtuella datorn:

    ```sh
    docker ps
    ```

    Anteckna behållar-ID för den **enheten simuleringen** service.

1. Stoppa den **enheten simuleringen** behållare, kör du följande kommando:

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>Klona GitHub-databaser

I kursen får du arbeta med den **enheten simuleringen** och **lagringsadapter** Visual Studio-projekt. Du kan också klona källkodslager från GitHub. Utför det här steget på lokala utvecklingsdatorn där du har Visual Studio installerat:

1. Öppna en kommandotolk och navigera till mappen där du vill spara en kopia av den **enheten simuleringen** och **lagringsadapter** GitHub-lagringsplatser.

1. Att klona .NET-versionen av den **enheten simuleringen** databasen, kör du följande kommando:

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    Tjänsten enhet simuleringen i fjärranslutna övervakningslösning gör att du kan göra ändringar i de inbyggda simulerade enhetstyper och om du vill skapa en ny simulerade enhetstyper. Du kan använda anpassade enhetstyper för att testa funktionen för fjärranslutna övervakningslösning innan du ansluter din fysiska enheter.

1. Att klona .NET-versionen av den **lagringsadapter** databasen, kör du följande kommando:

    ```cmd
    git clone https://github.com/Azure/pcs-storage-adapter-dotnet.git
    ```

    Tjänsten enhet simuleringen använder nätverkskort lagringstjänsten för att ansluta till tjänsten Cosmos-DB i Azure. Fjärråtkomst övervakningslösning lagrar konfigurationsdata för simulerade enheten i en Cosmos-DB-databas.

### <a name="run-the-storage-adapter-service-locally"></a>Kör kortet lagringstjänsten lokalt

Tjänsten enhet simuleringen använder nätverkskort lagringstjänsten för att ansluta till lösningens Cosmos-DB-databas. Om du kör tjänsten enhet simuleringen lokalt, måste du också köra kortet lagringstjänsten lokalt. Följande steg visar hur du kör kortet lagringstjänsten från Visual Studio:

1. Öppna i Visual Studio den **datorer-lagring – adapter.sln** lösningsfilen i din lokala kloning av den **lagringsadapter** databasen.

1. I Solution Explorer högerklickar du på den **WebService** projekt, Välj **egenskaper**, och välj sedan **felsöka**.

1. I den **miljövariabler** avsnitt, redigera värdet för den **datorer\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** variabeln ska vara Cosmos-DB-anslutning strängen som du antecknade tidigare. Spara dina ändringar.

1. I Solution Explorer högerklickar du på den **WebService** projekt, Välj **felsöka**, och välj sedan **Starta ny instans**.

1. Tjänsten har startats körs lokalt och öppnar `http://localhost:9022/v1/status` i din standardwebbläsare. Kontrollera att den **Status** värdet är ”OK: Alive och korrekt”.

1. Lämna kortet lagringstjänsten körs lokalt förrän du har slutfört guiden.

Nu har du allt på plats och du är redo att börja lägga till en ny typ av simulerade enheten i din fjärranslutna övervakningslösning.

## <a name="create-a-simulated-device-type"></a>Skapa en simulerad enhetstyp

Det enklaste sättet att skapa en ny typ av enhet i simuleringen tjänst är att kopiera och ändra en befintlig typ. Följande steg visar hur du kopierar inbyggt **kylaggregat** enhet för att skapa en ny **av glödlampa** enhet:

1. Öppna i Visual Studio den **enhet simulation.sln** lösningsfilen i din lokala kloning av den **enheten simuleringen** databasen.

1. I Solution Explorer högerklickar du på den **SimulationAgent** projekt, Välj **egenskaper**, och välj sedan **felsöka**.

1. I den **miljövariabler** avsnitt, redigera värdet för den **datorer\_IOTHUB\_CONNSTRING** variabeln ska vara IoT-hubb anslutningssträngen som du antecknade tidigare. Spara dina ändringar.

1. I Solution Explorer högerklickar du på den **WebService** projekt, Välj **egenskaper**, och välj sedan **felsöka**.

1. I den **miljövariabler** avsnitt, redigera värdet för den **datorer\_IOTHUB\_CONNSTRING** variabeln ska vara IoT-hubb anslutningssträngen som du antecknade tidigare. Spara dina ändringar.

1. I Solution Explorer högerklickar du på den **enheten simuleringen** lösning och välj **ange Startprojekt**. Välj **enda Startprojekt** och välj **WebService**. Klicka sedan på **OK**.

1. Varje typ av enhet har en modell JSON-fil och associerade skript i den **Services/data/devicemodels** mapp. I Solution Explorer, kopiera den **kylaggregat** filer för att skapa den **av glödlampa** filer som visas i följande tabell:

    | Källa                      | Mål                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | skript/kylaggregat-01-state.js | skript/av glödlampa-01-state.js |
    | omstart/skript-method.js    | SwitchOn/skript-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Definiera egenskaperna för den nya enhetstypen

Den **av glödlampa 01.json** filen definierar egenskaperna för typen, till exempel telemetrin genereras och metoderna som stöder. Följ anvisningarna nedan för uppdatering av **av glödlampa 01.json** filen för att definiera den **av glödlampa** enhet:

1. I den **av glödlampa 01.json** uppdaterar enhetens metadata som visas i följande utdrag:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. I den **av glödlampa 01.json** uppdaterar definitionen simuleringen som visas i följande utdrag:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Interval": "00:00:20",
      "Scripts": [
        {
          "Type": "javascript",
          "Path": "lightbulb-01-state.js"
        }
      ]
    },
    ```

1. I den **av glödlampa 01.json** uppdaterar Typegenskaper enheten som visas i följande utdrag:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. I den **av glödlampa 01.json** uppdaterar enheten telemetri typdefinitioner som visas i följande utdrag:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. I den **av glödlampa 01.json** uppdaterar enheten typen metoder som visas i följande utdrag:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. Spara den **av glödlampa 01.json** fil.

### <a name="simulate-custom-device-behavior"></a>Simulera anpassade beteende

Den **skript/av glödlampa-01-state.js** filen definierar beteendet simulering av den **av glödlampa** typen. Följ anvisningarna nedan för uppdatering av **skript/av glödlampa-01-state.js** filen för att definiera beteendet för den **av glödlampa** enhet:

1. Redigera definition av tillstånd i den **skript/av glödlampa-01-state.js** filen som visas i följande utdrag:

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. Lägg till en **Vänd** fungerar efter den **variera** funktionen med följande definition:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Redigera den **huvudsakliga** funktion för att implementera beteenden som visas i följande utdrag:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Spara den **skript/av glödlampa-01-state.js** fil.

Den **SwitchOn/skript-method.js** filen implementerar den **växeln på** metod i en **av glödlampa** enhet. Följ anvisningarna nedan för uppdatering av **SwitchOn/skript-method.js** fil:

1. Redigera definition av tillstånd i den **SwitchOn/skript-method.js** filen som visas i följande utdrag:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Om du vill aktivera den av glödlampa redigera den **huvudsakliga** fungerar på följande sätt:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Spara den **SwitchOn/skript-method.js** fil.

1. Skapa en kopia av **SwitchOn/skript-method.js** fil med namnet **SwitchOff/skript-method.js**.

1. Om du vill stänga av glödlampa redigera den **huvudsakliga** fungera i den **SwitchOff/skript-method.js** enligt följande:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Spara den **SwitchOff/skript-method.js** fil.

1. I Solution Explorer väljer du var och en av dina fyra nya filer i sin tur. I den **egenskaper** fönster för varje fil, kontrollerar du att **kopiera till utdatakatalog** är inställd på **kopiera om nyare**.

### <a name="configure-the-device-simulation-service"></a>Konfigurera simulering tjänst

Konfigurera tjänsten om du vill köra en enda kylaggregat och en enda av glödlampa enhet om du vill begränsa antalet simulerade enheter som ansluter till lösningen under testningen. Konfigurationsinformationen lagras i Cosmos-DB-instans i lösningens resursgruppen. Om du vill redigera konfigurationsdata, Använd den **Cloud Explorer** vyn i Visual Studio:

1. Öppna den **Cloud Explorer** visa i Visual Studio, Välj **visa** och sedan **Cloud Explorer**.

1. Att hitta simuleringen configuration dokumentet i **Sök efter resurser** ange **simualtions.1**.

1. Dubbelklicka på den **simulations.1** dokument för att öppna den för redigering.

1. I värdet för **Data**, leta upp den **DeviceModels** matris som ser ut som följande utdrag:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. Om du vill definiera en enda kylaggregat och en enda av glödlampa simulerade enhet, ersätter den **DeviceModels** matris med följande kod:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    Spara ändringen av **simulations.1** dokumentet.

> [!NOTE]
> Du kan också använda Cosmos DB Data Explorer i Azure-portalen för att redigera den **simulations.1** dokumentet.

### <a name="test-the-lightbulb-device-type-locally"></a>Testa typ av glödlampa enhet lokalt

Du är nu redo att testa din nya simulerade av glödlampa typ genom att köra enheten simuleringen projektet lokalt.

1. I Solution Explorer högerklickar du på **WebService**, Välj **felsöka** och välj sedan **Starta ny instans**.

1. Öppna Azure-portalen för att kontrollera att de två simulerade enheterna är anslutna till din IoT-hubb i webbläsaren.

1. Navigera till IoT-hubben i resursgruppen som innehåller din fjärranslutna övervakningslösning.

1. I den **övervakning** väljer **mått**. Kontrollera att antalet **anslutna enheter** är två:

    ![Antal anslutna enheter](./media/iot-accelerators-remote-monitoring-test/connecteddevices.png)

1. I din webbläsare, navigerar du till den **instrumentpanelen** för fjärråtkomst övervakning lösningen. I panelen telemetri för den **instrumentpanelen**väljer **temperatur**. Temperaturen för simulerad enheter visas i diagrammet:

    ![Temperatur telemetri](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

Du har nu av glödlampa enheten simuleringen körs lokalt. Nästa steg är att distribuera din kod med uppdaterade simulator till den virtuella datorn som kör fjärråtkomst övervakning mikrotjänster i Azure.

Innan du fortsätter kan stoppa du felsökningen både enheten simulering och lagring kortet projekt i Visual Studio.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>Distribuera den uppdaterade simulatorn till molnet

Mikrotjänster i fjärranslutna övervakningslösning kör i docker-behållare. Behållarna som finns i lösningens virtuell dator i Azure. I det här avsnittet får du:

* Skapa en ny enhet simuleringen docker-avbildning.
* Överför avbildningen till docker-hubb databasen.
* Importera den till din lösning virtuell dator.

Följande steg förutsätter att du har en databas som heter **av glödlampa** i Docker-hubb-konto.

1. I Visual Studio, i den **enheten simuleringen** projekt, öppna filen **solution\scripts\docker\build.cmd**.

1. Redigera den rad som anger den **DOCKER_IMAGE** miljövariabeln din hubb Docker-databasnamn:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Spara ändringen.

1. I Visual Studio, i den **enheten simuleringen** projekt, öppna filen **solution\scripts\docker\publish.cmd**.

1. Redigera den rad som anger den **DOCKER_IMAGE** miljövariabeln din hubb Docker-databasnamn:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Spara ändringen.

1. Öppna en kommandotolk som administratör. Gå till mappen **scripts\docker** i din kloning av den **enheten simuleringen** GitHub-lagringsplatsen.

1. Kör följande kommando för att skapa docker-avbildningen:

    ```cmd
    build.cmd
    ```

1. Om du vill logga in på ditt konto med Docker-hubb, kör du följande kommando:

    ```cmd
    docker login
    ```

1. Om du vill överföra din nya avbildningen Docker-hubb-konto, kör du följande kommando:

    ```cmd
    publish.cmd
    ```

1. För att kontrollera överföringen, gå till [ https://hub.docker.com/ ](https://hub.docker.com/). Leta upp din **av glödlampa** databasen och välj **information**. Välj **taggar**:

    ![Docker-hubb](./media/iot-accelerators-remote-monitoring-test/dockerhub.png)

    Skripten läggs den **testning** tagg på avbildningen.

1. Använda SSH för att ansluta till din lösning virtuell dator i Azure. Gå sedan till den **App** mapp och redigera den **docker compose.yaml** fil:

    ```sh
    cd /app
    sudo nano docker-compose.yaml
    ```

1. Redigera posten för tjänsten enhet simuleringen att använda docker-avbildningen:

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    Spara ändringarna.

1. Starta om alla tjänster med de nya inställningarna genom att köra följande kommando:

    ```sh
    sudo ./start.sh
    ```

1. Om du vill kontrollera loggfilen från din nya enhet simuleringen behållare, kör du följande kommando för att hitta behållar-ID:

    ```sh
    docker ps
    ```

    Kör följande kommando använder behållar-ID:

    ```sh
    docker logs {container ID}
    ```

Nu har du slutfört stegen för att distribuera en uppdaterad version av tjänsten simuleringen enhet till din fjärranslutna övervakningslösning.

I din webbläsare, navigerar du till den **instrumentpanelen** för fjärråtkomst övervakning lösningen. I panelen telemetri för den **instrumentpanelen**väljer **temperatur**. Temperaturen för din simulerade enheterna visas i diagrammet:

![Temperatur telemetri](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

På den **enheter** kan du etablera instanser av en ny typ:

![Visa listan över tillgängliga simulering](./media/iot-accelerators-remote-monitoring-test/devicesmodellist.png)

Du kan visa telemetri från den simulerade enheten:

![Vy av glödlampa telemetri](./media/iot-accelerators-remote-monitoring-test/devicestelemetry.png)

Du kan anropa den **SwitchOn** och **SwitchOff** metoder på din enhet:

![Anrop av glödlampa metoder](./media/iot-accelerators-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>Lägga till en ny telemetri

Det här avsnittet beskrivs hur du ändrar en befintlig typ simulerade enheten för att stödja en ny typ av telemetri.

### <a name="locate-the-chiller-device-type-files"></a>Hitta kylaggregat enheten typen filer

Följande steg visar hur du söker efter filer som definierar inbyggt **kylaggregat** enhet:

1. Om du inte redan har gjort det, använder du följande kommando för att klona den **enheten simuleringen** GitHub-lagringsplatsen till den lokala datorn:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Varje typ av enhet har en modell JSON-fil och associerade skript i den `data/devicemodels` mapp. Filer som definierar den simulerade **kylaggregat** enhetstyp är:

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>Ange den nya typen telemetri

Följande steg visar hur du lägger till en ny **inre temperatur** typ till den **kylaggregat** enhetstyp:

1. Öppna den **kylaggregat 01.json** fil.

1. Uppdatering av **SchemaVersion** värdet på följande sätt:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. I den **InitialState** lägger du till följande två definitioner:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. I den **telemetri** kan lägga till följande definition:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Spara den **kylaggregat 01.json** fil.

1. Öppna den **skript/kylaggregat-01-state.js** fil.

1. Lägg till följande fält till den **tillstånd** variabeln:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Lägg till följande rad i den **huvudsakliga** funktionen:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Spara den **skript/kylaggregat-01-state.js** fil.

### <a name="test-the-chiller-device-type"></a>Testa kylaggregat enhetstyp

Att testa den uppdaterade **kylaggregat** enhetstyp, först köra en lokal kopia av den **enheten simuleringen** -tjänsten för att testa din enhetstyp fungerar som förväntat. När du har testat och testar din uppdaterade enhetstyp lokalt, kan du återskapa behållaren och omdistribuera den **enheten simuleringen** tjänst till Azure.

När du kör den **enheten simuleringen** tjänsten lokalt, skickas telemetri till din fjärranslutna övervakningslösning. På den **enheter** kan du etablera instanser av din uppdaterade.

Om du vill testa och felsöka dina ändringar lokalt, finns i föregående avsnitt [testa typ av glödlampa enhet lokalt](#test-the-lightbulb-device-type-locally).

Om du vill distribuera uppdaterade enheten simuleringen tjänsten till lösningens virtuell dator i Azure finns i föregående avsnitt [distribuera uppdaterade simulatorn till molnet](#deploy-the-updated-simulator-to-the-cloud).

På den **enheter** kan du etablera instanser av din uppdaterade:

![Lägg till uppdaterade kylaggregat](./media/iot-accelerators-remote-monitoring-test/devicesupdatedchiller.png)

Du kan visa den nya **inre temperatur** telemetri från den simulerade enheten.

## <a name="next-steps"></a>Nästa steg

Den här självstudiekursen visades hur du vill:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Skapa en ny enhetstyp av
> * Simulera anpassade beteende
> * Lägga till en ny enhet på instrumentpanelen
> * Skicka telemetri om anpassade från en befintlig typ av enhet

Nu har du lärt dig hur du anpassar simuleringen tjänst. Föreslagna nästa steg är att lära dig hur du [ansluta en fysisk enhet till din fjärranslutna övervakningslösning](iot-accelerators-connecting-devices-node.md).

Utvecklare om remote övervakningslösning, Läs mer:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
