---
title: Lägga till ett utfällbart tillägg i lösningsgränssnittet för fjärrövervakning – Azure | Microsoft-dokument
description: I den här artikeln visas hur du lägger till ett nytt utfällbart utfällbart på en sida i webbgränssnittet för lösningsaccelerator för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447122"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägga till ett anpassat utfällbart alternativ i webbgränssnittet för lösningsaccelerator för fjärrövervakning

I den här artikeln visas hur du lägger till ett nytt utfällbart utfällbart till en sida i webbgränssnittet för lösningsaccelerator för fjärrövervakning. Artikeln beskriver:

- Hur man förbereder en lokal utvecklingsmiljö.
- Så här lägger du till ett nytt utfällbart utfällbart sätt på en sida i webbgränssnittet.

Exemplet utfällbart i den här artikeln visas på sidan med rutnätet som i artikeln [Lägg till ett anpassat rutnät i webbgränssnittet för fjärrövervakningslösnings-accelerator](iot-accelerators-remote-monitoring-customize-grid.md) visar hur du lägger till.

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här programguiden behöver du följande programvara installerad på din lokala utvecklingsdator:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Du bör slutföra stegen i följande artiklar innan du fortsätter:

- [Lägg till en anpassad sida i webbgränssnittet för lösningsaccelerator för fjärrövervakning](iot-accelerators-remote-monitoring-customize-page.md).
- [Lägga till en anpassad tjänst i webbgränssnittet för lösningsaccelerator för fjärrövervakning](iot-accelerators-remote-monitoring-customize-service.md)
- [Lägga till ett anpassat rutnät i webbgränssnittet för lösningsaccelerator för fjärrövervakning](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Lägg till en utfällbar meny

Om du vill lägga till ett utfällbart objekt i webbgränssnittet måste du lägga till källfilerna som definierar utfällbara objekt och ändra vissa befintliga filer så att webbgränssnittet får kännedom om den nya komponenten.

### <a name="add-the-new-files-that-define-the-flyout"></a>Lägga till de nya filerna som definierar utfällbara filer

För att komma igång innehåller **mappen src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout-mappen** de filer som definierar ett utfällbart objekt:

**exempelFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exempelFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Kopiera **mappen src/walkthrough/components/pages/pageWithFlyout/flyouts** till **mappen src/components/pages/example.**

### <a name="add-the-flyout-to-the-page"></a>Lägga till utfällbart på sidan

Ändra **src/components/pages/example/basicPage.js** för att lägga till utfällbara.

Lägg till **Btn** i importen från **komponenter/delade** och lägg till import för **svgs** och **ExampleFlyoutContainer:**

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

Lägg till en **const** definition för **closedFlyoutState** och lägg till den i tillståndet i konstruktorn:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Lägg till följande funktioner i klassen **BasicPage:**

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Lägg till följande **const** definitioner i **renderingsfunktionen:**

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Lägg till en knapp för att öppna utfällbara till snabbmenyn:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Lägg till text och den utfällbara behållaren i sidinnehållet:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Testa utfällbara

Om webbgränssnittet inte redan körs lokalt kör du följande kommando i roten till den lokala kopian av databasen:

```cmd/sh
npm start
```

Det föregående kommandot kör användargränssnittet lokalt på [http://localhost:3000/dashboard](http://localhost:3000/dashboard). Navigera till sidan **Exempel** och klicka på **Öppna utfällbart objekt.**

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de resurser som finns tillgängliga för att hjälpa dig att lägga till eller anpassa sidor i webbgränssnittet i lösningsacceleratorn för fjärrövervakning.

Nu har du definierat ett utfällbart på en sida, nästa steg är att [lägga till en panel på instrumentpanelen i webbgränssnittet för fjärrövervakningslösningsaccelerator](iot-accelerators-remote-monitoring-customize-panel.md).

Mer begreppsmässig information om lösningsacceleratorn för fjärrövervakning finns i [Fjärrövervakningsarkitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md).
