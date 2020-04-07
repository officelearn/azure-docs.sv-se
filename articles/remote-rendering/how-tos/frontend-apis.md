---
title: Azure Frontend API:er för autentisering
description: Förklarar hur du använder C#-klientdels-API:et för autentisering
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681355"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Använda Azure Frontend API:er för autentisering

I det här avsnittet beskriver vi hur du använder C# API för autentisering.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo används för att ställa in ```AzureFrontend``` autentiseringsinformation för en instans i SDK.

De viktiga områdena är:

```cs

    public class AzureFrontendAccountInfo
    {
        // Something akin to "<region>.mixedreality.azure.com"
        public string AccountDomain;

        // Can use one of:
        // 1) ID and Key.
        // 2) AuthenticationToken.
        // 3) AccessToken.
        public string AccountId = Guid.Empty.ToString();
        public string AccountKey = string.Empty;
        public string AuthenticationToken = string.Empty;
        public string AccessToken = string.Empty;
    }

```

Använd en [region nära dig](../reference/regions.md)för _regiondelen_ i domänen .

Kontoinformationen kan erhållas från portalen enligt beskrivningen i [återhämtningskontoinformationsstycket.](create-an-account.md#retrieve-the-account-information)

## <a name="azure-frontend"></a>Azure Frontend

De relevanta ```AzureFrontend``` klasserna är och ```AzureSession```. ```AzureFrontend```används för kontohantering och kontonivå, vilket inkluderar: tillgångskonvertering och skapande av återgivningssessioner. ```AzureSession```används för sessionsnivåfunktioner och den innehåller: sessionsuppdatering, frågor, förnyelse och avveckling.

Varje öppnas/skapas ```AzureSession``` behåller en referens till klientdelen som har skapat den. För att stängas av på ett rent sätt måste alla sessioner hanteras innan frontenden kommer att deallocated.

Att frigöra en session stoppar inte den `AzureSession.StopAsync` virtuella datorn på Azure, måste uttryckligen anropas.

När en session har skapats och dess tillstånd har markerats som `AzureSession.ConnectToRuntime`klar kan den ansluta till fjärrrenderingskörningen med .

### <a name="threading"></a>Threading

Alla AzureSession- och AzureFrontend-asynkronanrop har slutförts i en bakgrundstråd, inte huvudprogramtråden.

### <a name="conversion-apis"></a>Api:er för konvertering

Mer information om konverteringstjänsten finns [i REST API för modellkonvertering](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Starta konvertering av tillgångar

``` cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string modelName, string modelUrl, string assetContainerUrl)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionParams(modelName, modelUrl, assetContainerUrl));
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

#### <a name="get-conversion-status"></a>Hämta konverteringsstatus

``` cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

### <a name="rendering-apis"></a>Api:er för rendering

Mer information om [sessionshanterings-REST API finns i REST-API:et för sessionshantering.](session-rest-api.md)

En återgivningssession kan antingen skapas dynamiskt på tjänsten eller så kan ett redan befintligt sessions-ID "öppnas" till ett AzureSession-objekt.

#### <a name="create-rendering-session"></a>Skapa återgivningssession

``` cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="open-an-existing-rendering-session"></a>Öppna en befintlig återgivningssession

Att öppna en befintlig session är ett synkront anrop.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Hämta aktuella renderingssessioner

``` cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

### <a name="session-apis"></a>Api:er för session

#### <a name="get-rendering-session-properties"></a>Hämta egenskaper för renderingssession

``` cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="update-rendering-session"></a>Uppdatera renderingssession

``` cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="stop-rendering-session"></a>Stoppa renderingssession

``` cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="connect-to-arr-inspector"></a>Anslut till ARR-inspektör

``` cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

## <a name="next-steps"></a>Nästa steg

* [Skapa ett konto](create-an-account.md)
* [Exempel på PowerShell-skript](../samples/powershell-example-scripts.md)
