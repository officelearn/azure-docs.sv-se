---
title: Loggning och diagnostik
description: Djupgående förklaring av hur du genererar och hämtar loggning och diagnostik i Azure Spatial Anchors.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270135"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Loggning och diagnostik i Azure Spatial Anchors

Azure Spatial Anchors tillhandahåller en standardloggningsmekanism som är användbar för apputveckling. Loggningsläget Spatial Anchors är användbart när du behöver mer information för felsökning. Diagnostikloggning lagrar bilder av miljön.

## <a name="standard-logging"></a>Standardloggning
I API:et för spatialankar kan du prenumerera på loggningsmekanismen för att få användbara loggar för programutveckling och felsökning. Standardloggnings-API:erna lagrar inte bilder av miljön på enhetens disk. SDK tillhandahåller dessa loggar som händelseåterknuffningar. Det är upp till dig att integrera dessa loggar i programmets loggningsmekanism.

### <a name="configuration-of-log-messages"></a>Konfiguration av loggmeddelanden
Det finns två motringningar av intresse för användaren. Följande exempel visar hur du konfigurerar sessionen.

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

Dessa händelseåterklädningsapper tillhandahålls för att bearbeta loggar och fel från sessionen:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Anger detaljnivå för de händelser som ska ta emot från körningen.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Tillhandahåller standarddebug logghändelser.
- [Fel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Tillhandahåller logghändelser som körningen anser vara fel.

## <a name="diagnostics-logging"></a>Loggning av diagnostik

Förutom standarddriftsätt för loggning har Spatial Anchors också ett diagnostikläge. Diagnostikläget fångar bilder av miljön och loggar dem till disken. Du kan använda det här läget för att felsöka vissa typer av problem, till exempel om du inte kan hitta ett ankare på ett förutsägbart sätt. Aktivera diagnostikloggning endast för att återskapa ett specifikt problem. Stäng av den då. Aktivera inte diagnostik när du kör dina appar normalt.

Under en supportinteraktion med Microsoft kan en Microsoft-representant fråga om du är villig att skicka in ett diagnostikpaket för vidare undersökning. I det här fallet kan du välja att aktivera diagnostik och återskapa problemet så att du kan skicka diagnostikpaketet.

Om du skickar in en diagnostiklogg till Microsoft utan föregående bekräftelse från en Microsoft-representant förblir inlämningen obesvarad.

Följande avsnitt visar hur du aktiverar diagnostikläge och även hur du skickar diagnostikloggar till Microsoft.

### <a name="enable-diagnostics-logging"></a>Aktivera diagnostikloggning

När du aktiverar en session för diagnostikloggning har alla åtgärder i sessionen motsvarande diagnostik som loggar in i det lokala filsystemet. Vid loggning sparas avbildningar av miljön på disken.

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

### <a name="submit-the-diagnostics-bundle"></a>Skicka diagnostikpaketet

Följande kodavsnitt visar hur du skickar ett diagnostikpaket till Microsoft. Det här paketet innehåller bilder av miljön som tagits av sessionen när du har aktiverat diagnostik.

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

### <a name="parts-of-a-diagnostics-bundle"></a>Delar av ett diagnostikpaket
Diagnostikpaketet kan innehålla följande information:

- **Nyckelbildrutor**: Bilder av miljön som tagits under sessionen medan diagnostik aktiverades.
- **Loggar**: Logga händelser som registrerats av körningen.
- **Sessionsmetadata**: Metadata som identifierar sessionen.
