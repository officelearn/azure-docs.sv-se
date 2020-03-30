---
title: Använda funktionsfilter för att aktivera en funktion för en delmängd av användare
titleSuffix: Azure App Configuration
description: Lär dig hur du använder funktionsfilter för att aktivera en funktion för en delmängd av användare
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 181c97615985283011834dcf9145810b1563fb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057006"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Använda funktionsfilter för att aktivera en funktion för en delmängd av användare

Med funktionsflaggor kan du aktivera eller inaktivera funktioner i programmet. En enkel funktionsflagga är antingen på eller av. Programmet fungerar alltid på samma sätt. Du kan till exempel distribuera en ny funktion bakom en funktionsflagga. När funktionsflagga är aktiverad ser alla användare den nya funktionen. Om du inaktiverar funktionsflaggan döljs den nya funktionen.

Med en _villkorsstyrd funktionsflagga_ kan däremot funktionsflaggan aktiveras eller inaktiveras dynamiskt. Programmet kan fungera annorlunda beroende på funktionsflaggas villkor. Anta att du vill visa den nya funktionen för en liten delmängd av användarna först. Med en villkorsstyrd funktionsflagga kan du aktivera funktionsflaggan för vissa användare när du inaktiverar den för andra. _Funktionsfilter_ bestämmer läget för funktionsflaggan varje gång den utvärderas.

Biblioteket `Microsoft.FeatureManagement` innehåller två funktionsfilter:

- `PercentageFilter`aktiverar funktionsflaggan baserat på en procentsats.
- `TimeWindowFilter`aktiverar funktionsflaggan under en angiven tidsperiod.

Du kan också skapa ett eget funktionsfilter som implementerar [gränssnittet Microsoft.FeatureManagement.IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registrera ett funktionsfilter

Du registrerar ett funktionsfilter genom att anropa `AddFeatureFilter` metoden och ange namnet på funktionsfiltret. Följande kod registrerar till `PercentageFilter`exempel:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Konfigurera ett funktionsfilter i Azure App-konfiguration

Vissa funktionsfilter har ytterligare inställningar. Aktiverar till `PercentageFilter` exempel en funktion baserat på en procentsats. Den har en inställning som definierar den procentsats som ska användas.

Du kan konfigurera dessa inställningar för funktionsflaggor som definierats i Azure App-konfiguration. Gör du till exempel `PercentageFilter` så här om du vill använda funktionen för 50 % av begäranden till en webbapp:

1. Följ instruktionerna i [Snabbstart: Lägg till funktionsflaggor i en ASP.NET Core-app](./quickstart-feature-flag-aspnet-core.md) för att skapa en webbapp med en funktionsflagga.

1. Gå till konfigurationsarkivet i Azure-portalen och klicka på **Funktionshanteraren**.

1. Klicka på snabbmenyn *Beta* för betafunktionens flagga som du skapade i snabbstarten. Klicka på **Redigera**.

    > [!div class="mx-imgBorder"]
    > ![Flagga för funktionen Redigera beta](./media/edit-beta-feature-flag.png)

1. Välj knappen **På** **på** redigera om den inte redan är markerad. Klicka sedan på knappen **Lägg till filter.** (På-knappens etikett ändras till **villkorligt**.) **On**

1. Ange *Microsoft.Percentage*i fältet **Nyckel** .

    > [!div class="mx-imgBorder"]
    > ![Lägg till funktionsfilter](./media/feature-flag-add-filter.png)

1. Klicka på snabbmenyn bredvid funktionsfilternyckeln. Klicka på **Redigera parametrar**.

    > [!div class="mx-imgBorder"]
    > ![Redigera funktionsfilterparametrar](./media/feature-flag-edit-filter-parameters.png)

1. Hovra under **namnhuvudet** så att textrutor visas i rutnätet. Ange ett *värdenamn* och **värdet** 50. **Name** Fältet **Värde** anger hur många procent av begäranden som funktionsfiltret ska aktiveras för.

    > [!div class="mx-imgBorder"]
    > ![Ange funktionsfilterparametrar](./media/feature-flag-set-filter-parameters.png)

1. Klicka på **Använd** om du vill gå tillbaka till **flaggan Redigera.** Klicka sedan på **Använd** igen om du vill spara inställningarna för funktionens flagga.

1. **Tillståndet för** funktionsflaggan visas nu som *villkorlig .* Det här tillståndet anger att funktionsflaggan aktiveras eller inaktiveras per begäran, baserat på de kriterier som tillämpas av funktionsfiltret.

    > [!div class="mx-imgBorder"]
    > ![Flagga för villkorlig funktion](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Funktionsfilter i aktion

Om du vill se effekterna av den här funktionsflaggan startar du programmet och trycker på **knappen Uppdatera** i webbläsaren flera gånger. Du ser att *betaobjektet* visas i verktygsfältet ungefär 50 % av tiden. Det döljs resten av tiden, `PercentageFilter` eftersom *betafunktionen* inaktiveras för en delmängd av begäranden. Följande video visar detta beteende i aktion.

> [!div class="mx-imgBorder"]
> ![Procentfilter i åtgärd](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt över funktionshantering](./concept-feature-management.md)
