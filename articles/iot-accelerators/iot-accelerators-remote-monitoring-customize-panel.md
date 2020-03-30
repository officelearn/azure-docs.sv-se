---
title: Lägga till en panel i användargränssnittet för fjärrövervakningslösning – Azure | Microsoft-dokument
description: I den här artikeln visas hur du lägger till en ny panel på instrumentpanelen i webbgränssnittet för lösningsaccelerator för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447071"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägga till en anpassad panel på instrumentpanelen i webbgränssnittet för lösningsaccelerator för fjärrövervakning

I den här artikeln visas hur du lägger till en ny panel på en instrumentpanelssida i webbgränssnittet för lösningsaccelerator för fjärrövervakning. Artikeln beskriver:

- Hur man förbereder en lokal utvecklingsmiljö.
- Så här lägger du till en ny panel på en instrumentpanelssida i webbgränssnittet.

Exempelpanelen i den här artikeln visas på den befintliga instrumentpanelssidan.

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här programguiden behöver du följande programvara installerad på din lokala utvecklingsdator:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Du bör slutföra stegen i [sidan Lägg till en anpassad sida i webbgränssnittet för webbgränssnitt för fjärrövervakningslösningslösning](iot-accelerators-remote-monitoring-customize-page.md) innan du fortsätter.

## <a name="add-a-panel"></a>Lägg till en panel

Om du vill lägga till en panel i webbgränssnittet måste du lägga till källfilerna som definierar panelen och sedan ändra instrumentpanelen för att visa panelen.

### <a name="add-the-new-files-that-define-the-panel"></a>Lägga till de nya filerna som definierar panelen

För att komma igång innehåller **mappen src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel/examplePanels/examplePanels mappen** som definierar en panel, inklusive:

**exempelPanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Kopiera **mappen src/walkthrough/components/pages/dashboard/panels/examplePanel** till mappen **src/components/pages/dashboard/panels.**

Lägg till följande export i **src/genomgång/components/pages/dashboard/panels/index.js-filen:**

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Lägga till panelen på instrumentpanelen

Ändra **src/components/pages/dashboard/dashboard.js** för att lägga till panelen.

Lägg till exempelpanelen i listan över import från paneler:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Lägg till följande celldefinition i rutnätet i sidinnehållet:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Testa utfällbara

Om webbgränssnittet inte redan körs lokalt kör du följande kommando i roten till den lokala kopian av databasen:

```cmd/sh
npm start
```

Det föregående kommandot kör användargränssnittet lokalt på [http://localhost:3000/dashboard](http://localhost:3000/dashboard). Gå till **instrumentpanelssidan** för att visa den nya panelen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de resurser som finns tillgängliga för att hjälpa dig att lägga till eller anpassa instrumentpaneler i webbgränssnittet i lösningsacceleratorn för fjärrövervakning.

Mer begreppsmässig information om lösningsacceleratorn för fjärrövervakning finns i [Fjärrövervakningsarkitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md).
