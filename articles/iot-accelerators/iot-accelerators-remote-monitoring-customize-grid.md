---
title: Lägg till ett rutnät i gränssnittet för fjärr styrnings lösning – Azure | Microsoft Docs
description: Den här artikeln visar hur du lägger till ett nytt GID på en sida i webb gränssnittet för webb gränssnitt för Fjärrövervaknings Solution Accelerator.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 7fc878e0c9e099b201264c1c3981c603668214d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017771"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägg till ett anpassat rutnät i webb gränssnittet för lösnings Accelerator för fjärr styrning

Den här artikeln visar hur du lägger till ett nytt rutnät på en sida i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator. Artikeln beskriver:

- Hur du förbereder en lokal utvecklings miljö.
- Så här lägger du till ett nytt rutnät på en sida i webb gränssnittet.

Exemplet i rutnätet i den här artikeln visar de data från tjänsten som [lägger till en anpassad tjänst i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-service.md) , som visar hur du lägger till.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktions guiden behöver du följande program vara installerad på den lokala utvecklings datorn:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Du bör följa stegen i följande artiklar innan du fortsätter:

- [Lägg till en anpassad sida i webb gränssnittet för lösnings Accelerator för fjärr styrning](iot-accelerators-remote-monitoring-customize-page.md).
- [Lägg till en anpassad tjänst i webb gränssnittet för webb gränssnittet för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Lägg till ett rutnät

Om du vill lägga till ett rutnät i webb gränssnittet måste du lägga till källfilerna som definierar rutnätet och ändra några befintliga filer för att göra webb gränssnittet medveten om den nya komponenten.

### <a name="add-the-new-files-that-define-the-grid"></a>Lägg till de nya filerna som definierar rutnätet

För att komma igång innehåller mappen **src/genom gång/Components/Pages/pageWithGrid/exampleGrid** de filer som definierar ett rutnät:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Kopiera mappen **src/genom gång/Components/Pages/pageWithGrid/exampleGrid** till mappen **src/Components/Pages/example** .

### <a name="add-the-grid-to-the-page"></a>Lägg till rutnätet på sidan

Ändra de **src/Components/Pages/example/basicPage.container.js** enligt följande för att importera tjänst definitionerna:

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

Ändra **urspr/Components/Pages/example/basicPage.js** enligt följande för att lägga till rutnätet:

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

Ändra de **src/Components/Pages/example/basicPage.test.js** enligt följande för att uppdatera testerna:

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

Om webb gränssnittet inte redan körs lokalt kör du följande kommando i roten för din lokala kopia av lagrings platsen:

```cmd/sh
npm start
```

Föregående kommando kör användar gränssnittet lokalt på `http://localhost:3000/dashboard` . Gå till **exempel** sidan om du vill visa rutnätet som visar data från tjänsten.

## <a name="select-rows"></a>Markera rader

Det finns två alternativ för att göra det möjligt för en användare att välja rader i rutnätet:

### <a name="hard-select-rows"></a>Hårda urval av rader

Om en användare behöver agera på flera rader samtidigt använder du kryss rutor på rader:

1. Aktivera hård markering av rader genom att lägga till en **checkboxColumn** till **columnDefs** som tillhandahålls till rutnätet. **checkboxColumn** definieras i **/src/Components/Shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. För att få åtkomst till de valda objekten får du en referens till det interna rutnäts-API: et:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Ange Sammanhangs knappar till sidan när en rad i rutnätet är hårt vald:

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

1. När du klickar på en kontext knapp får du de hårda objekt som du kan använda för att arbeta på:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Mjuka markerings rader

Om användaren bara behöver agera på en enda rad konfigurerar du en mjuk markerings länk för en eller flera kolumner i **columnDefs**.

1. I **exampleGridConfig.js** lägger du till **SoftSelectLinkRenderer** som **cellRendererFramework** för en **columnDef**.

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

1. När du klickar på en mjuk markerings länk utlöses händelsen **onSoftSelectChange** . Utföra de åtgärder som behövs för den raden, till exempel öppna en utfälld information. Det här exemplet skriver bara till-konsolen:

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

I den här artikeln har du lärt dig om vilka resurser som är tillgängliga för att hjälpa dig att lägga till eller anpassa sidor i webb gränssnittet i Solution Accelerator för fjärr styrning.

Nu när du har definierat ett rutnät, är nästa steg att [lägga till en anpassad utfällning i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-flyout.md) som visas på exempel sidan.

Mer information om lösnings acceleratorn för fjärrövervakning finns i [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md).
