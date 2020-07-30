---
title: Lägga till en tjänst i gränssnittet för fjärr styrnings lösning – Azure | Microsoft Docs
description: Den här artikeln visar hur du lägger till en ny tjänst i webb gränssnittet för lösnings Accelerator för fjärr styrning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.custom: devx-track-javascript
ms.openlocfilehash: c214e9f8df88d6f48968e485b23d80c778d4d7ab
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422682"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägg till en anpassad tjänst i webb gränssnittet för webb gränssnittet för fjärrövervakning av Solution Accelerator

Den här artikeln visar hur du lägger till en ny tjänst i webb gränssnittet för lösnings Accelerator för fjärr styrning. Artikeln beskriver:

- Hur du förbereder en lokal utvecklings miljö.
- Så här lägger du till en ny tjänst i webb gränssnittet.

Exempel tjänsten i den här artikeln innehåller data för ett rutnät som avsnittet [Lägg till ett anpassat rutnät i avsnittet om hur du lägger till ett anpassat rutnät i webb gränssnittet för webb gränssnitt för fjärrövervakning i Solution Accelerator](iot-accelerators-remote-monitoring-customize-grid.md) .

I ett reagerar program agerar en tjänst vanligt vis i en server dels tjänst. I exemplen för Remote Monitoring Solution Accelerator ingår tjänster som interagerar med IoT Hub Manager och konfigurations mikrotjänster.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktions guiden behöver du följande program vara installerad på den lokala utvecklings datorn:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Du bör utföra stegen i artikeln [Lägg till en anpassad sida i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-page.md) innan du fortsätter.

## <a name="add-a-service"></a>Lägg till en tjänst

Om du vill lägga till en tjänst i webb gränssnittet måste du lägga till källfilerna som definierar tjänsten och ändra några befintliga filer för att göra webb gränssnittet medvetet för den nya tjänsten.

### <a name="add-the-new-files-that-define-the-service"></a>Lägg till de nya filerna som definierar tjänsten

För att komma igång innehåller mappen **src/genom gång/tjänster** de filer som definierar en enkel tjänst:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Mer information om hur tjänsterna implementeras finns i [introduktionen till den aktiva programmering som du saknar](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**modell/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Kopiera **exampleService.js** till mappen **src/services** och kopiera **exampleModels.js** till mappen **src/Services/Models** .

Uppdatera den **index.js** filen i mappen **src/Services** för att exportera den nya tjänsten:

```js
export * from './exampleService';
```

Uppdatera den **index.js** filen i mappen **src/Services/Models** för att exportera den nya modellen:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Konfigurera anrop till tjänsten från Store

För att komma igång innehåller mappen **src/genom gång/Arkiv/dämpare** en exempel minskning:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Kopiera **exampleReducer.js** till mappen **src/Store/reduces** .

Mer information om minsknings-och **Epics**finns i [Redux](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Konfigurera mellanprogram

Om du vill konfigurera mellanprogram lägger du till minsknings punkten i **rootReducer.js** -filen i mappen **src/Store** :

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

Lägg till Epics i **rootEpics.js** -filen i mappen **src/Store** :

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om vilka resurser som är tillgängliga för att hjälpa dig att lägga till eller anpassa tjänster i webb gränssnittet i Solution Accelerator för fjärr styrning.

Nu när du har definierat en tjänst är nästa steg att [lägga till ett anpassat rutnät i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-grid.md) som visar data som returnerats av tjänsten.

Mer information om lösnings acceleratorn för fjärrövervakning finns i [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md).
