---
title: "Enheten simuleringen i fjärranslutna övervakningslösning - Azure | Microsoft Docs"
description: "Den här kursen visar hur du använder enheten simulatorn med fjärråtkomst övervakning förkonfigurerade lösningen."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 426b7ff6114fd0b79a6af71a78705f11b80862bf
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>Testa din lösning med simulerade enheter

Den här kursen visar hur du använder anpassa enheten simulatorn mikrotjänster med fjärråtkomst övervakning förkonfigurerade lösningen. Den här kursen använder två scenarier i Contoso IoT-programmet för att visa funktionerna i enheten simulatorn.

Contoso vill testa en ny enhet för smart av glödlampa i det första scenariot. Om du vill utföra testerna måste skapa du en ny simulerade enhet med följande egenskaper:

*Egenskaper*

| Namn                     | Värden                      |
| ------------------------ | --------------------------- |
| Färg                    | Vit, röd, blå            |
| Ljusstyrkan               | 0 till 100                    |
| Uppskattad återstående livslängd | Nedräkning 10 000 timmar |

*Telemetri*

Följande tabell visar data i ligthbulb rapporterar till molnet som en dataström:

| Namn   | Värden      |
| ------ | ----------- |
| Status | ”på”, ”av” |

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

I det andra scenariot lägger du till en ny typ av telemetri till Contoso befintliga **kylaggregat** enhet.

Den här kursen visar hur du använder enheten simulatorn med fjärråtkomst övervakning förkonfigurerade lösningen:

I den här guiden får du lära dig hur man:

>[!div class="checklist"]
> * Skapa en ny enhetstyp av
> * Simulera anpassade beteende
> * Lägga till en ny enhet på instrumentpanelen
> * Skicka telemetri om anpassade från en befintlig typ av enhet

## <a name="prerequisites"></a>Krav

Om du vill följa den här självstudiekursen, måste en distribuerad instans av den fjärranslutna övervakningslösning i din Azure-prenumeration.

Om du inte har distribuerat remote övervakningslösning ännu, bör du genomföra den [Distribuera fjärråtkomst övervakning förkonfigurerade lösningen](iot-suite-remote-monitoring-deploy.md) kursen.

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>Tjänst för simuleringen

Tjänst för simuleringen i förkonfigurerade lösningen kan du göra ändringar i de inbyggda simulerade enhetstyper och skapa nya simulerade enhetstyper. Du kan använda anpassade enhetstyper för att testa funktionen för fjärranslutna övervakningslösning innan du ansluter din fysiska enheter till lösningen.

## <a name="create-a-simulated-device-type"></a>Skapa en simulerad enhetstyp

Det enklaste sättet att skapa en ny typ av enhet i simuleringen mikrotjänster är att kopiera och ändra en befintlig typ. Följande steg visar hur du kopierar inbyggt **kylaggregat** enhet för att skapa en ny **av glödlampa** enhet:

1. Använd följande kommando för att klona den **enheten simuleringen** GitHub-lagringsplatsen till den lokala datorn:

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. Varje typ av enhet har en modell JSON-fil och associerade skript i den `Services/data/devicemodels` mapp. Kopiera den **kylaggregat** filer för att skapa den **av glödlampa** filer som visas i följande tabell:

    | Källa                      | Mål                   |
    | --------------------------- | ----------------------------- |
    | kylaggregat 01.json             | Bild av glödlampa 01.json             |
    | skript/kylaggregat-01-state.js | skript/av glödlampa-01-state.js |
    | omstart/skript-method.js    | SwitchOn/skript-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Definiera egenskaperna för den nya enhetstypen

Den `lightbulb-01.json` filen definierar egenskaperna för typen, till exempel telemetrin genereras och metoderna som stöder. Följ anvisningarna nedan för uppdatering av `lightbulb-01.json` filen för att definiera den **av glödlampa** enhet:

1. I den `lightbulb-01.json` uppdaterar enhetens metadata som visas i följande utdrag:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. I den `lightbulb-01.json` uppdaterar definitionen simuleringen som visas i följande utdrag:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. I den `lightbulb-01.json` uppdaterar Typegenskaper enheten som visas i följande utdrag:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. I den `lightbulb-01.json` uppdaterar enheten telemetri typdefinitioner som visas i följande utdrag:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. I den `lightbulb-01.json` uppdaterar enheten typen metoder som visas i följande utdrag:

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

1. Spara den `lightbulb-01.json` filen.

### <a name="simulate-custom-device-behavior"></a>Simulera anpassade beteende

Den `scripts/lightbulb-01-state.js` filen definierar beteendet simulering av den **av glödlampa** typen. Följ anvisningarna nedan för uppdatering av `scripts/lightbulb-01-state.js` filen för att definiera beteendet för den **av glödlampa** enhet:

1. Redigera definition av tillstånd i den `scripts/lightbulb-01-state.js` filen som visas i följande utdrag:

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. Ersätt den **variera** funktionen med följande **Vänd** funktionen:

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

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Spara den `scripts/lightbulb-01-state.js` filen.

Den `scripts/SwitchOn-method.js` filen implementerar den **växeln på** metod i en **av glödlampa** enhet. Följ anvisningarna nedan för uppdatering av `scripts/SwitchOn-method.js` fil:

1. Redigera definition av tillstånd i den `scripts/SwitchOn-method.js` filen som visas i följande utdrag:

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

1. Spara den `scripts/SwitchOn-method.js` filen.

1. Skapa en kopia av `scripts/SwitchOn-method.js` fil med namnet `scripts/SwitchOff-method.js`.

1. Om du vill stänga av den av glödlampa redigera den **huvudsakliga** fungera i den `scripts/SwitchOff-method.js` enligt följande:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Spara den `scripts/SwitchOff-method.js` filen.

### <a name="test-the-lightbulb-device-type"></a>Testa typ av enhet av glödlampa

Att testa den **av glödlampa** typ av enhet måste du först testa din enhetstyp fungerar som förväntat genom att köra en lokal kopia av den **enheten simuleringen** service. När du har testat och testar dina nya enhetstypen lokalt, kan du återskapa behållaren och omdistribuera den **enheten simuleringen** tjänst till Azure.

Om du vill testa och felsöka dina ändringar lokalt, se [enheten simuleringen översikt](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md).

Konfigurera projektet för att kopiera den nya **av glödlampa** enhetsfiler till den angivna katalogen.

Om du vill testa den nya enheten i en distribuerad lösning finns i något av:

* [Distribuera behållare från anpassade docker-hubb-konto](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [Uppdatera en distribuerad behållare via manuell kopiering](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

På den **enheter** kan du etablera instanser av en ny typ:

![Visa listan över tillgängliga simulering](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

Du kan visa telemetri från den simulerade enheten:

![Vy av glödlampa telemetri](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

Du kan anropa den **SwitchOn** och **SwitchOff** metoder på din enhet:

![Anrop av glödlampa metoder](media/iot-suite-remote-monitoring-test/devicesmethods.png)

Om du vill skapa en Docker-avbildning med den nya enhetstypen för distribution till Azure, se [skapa en anpassad avbildning Docker](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image).

## <a name="add-a-new-telemetry-type"></a>Lägga till en ny telemetri

Det här avsnittet beskrivs hur du ändrar en befintlig typ simulerade enheten för att stödja en ny typ av telemetri.

### <a name="locate-the-chiller-device-type-files"></a>Hitta kylaggregat enheten typen filer

Följande steg visar hur du söker efter filer som definierar inbyggt **kylaggregat** enhet:

1. Om du inte redan har gjort det, använder du följande kommando för att klona den **enheten simuleringen** GitHub-lagringsplatsen till den lokala datorn:

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. Varje typ av enhet har en modell JSON-fil och associerade skript i den `Services/data/devicemodels` mapp. Filer som definierar den simulerade **kylaggregat** enhetstyp är:
    * `Services/data/devicemodels/chiller-01.json`
    * `Services/data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>Ange den nya typen telemetri

Följande steg visar hur du lägger till en ny **inre temperatur** typ till den **kylaggregat** enhetstyp:

1. Öppna filen `chiller-01.json`.

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

1. Spara den `chiller-01.json` filen.

1. Öppna filen `scripts/chiller-01-state.js`.

1. Lägg till följande fält till den **tillstånd** variabeln:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Lägg till följande rad i den **huvudsakliga** funktionen:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Spara den `scripts/chiller-01-state.js` filen.

### <a name="test-the-chiller-device-type"></a>Testa kylaggregat enhetstyp

Att testa den uppdaterade **kylaggregat** typ av enhet måste du först testa din enhetstyp fungerar som förväntat genom att köra en lokal kopia av den **enheten simuleringen** service. När du har testat och testar din uppdaterade enhetstyp lokalt, kan du återskapa behållaren och omdistribuera den **enheten simuleringen** tjänst till Azure.

När du kör den **enheten simuleringen** tjänsten lokalt, skickas telemetri till din fjärranslutna övervakningslösning. På den **enheter** kan du etablera instanser av din uppdaterade.

Om du vill testa och felsöka dina ändringar lokalt, se [kör tjänsten med Visual Studio](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio) eller [skapa och köra från kommandoraden](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line).

Om du vill testa den nya enheten i en distribuerad lösning finns i något av:

* [Distribuera behållare från anpassade docker-hubb-konto](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [Uppdatera en distribuerad behållare via manuell kopiering](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

På den **enheter** kan du etablera instanser av din uppdaterade:

![Lägg till uppdaterade kylaggregat](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

Du kan visa den nya **inre temperatur** telemetri från den simulerade enheten.

Om du vill skapa en Docker-avbildning med den nya enhetstypen för distribution till Azure, se [skapa en anpassad avbildning Docker](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image).

## <a name="next-steps"></a>Nästa steg

Den här självstudiekursen visades hur du vill:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Skapa en ny enhetstyp av
> * Simulera anpassade beteende
> * Lägga till en ny enhet på instrumentpanelen
> * Skicka telemetri om anpassade från en befintlig typ av enhet

Nu du har lärt dig hur du använder tjänsten enhet simulering, föreslagna nästa steg är att lära dig hur du [ansluta en fysisk enhet till din fjärranslutna övervakningslösning](iot-suite-connecting-devices-node.md).

Utvecklare om remote övervakningslösning, Läs mer:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->