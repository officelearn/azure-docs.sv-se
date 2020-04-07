---
title: Grafikbindning
description: Inställning av grafikbindningar och användningsfall
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681888"
---
# <a name="graphics-binding"></a>Grafikbindning

För att kunna använda Azure Remote Rendering i ett anpassat program måste den integreras i programmets renderingspipeline. Denna integration är grafikbindningens ansvar.

När grafikbindningen har ställts in får du tillgång till olika funktioner som påverkar den renderade bilden. Dessa funktioner kan delas in i två kategorier: allmänna funktioner som alltid `Microsoft.Azure.RemoteRendering.GraphicsApiType`är tillgängliga och specifika funktioner som bara är relevanta för det valda .

## <a name="graphics-binding-in-unity"></a>Grafikbindning i Unity

I Unity hanteras hela bindningen av `RemoteUnityClientInit` den `RemoteManagerUnity.InitializeManager`struktur som överförs till . Om du vill ställa `GraphicsApiType` in grafikläget måste fältet ställas in på den valda bindningen. Fältet fylls i automatiskt beroende på om det finns en XRDevice. Beteendet kan åsidosättas manuellt med följande beteenden:

* **HoloLens 2**: Windows Mixed Reality-grafikbindningen används alltid. [Windows Mixed Reality](#windows-mixed-reality)
* **Platt UWP-skrivbordsapp:** [Simulering](#simulation) används alltid. Om du vill använda det här läget måste du följa stegen i [Självstudiekurs: Konfigurera ett Unity-projekt från grunden](../tutorials/unity/project-setup.md).
* **Unity editor**: [Simulering](#simulation) används alltid om inte ett WMR VR-headset är anslutet, i vilket fall ARR kommer att inaktiveras för att tillåta att felsöka icke-ARR-relaterade delar av programmet. Se även [holografisk remoting](../how-tos/unity/holographic-remoting.md).

Den enda andra relevanta delen för Unity är att komma åt den [grundläggande bindningen](#access), alla andra avsnitt nedan kan hoppas över.

## <a name="graphics-binding-setup-in-custom-applications"></a>Konfigurera grafikbindning i anpassade program

Om du vill välja en grafikbindning gör du följande två steg: För det första måste grafikbindningen initieras statiskt när programmet initieras:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

Anropet ovan är nödvändigt för att initiera Azure Remote Rendering till holografiska API:er. Den här funktionen måste anropas innan ett holografiskt API anropas och innan andra API:er för fjärrrendering används. På samma sätt bör motsvarande `RemoteManagerStatic.ShutdownRemoteRendering();` de-init-funktion anropas efter att inga holografiska API:er anropas längre.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Komma åt grafikbindning

När en klient har konfigurerats kan den grundläggande `AzureSession.GraphicsBinding` grafikbindningen nås med gettern. Som ett exempel kan den senaste bildrutestatistiken hämtas så här:

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Grafiska API:er

Det finns för närvarande två grafik-API:er som kan väljas `WmrD3D11` och `SimD3D11`. En tredje `Headless` finns men stöds ännu inte på klientsidan.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`är standardbindningen som ska köras på HoloLens 2. Det kommer `GraphicsBindingWmrD3d11` att skapa bindningen. I det här läget Azure Remote Rendering krokar direkt i holografiska API: er.

För att komma åt de härledda grafikbindningarna måste basen `GraphicsBinding` gjutas.
Det finns två saker som måste göras för att använda WMR-bindningen:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informera fjärrrendering av det använda koordinatsystemet

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Där ovanstående `ptr` måste vara en `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` pekare till ett inbyggt objekt som definierar det globala rymdkoordinatsystem där koordinater i API:et uttrycks i.

#### <a name="render-remote-image"></a>Återge fjärrbild

I början av varje bildruta måste fjärrramen återges i den bakre bufferten. Detta görs genom `BlitRemoteFrame`att anropa , som fyller både färg- och djupinformation i det för närvarande bundna renderingsmålet. Därför är det viktigt att detta görs efter bindning tillbaka bufferten som en render mål.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Simulering

`GraphicsApiType.SimD3D11`är simuleringsbindningen och `GraphicsBindingSimD3d11` om det här alternativet väljs skapas grafikbindningen. Detta gränssnitt används för att simulera huvudets rörelse, till exempel i ett skrivbordsprogram och återger en monoskopisk bild.
Installationen är lite mer involverad och fungerar på följande sätt:

#### <a name="create-proxy-render-target"></a>Skapa mål för proxyåtergivning

Fjärrinnehåll och lokalt innehåll måste återges till en offscreen färg / djup göra mål `GraphicsBindingSimD3d11.Update` som kallas "proxy" med hjälp av proxy kameradata som tillhandahålls av funktionen. Proxyn måste matcha upplösningen på bakåtbufferten. När en session `GraphicsBindingSimD3d11.InitSimulation` är klar måste du anropas innan du ansluter till den:

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Init-funktionen måste förses med pekare till den inbyggda d3d-enheten samt till proxyåtergivningsmålets färg- och djupstruktur. En gång `AzureSession.ConnectToRuntime` initierat, `DisconnectFromRuntime` och kanna bli alarmerat `GraphicsBindingSimD3d11.DeinitSimulation` flera tiden utom när kopplande `GraphicsBindingSimD3d11.InitSimulation` till en olik session, nödvändigtvis till vara alarmerat första på den gammal session framför kanna bli kallat på en annan session.

#### <a name="render-loop-update"></a>Uppdatera renderingsloop

Renderingsloopuppdateringen består av flera steg:

1. Varje bildruta, innan någon `GraphicsBindingSimD3d11.Update` rendering sker, anropas med den aktuella kameratransformeringen som skickas över till den server som ska återges. Samtidigt bör den returnerade proxytransformeringen tillämpas på proxykameran för att renderas i proxyåtergivningsmålet.
Om den returnerade `SimulationUpdate.frameId` proxyuppdateringen är null finns det inga fjärrdata ännu. I det här fallet, i stället för att återge till proxyåtergivningsmålet, ska allt lokalt innehåll återges till den bakre bufferten direkt med hjälp av aktuella kameradata och de följande två stegen hoppas över.
1. Programmet bör nu binda proxy återge mål och samtal `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`. Detta fyller fjärrfärg och djupinformation i proxyåtergivningsmålet. Allt lokalt innehåll kan nu återges på proxyn med hjälp av proxykameratransformeringen.
1. Därefter måste bakåtbufferten vara bunden `GraphicsBindingSimD3d11.ReprojectProxy` som ett återgemål och anropas vid vilken tidpunkt den bakre bufferten kan visas.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>Nästa steg

* [Självstudiekurs: Ställa in ett Unity-projekt från grunden](../tutorials/unity/project-setup.md)
