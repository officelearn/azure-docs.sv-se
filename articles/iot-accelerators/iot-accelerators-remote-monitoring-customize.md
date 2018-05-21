---
title: Anpassa Fjärrövervaknings lösningen UI - Azure | Microsoft Docs
description: Den här artikeln innehåller information om hur du kan använda källkoden för Fjärrövervaknings solution accelerator UI och göra vissa anpassningar.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 94bd864215ad80b299db09b6237f2cebe63feb88
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Anpassa Fjärrövervaknings solution accelerator

Den här artikeln innehåller information om hur du kan komma åt källkoden och anpassa Fjärrövervaknings solution accelerator Användargränssnittet. Den här artikeln innehåller:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Förbereda en lokal utvecklingsmiljö för Användargränssnittet

Fjärrövervaknings solution accelerator UI-kod har implementerats med hjälp av React.js framework. Du hittar källkoden i den [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub-lagringsplatsen.

Om du vill göra ändringar i Användargränssnittet, kan du köra en kopia av den lokalt. Den lokala kopian ansluter till en distribuerad instans av lösningen för att utföra åtgärder som hämtar telemetri.

Följande steg beskriver processen för att konfigurera en lokal miljö för utveckling av UI:

1. Distribuera en **grundläggande** instans av solution accelerator med den **datorer** CLI. Anteckna namnet på din distribution och autentiseringsuppgifterna för den virtuella datorn. Mer information finns i [distribuera med hjälp av CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Använd Azure-portalen eller [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för SSH-åtkomst till den virtuella datorn som är värd för mikrotjänster i din lösning. Exempel:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

1. Använd Azure-portalen eller [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) att hitta namnet och den offentliga IP-adressen för den virtuella datorn. Exempel:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Använda SSH för att ansluta till den virtuella datorn med IP-adress från föregående steg, och de autentiseringsuppgifter du angav när du körde **datorer** du distribuerar lösningen.

1. Kör följande kommandon i bash-gränssnitt på den virtuella datorn så att den lokala UX att ansluta:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. När du ser kommandot har slutförts och webbplatsen startar, kan du koppla från den virtuella datorn.

1. I den lokala kopian av den [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) databasen, redigera den **.env** fil att lägga till URL: en i distribuerade lösningen:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Vid en kommandotolk i den lokala kopian av den `azure-iot-pcs-remote-monitoring-webui` mapp, kör följande kommandon för att installera bibliotek som krävs och köra Användargränssnittet lokalt:

    ```cmd/sh
    npm install
    npm start
    ```

1. Föregående kommando kör Användargränssnittet lokalt på http://localhost:3000/dashboard. Du kan redigera koden medan platsen körs och se den uppdateras dynamiskt.

## <a name="customize-the-layout"></a>Anpassa layout

Varje sida i lösningen Fjärrövervaknings består av en uppsättning kontroller, kallas *paneler* i källkoden. Till exempel den **instrumentpanelen** sidan består av fem paneler: översikt, karta, larm, telemetri och KPI: er. Du hittar källkoden som definierar varje sida och dess paneler i den [datorer-fjärr-övervakning-webui](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub-lagringsplatsen. Till exempel den kod som definierar den **instrumentpanelen** sida, layouten och paneler på sidan finns i den [src/komponenter/sidor/instrumentpanel](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) mapp.

Eftersom panelerna hantera sina egna layout och storlek, kan du enkelt ändra layouten för en sida. Till exempel följande ändringar av den **PageContent** element i den `src/components/pages/dashboard/dashboard.js` filen växla positioner panelerna kartan och telemetri och ändra de relativa bredden på karta och KPI paneler:

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

![Ändra panelen layout](./media/iot-accelerators-remote-monitoring-customize/layout.png)

> [!NOTE]
> Kartan har inte konfigurerats i den lokala distributionen.

Du kan också lägga till flera instanser av samma panelen eller flera versioner om du [Duplicera och anpassa en panel](#duplicate-and-customize-an-existing-control). I följande exempel visas hur du lägger till två instanser av panelen telemetri genom att redigera den `src/components/pages/dashboard/dashboard.js` filen:

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

![Flera telemetri paneler](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> Kartan har inte konfigurerats i den lokala distributionen.

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicera och anpassa en befintlig kontroll

Följande steg beskriver hur du använder den **larm** panelen som ett exempel på hur du duplicera en befintlig Kontrollpanelen, ändra den och använda den ändrade versionen:

1. I den lokala kopian av databasen att göra en kopia av den **larm** mapp i den `src/components/pages/dashboard/panels` mapp. Namnge den nya kopian **cust_alarms**.

1. I den **alarmsPanel.js** filen i den **cust_alarms** mapp, redigera namnet på klassen som ska vara **CustAlarmsPanel**:

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Lägg till följande rad i den `src/components/pages/dashboard/panels/index.js` filen:

    ```nodejs
    export * from './cust_alarms';
    ```

1. Ersätt `AlarmsPanel` med `CustAlarmsPanel` i den `src/components/pages/dashboard/dashboard.js` filen:

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

Du har nu ersätts ursprungligt **larm** panel med en kopia som kallas **CustAlarms**. Den här kopian är identisk med ursprungligt. Du kan nu ändra kopian. Till exempel ändra kolumnens sortering i den **larm** panelen:

1. Öppna filen `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js`.

1. Ändra kolumndefinitionerna enligt följande kodavsnitt:

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

Följande skärmbild visar den nya versionen av den **larm** panelen:

![Larm panelen uppdateras](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Anpassa telemetri diagram

Telemetri diagram på den **instrumentpanelen** sidan definieras av filerna i den `src/components/pages/dashboard/panels/telemtry` mapp. Användargränssnittet hämtar telemetrin från lösningens serverdel i den `src/services/telemetryService.js` filen. Följande steg visar hur du ändrar den tidsperiod som visas i diagrammet telemetri från 15 minuter till 5 minuter:

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

1. Om du vill använda den här nya funktionen för att fylla i diagrammet telemetri, öppna den `src/components/pages/dashboard/dashboard.js` filen. Leta upp den rad som initierar dataströmmen telemetri och ändra på följande sätt:

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Telemetri diagrammet visas nu de fem minuterna av telemetridata:

![Telemetri diagram med en dag](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Lägg till en ny KPI

Den **instrumentpanelen** sidan visar KPI: er i den **System KPI: er** panelen. Dessa KPI: er beräknas i den `src/components/pages/dashboard/dashboard.js` filen. KPI: er som återges av den `src/components/pages/dashboard/panels/kpis/kpisPanel.js` filen. Följande steg beskriver hur du beräkna och göra ett nytt värde för KPI: N på den **instrumentpanelen** sidan. I exemplet är att lägga till en ny ändring i procent i larm KPI:

1. Öppna filen `src/components/pages/dashboard/dashboard.js`. Ändra den **initialState** objekt att inkludera en **warningAlarmsChange** egenskapen på följande sätt:

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

1. Beräkna den nya KPI: N. Hitta beräkningen för kritiska larm count. Kopiera koden och ändra kopian på följande sätt:

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

1. Inkludera den nya **warningAlarmsChange** KPI i dataströmmen KPI:

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

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

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

Du är nu klar med ändringarna i den `src/components/pages/dashboard/dashboard.js` filen. Följande steg beskriver ändringarna i den `src/components/pages/dashboard/panels/kpis/kpisPanel.js` filen ska visas den nya KPI:

1. Ändra följande rad med kod för att hämta det nya värdet för KPI: N på följande sätt:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Ändra koden för att visa det nya värdet för KPI: N på följande sätt:

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

Den **instrumentpanelen** nu visas det nya värdet för KPI:

![Varning KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Anpassa kartan

Finns det [anpassa kartan](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) sida i GitHub för information om kartan komponenter i lösningen.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Andra alternativ för anpassning

Om du vill ändra presentationen och visualiseringar lagret i Fjärrövervaknings-lösningen, kan du redigera koden. De relevanta GitHub-lagringsplatser är:

* [Konfigurationen mikrotjänster för Azure IoT lösningar (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [Konfigurationen mikrotjänster för Azure IoT-lösningar (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Azure IoT-datorer Remote övervakning webbgränssnittet](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om resurserna som är tillgängliga för att anpassa webbgränssnittet Fjärrövervaknings solution accelerator.

Mer information om Fjärrövervaknings solution accelerator finns [Fjärrövervaknings arkitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Mer information om hur du anpassar Fjärrövervaknings-lösningen finns [anpassa och distribuera om ett mikrotjänster](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->