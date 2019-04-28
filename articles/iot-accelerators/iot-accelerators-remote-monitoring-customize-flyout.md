---
title: Lägga till en utfällt av lösningen för fjärrövervakning gränssnitt – Azure | Microsoft Docs
description: Den här artikeln visar hur du lägger till en ny utfällt på en sida i Remote Monitoring solution accelerator webbgränssnittet.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/05/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447122"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägg till en anpassad utfällt till fjärrövervakning solution accelerator webbgränssnittet

Den här artikeln visar hur du lägger till en ny utfällt på en sida i Remote Monitoring solution accelerator webbgränssnittet. Artikeln beskriver:

- Så här förbereder du en lokal utvecklingsmiljö.
- Hur du lägger till en ny utfällt till en sida i webbläsaren.

Exempel utfällt i den här artikeln visas på sidan med rutnätet som den [lägga till ett anpassat rutnät till fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-grid.md) artikel visar hur du lägger till.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande programvara installerad på din lokala utvecklingsdator måste slutföra stegen i den här guiden:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Innan du fortsätter bör du genomföra stegen i följande artiklar:

- [Lägg till en anpassad sida fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-page.md).
- [Lägga till en anpassad tjänst fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-service.md)
- [Lägg till ett anpassat rutnät till fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Lägg till en utfällbar meny

Om du vill lägga till en utfällt till webbgränssnittet för måste du lägga till källfilerna som definierar utfällt och ändra befintliga filer om du vill göra webbgränssnittet medveten om den nya komponenten.

### <a name="add-the-new-files-that-define-the-flyout"></a>Lägg till de nya filerna som definierar utfällt

Att komma igång, den **src/genomgången/komponenter/sidor/pageWithFlyout/egna undermenyer/exampleFlyout** mappen innehåller de filer som definierar utfällbar meny:

**exampleFlyout.container.js**



**exampleFlyout.js**



Kopiera den **src/genomgången/komponenter/sidor/pageWithFlyout/egna undermenyer** mappen till den **src/komponenter/sidor/exempel** mapp.

### <a name="add-the-flyout-to-the-page"></a>Lägga till utfällt på sidan

Ändra den **src/components/pages/example/basicPage.js** att lägga till den utfällbar meny.

Lägg till **Btn** för import från **komponenter/delat** och Lägg till import för **svgs** och **ExampleFlyoutContainer**:

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Lägg till en **konstanta** definition för **closedFlyoutState** och lägga till den i tillståndet i konstruktorn:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Lägg till följande funktioner till den **BasicPage** klass:

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Lägg till följande **konstanta** definitioner för att den **rendera** funktionen:

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Lägg till en knapp för att öppna utfällt till på snabbmenyn:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Lägg till text och behållaren utfällt sidinnehållet:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Testa utfällt

Om webb Användargränssnittet inte redan körs lokalt, kör du följande kommando i roten av den lokala kopian av databasen:

```cmd/sh
npm start
```

Föregående kommando körs lokalt på Användargränssnittet [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Navigera till den **exempel** och klicka på **öppna utfällt**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om resurserna som är tillgängliga för att hjälpa dig lägga till eller anpassa sidor i webbgränssnittet lösningsacceleratorn för fjärrövervakning.

Nu du har definierat utfällbar meny på en sida, nästa steg är att [lägga till en panel på instrumentpanelen för fjärrövervakning solution accelerator webbläsaren](iot-accelerators-remote-monitoring-customize-panel.md).

Mer information om lösningsacceleratorn för fjärrövervakning finns [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md).
