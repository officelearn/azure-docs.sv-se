---
title: Loggning och diagnostik i Azure Spatial ankare | Microsoft Docs
description: Detaljerad förklaring av hur du skapar och hämtar loggning och diagnostik i Azure Spatial fästpunkter.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: d9377e2b5b66a7d426373a8a85e4880dafeaeee6
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753287"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Loggning och diagnostik i Azure Spatial ankare

Azure Spatial fästpunkter är en loggningsmekanism för standard som är användbar för utveckling av appar. Det finns också ett diagnostik loggningsläge användbart när det krävs ytterligare information för felsökning. Diagnostikloggning omfattar lagring av bilder av miljön.

## <a name="standard-logging-in-azure-spatial-anchors"></a>Standard loggning i Azure Spatial ankare
Azure-API: et för Spatial ankare tillhandahåller en loggningsmekanism för som program kan prenumerera för att ta emot användbara loggar för utveckling av program och felsöka. Standard loggning API: er inte spara bilder av miljön till enheten disken. SDK innehåller loggarna som händelsen återanrop. Det är upp till dig att integrera dessa loggar i programmets loggningsmekanism.

### <a name="how-to-configure-the-log-messages"></a>Så här konfigurerar du loggmeddelanden
Det finns två återanrop av intresse för användaren. I följande exempel visas hur du konfigurerar sessionen.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // setup the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events--properties"></a>Händelser och egenskaper

Händelsen återanrop har angetts för att bearbeta loggar och fel från sessionen.

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Anger detaljnivån för att händelser ska ta emot från körningen.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Den här händelsen återanrop innehåller standard debug logghändelser.
- [Fel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Den här händelsen motringning ger logghändelser anses vara fel av körningen.

## <a name="diagnostics-logging-in-azure-spatial-anchors"></a>Logga in Azure Spatial ankare diagnostik

Förutom standardläge för loggning som beskrivs ovan, har Azure Spatial ankare också en diagnostikläge som utvecklare kan välja. Diagnostics samlar in bilder av miljön och loggar dem till disken. Det här läget är användbart för felsökning av vissa typer av problem som när du inte kan hitta en fästpunkt förutsägbart. Endast aktivera diagnostik loggning för att återskapa ett specifikt problem och inaktivera det sedan. Kör inte dina appar normalt med aktiverat Diagnostikfunktionen.

Under en stöd för interaktion med Microsoft begära en Microsoft-representant om du är beredd att skicka ett diagnostik-paket till Microsoft för vidare studier. I det här fallet kan du välja att aktivera diagnostik, återskapa problemet och skicka diagnostiska paketet till Microsoft för vidare studier. Diagnostikloggar som skickas till Microsoft utan föregående bekräftelse av en Microsoft-representant hamnar obesvarade.

I följande kodavsnitt visar hur du aktiverar diagnostikläge och hur du kan använda för att skicka diagnostikloggar till Microsoft.

### <a name="enabling-diagnostics-logging"></a>Aktivera diagnostikloggning

När en session har aktiverats för diagnostikloggning, kommer alla åtgärder för sessionen har motsvarande diagnostik loggning på det lokala filsystemet. Loggning till exempel bilder av miljön på disk.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // setup the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // Opt-in to diagnostics logging of environment images.
    // If this is enabled, the diagnostics bundle will include images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submitting-the-diagnostic-bundle"></a>Skicka diagnostiska paket

Följande kodfragment visar hur du skickar in ett diagnostik-paket till Microsoft. Observera att den här innehåller bilder av miljö som avbildas av sessionen när du har aktiverat diagnostik. Dessutom hamnar diagnostik-paket som skickats till Microsoft utan föregående bekräftelse av en Microsoft-representant obesvarade.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest  to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="anatomy-of-the-diagnostics-bundle"></a>Uppbyggnad av diagnostik-paket
Följande information kan finnas i ett paket för diagnostik:

- Bildrutan avbildningar - bilder av miljö som hämtats under sessionen medan diagnostik aktiverades.
- Loggar – Log-händelser som registreras av körningen.
- Sessionen metadata - metadata som identifierar sessionen.
