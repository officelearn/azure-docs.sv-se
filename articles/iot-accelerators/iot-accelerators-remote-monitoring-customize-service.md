---
title: Lägga till en tjänst i lösningsgränssnittet för fjärrövervakning – Azure | Microsoft-dokument
description: I den här artikeln visas hur du lägger till en ny tjänst i webbgränssnittet för fjärrövervakningslösningsaccelerator.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447054"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägga till en anpassad tjänst i webbgränssnittet för lösningsaccelerator för fjärrövervakning

I den här artikeln visas hur du lägger till en ny tjänst i webbgränssnittet för fjärrövervakningslösningsaccelerator. Artikeln beskriver:

- Hur man förbereder en lokal utvecklingsmiljö.
- Så här lägger du till en ny tjänst i webbgränssnittet.

Exempeltjänsten i den här artikeln innehåller data för ett rutnät som i artikeln [Lägg till ett anpassat rutnät i webbgränssnittet för fjärrövervakningslösningsportal](iot-accelerators-remote-monitoring-customize-grid.md) visar hur du lägger till.

I ett React-program interagerar en tjänst vanligtvis med en backend-tjänst. Exempel i lösningsacceleratorn för fjärrövervakning är tjänster som interagerar med IoT-hubbhanteraren och konfigurationsmikrotjänsterna.

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här programguiden behöver du följande programvara installerad på din lokala utvecklingsdator:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Du bör slutföra stegen i sidan Lägg till en anpassad sida i [webbgränssnittet för webbgränssnitt för fjärrövervakningslösningsaccelerator](iot-accelerators-remote-monitoring-customize-page.md) innan du fortsätter.

## <a name="add-a-service"></a>Lägg till en tjänst

Om du vill lägga till en tjänst i webbgränssnittet måste du lägga till källfilerna som definierar tjänsten och ändra vissa befintliga filer så att webbgränssnittet får kännedom om den nya tjänsten.

### <a name="add-the-new-files-that-define-the-service"></a>Lägga till de nya filer som definierar tjänsten

För att komma igång innehåller **mappen src/walkthrough/services** de filer som definierar en enkel tjänst:

**exempelService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Mer information om hur tjänster implementeras finns i [Introduktionen till Reaktiv programmering som du har saknat.](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)

**modell/exempelModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Kopiera **exempelService.js** till **mappen src/services** och kopiera **exampleModels.js** till **mappen src/services/models.**

Uppdatera **filen index.js** i **mappen src/services** för att exportera den nya tjänsten:

```js
export * from './exampleService';
```

Uppdatera **filen index.js** i mappen **src/services/models** för att exportera den nya modellen:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Ställ in anrop till tjänsten från butiken

För att komma igång innehåller **mappen src/walkthrough/store/reducers** en exempelreducerare:

**exempelReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Kopiera **exempelReducer.js** till **mappen src/store/reducers.**

Mer information om reduceraren och **Epics**finns i [redux-observerable](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Konfigurera mellanmaterialet

Om du vill konfigurera mellanprogram lägger du till reduceraren i **filen rootReducer.js** i **src/store-mappen:**

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

Lägg till eposen i **filen rootEpics.js** i **mappen src/store:**

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

I den här artikeln har du lärt dig om de resurser som finns tillgängliga för att hjälpa dig att lägga till eller anpassa tjänster i webbgränssnittet i lösningsacceleratorn för fjärrövervakning.

Nu när du har definierat en tjänst är nästa steg att [lägga till ett anpassat rutnät i webbgränssnittet för fjärrövervakningslösningsaccelerator](iot-accelerators-remote-monitoring-customize-grid.md) som visar data som returneras av tjänsten.

Mer begreppsmässig information om lösningsacceleratorn för fjärrövervakning finns i [Fjärrövervakningsarkitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md).
