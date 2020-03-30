---
title: Använda Visual Studio-kod för att skapa mallar
description: Lär dig hur du installerar och använder Visual Studio-kod och Azure Resource Manager Tools-tillägget.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273636"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Använda Visual Studio-kod för att skapa Azure Resource Manager-mallar

Visual Studio Code är en lätt, multiplattform, öppen källkod editor. Tillägget Azure Resource Manager Template Tools är ett plugin för resource manager-mallutveckling. Tillägget lägger till språkstöd för mallar för att ge dig intellisense, syntaxmarkering, in-line hjälp och många andra språkfunktioner. Tillsammans ger de rekommenderad upplevelse av mallutveckling.

## <a name="install-visual-studio-code"></a>Installera Visual Studio Code

Visual studio Code stöder MacOS, Windows och Linux.  Den kan installeras från [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Installera tillägg för Resource Manager-verktyg

1. Öppna Visual Studio Code.
1. Välj **Tillägg** på den vänstra menyn. Eller på **Visa-menyn** väljer du **Tillägg** för att öppna fönstret Tillägg.

    ![Installera tillägg för Verktyg för Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Sök efter **Resurshanteraren**.
1. Välj **Installera** under **Azure Resource Manager Tools**.

## <a name="the-extension-features"></a>Tilläggsfunktionerna

### <a name="colorization-for-template-language-expressions"></a>Färgläggning för mallspråksuttryck

Parametrar, variabler, funktioner, namn och uttryck färgkodas enligt följande skärmbild:

![Visual Studio Code Resource Manager-verktyg tillägg färgläggning](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

Malldisturvyn gör det enkelt att navigera genom stora mallar.

### <a name="intellisense"></a>Intellisense

Malltillägget Resurshanteraren känner till möjliga kompletteringar för funktionsnamn, parametrar, variabler och referenser. IntelliSense-förslagen dyker upp medan du skriver. Om du fortsätter att skriva tecken filtreras listan över medlemmar (variabler, metoder osv.) så att endast medlemmar som innehåller dina maskinskrivna tecken inkluderas. Om du trycker på **Tabb** eller **Retur** infogas den markerade medlemmen.

- Inbyggda funktionsnamn

    ![Visual Studio Code Resource Manager-verktygstillägg intellisense-funktioner](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Parameterreferenser

    ![Verktyg för Visual Studio Code Resource Manager-verktygstillägg intellisense-parametrar](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Variabla referenser

    ![Visual Studio Code Resource Manager-verktygstillägg intellisense-variabler](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- egenskaper för resourceGroup()

    ![Visual Studio Code Resource Manager-verktygstillägg intellisense-funktioner](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Dessutom fungerar intellisense även med egenskaper för prenumeration() och egenskaperna för referenser till variabler som är objekt.

### <a name="signature-help-for-function-parameters"></a>Signaturhjälp för funktionsparametrar

När du hovrar över funktionsnamn visar tillägget signaturhjälpen för funktionsparametrar.

![Signaturfunktionen för Visual Studio Code Resource Manager-verktyg](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Gå till definition för variabel- och parameterreferenser

Du kan gå till definitionen med **Ctrl+Click**eller genom att använda ![snabbmenyn som visas i skärmbilden: Tilläggen för Verktygen för Visual Studio-kodresurshanteraren går till definition](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

Du kan öppna definitionen åt sidan med **Ctrl+Alt+Click**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Granska efter variabel- och parameterdefinitioner

Om du vill öppna den intittade redigeraren använder du snabbmenyn som visas i föregående skärmbild.

Följande skärmbild visar peek editor:

![Visual Studio Code Resource Manager verktyg tillägg peek editor](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Hitta alla referenser för variabler och parametrar

Om du vill hitta alla referenser använder du snabbmenyn som visas i föregående skärmbild.

Följande skärmbild visar hur referenserna är markerade:

![Tillägg för Verktygstillägg för Visual Studio Code Resource Manager hittar alla referenser](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Byta namn på alla referenser för variabler och parametrar

Om du vill byta namn på alla referenser för variabler och parametrar använder du snabbmenyn som visas i föregående skärmbild.

### <a name="hover-for-parameter-description"></a>Hovra för parameterbeskrivning

![Visual Studio Code Resource Manager-verktygstillägg hovrar definition](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Matchning av stag

Matchande parenteser markeras så snart markören är nära en av dem. När du klickar på ett klammerparentes markeras även det matchande klammerparentesen enligt följande skärmbild:

![Visual Studio Code Resource Manager verktyg tillägg stag matchning](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Visa fel och varningar

De fel som identifieras i tillägget är:

- Odefinierade parameterreferenser
- Odefinierade variabelreferenser
- Okända funktionsnamn
- reference() funktionsanvändning i variabel definition
- Felaktigt antal argument i funktioner

Varningarna omfattar:

- Oanvända parametrar
- Oanvända variabler

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar Azure Resource Manager-mallar finns i [Självstudiekurs: Skapa och distribuera din första Azure Resource Manager-mall](template-tutorial-create-first-template.md).
- Information om hur du går igenom en snabbstart med Visual Studio-kod finns i [Snabbstart: Skapa Azure Resource Manager-mallar med visual studio-kod](quickstart-create-templates-use-visual-studio-code.md)
