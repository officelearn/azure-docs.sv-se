---
title: Anpassa gränssnittet för fjärr styrnings lösning – Azure | Microsoft Docs
description: Den här artikeln innehåller information om hur du kan få åtkomst till käll koden för gränssnittet för fjärr styrnings Accelerator och göra vissa anpassningar.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 2789ed642979616a4491a61d146d8468552ec2e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318474"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Anpassa acceleratorn för fjärr styrnings lösning

Den här artikeln innehåller information om hur du får åtkomst till käll koden och hur du anpassar gränssnittet för Remote Monitoring Solution Accelerator.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Förbered en lokal utvecklings miljö för användar gränssnittet

UI-koden för fjärr styrnings Accelerator implementeras med hjälp av React.js Framework. Du hittar käll koden i [Azure-IoT-PC-Remote-Monitoring-webui GitHub-](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) lagringsplatsen.

Om du vill göra ändringar i användar gränssnittet kan du köra en kopia av det lokalt. För att utföra åtgärder som att hämta telemetri ansluter den lokala kopian till en distribuerad instans av lösningen.

Följande steg beskriver processen för att konfigurera en lokal miljö för UI-utveckling:

1. Distribuera en **grundläggande** instans av Solution Accelerator med **PC** cli. Anteckna namnet på distributionen och de autentiseringsuppgifter som du har angett för den virtuella datorn. Mer information finns i [distribuera med CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Använd Azure Portal eller Azure Cloud Shell för att aktivera SSH-åtkomst till den virtuella datorn som är värd för mikrotjänsterna i lösningen. Exempel:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Aktivera endast SSH-åtkomst under testning och utveckling. Om du aktiverar SSH [bör du inaktivera det så snart du är klar med det](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Använd Azure Portal eller Azure Cloud Shell för att hitta namnet och den offentliga IP-adressen för den virtuella datorn. Exempel:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Använd SSH för att ansluta till den virtuella datorn. Använd IP-adressen från föregående steg och de autentiseringsuppgifter du angav när du körde **datorerna** för att distribuera lösningen. `ssh`Kommandot är tillgängligt i Azure Cloud Shell.

1. Om du vill tillåta att det lokala UX: en ansluter kör du följande kommandon i bash-gränssnittet på den virtuella datorn:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. När du har granskat kommandot slutförs och webbplatsen startar kan du koppla från den virtuella datorn.

1. I din lokala kopia av databasen [Azure-IoT-PC-Remote-Monitoring – webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) , redigerar du **. kuvert** -filen för att lägga till URL: en för din distribuerade lösning:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. I en kommando tolk navigerar du till din lokala kopia av `azure-iot-pcs-remote-monitoring-webui` mappen.

1. Kör följande kommandon för att installera de nödvändiga biblioteken och köra användar gränssnittet lokalt:

    ```cmd/sh
    npm install
    npm start
    ```

1. Föregående kommando kör användar gränssnittet lokalt på http: \/ /localhost: 3000/instrument panel. Du kan redigera koden medan platsen körs och se att den uppdateras dynamiskt.

## <a name="customize-the-layout"></a>Anpassa layouten

Varje sida i fjärr styrnings lösningen består av en uppsättning kontroller som kallas *paneler* i käll koden. **Instrument panels** sidan består av fem paneler: översikt, karta, aviseringar, telemetri och analys. Du hittar käll koden som definierar varje sida och dess paneler i webui GitHub-lagringsplatsen [PC-Remote-Monitoring](https://github.com/Azure/pcs-remote-monitoring-webui) . Till exempel är den kod som definierar **instrument panels** sidan, dess layout och panelerna på sidan placerad i mappen [src/Components/Pages/Dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) .

Eftersom panelerna hanterar sin egen layout och storlek kan du enkelt ändra layouten för en sida. Gör följande ändringar i **PageContent** -elementet i `src/components/pages/dashboard/dashboard.js` filen för att:

* Växla placeringen av panelerna kartor och telemetri.
* Ändra relativa bredder för kartan och analys panelerna.

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

![Ändra panelens layout](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Du kan också lägga till flera instanser av samma panel eller flera versioner om du [duplicerar och anpassar en panel](#duplicate-and-customize-an-existing-control). I följande exempel visas hur du lägger till två instanser av panelen telemetri. Redigera filen om du vill göra dessa ändringar `src/components/pages/dashboard/dashboard.js` :

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

Du kan sedan Visa olika telemetri på varje panel:

![Flera telemetri paneler](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicera och anpassa en befintlig kontroll

Följande steg beskriver hur du duplicerar en befintlig panel, ändrar den och använder sedan den ändrade versionen. I stegen används **aviserings** panelen som exempel:

1. I din lokala kopia av lagrings platsen gör du en kopia av mappen **aviseringar** i `src/components/pages/dashboard/panels` mappen. Namnge den nya kopian **cust_alerts**.

1. I **alertsPanel.js** -filen i mappen **cust_alerts** redigerar du namnet på klassen som ska vara **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Lägg till följande rad i `src/components/pages/dashboard/panels/index.js` filen:

    ```javascript
    export * from './cust_alerts';
    ```

1. Ersätt `alertsPanel` med `CustAlertsPanel` i `src/components/pages/dashboard/dashboard.js` filen:

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

Nu har du ersatt den ursprungliga **aviserings** panelen med en kopia som heter **CustAlerts**. Den här kopian är samma som originalet. Nu kan du ändra kopian. Om du till exempel vill ändra kolumn ordningen i panelen **aviseringar** :

1. Öppna filen `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Ändra kolumn definitionerna som du ser i följande kodfragment:

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

Följande skärm bild visar den nya versionen av **aviserings** panelen:

![aviserings panelen har uppdaterats](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Anpassa telemetri-diagrammet

Filerna i `src/components/pages/dashboard/panels/telemtry` mappen definierar telemetri-diagrammet på **instrument panelens** sida. Användar gränssnittet hämtar Telemetrin från Server delen av lösningen i `src/services/telemetryService.js` filen. Följande steg visar hur du ändrar den tids period som visas i telemetri-diagrammet från 15 till 5 minuter:

1. `src/services/telemetryService.js`Leta upp funktionen med namnet **getTelemetryByDeviceIdP15M**i filen. Gör en kopia av den här funktionen och ändra kopian enligt följande:

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

1. Om du vill använda den här nya funktionen för att fylla i telemetri-diagrammet öppnar du `src/components/pages/dashboard/dashboard.js` filen. Leta upp raden som initierar telemetri-dataströmmen och ändra den på följande sätt:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Telemetri-diagrammet visar nu fem minuters telemetri-data:

![Telemetri diagram som visar en dag](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Lägg till en ny KPI

Sidan **instrument panel** visar KPI: er i **Analytics** -panelen. Dessa KPI: er beräknas i `src/components/pages/dashboard/dashboard.js` filen. KPI: erna återges av `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` filen. Följande steg beskriver hur du beräknar och återger ett nytt KPI-värde på **instrument panelens** sida. Exemplet visar hur du lägger till en ny procentuell ändring i varnings aviserings-KPI:

1. Öppna filen `src/components/pages/dashboard/dashboard.js`. Ändra **initialState** -objektet så att det innehåller en **warningAlertsChange** -egenskap enligt följande:

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

1. Ändra **currentAlertsStats** -objektet så att det innehåller **totalWarningCount** som en egenskap:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Beräkna den nya KPI: n. Hitta beräkningen för antalet kritiska varningar. Duplicera koden och ändra kopian enligt följande:

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

1. Ta med den nya **warningAlertsChange** -KPI: en i KPI-dataströmmen:

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

1. Inkludera den nya **warningAlertsChange** -KPI: en i de tillstånds data som används för att återge gränssnittet:

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

Nu har du slutfört ändringarna i `src/components/pages/dashboard/dashboard.js` filen. Följande steg beskriver de ändringar som ska göras i `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` filen för att visa den nya KPI: en:

1. Ändra följande kodrad för att hämta det nya KPI-värdet enligt följande:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Ändra markeringen för att visa det nya KPI-värdet enligt följande:

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

På **instrument panels** sidan visas nu det nya KPI-värdet:

![Varnings-KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Anpassa kartan

Se sidan [Anpassa kartan](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) i GitHub för mer information om kart komponenterna i lösningen.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Andra anpassnings alternativ

Om du vill ändra lagret och visualiserings lagret ytterligare i lösningen för fjärrövervakning kan du redigera koden. Relevanta GitHub-databaser är:

* [Konfigurations mikrotjänsten för Azure IoT-lösningar (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Konfigurations mikrotjänsten för Azure IoT-lösningar (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Webb gränssnitt för Azure IoT-datorer med fjärr övervakning](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om vilka resurser som är tillgängliga som hjälper dig att anpassa webb gränssnittet i Solution Accelerator för fjärr styrning. Mer information om hur du anpassar användar gränssnittet finns i följande artiklar:

* [Lägg till en anpassad sida i webb gränssnittet för webb gränssnittet för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-page.md)
* [Lägg till en anpassad tjänst i webb gränssnittet för webb gränssnittet för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-service.md)
* [Lägg till ett anpassat rutnät i webb gränssnittet för lösnings Accelerator för fjärr styrning](iot-accelerators-remote-monitoring-customize-grid.md)
* [Lägg till en anpassad utfällning i webb gränssnittet för lösnings Accelerator för fjärr styrning](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Lägg till en anpassad panel i instrument panelen i webb gränssnittet för webb gränssnitt för fjärrövervakning av Solution Accelerator](iot-accelerators-remote-monitoring-customize-panel.md)

Mer information om lösnings acceleratorn för fjärrövervakning finns i [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Mer information om hur du anpassar fjärr styrnings lösning mikrotjänster finns i [Anpassa och distribuera om en mikrotjänst](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
