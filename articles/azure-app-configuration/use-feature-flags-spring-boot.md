---
title: Självstudiekurs för att använda funktionsflaggor i en Spring Boot-app - Azure App Configuration | Microsoft-dokument
description: I den här självstudien får du lära dig hur du implementerar funktionsflaggor i Spring Boot-appar.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: d519ac44d617f725aa9b3d3f11671122bd9477bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944318"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Självstudiekurs: Använda funktionsflaggor i en spring boot-app

Spring Boot Core Feature Management bibliotek ger stöd för att genomföra funktionen flaggor i en Spring Boot ansökan. Med de här biblioteken kan du deklarativt lägga till funktionsflaggor i koden.

Funktionshanteringsbiblioteken hanterar också funktionsflagglivscykler bakom kulisserna. Biblioteken uppdaterar och cacheföreskar tillstånd, eller garanterar att ett flaggtillstånd inte kan ändras under ett begärandeanrop. Dessutom erbjuder Spring Boot-biblioteket integrationer, inklusive MVC-styrenhetsåtgärder, rutter och mellanprogram.

[Snabbstarten för lägg till funktionsflaggor i en spring boot-app](./quickstart-feature-flag-spring-boot.md) visar flera sätt att lägga till funktionsflaggor i ett Spring Boot-program. Den här självstudien förklarar dessa metoder mer i detalj.

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Lägg till funktionsflaggor i viktiga delar av programmet för att styra funktionens tillgänglighet.
> * Integrera med appkonfiguration när du använder den för att hantera funktionsflaggor.

## <a name="set-up-feature-management"></a>Konfigurera funktionshantering

Spring Boot-funktionshanteraren `FeatureManager` får funktionsflaggor från ramverkets inbyggda konfigurationssystem. Därför kan du definiera programmets funktionsflaggor med hjälp av valfri konfigurationskälla som Spring Boot stöder, inklusive de lokala *bootstrap.yml-fil-* eller miljövariablerna. `FeatureManager`beroendeinjektion. Du kan registrera funktionshanteringstjänsterna med hjälp av standardkonventioner:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Vi rekommenderar att du håller funktionsflaggor utanför programmet och hanterar dem separat. På så sätt kan du ändra flaggtillstånd när som helst och få dessa ändringar att träda i kraft i programmet direkt. Appkonfiguration är en centraliserad plats för att organisera och styra alla dina funktionsflaggor via ett dedikerat portalgränssnitt. Appkonfiguration levererar också flaggorna till ditt program direkt via dess Spring Boot-klientbibliotek.

Det enklaste sättet att ansluta ditt Spring Boot-program till appkonfiguration är via konfigurationsleverantören:

### <a name="spring-cloud-11x"></a>Vårmoln 1.1.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Vårmoln 1.2.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Flagga deklaration för funktion

Varje funktionsflagga har två delar: ett namn och en lista med ett *on* eller flera filter som används `True`för att utvärdera om en funktions tillstånd är aktiverat (det vill säga när dess värde är ). Ett filter definierar ett användningsfall för när en funktion ska aktiveras.

När en funktionsflagga har flera filter genomdäms filterlistan i ordning tills ett av filtren bestämmer att funktionen ska aktiveras. Då är funktionsflaggan *aktiverad*och eventuella återstående filterresultat hoppas över. Om inget filter anger att funktionen ska aktiveras är funktionsflaggan *inaktiverad*.

Funktionshanteraren stöder *application.yml* som en konfigurationskälla för funktionsflaggor. I följande exempel visas hur du ställer in funktionsflaggor i en YAML-fil:

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

Av konvention `feature-management` används avsnittet i detta YML-dokument för funktionsflagginställningar. I föregående exempel visas tre funktionsflaggor med filter definierade i egenskapen: `EnabledFor`

* `feature-a`är *på*.
* `feature-b`är *avstängd*.
* `feature-c`anger ett filter `Percentage` som `parameters` namnges med en egenskap. `Percentage`är ett konfigurerbart filter. I det `Percentage` här exemplet anger en 50-procentig sannolikhet för att `feature-c` flaggan ska vara *på*.

## <a name="feature-flag-checks"></a>Kontroller av funktionsflagga

Det grundläggande mönstret för funktionshantering är att först kontrollera om en funktionsflagga är inställd *på*. Om så är fallet kör funktionshanteraren sedan de åtgärder som funktionen innehåller. Ett exempel:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>Beroendeinmatning

I Spring Boot kan du `FeatureManager` komma åt funktionshanteraren genom beroendeinjektion:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Åtgärder för kontrollant

I MVC-styrenheter använder `@FeatureGate` du attributet för att styra om en viss åtgärd är aktiverad. Följande `Index` åtgärd `feature-a` måste vara *aktiverad* innan den kan köras:

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

När en MVC-styrenhet eller åtgärd blockeras eftersom `IDisabledFeaturesHandler` kontrollfunktionsflaggan är *inaktiverad*anropas ett registrerat gränssnitt. Standardgränssnittet `IDisabledFeaturesHandler` returnerar en 404-statuskod till klienten utan svarstext.

## <a name="mvc-filters"></a>MVC-filter

Du kan ställa in MVC-filter så att de aktiveras baserat på tillståndet för en funktionsflagga. Följande kod lägger till ett `FeatureFlagFilter`MVC-filter med namnet . Det här filtret utlöses endast i `feature-a` MVC-pipelinen om det är aktiverat.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Vägar

Du kan använda funktionsflaggor för att omdirigera rutter. Följande kod omdirigerar en `feature-a` användare från är aktiverad:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du `spring-cloud-azure-feature-management-web` implementerar funktionsflaggor i spring boot-programmet med hjälp av biblioteken. Mer information om stöd för funktionshantering i Spring Boot och App Configuration finns i följande resurser:

* [Exempelkod för fjädrarstart funktionens flagga](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Hantera funktionsflaggor](./manage-feature-flags.md)
