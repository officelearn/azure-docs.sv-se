---
title: Lägg till en sida av lösningen för fjärrövervakning gränssnitt – Azure | Microsoft Docs
description: Den här artikeln visar hur du lägger till en ny sida i Remote Monitoring solution accelerator webbgränssnittet.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 95830cdffb232e16f9fbae51cfa11fbd18172c3c
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094590"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägg till en anpassad sida fjärrövervakning solution accelerator webbgränssnittet

Den här artikeln visar hur du lägger till en ny sida i Remote Monitoring solution accelerator webbgränssnittet. Artikeln beskriver:

- Så här förbereder du en lokal utvecklingsmiljö.
- Så här att lägga till en ny sida i webbläsaren.

Andra instruktionsguider utöka det här scenariot för att lägga till fler funktioner på sidan som du lägger till.

## <a name="prerequisites"></a>Förutsättningar

Följande programvara installerad på din lokala utvecklingsdator måste slutföra stegen i den här guiden:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Förbereda en lokal utvecklingsmiljö för Användargränssnittet

Lösningsacceleratorn för fjärrövervakning UI-kod implementeras med hjälp av den [reagera](https://reactjs.org/) javascriptramverk. Du hittar källkoden i den [Remote Monitoring webbgränssnittet](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub-lagringsplatsen.

Om du vill se och testa ändringar i Användargränssnittet, kan du köra den på din lokala utvecklingsdator. Du kan också ansluta den lokala kopian till en distribuerad instans av lösningsaccelerator så att den kan interagera med dina faktiska eller simulerade enheter.

För att förbereda din lokala utvecklingsmiljö, använder du Git för att klona den [Remote Monitoring webbgränssnittet](https://github.com/Azure/pcs-remote-monitoring-webui) databasen till den lokala datorn:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Lägg till en sida

Om du vill lägga till en sida i webbläsaren som du behöver lägga till källfilerna som definierar sidan och ändra befintliga filer om du vill göra webbgränssnittet medveten om den nya sidan.

### <a name="add-the-new-files-that-define-the-page"></a>Lägg till de nya filerna som definierar sidan

Att komma igång, den **src/genomgången/komponenter/sidor/basicPage** mappen innehåller fyra filer som definierar en enkel sida:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Skapa en ny mapp **src/komponenter/sidor/exempel** och kopiera dessa fyra filer till den.

### <a name="add-the-new-page-to-the-web-ui"></a>Lägga till den nya sidan i webbläsaren

Om du vill lägga till den nya sidan i webbläsaren, gör du följande ändringar i befintliga filer:

1. Lägg till den nya sida behållaren till den **src/components/pages/index.js** fil:

    ```js
    export * from './example/basicPage.container';
    ```

1. (Valfritt)  Lägg till en SVG-ikon för den nya sidan. Mer information finns i [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Du kan använda en befintlig SVG-fil.

1. Lägg till sidnamn i översättningsfilen **public/locales/en/translations.json**. Webb Gränssnittet använder [i18next](https://www.i18next.com/) för nationella inställningar.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Öppna den **src/components/app.js** -fil som definierar översta appen på sidan. Lägg till den nya sidan i listan med importer:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. I samma fil, lägger du till den nya sidan till den `pagesConfig` matris. Ange den `to` adressen för vägen, referera till SVG-ikonen och lagt till tidigare översättningar och ange den `component` till sidans behållare:

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. Lägg till några nya sökvägarna till den `crumbsConfig` matris:

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    Den här exempelsida har bara en brödsmula, men vissa sidor kan ha fler.

Spara alla ändringar. Du är redo att köra webbläsaren med den nya sidan har lagts till.

### <a name="test-the-new-page"></a>Testa den nya sidan

Navigera till roten i den lokala kopian av databasen i en kommandotolk och kör följande kommandon för att installera nödvändiga bibliotek och kör webbgränssnittet lokalt:

```cmd/sh
npm install
npm start
```

Föregående kommando körs lokalt på Användargränssnittet [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard).

Utan att ansluta din lokala instans av webbgränssnittet till en distribuerad solution accelerator-instans, får du felmeddelanden på instrumentpanelen. De här felen påverkar inte din möjlighet att testa den nya sidan.

Nu kan du redigera koden medan platsen körs lokalt och visa web UI uppdateras dynamiskt.

## <a name="optional-connect-to-deployed-instance"></a>[Valfritt] Ansluta till distribuerad instans

Alternativt kan du ansluta din lokala körande kopia av webbgränssnittet till lösningsacceleratorn för fjärrövervakning i molnet:

1. Distribuera en **grundläggande** instans av en solution accelerator med hjälp av den **datorer** CLI. Anteckna namnet på distributionen och autentiseringsuppgifterna för den virtuella datorn. Mer information finns i [distribuera med hjälp av CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Använd Azure-portalen eller [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) att aktivera SSH-åtkomst till den virtuella datorn som är värd för mikrotjänster i din lösning. Exempel:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Du bör bara aktivera SSH-åtkomst under testning och utveckling. Om du aktiverar SSH, [bör du inaktivera det igen så snart som möjligt](../security/azure-security-network-security-best-practices.md).

1. Använd Azure-portalen eller [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) att hitta namnet och den offentliga IP-adressen för den virtuella datorn. Exempel:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Använda SSH för att ansluta till den virtuella datorn med IP-adress från föregående steg och de autentiseringsuppgifter som du angav när du körde **datorer** du distribuerar lösningen.

1. Om du vill tillåta att ansluta lokala UX, kör du följande kommandon i bash-gränssnittet på den virtuella datorn:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. När du ser kommandot har slutförts och webbplatsen startar kan koppla du från den virtuella datorn.

1. I den lokala kopian av den [Remote Monitoring webbgränssnittet](https://github.com/Azure/pcs-remote-monitoring-webui) lagringsplatsen, redigera den **.env** fil att lägga till URL: en för din distribuerade lösningen:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om resurserna som är tillgängliga om du vill anpassa webbgränssnittet lösningsacceleratorn för fjärrövervakning.

Nu du har definierat en sida, nästa steg är att [lägga till en anpassad tjänst fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-service.md) som hämtar data som ska visas i Användargränssnittet.

Mer information om lösningsacceleratorn för fjärrövervakning finns [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md).
