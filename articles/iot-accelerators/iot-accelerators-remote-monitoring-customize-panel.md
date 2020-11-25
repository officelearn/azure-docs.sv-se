---
title: Lägga till en panel i gränssnittet för fjärr styrnings lösning – Azure | Microsoft Docs
description: Den här artikeln visar hur du lägger till en ny panel på instrument panelen i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 1dcca8409022ba4cf1f988b7c777e3a1fa511060
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006072"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägg till en anpassad panel i instrument panelen i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator

Den här artikeln visar hur du lägger till en ny panel på en instrument panels sida i webb gränssnittet för övervakning av Solution Accelerator. Artikeln beskriver:

- Hur du förbereder en lokal utvecklings miljö.
- Hur du lägger till en ny panel på en instrument panels sida i webb gränssnittet.

Exempel panelen i den här artikeln visas på sidan befintlig instrument panel.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktions guiden behöver du följande program vara installerad på den lokala utvecklings datorn:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Du bör följa stegen i artikeln [Lägg till en anpassad sida i avsnittet om webb gränssnitt för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-page.md) innan du fortsätter.

## <a name="add-a-panel"></a>Lägg till en panel

Om du vill lägga till en panel i webb gränssnittet måste du lägga till källfilerna som definierar panelen och sedan ändra instrument panelen så att panelen visas.

### <a name="add-the-new-files-that-define-the-panel"></a>Lägg till de nya filerna som definierar panelen

För att komma igång innehåller mappen **src/genom gång/komponenter/sidor/instrument panel/paneler/examplePanel** de filer som definierar en panel, inklusive:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Kopiera mappen **src/genom gång/komponenter/sidor/instrument panel/paneler/examplePanel** till mappen **src/Components/Pages/Dashboard/** panels.

Lägg till följande export i filen **src/genom gång/Components/Pages/Dashboard/panels/index.js** :

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Lägg till panelen på instrument panelen

Ändra fliken **src/Components/Pages/Dashboard/dashboard.js** för att lägga till panelen.

Lägg till exempel panelen i listan över importer från paneler:

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

Lägg till följande cell definition i rutnätet i sid innehållet:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Testa utfällingen

Om webb gränssnittet inte redan körs lokalt kör du följande kommando i roten för din lokala kopia av lagrings platsen:

```cmd/sh
npm start
```

Föregående kommando kör användar gränssnittet lokalt på `http://localhost:3000/dashboard` . Gå till **instrument panels** sidan för att visa den nya panelen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de resurser som är tillgängliga för att hjälpa dig att lägga till eller anpassa instrument paneler i webb gränssnittet i Solution Accelerator för fjärr styrning.

Mer information om lösnings acceleratorn för fjärrövervakning finns i [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md).
