---
title: Lägga till en tjänst av lösningen för fjärrövervakning gränssnitt – Azure | Microsoft Docs
description: Den här artikeln visar hur du lägger till en ny tjänst i Remote Monitoring solution accelerator webbgränssnittet.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/02/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447054"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägga till en anpassad tjänst fjärrövervakning solution accelerator webbgränssnittet

Den här artikeln visar hur du lägger till en ny tjänst i Remote Monitoring solution accelerator webbgränssnittet. Artikeln beskriver:

- Så här förbereder du en lokal utvecklingsmiljö.
- Hur du lägger till en ny tjänst till webbgränssnittet.

Tjänsten exemplet i den här artikeln visar data för ett rutnät som den [lägga till ett anpassat rutnät till fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-grid.md) artikel visar hur du lägger till.

I en React-program interagerar en tjänst normalt med en backend tjänst. Exempel på lösningsacceleratorn för fjärrövervakning är tjänster som interagerar med IoT hub-hanterare och konfiguration av mikrotjänster.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande programvara installerad på din lokala utvecklingsdator måste slutföra stegen i den här guiden:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Du måste slutföra stegen i den [Lägg till en anpassad sida fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-page.md) artikel innan du fortsätter.

## <a name="add-a-service"></a>Lägg till en tjänst

Om du vill lägga till en tjänst till webbgränssnittet för måste du lägga till källfilerna som definierar tjänsten och ändra befintliga filer om du vill göra webbgränssnittet medveten om den nya tjänsten.

### <a name="add-the-new-files-that-define-the-service"></a>Lägg till de nya filerna som definierar tjänsten

Att komma igång, den **src/genomgången/tjänster** mappen innehåller de filer som definierar en enkel tjänst:

**exampleService.js**



Läs mer om hur tjänster är implementerade i [introduktion till reaktiv programmering du har saknat](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**model/exampleModels.js**



Kopiera **exampleService.js** till den **src/tjänster** mapp och kopierar **exampleModels.js** till den **src-services-modeller** mapp.

Uppdatera den **index.js** fil i den **src/tjänster** mapp du vill exportera den nya tjänsten:

```js
export * from './exampleService';
```

Uppdatera den **index.js** fil i den **src-services-modeller** mapp du vill exportera den nya modellen:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Konfigurera anrop till tjänsten från store

Att komma igång, den **src/genomgången/store/reducerare** mappen innehåller en exempel-reducer:

**exampleReducer.js**



Kopiera **exampleReducer.js** till den **src/store/reducerare** mapp.

Mer information om reducer och **Epics**, se [redux övervakas](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Konfigurera mellanprogrammet

Om du vill konfigurera mellanprogrammet, lägger du till reducer till den **rootReducer.js** fil i den **src/store** mapp:

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

Lägg till epics till den **rootEpics.js** fil i den **src/store** mapp:

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

I den här artikeln har du lärt dig om resurserna som är tillgängliga för att hjälpa dig lägga till eller anpassa tjänster i webbgränssnittet lösningsacceleratorn för fjärrövervakning.

Nu du har definierat en tjänst, nästa steg är att [lägga till ett anpassat rutnät till fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-grid.md) som visar data som returnerats av tjänsten.

Mer information om lösningsacceleratorn för fjärrövervakning finns [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md).
