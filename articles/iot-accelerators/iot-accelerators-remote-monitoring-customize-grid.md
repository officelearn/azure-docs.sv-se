---
title: Lägga till ett rutnät av lösningen för fjärrövervakning gränssnitt – Azure | Microsoft Docs
description: Den här artikeln visar hur du lägger till en ny gid på en sida i Remote Monitoring solution accelerator webbgränssnittet.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/04/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447105"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Lägg till ett anpassat rutnät till fjärrövervakning solution accelerator webbgränssnittet

Den här artikeln visar hur du lägger till ett nytt rutnät till en sida i Remote Monitoring solution accelerator webbgränssnittet. Artikeln beskriver:

- Så här förbereder du en lokal utvecklingsmiljö.
- Hur du lägger till ett nytt rutnät till en sida i webbläsaren.

Exemplet i den här artikeln visar data från tjänsten som den [lägga till en anpassad tjänst fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-service.md) artikel visar hur du lägger till.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande programvara installerad på din lokala utvecklingsdator måste slutföra stegen i den här guiden:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Innan du börjar

Innan du fortsätter bör du genomföra stegen i följande artiklar:

- [Lägg till en anpassad sida fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-page.md).
- [Lägga till en anpassad tjänst fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Lägg till ett rutnät

Om du vill lägga till ett rutnät till webbgränssnittet för måste du lägga till källfilerna som definierar rutnätet och ändra befintliga filer om du vill göra webbgränssnittet medveten om den nya komponenten.

### <a name="add-the-new-files-that-define-the-grid"></a>Lägg till de nya filerna som definierar rutnätet

Att komma igång, den **genomgången/src/komponenter/pageWithGrid/sidor/exampleGrid** mappen innehåller de filer som definierar ett rutnät:

**exampleGrid.js**



**exampleGridConfig.js**



Kopiera den **genomgången/src/komponenter/pageWithGrid/sidor/exampleGrid** mappen till den **src/komponenter/sidor/exempel** mapp.

### <a name="add-the-grid-to-the-page"></a>Lägga till rutnätet på sidan

Ändra den **src/components/pages/example/basicPage.container.js** enligt följande för att importera tjänstdefinitioner för:

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

Ändra den **src/components/pages/example/basicPage.js** på följande sätt för att lägga till rutnätet:

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

Ändra den **src/components/pages/example/basicPage.test.js** enligt följande för att uppdatera testerna:

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

Om webb Användargränssnittet inte redan körs lokalt, kör du följande kommando i roten av den lokala kopian av databasen:

```cmd/sh
npm start
```

Föregående kommando körs lokalt på Användargränssnittet [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Navigera till den **exempel** sidan för att se rutnät som visar data från tjänsten.

## <a name="select-rows"></a>Välj rader

Det finns två alternativ för att aktivera en användare att välja rader i rutnätet:

### <a name="hard-select-rows"></a>Hård-Välj rader

Om en användare behöver vidta åtgärder för flera rader på samma gång, Använd kryssrutorna på rader:

1. Aktivera hårda valet av rader genom att lägga till en **checkboxColumn** till den **columnDefs** till rutnätet. **checkboxColumn** har definierats i **/src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Om du vill få åtkomst till de valda objekten måste hämta du en referens interna grid-API: et:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Ange kontext knappar till sidan när en rad i rutnätet är hårda har valts:

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

1. När du klickar på en knapp för kontext får du svårt markerade element att utföra ditt arbete på:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Mjuk-Välj rader

Om användaren behöver bara att kunna agera på en enskild rad, konfigurera en mjuk-Välj länk för en eller flera kolumner i den **columnDefs**.

1. I **exampleGridConfig.js**, lägga till **SoftSelectLinkRenderer** som den **cellRendererFramework** för en **columnDef**.

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

1. När du klickar på en länk för mjuk-Välj utlöser den **onSoftSelectChange** händelse. Utför åtgärder är det önskade för den raden, till exempel att öppna information utfällbar meny. Det här exemplet skriver helt enkelt till konsolen:

    ```js
    onSoftSelectChange = (rowId, rowEvent) => {
      const { onSoftSelectChange } = this.props;
      const obj = (this.gridApi.getDisplayedRowAtIndex(rowId) || {}).data;
      if (obj) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', obj);
        this.setState({ softSelectedObj: obj });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(obj, rowEvent);
      }
    }
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om resurserna som är tillgängliga för att hjälpa dig lägga till eller anpassa sidor i webbgränssnittet lösningsacceleratorn för fjärrövervakning.

Nu du har definierat ett rutnät med nästa steg är att [lägga till en anpassad utfällt fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-flyout.md) som visas på exempelsidan.

Mer information om lösningsacceleratorn för fjärrövervakning finns [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md).
