---
title: Lägga till en utfällbara av lösningen för fjärrövervakning gränssnitt – Azure | Microsoft Docs
description: Den här artikeln visar hur du lägger till en ny utfällbara på en sida i Remote Monitoring solution accelerator webbgränssnittet.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 9ba58ca887332d2ea224320951b25031cacbef0d
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094608"
---
# <a name="add-a-custom-fly-out-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägg till en anpassad utfällbara till fjärrövervakning solution accelerator webbgränssnittet

Den här artikeln visar hur du lägger till en ny utfällbara på en sida i Remote Monitoring solution accelerator webbgränssnittet. Artikeln beskriver:

- Så här förbereder du en lokal utvecklingsmiljö.
- Hur du lägger till en ny utfällbara till en sida i webbläsaren.

I exempel utfällbara i den här artikeln visas på sidan med rutnätet som den [lägga till ett anpassat rutnät till fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-grid.md) artikel visar hur du lägger till.

## <a name="prerequisites"></a>Förutsättningar

Följande programvara installerad på din lokala utvecklingsdator måste slutföra stegen i den här guiden:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Innan du fortsätter bör du genomföra stegen i följande artiklar:

- [Lägg till en anpassad sida fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-page.md).
- [Lägga till en anpassad tjänst fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-service.md)
- [Lägg till ett anpassat rutnät till fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-fly-out"></a>Lägg till en utfällbara

Om du vill lägga till en utfällbara till webbgränssnittet för måste du lägga till källfilerna som definierar utfällbara och ändra befintliga filer om du vill göra webbgränssnittet medveten om den nya komponenten.

### <a name="add-the-new-files-that-define-the-fly-out"></a>Lägg till de nya filerna som definierar utfällbara

Att komma igång, den **src/genomgången/komponenter/sidor/pageWithFlyout/egna undermenyer/exampleFlyout** mappen innehåller de filer som definierar en utfällbara:

**exampleFlyout.container.js**

[!code-javascript[Example fly-out container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example fly-out container")]

**exampleFlyout.js**

[!code-javascript[Example fly-out](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example fly-out")]

Kopiera den **src/genomgången/komponenter/sidor/pageWithFlyout/egna undermenyer** mappen till den **src/komponenter/sidor/exempel** mapp.

### <a name="add-the-fly-out-to-the-page"></a>Lägga till utfällbara på sidan

Ändra den **src/components/pages/example/basicPage.js** att lägga till utfällbara.

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

Lägg till en knapp för att öppna utfällbara till på snabbmenyn:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Lägg till text och behållaren utfällbara sidinnehållet:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-fly-out"></a>Testa utfällbara

Om webb Användargränssnittet inte redan körs lokalt, kör du följande kommando i roten av den lokala kopian av databasen:

```cmd/sh
npm start
```

Föregående kommando körs lokalt på Användargränssnittet [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Navigera till den **exempel** och klicka på **öppna utfällt**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om resurserna som är tillgängliga för att hjälpa dig lägga till eller anpassa sidor i webbgränssnittet lösningsacceleratorn för fjärrövervakning.

Nu du har definierat en utfällbara på en sida, nästa steg är att [lägga till en panel på instrumentpanelen för fjärrövervakning solution accelerator webbläsaren](iot-accelerators-remote-monitoring-customize-panel.md).

Mer information om lösningsacceleratorn för fjärrövervakning finns [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md).
