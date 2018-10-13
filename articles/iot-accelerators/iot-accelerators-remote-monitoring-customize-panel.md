---
title: Lägga till en panel i av lösningen för fjärrövervakning gränssnitt – Azure | Microsoft Docs
description: Den här artikeln visar hur du lägger till en ny panel på instrumentpanelen för fjärrövervakning solution accelerator webbläsaren.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165886"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägga till en anpassad panel på instrumentpanelen för fjärrövervakning solution accelerator webbläsaren

Den här artikeln visar hur du lägger till en ny panel på en instrumentpanelssida för fjärrövervakning solution accelerator webbläsaren. Artikeln beskriver:

- Så här förbereder du en lokal utvecklingsmiljö.
- Hur du lägger till en ny panel till en instrumentpanelssida i webbläsaren.

Exempel-panelen i den här artikeln visar på den befintliga instrumentpanelen.

## <a name="prerequisites"></a>Förutsättningar

Följande programvara installerad på din lokala utvecklingsdator måste slutföra stegen i den här guiden:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Du måste slutföra stegen i den [Lägg till en anpassad sida fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-page.md) artikeln innan du fortsätter.

## <a name="add-a-panel"></a>Lägg till en panel

Om du vill lägga till en panel till webbgränssnittet för måste du lägga till källfilerna som definierar panelen och sedan ändra instrumentpanelen för att visa panelen.

### <a name="add-the-new-files-that-define-the-panel"></a>Lägg till de nya filerna som definierar panelen

Att komma igång, den **src/genomgången/komponenter/sidor/instrumentpanel/paneler/examplePanel** mappen innehåller de filer som definierar en panel, inklusive:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Kopiera den **src/genomgången/komponenter/sidor/instrumentpanel/paneler/examplePanel** mappen till den **src/komponenter/sidor/instrumentpanel/paneler** mapp.

Lägg till följande export till den **src/walkthrough/components/pages/dashboard/panels/index.js** fil:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Lägga till panelen på instrumentpanelen

Ändra den **src/components/pages/dashboard/dashboard.js** att lägga till panelen.

Lägg till panelen exempel i listan med importer från paneler:

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

Lägg till följande celldefinition i rutnätet i sidans innehåll:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Testa utfällt

Om webb Användargränssnittet inte redan körs lokalt, kör du följande kommando i roten av den lokala kopian av databasen:

```cmd/sh
npm start
```

Föregående kommando körs lokalt på Användargränssnittet [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Navigera till den **instrumentpanelen** sidan för att se den nya panelen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om resurserna som är tillgängliga för att hjälpa dig lägga till eller anpassa instrumentpaneler i webbgränssnittet lösningsacceleratorn för fjärrövervakning.

Mer information om lösningsacceleratorn för fjärrövervakning finns [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md).
