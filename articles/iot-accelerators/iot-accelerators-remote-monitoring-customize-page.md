---
title: Lägga till en sida i gränssnittet för fjärr styrnings lösning – Azure | Microsoft Docs
description: Den här artikeln visar hur du lägger till en ny sida i webb gränssnittet för lösnings Accelerator för fjärr styrning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 2e54cd0e3c5b9499a3bc1a499ae4cef62c73160e
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070715"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägg till en anpassad sida i webb gränssnittet för webb gränssnittet för fjärrövervakning av Solution Accelerator

Den här artikeln visar hur du lägger till en ny sida i webb gränssnittet för lösnings Accelerator för fjärr styrning. Artikeln beskriver:

- Hur du förbereder en lokal utvecklings miljö.
- Så här lägger du till en ny sida i webb gränssnittet.

Andra instruktions guider utökar det här scenariot för att lägga till fler funktioner på sidan som du lägger till.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktions guiden behöver du följande program vara installerad på den lokala utvecklings datorn:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Förbered en lokal utvecklings miljö för användar gränssnittet

UI-koden för fjärr styrnings Accelerator implementeras med hjälp av den [reagera](https://reactjs.org/) på JavaScript-ramverket. Du hittar käll koden i WebUI GitHub-lagringsplatsen för [fjärr övervakning](https://github.com/Azure/pcs-remote-monitoring-webui) .

Om du vill göra och testa ändringar i användar gränssnittet kan du köra det på din lokala utvecklings dator. Om du vill kan den lokala kopian ansluta till en distribuerad instans av Solution Accelerator för att göra det möjligt att interagera med dina verkliga eller simulerade enheter.

För att förbereda din lokala utvecklings miljö använder du git för att klona WebUI-lagringsplatsen för [fjärrövervakning](https://github.com/Azure/pcs-remote-monitoring-webui) till din lokala dator:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Lägg till en sida

Om du vill lägga till en sida i webb gränssnittet måste du lägga till källfilerna som definierar sidan och ändra några befintliga filer för att göra webb gränssnittet medvetet för den nya sidan.

### <a name="add-the-new-files-that-define-the-page"></a>Lägg till de nya filerna som definierar Sidan

För att komma igång innehåller mappen **src/genom gång/komponenter/sidor/basicPage** fyra filer som definierar en enkel sida:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage. scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Skapa en ny mapp **src/Components/Pages/example** och kopiera dessa fyra filer till den.

### <a name="add-the-new-page-to-the-web-ui"></a>Lägg till den nya sidan i webb gränssnittet

Om du vill lägga till den nya sidan i webb gränssnittet gör du följande ändringar i befintliga filer:

1. Lägg till den nya sid behållaren i filen **src/Components/Pages/index.js** :

    ```js
    export * from './example/basicPage.container';
    ```

1. Valfritt  Lägg till en SVG-ikon för den nya sidan. Mer information finns i [webui/src/Utilities/Readme. MD](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Du kan använda en befintlig SVG-fil.

1. Lägg till sid namnet i översättnings filen, **offentliga/lokala/en/translations.jspå**. Webb gränssnittet använder [i18next](https://www.i18next.com/) för internationalisering.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Öppna filen **src/Components/app.js** som definierar program sidan på den översta nivån. Lägg till den nya sidan i listan över importer:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. I samma fil lägger du till den nya sidan i `pagesConfig` matrisen. Ange `to` adressen för vägen, referera till SVG-ikonen och de översättningar som lagts till tidigare och Ställ in på `component` sidans behållare:

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

1. Lägg till eventuella nya spår i `crumbsConfig` matrisen:

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

    Den här exempel sidan har bara en dynamiska information, men vissa sidor kan ha mer.

Spara alla ändringar. Du är redo att köra webb gränssnittet med den nya sidan som lagts till.

### <a name="test-the-new-page"></a>Testa den nya sidan

I en kommando tolk navigerar du till roten i din lokala kopia av lagrings platsen och kör följande kommandon för att installera de bibliotek som krävs och kör webb gränssnittet lokalt:

```cmd/sh
npm install
npm start
```

Föregående kommando kör användar gränssnittet lokalt på `http://localhost:3000/dashboard` .

Om du inte ansluter din lokala instans av webb gränssnittet till en distribuerad instans av Solution Accelerator visas fel på instrument panelen. De här felen påverkar inte din möjlighet att testa din nya sida.

Nu kan du redigera koden medan platsen körs lokalt och se webb GRÄNSSNITTs uppdateringen dynamiskt.

## <a name="optional-connect-to-deployed-instance"></a>Valfritt Anslut till distribuerad instans

Du kan också ansluta din lokala kopia av webb gränssnittet till den fjärrstyrda lösningen för övervakning i molnet:

1. Distribuera en **grundläggande** instans av Solution Accelerator med **PC** cli. Anteckna namnet på distributionen och de autentiseringsuppgifter som du har angett för den virtuella datorn. Mer information finns i [distribuera med CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Använd Azure Portal eller [AZ CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) för att aktivera SSH-åtkomst till den virtuella dator som är värd för mikrotjänsterna i din lösning. Exempel:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Du bör bara aktivera SSH-åtkomst vid testning och utveckling. Om du aktiverar SSH [bör du inaktivera det igen så snart som möjligt](../security/fundamentals/network-best-practices.md).

1. Använd Azure Portal eller [AZ CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) för att hitta namnet och den offentliga IP-adressen för den virtuella datorn. Exempel:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Använd SSH för att ansluta till din virtuella dator med hjälp av IP-adressen från föregående steg och de autentiseringsuppgifter du angav när du körde **datorerna** för att distribuera lösningen.

1. Om du vill tillåta att det lokala UX: en ansluter kör du följande kommandon i bash-gränssnittet på den virtuella datorn:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. När du har granskat kommandot slutförs och webbplatsen startar kan du koppla från den virtuella datorn.

1. I din lokala kopia av WebUI-lagringsplatsen för [fjärrövervakning](https://github.com/Azure/pcs-remote-monitoring-webui) redigerar du **. kuvert** -filen för att lägga till URL: en för din distribuerade lösning:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om vilka resurser som är tillgängliga som hjälper dig att anpassa webb gränssnittet i Solution Accelerator för fjärr styrning.

Nu när du har definierat en sida är nästa steg att [lägga till en anpassad tjänst i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-service.md) som hämtar data som ska visas i användar gränssnittet.

Mer information om lösnings acceleratorn för fjärrövervakning finns i [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md).