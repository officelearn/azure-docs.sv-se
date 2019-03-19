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
ms.openlocfilehash: 134023c0884ce3a402b99806f1bf19dcb59ecc32
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57882836"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Loggning och diagnostik i Azure Spatial ankare

Azure Spatial fästpunkter är en standard loggningsmekanism som är användbar för utveckling av appar. Loggning för Spatial ankare diagnostikläge är användbart när du behöver mer information för felsökning. Diagnostikloggning lagrar bilder av miljön.

## <a name="standard-logging"></a>Standard-loggning
I Spatial ankare API, kan du prenumerera mekanismen loggning för att få användbara loggar för utveckling och felsökning. Standard loggning API: er inte lagra bilder av miljön på disken för enheten. SDK innehåller loggarna som händelsen återanrop. Det är upp till dig att integrera dessa loggar i programmets loggningsmekanism.

### <a name="configuration-of-log-messages"></a>Konfigurationen av loggmeddelanden
Det finns två återanrop av intresse för användaren. I följande exempel visas hur du konfigurerar sessionen.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Händelser och egenskaper

Dessa event återanrop tillhandahålls för att bearbeta loggar och fel från sessionen:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Anger detaljnivån för att händelser ska ta emot från körningen.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Innehåller standard debug logghändelser.
- [Fel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Ger händelser som körningen anser vara fel.

## <a name="diagnostics-logging"></a>Diagnostikloggning

Spatial fästpunkter har också en diagnostikläge förutom standardläge för loggning. Diagnostikläge samlar in bilder av miljön och loggar dem till disken. Du kan använda det här läget för att felsöka vissa typer av problem, t.ex. Det gick inte att hitta en fästpunkt förutsägbart. Aktivera diagnostik loggning endast för att återskapa ett specifikt problem. Inaktivera det sedan. Aktivera inte diagnostik när du kör dina appar normalt.

Under en stöd för interaktion med Microsoft kan en Microsoft-representant fråga om du är beredd att skicka ett paket med diagnostik för vidare studier. I det här fallet kan du välja att aktivera diagnostik och återskapa problemet så att du kan skicka diagnostiska paket. 

Om du skickar en diagnostiklogg till Microsoft utan föregående bekräftelse från en Microsoft-representant hamnar överföringen obesvarade.

I följande avsnitt visas hur du aktiverar diagnostikläge och hur du skickar in diagnostikloggar till Microsoft.

### <a name="enable-diagnostics-logging"></a>Aktivera diagnostikloggning

När du aktiverar en session för diagnostik loggar har alla åtgärder i sessionen motsvarande diagnostik loggning i det lokala filsystemet. Under loggning sparas bilder miljöns till disken.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Skicka diagnostik-paket

Följande kodfragment visar hur du skickar in ett diagnostik-paket till Microsoft. Det här paketet innehåller bilder av miljö som avbildas av sessionen när du aktiverar diagnostik. 

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Delar av ett paket med diagnostik
Diagnostik-paket kan innehålla följande information:

- **Bildrutan avbildningar**: Avbildningar av miljö som hämtats under sessionen medan diagnostik aktiverades.
- **Loggar**: Logga händelser som registreras av körningen.
- **Sessionen metadata**: Metadata som identifierar sessionen.
