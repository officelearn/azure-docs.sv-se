---
title: Använd Visual Studio Code för att skapa mallar
description: Lär dig hur du installerar och använder Visual Studio Code och Azure Resource Manager Tools-tillägget.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 3f0eaf44a536cfb54796744068fee8a70135597a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149388"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Använd Visual Studio Code för att skapa Azure Resource Manager mallar

Visual Studio Code är en enkel redigerare med öppen källkod med fler än plattformar. Tillägget Azure Resource Manager Template Tools är ett plugin-program för utveckling av Resource Manager-mallar. Tillägget lägger till språk stöd för mallar för att ge dig IntelliSense, syntax för syntax, direkt hjälp och många andra språk funktioner. Tillsammans ger de den rekommenderade mall utvecklings upplevelsen.

## <a name="install-visual-studio-code"></a>Installera Visual Studio Code

Visual Studio Code stöder MacOS, Windows och Linux.  Den kan installeras från [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Installera tillägg för Resource Manager-verktyg

1. Öppna Visual Studio Code.
1. Välj **tillägg** på den vänstra menyn. Eller Välj **tillägg** på **Visa** -menyn för att öppna fönstret tillägg.

    ![Installera tillägg för Visual Studio Code Resource Manager-verktyg](./media/resource-manager-tools-vs-code/resource-manager-visual-studio-code-tools-extension.png)
1. Sök efter **Resource Manager**.
1. Välj **Installera** under **Azure Resource Manager verktyg**.

## <a name="the-extension-features"></a>Tilläggsfunktioner

### <a name="colorization-for-template-language-expressions"></a>Färg för språk uttryck för mall

Parametrar, variabler, funktioner, namn och uttryck färgkodas som visas på följande skärm bild:

![Färg tillägg för Visual Studio Code Resource Manager-verktyg](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-colorization.png)

I vyn mall för disposition är det enkelt att navigera genom stora mallar.

### <a name="intellisense"></a>Tillhandahåller

Med tillägget Resource Manager-mall får du möjliga slut för ande av funktions namn, parametrar, variabler och referenser. IntelliSense-förslagen visas när du skriver. Om du fortsätter att skriva tecken filtreras listan över medlemmar (variabler, metoder osv.) för att bara inkludera medlemmar som innehåller dina skrivna tecken. Om du trycker på **TABB** eller **RETUR** infogas den markerade medlemmen.

- Inbyggda funktions namn

    ![Visual Studio Code Resource Manager Tools Extensions IntelliSense-funktioner](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-functions.png)

- Parameter referenser

    ![Visual Studio Code Resource Manager Tools tillägg IntelliSense-parametrar](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-parameters.png)

- Variabel referenser

    ![Visual Studio Code Resource Manager Tools tillägg IntelliSense-variabler](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-variables.png)

- egenskaper för resourceGroup ()

    ![Visual Studio Code Resource Manager Tools Extensions IntelliSense-funktioner](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-resourcegroup.png)

Dessutom fungerar IntelliSense med prenumerations egenskaper () och egenskaperna för referenser till variabler som är objekt.

### <a name="signature-help-for-function-parameters"></a>Signatur hjälp för funktions parametrar

När du hovrar över funktions namn, visar tillägget Signature-hjälpen för funktions parametrar.

![Funktionen signatur för Visual Studio Code Resource Manager-verktyg](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Gå till definition för variabel-och parameter referenser

Du kan gå till definitionen med **CTRL + klicka**eller genom att använda snabb menyn som visas i skärm bilden: ![tillägg för Visual Studio Code Resource Manager-verktyg gå till definition](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-context-menu.png)

Du kan öppna definitionen med **Ctrl + Alt + klicka**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Spetsigt för variabler och parameter definitioner

Om du vill öppna gransknings redigeraren använder du snabb menyn som visas på föregående skärm bild.

I följande skärm bild visas gransknings redigeraren:

![Visual Studio Code Resource Manager Tools Extensions Peek Editor](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Sök efter alla referenser för variabler och parametrar

Om du vill söka efter alla referenser använder du snabb menyn som visas på föregående skärm bild.

Följande skärm bild visar hur referenserna är markerade:

![Tillägg för Visual Studio Code Resource Manager-verktyg Sök efter alla referenser](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Byt namn på alla referenser för variabler och parametrar

Om du vill byta namn på alla referenser för variabler och parametrar använder du snabb menyn som visas på föregående skärm bild.

### <a name="hover-for-parameter-description"></a>Hovring för parameter Beskrivning

![Objekt hov rings definition för Visual Studio Code Resource Manager-verktyg](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Matchning av klammerparentes

Matchande hakparenteser markeras så snart som markören är nära en av dem. När du klickar på en klammerparentes, markeras även den matchande klammerparentesen som visas på följande skärm bild:

![Matchning av tillägg för Visual Studio Code Resource Manager-verktyg](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Visa fel och varningar

Felen som identifieras av tillägget är:

- Odefinierade parameter referenser
- Odefinierade variabel referenser
- Okända funktions namn
- referens ()-funktions användning i variabel definition
- Felaktigt antal argument i functions

Varningarna är:

- Oanvända parametrar
- Oanvända variabler

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar Azure Resource Manager-mallar finns i [Självstudier: skapa och distribuera din första Azure Resource Manager-mall](template-tutorial-create-first-template.md).
- Information om hur du går igenom en snabb start med hjälp av Visual Studio Code finns i [snabb start: skapa Azure Resource Manager mallar med hjälp av Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
