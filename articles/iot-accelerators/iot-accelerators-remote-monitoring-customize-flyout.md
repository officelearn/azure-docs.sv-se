---
title: Lägg till en utfällning i gränssnittet för fjärr styrnings lösning – Azure | Microsoft Docs
description: Den här artikeln visar hur du lägger till en ny utfällning på en sida i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: afd8c5b01b9f36606bf6bd43fbed747ca83ba320
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006123"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägg till en anpassad utfällning i webb gränssnittet för lösnings Accelerator för fjärr styrning

Den här artikeln visar hur du lägger till en ny utfällning på en sida i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator. Artikeln beskriver:

- Hur du förbereder en lokal utvecklings miljö.
- Hur du lägger till en ny utfällning på en sida i webb gränssnittet.

Exemplet som utfälls i den här artikeln visas på sidan med rutnätet som beskriver instruktionen [Lägg till ett anpassat rutnät i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-grid.md) , som visar hur du lägger till.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktions guiden behöver du följande program vara installerad på den lokala utvecklings datorn:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Du bör följa stegen i följande artiklar innan du fortsätter:

- [Lägg till en anpassad sida i webb gränssnittet för lösnings Accelerator för fjärr styrning](iot-accelerators-remote-monitoring-customize-page.md).
- [Lägg till en anpassad tjänst i webb gränssnittet för webb gränssnittet för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-service.md)
- [Lägg till ett anpassat rutnät i webb gränssnittet för lösnings Accelerator för fjärr styrning](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Lägg till en utfällbar meny

För att lägga till en utfällning i webb gränssnittet, måste du lägga till källfilerna som definierar utfällingen och ändra vissa befintliga filer för att göra webb gränssnittet medvetet för den nya komponenten.

### <a name="add-the-new-files-that-define-the-flyout"></a>Lägg till de nya filerna som definierar utfällingen

För att komma igång innehåller mappen **src/genom gång/Components/Pages/pageWithFlyout/flyouts/exampleFlyout** de filer som definierar en utfällning:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Kopiera mappen **src/genom gång/Components/Pages/pageWithFlyout/flyouts** till mappen **src/Components/Pages/example** .

### <a name="add-the-flyout-to-the-page"></a>Lägg till utfällande på sidan

Ändra **urspr/Components/Pages/example/basicPage.js** för att lägga till utfällingen.

Lägg till **BTN** i importer från **komponenter/delade** och Lägg till importer för **svgs** och **ExampleFlyoutContainer**:

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

Lägg till en **Const** -definition för **closedFlyoutState** och Lägg till den i status i konstruktorn:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Lägg till följande funktioner i klassen **BasicPage** :

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Lägg till följande **Const** -definitioner till **Render** -funktionen:

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Lägg till en knapp för att öppna utfällingen på snabb menyn:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Lägg till text och den utfällda behållaren på sidans innehåll:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Testa utfällingen

Om webb gränssnittet inte redan körs lokalt kör du följande kommando i roten för din lokala kopia av lagrings platsen:

```cmd/sh
npm start
```

Föregående kommando kör användar gränssnittet lokalt på `http://localhost:3000/dashboard` . Gå till **exempel** sidan och klicka på **Öppna utfälld**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om vilka resurser som är tillgängliga för att hjälpa dig att lägga till eller anpassa sidor i webb gränssnittet i Solution Accelerator för fjärr styrning.

Nu när du har definierat en utfällning på en sida är nästa steg att [lägga till en panel på instrument panelen i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-panel.md).

Mer information om lösnings acceleratorn för fjärrövervakning finns i [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md).
