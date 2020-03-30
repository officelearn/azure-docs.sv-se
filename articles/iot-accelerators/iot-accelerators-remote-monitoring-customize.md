---
title: Anpassa användargränssnittet för fjärrövervakningslösning – Azure | Microsoft-dokument
description: Den här artikeln innehåller information om hur du kan komma åt källkoden för lösningsacceleratorgränssnittet för fjärrövervakning och göra vissa anpassningar.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: eb3d5fea68b5b1b6e648943cb3dbaab5857e9e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68607999"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Anpassa lösningsacceleratorn för fjärrövervakning

Den här artikeln innehåller information om hur du kan komma åt källkoden och anpassa lösningsacceleratorgränssnittet för fjärrövervakning.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Förbereda en lokal utvecklingsmiljö för användargränssnittet

Gränssnittskoden för fjärrövervakningslösningslösning implementeras med hjälp av React.js-ramverket. Du hittar källkoden i [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub-databasen.

Om du vill göra ändringar i användargränssnittet kan du köra en kopia av det lokalt. Om du vill slutföra åtgärder som att hämta telemetri ansluter den lokala kopian till en distribuerad instans av lösningen.

I följande steg beskrivs processen för att konfigurera en lokal miljö för gränssnittsutveckling:

1. Distribuera en **grundläggande** instans av lösningsacceleratorn med hjälp av **PCS** CLI. Anteckna namnet på distributionen och de autentiseringsuppgifter som du angav för den virtuella datorn. Mer information finns i [Distribuera med CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Om du vill aktivera SSH-åtkomst till den virtuella datorn som är värd för mikrotjänsterna i din lösning använder du Azure-portalen eller Azure Cloud Shell. Ett exempel:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Aktivera endast SSH-åtkomst under test och utveckling. Om du aktiverar SSH [bör du inaktivera det så fort du är klar med det](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Använd Azure-portalen eller Azure Cloud Shell för att hitta namnet och den offentliga IP-adressen för din virtuella dator. Ett exempel:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Använd SSH för att ansluta till din virtuella dator. Använd IP-adressen från föregående steg och de autentiseringsuppgifter du angav när du körde **datorer** för att distribuera lösningen. Kommandot `ssh` är tillgängligt i Azure Cloud Shell.

1. Om du vill att den lokala användarupplevelsen ska ansluta kör du följande kommandon vid bash-skalet i den virtuella datorn:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. När du ser att kommandot har slutförts och webbplatsen startar kan du koppla från den virtuella datorn.

1. Redigera **.env-filen** i den lokala kopian av [azure-iot-pcs-remote-monitoring-webui-databasen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) för att lägga till URL:en för den distribuerade lösningen:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. I en kommandotolk navigerar du `azure-iot-pcs-remote-monitoring-webui` till den lokala kopian av mappen.

1. Om du vill installera de bibliotek som krävs och köra användargränssnittet lokalt kör du följande kommandon:

    ```cmd/sh
    npm install
    npm start
    ```

1. Föregående kommando kör användargränssnittet lokalt\/på http: /localhost:3000/dashboard. Du kan redigera koden medan webbplatsen körs och se den uppdateras dynamiskt.

## <a name="customize-the-layout"></a>Anpassa layouten

Varje sida i lösningen för fjärrövervakning består av en uppsättning kontroller, så kallade *paneler* i källkoden. **Instrumentpanelssidan** består av fem paneler: Översikt, Karta, Aviseringar, Telemetri och Analys. Du hittar källkoden som definierar varje sida och dess paneler i [GitHub-databasen för pcs-remote-monitoring-webui.](https://github.com/Azure/pcs-remote-monitoring-webui) Koden som definierar **instrumentpanelssidan,** dess layout och panelerna på sidan finns till exempel i mappen [src/components/pages/dashboard.](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard)

Eftersom panelerna hanterar sin egen layout och storlek kan du enkelt ändra layouten på en sida. Gör följande ändringar i **PageContent-elementet** `src/components/pages/dashboard/dashboard.js` i filen till:

* Byt positionerna för kart- och telemetripanelerna.
* Ändra kartans och analyspanelernas relativa bredd.

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

![Ändra panellayout](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Du kan också lägga till flera instanser av samma panel, eller flera versioner om du [duplicerar och anpassar en panel](#duplicate-and-customize-an-existing-control). I följande exempel visas hur du lägger till två förekomster av telemetripanelen. Om du vill göra `src/components/pages/dashboard/dashboard.js` dessa ändringar redigerar du filen:

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

Du kan sedan visa olika telemetri på varje panel:

![Flera telemetripaneler](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicera och anpassa en befintlig kontroll

I följande steg beskrivs hur du duplicerar en befintlig panel, ändrar den och använder sedan den ändrade versionen. Stegen använder **varningspanelen** som ett exempel:

1. Gör en kopia av **mappen** i `src/components/pages/dashboard/panels` den lokala kopian av databasen. Ge den nya **kopian namnet cust_alerts**.

1. Redigera namnet på den klass som ska vara **CustAlertsPanel**i mappen **alertsPanel.js** i **mappen cust_alerts** :

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Lägg till följande `src/components/pages/dashboard/panels/index.js` rad i filen:

    ```javascript
    export * from './cust_alerts';
    ```

1. Ersätt `alertsPanel` `CustAlertsPanel` med `src/components/pages/dashboard/dashboard.js` i filen:

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

Du har nu ersatt den ursprungliga **varningspanelen** med en kopia som heter **CustAlerts**. Den här kopian är samma som originalet. Du kan nu ändra kopian. Om du till exempel vill ändra kolumnordningen på **varningspanelen:**

1. Öppna filen `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Ändra kolumndefinitionerna enligt följande kodavsnitt:

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

Följande skärmbild visar den nya versionen av **varningspanelen:**

![varningspanelen uppdaterad](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Anpassa telemetridiagrammet

Filerna i `src/components/pages/dashboard/panels/telemtry` mappen definierar telemetridiagrammet på **instrumentpanelssidan.** Användargränssnittet hämtar telemetrin från lösningens baksida `src/services/telemetryService.js` i filen. Följande steg visar hur du ändrar tidsperioden som visas i telemetridiagrammet från 15 till 5 minuter:

1. Leta `src/services/telemetryService.js` reda på funktionen **getTelemetryByDeviceIdP15M**i filen . Gör en kopia av denna funktion och ändra kopian enligt följande:

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

1. Om du vill använda den nya funktionen för `src/components/pages/dashboard/dashboard.js` att fylla i telemetridiagrammet öppnar du filen. Leta reda på raden som initierar telemetriströmmen och ändra den på följande sätt:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Telemetridiagrammet visar nu fem minuter telemetridata:

![Telemetridiagram som visar en dag](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Lägga till en ny KPI

På **sidan Instrumentpanel** visas KPI:er på **analyspanelen.** Dessa KPI:er beräknas `src/components/pages/dashboard/dashboard.js` i filen. KPI:erna återges av `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` filen. I följande steg beskrivs hur du beräknar och renderar ett nytt KPI-värde på **instrumentpanelssidan.** Exemplet som visas är att lägga till en ny procentuell förändring i KPI:er för varningsaviseringar:

1. Öppna filen `src/components/pages/dashboard/dashboard.js`. Ändra **initialState-objektet** så att det innehåller en **varningAlertsChange-egenskap** enligt följande:

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

1. Ändra objektet **currentAlertsStats** så att **det inkluderar totalWarningCount** som egenskap:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Beräkna den nya KPI:n. Hitta beräkningen för antalet kritiska aviseringar. Duplicera koden och ändra kopian enligt följande:

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

1. Inkludera den nya **varnings-KPI:nÄndning** i KPI-strömmen:

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

1. Inkludera den nya **varnings-KPI:nÄndsänd** i tillståndsdata som används för att återge användargränssnittet:

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

1. Uppdatera data som skickas till KPI:erna:

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

Du har nu slutfört ändringarna `src/components/pages/dashboard/dashboard.js` i filen. I följande steg beskrivs de `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` ändringar som ska görs i filen för att visa den nya KPI:n:

1. Ändra följande kodrad för att hämta det nya KPI-värdet enligt följande:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Ändra markeringen för att visa det nya KPI-värdet på följande sätt:

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

**Instrumentpanelssidan** visar nu det nya KPI-värdet:

![KPI för varning](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Anpassa kartan

Mer information om kartkomponenterna i lösningen finns på [sidan Anpassa karta](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) i GitHub.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Andra anpassningsalternativ

Om du vill ändra lagret presentation och visualiseringar ytterligare i lösningen För fjärrövervakning kan du redigera koden. Relevanta GitHub-databaser är:

* [Konfigurationsmikrotjänsten för Azure IoT Solutions (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Konfigurationsmikrotjänsten för Azure IoT Solutions (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Webbgränssnittet för fjärrövervakning av Azure IoT-datorer](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om de resurser som finns tillgängliga för att hjälpa dig att anpassa webbgränssnittet i lösningsacceleratorn för fjärrövervakning. Mer information om hur du anpassar användargränssnittet finns i följande artiklar:

* [Lägga till en anpassad sida i webbgränssnittet för lösningsaccelerator för fjärrövervakning](iot-accelerators-remote-monitoring-customize-page.md)
* [Lägga till en anpassad tjänst i webbgränssnittet för lösningsaccelerator för fjärrövervakning](iot-accelerators-remote-monitoring-customize-service.md)
* [Lägga till ett anpassat rutnät i webbgränssnittet för lösningsaccelerator för fjärrövervakning](iot-accelerators-remote-monitoring-customize-grid.md)
* [Lägga till ett anpassat utfällbart alternativ i webbgränssnittet för lösningsaccelerator för fjärrövervakning](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Lägga till en anpassad panel på instrumentpanelen i webbgränssnittet för lösningsaccelerator för fjärrövervakning](iot-accelerators-remote-monitoring-customize-panel.md)

Mer begreppsmässig information om lösningsacceleratorn för fjärrövervakning finns i [Fjärrövervakningsarkitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Mer information om hur du anpassar mikrotjänsterna för lösning för fjärrövervakning finns i [Anpassa och distribuera om en mikrotjänst](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
