---
title: Anpassa av lösningen för fjärrövervakning gränssnitt – Azure | Microsoft Docs
description: Den här artikeln innehåller information om hur du kan komma åt källkoden för lösningsacceleratorn för fjärrövervakning Användargränssnittet och göra vissa anpassningar.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2018
ms.topic: conceptual
ms.openlocfilehash: 7971a5aeadd2b2edaa43da8bacae9743937ce30e
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127354"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Anpassa lösningsacceleratorn för fjärrövervakning

Den här artikeln innehåller information om hur du kan få åtkomst till källkoden och anpassa lösningsacceleratorn för fjärrövervakning Användargränssnittet. Artikeln beskriver:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Förbereda en lokal utvecklingsmiljö för Användargränssnittet

Lösningsacceleratorn för fjärrövervakning UI-kod har implementerats med hjälp av React.js-ramverket. Du hittar källkoden i den [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub-lagringsplatsen.

Om du vill göra ändringar i Användargränssnittet, kan du köra en kopia av den lokalt. Den lokala kopian ansluter till en distribuerad instans av lösningen för att utföra åtgärder som att hämta telemetri.

Följande steg beskriver hur du ställer in en lokal miljö för utveckling av Användargränssnittet:

1. Distribuera en **grundläggande** instans av en solution accelerator med hjälp av den **datorer** CLI. Anteckna namnet på distributionen och autentiseringsuppgifterna för den virtuella datorn. Mer information finns i [distribuera med hjälp av CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Använd Azure-portalen eller [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) att aktivera SSH-åtkomst till den virtuella datorn som är värd för mikrotjänster i din lösning. Exempel:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Du bör bara aktivera SSH-åtkomst under testning och utveckling. Om du aktiverar SSH, [bör du inaktivera det igen så snart som möjligt](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Använd Azure-portalen eller [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) att hitta namnet och den offentliga IP-adressen för den virtuella datorn. Exempel:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Använda SSH för att ansluta till den virtuella datorn med IP-adress från föregående steg och de autentiseringsuppgifter som du angav när du körde **datorer** du distribuerar lösningen.

1. Om du vill tillåta att ansluta lokala UX, kör du följande kommandon i bash-gränssnittet på den virtuella datorn:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. När du ser kommandot har slutförts och webbplatsen startar kan koppla du från den virtuella datorn.

1. I den lokala kopian av den [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) lagringsplatsen, redigera den **.env** fil att lägga till URL: en för din distribuerade lösningen:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. I en kommandotolk i den lokala kopian av den `azure-iot-pcs-remote-monitoring-webui` mapp, kör följande kommandon för att installera nödvändiga bibliotek och kör Användargränssnittet lokalt:

    ```cmd/sh
    npm install
    npm start
    ```

1. Föregående kommando körs lokalt på Användargränssnittet http://localhost:3000/dashboard. Du kan redigera koden medan platsen körs och se hur det uppdateras dynamiskt.

## <a name="customize-the-layout"></a>Anpassa layouten

Varje sida i lösningen för fjärrövervakning består av en uppsättning kontroller, kallas *paneler* i källkoden. Till exempel den **instrumentpanelen** sidan består av fem paneler: översikt, karta, larm, telemetri och KPI: er. Du hittar källkoden som definierar varje sida och dess paneler i den [datorer-remote-monitoring-webbgränssnittet](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub-lagringsplatsen. Till exempel den kod som definierar den **instrumentpanelen** sidan, layout och paneler på sidan finns i den [src/komponenter/sidor/instrumentpanelen](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) mapp.

Eftersom panelerna hantera sin egen layout och storlek, kan du enkelt ändra layouten för en sida. Till exempel följande ändringar i **PageContent** elementet i den `src/components/pages/dashboard/dashboard.js` filen växla positionerna för panelerna kartan och telemetri och ändra de relativa bredden på kartan och KPI-paneler:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![Ändra parameterpanelens layout](./media/iot-accelerators-remote-monitoring-customize/layout.png)

> [!NOTE]
> Kartan har inte konfigurerats i den lokala distributionen.

Du kan också lägga till flera instanser av samma panelen eller flera versioner om du [Duplicera och anpassa en panel](#duplicate-and-customize-an-existing-control). I följande exempel visas hur du lägger till två instanser av panelen telemetri genom att redigera den `src/components/pages/dashboard/dashboard.js` fil:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

Du kan sedan visa olika telemetri i varje panel:

![Flera telemetri-paneler](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> Kartan har inte konfigurerats i den lokala distributionen.

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicera och anpassa en befintlig kontroll

Följande steg beskriver hur du använder den **larm** panelen som ett exempel på hur du duplicera en befintlig panelen, ändrar du den och den ändrade versionen:

1. I den lokala kopian av databasen, göra en kopia av den **larm** mapp i den `src/components/pages/dashboard/panels` mapp. Namnge den nya kopian **cust_alarms**.

1. I den **alarmsPanel.js** fil i den **cust_alarms** mappen redigera namnet på klassen för att vara **CustAlarmsPanel**:

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Lägg till följande rad i den `src/components/pages/dashboard/panels/index.js` fil:

    ```nodejs
    export * from './cust_alarms';
    ```

1. Ersätt `AlarmsPanel` med `CustAlarmsPanel` i den `src/components/pages/dashboard/dashboard.js` fil:

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Du har nu ersätts ursprungligt **larm** panelen med en kopia som kallas **CustAlarms**. Den här kopian är identisk med ursprungligt. Du kan nu ändra kopian. Till exempel vill ändra kolumnordning i den **larm** panelen:

1. Öppna filen `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js`.

1. Ändra kolumndefinitionen enligt följande kodavsnitt:

    ```nodejs
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

I följande skärmbild visas den nya versionen av den **larm** panelen:

![Larmpanelen uppdateras](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Anpassa telemetri-diagram

Telemetri-diagrammet i den **instrumentpanelen** sidan definieras av filerna i den `src/components/pages/dashboard/panels/telemtry` mapp. Användargränssnittet hämtar telemetri från serverdelen i lösningen i den `src/services/telemetryService.js` filen. Följande steg visar hur du ändrar den tidsperiod som visas i diagrammet telemetri från 15 minuter till 5 minuter:

1. I den `src/services/telemetryService.js` filen, leta upp den anropade funktionen **getTelemetryByDeviceIdP15M**. Skapa en kopia av den här funktionen och ändra kopian på följande sätt:

    ```nodejs
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Om du vill använda den här nya funktionen för att fylla i diagrammet telemetri, öppna den `src/components/pages/dashboard/dashboard.js` filen. Leta upp den rad som initierar telemetriströmmen och ändra det på följande sätt:

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Telemetri diagrammet visar nu de fem minuterna av telemetridata:

![Telemetri diagram som visar en dag](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Lägg till en ny KPI

Den **instrumentpanelen** sidan visar KPI: er i den **System KPI: er** panelen. Dessa KPI: er beräknas för den `src/components/pages/dashboard/dashboard.js` filen. KPI: er är renderas av de `src/components/pages/dashboard/panels/kpis/kpisPanel.js` fil. Följande steg beskriver hur du beräknar och visa en ny KPI-värde på den **instrumentpanelen** sidan. Exemplet som visas är att lägga till en ny procentuella förändringen i larm KPI:

1. Öppna filen `src/components/pages/dashboard/dashboard.js`. Ändra den **initialState** objekt att inkludera en **warningAlarmsChange** egenskap enligt följande:

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. Ändra den **currentAlarmsStats** objekt som inkluderas **totalWarningCount** som en egenskap:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Beräkna ny KPI. Hitta beräkning för antalet kritiska larm. Duplicera koden och ändra kopian på följande sätt:

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. Inkludera den nya **warningAlarmsChange** KPI i KPI-stream:

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });
    ```

1. Inkludera den nya **warningAlarmsChange** KPI i tillståndsdata som används för att återge Användargränssnittet:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. Uppdatera data som skickas till panelen KPI: er:

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

Du är nu klar med ändringarna i den `src/components/pages/dashboard/dashboard.js` filen. Följande steg beskriver de ändringar du gör i den `src/components/pages/dashboard/panels/kpis/kpisPanel.js` filen för att visa ny KPI:

1. Ändra följande rad med kod för att hämta ny KPI-värde på följande sätt:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Ändra koden för att visa det nya KPI-värdet på följande sätt:

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

Den **instrumentpanelen** sidan visar nu det nya värdet för KPI:

![Varning KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Anpassa kartan

Se den [anpassa kartan](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) sidan i GitHub för information om kartkomponenter i lösningen.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Andra anpassningsalternativ för

Du kan redigera koden för att ändra presentationen och visualiseringar lagret i lösningen för fjärrövervakning ytterligare. De relevanta GitHub-databaserna är:

* [Konfiguration av mikrotjänster för Azure IoT lösningar (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Konfiguration av mikrotjänster för Azure IoT-lösningar (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Azure IoT-datorer webbgränssnittet för fjärrövervakning](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om resurserna som är tillgängliga om du vill anpassa webbgränssnittet lösningsacceleratorn för fjärrövervakning.

Mer information om lösningsacceleratorn för fjärrövervakning finns [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Mer information om hur du anpassar lösningen för fjärrövervakning finns [anpassa och distribuera om en mikrotjänst](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->
