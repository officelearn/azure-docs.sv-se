---
title: Lägga till en sida i lösningsgränssnittet för fjärrövervakning – Azure | Microsoft-dokument
description: I den här artikeln visas hur du lägger till en ny sida i webbgränssnittet för fjärrövervakningslösningsaccelerator.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 0228f317e2d3380f2387dd557a27203eb3abc4ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240267"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägga till en anpassad sida i webbgränssnittet för lösningsaccelerator för fjärrövervakning

I den här artikeln visas hur du lägger till en ny sida i webbgränssnittet för fjärrövervakningslösningsaccelerator. Artikeln beskriver:

- Hur man förbereder en lokal utvecklingsmiljö.
- Så här lägger du till en ny sida i webbgränssnittet.

Andra hur du kan visa utöka det här scenariot om du vill lägga till fler funktioner på sidan du lägger till.

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här programguiden behöver du följande programvara installerad på din lokala utvecklingsdator:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Förbereda en lokal utvecklingsmiljö för användargränssnittet

Gränssnittskoden för fjärrövervakningslösningsaccelerator implementeras med hjälp av [React](https://reactjs.org/) JavaScript-ramverket. Du hittar källkoden i [WebUI GitHub-databasen för fjärrövervakning.](https://github.com/Azure/pcs-remote-monitoring-webui)

Om du vill göra och testa ändringar i användargränssnittet kan du köra det på den lokala utvecklingsdatorn. Du kan också ansluta till en distribuerad instans av lösningsacceleratorn så att den kan interagera med dina riktiga eller simulerade enheter.

Om du vill förbereda din lokala utvecklingsmiljö använder du Git för att klona [WebUI-databasen](https://github.com/Azure/pcs-remote-monitoring-webui) för fjärrövervakning till den lokala datorn:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Lägg till en sida

Om du vill lägga till en sida i webbgränssnittet måste du lägga till källfilerna som definierar sidan och ändra vissa befintliga filer så att webbgränssnittet får kännedom om den nya sidan.

### <a name="add-the-new-files-that-define-the-page"></a>Lägga till de nya filerna som definierar sidan

För att komma igång innehåller **mappen src/walkthrough/components/pages/basicPage** fyra filer som definierar en enkel sida:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Skapa en ny mapp **src /components/pages/example** och kopiera dessa fyra filer till den.

### <a name="add-the-new-page-to-the-web-ui"></a>Lägga till den nya sidan i webbgränssnittet

Om du vill lägga till den nya sidan i webbgränssnittet gör du följande ändringar i befintliga filer:

1. Lägg till den nya sidbehållaren i **filen src/components/pages/index.js:**

    ```js
    export * from './example/basicPage.container';
    ```

1. (Valfritt)  Lägg till en SVG-ikon för den nya sidan. Mer information finns i [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Du kan använda en befintlig SVG-fil.

1. Lägg till sidnamnet i översättningsfilen, **public/locales/en/translations.json**. Webbgränssnittet använder [i18next](https://www.i18next.com/) för internationalisering.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Öppna **filen src/components/app.js** som definierar programsidan på den översta nivån. Lägg till den nya sidan i listan över importer:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Lägg till den nya sidan i `pagesConfig` matrisen i samma fil. Ange `to` adressen för rutten, referera till SVG-ikonen och översättningar som lagts `component` till tidigare och ange till sidans behållare:

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

1. Lägg till nya sökvägar `crumbsConfig` i matrisen:

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

    Den här exempelsidan har bara en sökspa, men vissa sidor kan ha fler.

Spara alla ändringar. Du är redo att köra webbgränssnittet med den nya sidan tillagd.

### <a name="test-the-new-page"></a>Testa den nya sidan

Vid en kommandotolk navigera till roten till din lokala kopia av databasen och kör följande kommandon för att installera de nödvändiga biblioteken och köra webbgränssnittet lokalt:

```cmd/sh
npm install
npm start
```

Det föregående kommandot kör användargränssnittet lokalt på [http://localhost:3000/dashboard](http://localhost:3000/dashboard).

Utan att ansluta din lokala instans av webbgränssnittet till en distribuerad instans av lösningsacceleratorn visas fel på instrumentpanelen. Dessa fel påverkar inte din möjlighet att testa din nya sida.

Du kan nu redigera koden medan webbplatsen körs lokalt och se webbgränssnittet uppdateras dynamiskt.

## <a name="optional-connect-to-deployed-instance"></a>[Valfritt] Ansluta till distribuerad instans

Du kan också ansluta din lokala kopia av webbgränssnittet till lösningsacceleratorn för fjärrövervakning i molnet:

1. Distribuera en **grundläggande** instans av lösningsacceleratorn med hjälp av **PCS** CLI. Anteckna namnet på distributionen och de autentiseringsuppgifter som du angav för den virtuella datorn. Mer information finns i [Distribuera med CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Använd Azure-portalen eller [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att aktivera SSH-åtkomst till den virtuella datorn som är värd för mikrotjänsterna i din lösning. Ett exempel:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Du bör endast aktivera SSH-åtkomst under test och utveckling. Om du aktiverar SSH [bör du inaktivera det igen så snart som möjligt](../security/fundamentals/network-best-practices.md).

1. Använd Azure-portalen eller [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att hitta namn och offentlig IP-adress för din virtuella dator. Ett exempel:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Använd SSH för att ansluta till din virtuella dator med IP-adressen från föregående steg och de autentiseringsuppgifter du angav när du körde **datorer** för att distribuera lösningen.

1. Om du vill att den lokala användarupplevelsen ska ansluta kör du följande kommandon vid bash-skalet i den virtuella datorn:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. När du ser att kommandot har slutförts och webbplatsen startar kan du koppla från den virtuella datorn.

1. I den lokala kopian av [WebUI-databasen](https://github.com/Azure/pcs-remote-monitoring-webui) för fjärrövervakning redigerar **du .env-filen** för att lägga till URL:en för den distribuerade lösningen:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de resurser som finns tillgängliga för att hjälpa dig att anpassa webbgränssnittet i lösningsacceleratorn för fjärrövervakning.

Nu när du har definierat en sida är nästa steg att lägga till [en anpassad tjänst i webbgränssnittet för fjärrövervakningslösningsaccelerator](iot-accelerators-remote-monitoring-customize-service.md) som hämtar data som ska visas i användargränssnittet.

Mer begreppsmässig information om lösningsacceleratorn för fjärrövervakning finns i [Fjärrövervakningsarkitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md).
