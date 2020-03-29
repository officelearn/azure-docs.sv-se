---
title: Lägga till ett rutnät i lösningsgränssnittet för fjärrövervakning – Azure | Microsoft-dokument
description: I den här artikeln visas hur du lägger till en ny gid på en sida i webbgränssnittet för lösningsaccelerator för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447105"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägga till ett anpassat rutnät i webbgränssnittet för lösningsaccelerator för fjärrövervakning

I den här artikeln visas hur du lägger till ett nytt rutnät på en sida i webbgränssnittet för lösningsaccelerator för fjärrövervakning. Artikeln beskriver:

- Hur man förbereder en lokal utvecklingsmiljö.
- Så här lägger du till ett nytt rutnät på en sida i webbgränssnittet.

Exempelrutnätet i den här artikeln visar data från tjänsten som hur du lägger till [en anpassad tjänst i webbgränssnittet för fjärrövervakningslösningsaccelerator](iot-accelerators-remote-monitoring-customize-service.md) visar hur du lägger till.

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här programguiden behöver du följande programvara installerad på din lokala utvecklingsdator:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Du bör slutföra stegen i följande artiklar innan du fortsätter:

- [Lägg till en anpassad sida i webbgränssnittet för lösningsaccelerator för fjärrövervakning](iot-accelerators-remote-monitoring-customize-page.md).
- [Lägga till en anpassad tjänst i webbgränssnittet för lösningsaccelerator för fjärrövervakning](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Lägg till ett rutnät

Om du vill lägga till ett rutnät i webbgränssnittet måste du lägga till källfilerna som definierar rutnätet och ändra vissa befintliga filer så att webbgränssnittet får kännedom om den nya komponenten.

### <a name="add-the-new-files-that-define-the-grid"></a>Lägga till de nya filerna som definierar rutnätet

För att komma igång innehåller **mappen src/walkthrough/components/pages/pageWithGrid/exampleGrid** de filer som definierar ett rutnät:

**exempelGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exempelGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Kopiera **mappen src/walkthrough/components/pages/pageWithGrid/exampleGrid** till **mappen src/components/pages/example.**

### <a name="add-the-grid-to-the-page"></a>Lägga till rutnätet på sidan

Ändra **src/components/pages/example/basicPage.container.js** enligt följande för att importera tjänstdefinitionerna:

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Ändra **src/components/pages/example/basicPage.js** enligt följande för att lägga till rutnätet:

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Ändra **src/components/pages/example/basicPage.test.js** enligt följande för att uppdatera testerna:

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>Testa rutnätet

Om webbgränssnittet inte redan körs lokalt kör du följande kommando i roten till den lokala kopian av databasen:

```cmd/sh
npm start
```

Det föregående kommandot kör användargränssnittet lokalt på [http://localhost:3000/dashboard](http://localhost:3000/dashboard). Navigera till sidan **Exempel** om du vill visa rutnätets visningsdata från tjänsten.

## <a name="select-rows"></a>Markera rader

Det finns två alternativ för att göra det möjligt för en användare att välja rader i rutnätet:

### <a name="hard-select-rows"></a>Hårdval rader

Om en användare behöver agera på flera rader samtidigt använder du kryssrutor på rader:

1. Aktivera hårdval av rader genom att lägga till en **kryssrutaColumn** i **kolumnenDefs** som tillhandahålls i rutnätet. **kryssrutaColumn** definieras i **/src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Om du vill komma åt de markerade objekten får du en referens till det interna rutnätets API:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Ange sammanhangsknappar till sidan när en rad i rutnätet är hårt markerad:

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. När du klickar på en kontextknapp får du de hårt valda objekten att utföra arbetet med:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Mjuka rader

Om användaren bara behöver agera på en enda rad konfigurerar du en länk för mjukval för en eller flera kolumner i **columnDefs**.

1. I **exempelGridConfig.js**lägger du till **SoftSelectLinkRenderer** som **cellRendererFramework** för en **columnDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. När du klickar på en länk för mjuk markering utlöses händelsen **onSoftSelectChange.** Utför vilken åtgärd som önskas för den raden, till exempel att öppna en utfällbara information. Det här exemplet skriver helt enkelt till konsolen:

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de resurser som finns tillgängliga för att hjälpa dig att lägga till eller anpassa sidor i webbgränssnittet i lösningsacceleratorn för fjärrövervakning.

Nu när du har definierat ett rutnät är nästa steg att lägga till [ett anpassat utfällbart alternativ i webbgränssnittet för fjärrövervakningslösning som](iot-accelerators-remote-monitoring-customize-flyout.md) visas på exempelsidan.

Mer begreppsmässig information om lösningsacceleratorn för fjärrövervakning finns i [Fjärrövervakningsarkitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md).
