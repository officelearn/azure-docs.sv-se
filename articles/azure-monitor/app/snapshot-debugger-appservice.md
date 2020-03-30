---
title: Aktivera felsökare för ögonblicksbilder för .NET-appar i Azure App Service | Microsoft-dokument
description: Aktivera felsökare för ögonblicksbilder för .NET-appar i Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8af688e38003e0613a06d7d8622ce279a3838589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298279"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Aktivera felsökare för ögonblicksbilder för .NET-appar i Azure App Service

Snapshot Debugger fungerar för närvarande för ASP.NET och ASP.NET Core-appar som körs på Azure App Service i Windows-tjänstplaner.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>Aktivera felsökare för ögonblicksbilder
Om du vill aktivera Snapshot Debugger för en app följer du instruktionerna nedan. Om du kör en annan typ av Azure-tjänst följer här instruktioner för hur du aktiverar felsökare för ögonblicksbilder på andra plattformar som stöds:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines och skalningsuppsättningar för virtuella datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Om du använder en förhandsversion av .NET Core följer du instruktionerna för [Aktivera felsökare för ögonblicksbilder för andra miljöer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) för att först inkludera Paketet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet med programmet och fyll sedan i resten av instruktionerna nedan. 

Snapshot-felsökning av application insights är förinstallerad som en del av App Services-körningen, men du måste aktivera den för att få ögonblicksbilder för apptjänstappen. När du har distribuerat en app, även om du har inkluderat Application Insights SDK i källkoden, följer du stegen nedan för att aktivera felsökaren för ögonblicksbilder.

1. Gå till **fönstret App Services** i Azure-portalen.
2. Navigera till **fönstret Inställningar > Programstatistik.**

   ![Aktivera appstatistik på App Services-portalen](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Följ antingen instruktionerna i fönstret för att skapa en ny resurs eller välj en befintlig App Insights-resurs för att övervaka din app. Kontrollera också att båda växlarna för felsökare för ögonblicksbilder är **på**.

   ![Tillägg till webbplatstillägg för Lägg till appstatistik][Enablement UI]

4. Ögonblicksbildfelsökaren är nu aktiverad med hjälp av en appinställning för App Services.

    ![Appinställning för felsökare för ögonblicksbilder][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Inaktivera felsökare för ögonblicksbilder

Följ samma steg som för **Aktivera felsökare för ögonblicksbilder**, men växla båda växlarna för ögonblicksbildfelsökare till **Av**.
Vi rekommenderar att du har ögonblicksbildfelsökare aktiverat på alla dina appar för att underlätta diagnostiken av programundantag.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

För en Azure App-tjänst kan du ange appinställningar i en Azure Resource Manager-mall för att aktivera Felsökare för ögonblicksbilder och Profiler. Du lägger till en konfigurationsresurs som innehåller appinställningarna som en underordnad resurs på webbplatsen:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program som kan utlösa ett undantag. Vänta sedan 10 till 15 minuter på att ögonblicksbilder ska skickas till application insights-instansen.
- Se [ögonblicksbilder](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) i Azure-portalen.
- Mer information om felsökning av felsökare i ögonblicksbilder finns i [felsökning av ögonblicksbildfelsökning](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

