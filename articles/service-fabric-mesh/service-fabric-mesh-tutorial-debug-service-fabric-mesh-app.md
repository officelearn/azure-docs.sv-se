---
title: 'Självstudie: Felsöka ett Azure Service Fabric Mesh-webbprogram | Microsoft Docs'
description: I den här självstudien får du felsöka ett Azure Service Fabric Mesh-program som körs i ett lokalt kluster.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: e39e5861c0606c43a869fe02a7de2dc9b6f489ea
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125661"
---
# <a name="tutorial-debug-a-service-fabric-mesh-web-application"></a>Självstudie: Felsöka ett Service Fabric Mesh-webbprogram

Den här självstudiekursen är del två i en serie. Här får du se hur du felsöker ett Azure Service Fabric Mesh-program i ett lokalt utvecklingskluster.

I den här självstudien lär du dig:

> [!div class="checklist"]
> * Vad händer när du skapar ett Azure Service Fabric Mesh-program
> * Så ställer du in en brytpunkt för att observera ett tjänst-till-tjänst-samtal

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett Service Fabric Mesh-program](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Felsöka programmet lokalt
> * [Publicera appen i Azure](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Se till att du har [skapat utvecklingsmiljön](service-fabric-mesh-howto-setup-developer-environment-sdk.md) vilket omfattar att installera Service Fabric-runtime, SDK, Docker, och Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Hämta att göra-exempelprogrammet

Om du inte skapade att göra-exempelprogrammet i [del ett av självstudieserien](service-fabric-mesh-tutorial-create-dotnetcore.md) kan du ladda ned det. Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Programmet ligger under katalogen `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Skapa och felsöka i lokalt kluster

En Docker-avbildning skapas och distribuerats automatiskt till det lokala klustret så fort projektet läses in. Den här processen kan ta en stund. För att övervaka förloppet i Visual Studio-rutan **Output** (Utdata) ställer du in Output-fönstret **Show output from:** listrutan (Visa utdata från) på **Service Fabric Tools** (Service Fabric-verktyg).

Tryck på **F5** för att kompilera och köra tjänsten lokalt. När projektet körs och felsöks lokalt händer följande i Visual Studio:

* Se till att Docker för Windows körs och är inställt på att använda Windows som operativsystem för containern.
* Hämta alla eventuella saknade Docker-grundavbildningar. Det här kan ta lite tid
* Skapa (eller återskapa) den Docker-avbildning som användes under kodprojektet.
* Distribuera och kör containern i det lokala Service Fabric-utvecklingsklustret.
* Kör dina tjänster och nå alla brytpunkter som du har angett.

När den lokala distributionen är klar och Visual Studio kör din app öppnas ett webbläsarfönster med en standardexempelwebbsida.

**Felsökningstips**

* Om du får felet **No Service Fabric local cluster is running** (Inget lokalt Service Fabric-kluster körs) ska du se till att SLCM (Service Local Custer Manager) körs och högerklicka på SLCM-ikonen i uppgiftsfältet. Klicka på **Starta lokalt kluster**. När starten är klar återgår du till Visual Studio och trycker på **F5**.
* Om du får ett fel av typen **404** när appen startar innebär det förmodligen att miljövariablerna i **service.yaml** är felaktiga. Se till att `AppName`, `ApiHostPort` och `ServiceName` är rätt inställda enligt instruktionerna i dokumentationen kring att [ställa in miljövariabler](#set-environment-variables).
* Om det uppstår kompileringsfel i **service.yaml** kontrollerar du att blanksteg används för att dra in linjerna. Det går inte att använda tabbar.

### <a name="debug-in-visual-studio"></a>Felsökning i Visual Studio

När du felsöker ett Service Fabric Mesh-program i Visual Studio använder du ett lokalt utvecklingskluster för Service Fabric. Om du vill se hur att göra-objekt hämtas från serversidetjänsten felsöker du i OnGet()-metoden.
1. I projektet **WebFrontEnd** öppnar du **Pages** (sidor)  > **Index.cshtml** > **Index.cshtml.cs** och ställer in en brytpunkt i metoden **Get** (linje 17).
2. I projektet **ToDoService** öppnar du **TodoController.cs** och ställer in en brytpunkt i metoden **OnGet** (linje 15).
3. Gå tillbaka till webbläsaren och uppdatera sidan. Du kommer till brytpunkten i webbklientdelens `OnGet()`-metod. Du kan granska variabeln `backendUrl` för att se hur miljövariablerna som du definierade i **service.yaml**-filen kombineras i den webbadress som användes för att kontakta serversidetjänsten.
4. Stega över (F10) `client.GetAsync(backendUrl).GetAwaiter().GetResult())`-anropet. Du kommer till kontrollantens `Get()`-brytpunkt. I den här metoden kan du se hur listan över att göra-objekt hämtas från listan i minnet.
5. När du är klar kan du stoppa felsökningen av projektet i Visual Studio genom att trycka på **Shift + F5**.
 
## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig:

> [!div class="checklist"]
> * Vad händer när du skapar ett Azure Service Fabric Mesh-program
> * Så ställer du in en brytpunkt för att observera ett tjänst-till-tjänst-samtal

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Publicera ett Service Fabric Mesh-webbprogram](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)