---
title: Anpassa av lösningen för fjärrövervakning gränssnitt – Azure | Microsoft Docs
description: Den här artikeln innehåller information om hur du kan komma åt källkoden för lösningsacceleratorn för fjärrövervakning Användargränssnittet och göra vissa anpassningar.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: dc2b38f8e8065b8d8763365bf0cbad56ae00cd4b
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565436"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Anpassa lösningsacceleratorn för fjärrövervakning

Den här artikeln innehåller information om hur du kan få åtkomst till källkoden och anpassa lösningsacceleratorn för fjärrövervakning Användargränssnittet.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Förbereda en lokal utvecklingsmiljö för Användargränssnittet

Lösningsacceleratorn för fjärrövervakning UI-kod har implementerats med hjälp av React.js-ramverket. Du hittar källkoden i den [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub-lagringsplatsen.

Om du vill göra ändringar i Användargränssnittet, kan du köra en kopia av den lokalt. För att slutföra åtgärder som att hämta telemetri, ansluter den lokala kopian till en distribuerad instans av lösningen.

Följande steg beskriver hur du ställer in en lokal miljö för utveckling av Användargränssnittet:

1. Distribuera en **grundläggande** instans av en solution accelerator med hjälp av den **datorer** CLI. Anteckna namnet på distributionen och autentiseringsuppgifterna för den virtuella datorn. Mer information finns i [distribuera med hjälp av CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Om du vill aktivera SSH-åtkomst till den virtuella datorn som är värd för mikrotjänster i din lösning, använder du Azure portal eller Azure Cloud Shell. Exempel:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Aktivera endast SSH-åtkomst under testning och utveckling. Om du aktiverar SSH, [bör du inaktivera det när du är klar med den](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Använda Azure portal eller Azure Cloud Shell för att hitta namnet och den offentliga IP-adressen för den virtuella datorn. Exempel:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Använda SSH för att ansluta till den virtuella datorn. Använd IP-adressen från föregående steg och de autentiseringsuppgifter som du angav när du körde **datorer** du distribuerar lösningen. Den `ssh` kommandot är tillgängligt i Azure Cloud Shell.

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

1. I en kommandotolk, navigerar du till den lokala kopian av den `azure-iot-pcs-remote-monitoring-webui` mapp.

1. Kör följande kommandon för att installera nödvändiga bibliotek och köra Användargränssnittet lokalt:

    ```cmd/sh
    npm install
    npm start
    ```

1. Föregående kommando körs lokalt på Användargränssnittet http://localhost:3000/dashboard. Du kan redigera koden medan platsen körs och se hur det uppdateras dynamiskt.

## <a name="customize-the-layout"></a>Anpassa layouten

Varje sida i lösningen för fjärrövervakning består av en uppsättning kontroller, kallas *paneler* i källkoden. Den **instrumentpanelen** sidan består av fem paneler: Översikt över, karta, aviseringar, telemetri och analys. Du hittar källkoden som definierar varje sida och dess paneler i den [datorer-remote-monitoring-webbgränssnittet](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub-lagringsplatsen. Till exempel den kod som definierar den **instrumentpanelen** sidan, layout och paneler på sidan finns i den [src/komponenter/sidor/instrumentpanelen](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) mapp.

Eftersom panelerna hantera sin egen layout och storlek, kan du enkelt ändra layouten för en sida. Gör följande ändringar till den **PageContent** elementet i den `src/components/pages/dashboard/dashboard.js` filen till:

* Växla positionerna för panelerna kartan och telemetri.
* Ändra de relativa bredden på panelerna kartan och analys.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Ändra parameterpanelens layout](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Du kan också lägga till flera instanser av samma panelen eller flera versioner om du [Duplicera och anpassa en panel](#duplicate-and-customize-an-existing-control). I följande exempel visas hur du lägger till två instanser av panelen telemetri. Om du vill göra dessa ändringar, redigera den `src/components/pages/dashboard/dashboard.js` fil:

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

Du kan sedan visa olika telemetri i varje panel:

![Flera telemetri-paneler](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicera och anpassa en befintlig kontroll

Följande steg beskriver hur du duplicera en befintlig panelen, ändra den och sedan använda den ändrade versionen. Stegen använder den **aviseringar** panelen som exempel:

1. I den lokala kopian av databasen, göra en kopia av den **aviseringar** mapp i den `src/components/pages/dashboard/panels` mapp. Namnge den nya kopian **cust_alerts**.

1. I den **alertsPanel.js** fil i den **cust_alerts** mappen redigera namnet på klassen för att vara **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Lägg till följande rad i den `src/components/pages/dashboard/panels/index.js` fil:

    ```javascript
    export * from './cust_alerts';
    ```

1. Ersätt `alertsPanel` med `CustAlertsPanel` i den `src/components/pages/dashboard/dashboard.js` fil:

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Nu har du ersätta ursprungligt **aviseringar** panelen med en kopia som kallas **CustAlerts**. Den här kopian är samma som ursprungligt. Du kan nu ändra kopian. Till exempel vill ändra kolumnordning i den **aviseringar** panelen:

1. Öppna filen `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Ändra kolumndefinitionen enligt följande kodavsnitt:

    ```javascript
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

I följande skärmbild visas den nya versionen av den **aviseringar** panelen:

![aviseringspanelen uppdateras](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Anpassa telemetri-diagram

Filerna i den `src/components/pages/dashboard/panels/telemtry` mappen definiera telemetri-diagram på den **instrumentpanelen** sidan. Användargränssnittet hämtar telemetri från serverdelen i lösningen i den `src/services/telemetryService.js` filen. Följande steg visar hur du ändrar den tidsperiod som visas i diagrammet telemetri från 15 till 5 minuter:

1. I den `src/services/telemetryService.js` filen, leta upp den anropade funktionen **getTelemetryByDeviceIdP15M**. Skapa en kopia av den här funktionen och ändra kopian på följande sätt:

    ```javascript
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

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Telemetri diagrammet visar nu de fem minuterna av telemetridata:

![Telemetri diagram som visar en dag](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Lägg till en ny KPI

Den **instrumentpanelen** sidan visar KPI: er i den **Analytics** panelen. Dessa KPI: er beräknas för den `src/components/pages/dashboard/dashboard.js` filen. KPI: er är renderas av de `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` fil. Följande steg beskriver hur du beräknar och visa en ny KPI-värde på den **instrumentpanelen** sidan. Exemplet som visas är att lägga till en ny procentuella förändringen i varningsaviseringar KPI:

1. Öppna filen `src/components/pages/dashboard/dashboard.js`. Ändra den **initialState** objekt att inkludera en **warningAlertsChange** egenskap enligt följande:

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Ändra den **currentAlertsStats** objekt som inkluderas **totalWarningCount** som en egenskap:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Beräkna ny KPI. Hitta beräkning för antalet kritiska aviseringar. Duplicera koden och ändra kopian på följande sätt:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Inkludera den nya **warningAlertsChange** KPI i KPI-stream:

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Inkludera den nya **warningAlertsChange** KPI i tillståndsdata som används för att återge Användargränssnittet:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. Uppdatera data som skickas till panelen KPI: er:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Du är nu klar med ändringarna i den `src/components/pages/dashboard/dashboard.js` filen. Följande steg beskriver de ändringar du gör i den `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` filen för att visa ny KPI:

1. Ändra följande rad med kod för att hämta ny KPI-värde på följande sätt:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Ändra koden för att visa det nya KPI-värdet på följande sätt:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
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

I den här artikeln har du lärt dig om resurserna som är tillgängliga om du vill anpassa webbgränssnittet lösningsacceleratorn för fjärrövervakning. Mer information om hur du anpassar Användargränssnittet finns i följande artiklar:

* [Lägg till en anpassad sida fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-page.md)
* [Lägga till en anpassad tjänst fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-service.md)
* [Lägg till ett anpassat rutnät till fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-grid.md)
* [Lägg till en anpassad utfällt till fjärrövervakning solution accelerator webbgränssnittet](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Lägga till en anpassad panel på instrumentpanelen för fjärrövervakning solution accelerator webbläsaren](iot-accelerators-remote-monitoring-customize-panel.md)

Mer information om lösningsacceleratorn för fjärrövervakning finns [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Läs mer om hur du anpassar lösningen fjärrövervakning mikrotjänsterna [anpassa och distribuera om en mikrotjänst](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
