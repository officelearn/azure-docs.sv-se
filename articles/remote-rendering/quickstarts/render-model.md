---
title: Återge en modell med Unity
description: Snabbstart som guidar användaren genom stegen för att återge en modell
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b0af45ba4a6b1ca7f9e751af082ff0db80776ec0
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679788"
---
# <a name="quickstart-render-a-model-with-unity"></a>Snabbstart: Återge en modell med Unity

Den här snabbstarten beskriver hur du kör ett Unity-exempel som återger en inbyggd modell på distans med hjälp av Azure Remote Rendering (ARR) tjänsten.

Vi kommer inte att gå in på detaljer om ARR API själv eller hur man ställer in ett nytt Unity-projekt. Dessa ämnen behandlas i [Handledning: Inrätta en Enhet projekt från grunden](../tutorials/unity/project-setup.md).

I den här snabbstarten får du lära dig hur du:
> [!div class="checklist"]
>
>* Konfigurera din lokala utvecklingsmiljö
>* Hämta och skapa exempelappen ARR Quickstart för Unity
>* Återge en modell i exempelappen Snabbstart i ARR

## <a name="prerequisites"></a>Krav

För att få åtkomst till Azure Remote Rendering-tjänsten måste du först [skapa ett konto](../how-tos/create-an-account.md).

Följande program måste installeras:

* Windows SDK 10.0.18362.0 [(hämta)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Den senaste versionen av Visual Studio 2019 [(hämtad)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(ladda ner)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(ladda ner)](https://unity3d.com/get-unity/download)
  * Installera dessa moduler i Unity:
    * **UWP** – Stöd för universal windows-plattformsbygge
    * **IL2CPP** - Stöd för Windows Build (IL2CPP)

## <a name="clone-the-sample-app"></a>Klona exempelappen

Öppna en kommandotolk (skriv `cmd` in Start-menyn i Windows) och ändra till en katalog där du vill lagra ARR-exempelprojektet.

Kör följande kommandon:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Det sista kommandot skapar en underkatalog i ARR-katalogen som innehåller de olika exempelprojekten för Azure Remote Rendering.

Snabbstartsprovappen för Unity finns i underkatalogen *Unity/Quickstart*.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Återge en modell med exempelprojektet Unity

Öppna Unity Hub och lägg till exempelprojektet, som är mappen *ARR\azure-remote-rendering\Unity\Quickstart.*
Öppna projektet. Om det behövs kan Unity uppgradera projektet till den installerade versionen.

Standardmodellen vi renderar är en [inbyggd exempelmodell](../samples/sample-model.md). Vi visar hur du konverterar en anpassad modell med hjälp av ARR-konverteringstjänsten i [nästa snabbstart.](convert-model.md)

### <a name="enter-your-account-info"></a>Ange din kontoinformation

1. I webbläsaren Enhetstillgångar navigerar du till mappen *Scener* och öppnar **snabbstartsscenen.**
1. Välj **fjärrrenderingsspelobjektet** i *hierarkin.*
1. Ange dina [kontouppgifter](../how-tos/create-an-account.md)i *inspektören*.

![ARR-kontoinformation](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Azure Portal visar ditt kontos domän endast *som mixedreality.azure.com*. Detta är otillräckligt för anslutning.
> Ange **AccountDomain** `<region>.mixedreality.azure.com`till `<region>` , där [är ett av de tillgängliga områdena nära dig](../reference/regions.md).

Senare vill vi distribuera det här projektet till en HoloLens och ansluta till fjärrrenderingstjänsten från den enheten. Eftersom vi inte har något enkelt sätt att ange autentiseringsuppgifter på enheten, kommer snabbstartsprovet att **spara autentiseringsuppgifterna i Unity-scenen**.

> [!WARNING]
> Se till att inte kontrollera projektet med dina sparade autentiseringsuppgifter i något arkiv där det skulle läcka hemlig inloggningsinformation!

### <a name="create-a-session-and-view-the-default-model"></a>Skapa en session och visa standardmodellen

Tryck på Unity's **Play-knappen** för att starta sessionen. Du bör se ett överlägg med statustext längst ned *Game* i visningsområdet på spelpanelen. Sessionen genomgår en rad tillståndsövergångar. I **startläget** snurras fjärrdatorn upp, vilket tar flera minuter. När den har lyckats övergår den till tillståndet **Klar.** Nu går sessionen **Connecting** in i anslutningstillståndet, där den försöker nå renderingskörningen på den virtuella datorn. När det lyckas övergår exemplet till läget **Ansluten.** Vid denna punkt, kommer det att börja ladda ner modellen för rendering. På grund av modellens storlek kan nedladdningen ta några minuter till. Då visas den fjärrrenderade modellen.

![Utdata från provet](media/arr-sample-output.png)

Grattis! Du visar nu en fjärrrengörd modell!

## <a name="inspecting-the-scene"></a>Inspektera scenen

När fjärrrenderingsanslutningen har körts uppdateras panelen Inspektör med ytterligare statusinformation:

![Unity provspela](./media/arr-sample-configure-session-running.png)

Du kan nu utforska scendiagrammet genom att välja den nya noden och klicka på **Visa underordnade** i inspektören.

![Enhetshierarki](./media/unity-hierarchy.png)

Det finns ett [avklippt planföremål](../overview/features/cut-planes.md) på platsen. Prova att aktivera den i dess egenskaper och flytta runt den:

![Byta snittplan](media/arr-sample-unity-cutplane.png)

Om du vill synkronisera transformeringar klickar du antingen på **Synkronisera nu** eller kontrollerar alternativet Synkronisera **alla ramar.** För komponentegenskaper räcker det med att bara ändra dem.

## <a name="next-steps"></a>Nästa steg

I nästa snabbstart distribuerar vi exemplet till en HoloLens för att visa den fjärrrenderade modellen i dess ursprungliga storlek.

> [!div class="nextstepaction"]
> [Snabbstart: Distribuera Unity-exempel till HoloLens](deploy-to-hololens.md)

Alternativt kan exemplet också distribueras till en stationär dator.

> [!div class="nextstepaction"]
> [Snabbstart: Distribuera Unity-exempel till skrivbordet](deploy-to-desktop.md)