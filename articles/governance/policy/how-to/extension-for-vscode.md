---
title: Azure Policy tillägg för Visual Studio Code
description: Lär dig hur du använder Azure Policy-tillägget för Visual Studio Code för att leta upp Resource Manager-alias.
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 20af29c40ec13add90294e28d64f0166acc95319
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514815"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Använd Azure Policy-tillägg för Visual Studio Code

> Gäller Azure Policy Extension version **0.0.21** och senare

Lär dig hur du använder Azure Policy tillägget för Visual Studio Code för att söka efter [alias](../concepts/definition-structure.md#aliases) och granska resurser och principer. Först beskriver vi hur du installerar tillägget Azure Policy i Visual Studio Code. Sedan kommer vi att gå igenom hur du söker efter alias.

Azure Policy-tillägget för Visual Studio Code kan installeras på alla plattformar som stöds av Visual Studio Code. Det här stödet omfattar Windows, Linux och macOS.

> [!NOTE]
> Ändringar som görs lokalt i principer som visas i Azure Policy-tillägget för Visual Studio Code synkroniseras inte med Azure.

## <a name="prerequisites"></a>Förutsättningar

Följande objekt krävs för att slutföra stegen i den här artikeln:

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Installera Azure Policy tillägg

När du uppfyller kraven kan du installera Azure Policy tillägget för Visual Studio Code genom att följa dessa steg:

1. Öppna Visual Studio Code.

1. I meny raden går du till **visa** > **tillägg**.

1. I rutan Sök anger du **Azure policy**.

1. Välj **Azure policy** från Sök resultaten och välj sedan **Installera**.

1. Välj **Läs in igen** när det behövs.

## <a name="set-the-azure-environment"></a>Ange Azure-miljön

För en nationell moln användare följer du de här stegen för att ställa in Azure-miljön först:

1. Välj **File\Preferences\Settings**.

1. Sök efter följande sträng: _Azure: Cloud_

1. Välj nation-molnet i listan:

   ![Ange standard Azure Cloud-inloggning för Visual Studio Code](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Ansluta till ett Azure-konto

Om du vill utvärdera resurser och uppslags-alias måste du ansluta till ditt Azure-konto. Följ dessa steg för att ansluta till Azure från Visual Studio Code:

1. Logga in på Azure från Azure Policy-tillägget eller paletten Command.

   - Azure Policy tillägg

     Från Azure Policy-tillägget väljer **du logga in på Azure**.

     ![Azure Cloud-inloggning för Visual Studio Code från Azure Policy-tillägget](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Kommando palett

     I meny raden går du till **visa** > **Command-paletten**och anger **Azure: Logga**in.

     ![Azure Cloud-inloggning för Visual Studio Code från kommando paletten](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Följ anvisningarna för inloggning för att logga in på Azure. När du är ansluten visas namnet på ditt Azure-konto i statusfältet längst ned i Visual Studio Code-fönstret.

## <a name="select-subscriptions"></a>Välj prenumerationer

Första gången du loggar in, läses endast standard prenumerations resurser och principer in av Azure Policy-tillägget. Följ dessa steg om du vill lägga till eller ta bort prenumerationer från att visa resurser och principer:

1. Starta kommandot prenumeration från kommando tolken eller fönster foten.

   - Kommando palett: 

     I meny raden går du till **visa** > - **kommando paletten**och anger **Azure policy: Välj prenumerationer**.

   - Fönster fot

     I fönster foten längst ned på skärmen väljer du det segment som matchar **Azure: \<ditt konto\>** .

1. Använd filter rutan för att snabbt hitta prenumerationer efter namn. Markera eller ta bort kontrollen från varje prenumeration för att ange de prenumerationer som visas i Azure Policy tillägget. När du har lagt till eller tagit bort prenumerationer som ska visas väljer du **OK**.

## <a name="search-for-and-view-resources"></a>Sök efter och visa resurser

I Azure Policy-tillägget visas resurser i de valda prenumerationerna per resurs leverantör och resurs grupp i fönstret **resurser** . Trädvyn innehåller följande grupperingar av resurser i den valda prenumerationen eller på prenumerations nivån:

- **Resurs leverantörer**
  - Varje registrerad resurs leverantör med resurser och relaterade underordnade resurser som har princip-alias
- **Resurs grupper**
  - Alla resurser av resurs gruppen som de befinner sig i

Som standard filtrerar tillägget "Resource Provider"-delen av befintliga resurser och resurser som har princip-alias. Ändra det här beteendet i **inställningar** > **tillägg** > **Azure policy** för att se alla resurs leverantörer utan att filtrera.

Kunder med hundratals eller tusentals resurser i en enda prenumeration kan föredra ett sökbart sätt att hitta sina resurser. Med Azure Policy-tillägget kan du söka efter en speciell resurs med följande steg:

1. Starta Sök gränssnittet från Azure Policy-tillägget eller paletten Command.

   - Azure Policy tillägg

     Från Azure Policy tillägget, Hovra över panelen **resurser** och välj ellipsen och välj sedan **Sök resurser**.

   - Kommando palett:

     I meny raden går du till **visa** > **kommando palett**och anger **resurser: Sök efter resurser**.

1. Om fler än en prenumeration har valts för visning använder du filtret för att välja vilken prenumeration som ska genomsökas.

1. Använd filtret för att välja vilken resurs grupp som ska genomsökas som är en del av den tidigare valda prenumerationen.

1. Använd filtret för att välja vilken resurs som ska visas. Filtret fungerar både för resurs namnet och resurs typen.

## <a name="discover-aliases-for-resource-properties"></a>Identifiera alias för resurs egenskaper

När en resurs väljs, oavsett om det sker via Sök gränssnittet eller genom att välja den i trädvyn, öppnar Azure Policy-tillägget JSON-filen som representerar resursen och alla dess egenskaps värden för Resource Manager.

När en resurs är öppen visas Azure Policy alias om ett sådant finns under hovring över resurs hanterarens egenskaps namn eller värde. I det här exemplet är resursen en `Microsoft.Compute/virtualMachines` resurs typ och egenskapen **Properties. storageProfile. imageReference. erbjudande** hovras över. Vid hovring visas matchande alias.

![Azure Policy förlängnings hovring visar resurs hanterarens egenskaps-alias](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Sök efter och Visa principer och tilldelningar

I Azure Policy-tillägget visas princip typer och princip tilldelningar som en TreeView för de prenumerationer som valts för att visas i fönstret **principer** . Kunder med hundratals eller tusentals principer eller tilldelningar i en enda prenumeration kan föredra ett sökbart sätt att hitta sina principer eller uppgifter. Med Azure Policy-tillägget kan du söka efter en speciell princip eller tilldelning med följande steg:

1. Starta Sök gränssnittet från Azure Policy-tillägget eller paletten Command.

   - Azure Policy tillägg

     Från Azure Policy tillägget, Hovra över panelen **principer** och välj ellipsen och välj sedan **Sök principer**.

   - Kommando palett:

     I meny raden går du till **visa** > **kommando palett**och anger **principer: Sök principer**.

1. Om fler än en prenumeration har valts för visning använder du filtret för att välja vilken prenumeration som ska genomsökas.

1. Använd filtret för att välja vilken princip typ eller tilldelning som ska genomsökas som är en del av den tidigare valda prenumerationen.

1. Använd filtret för att välja vilken princip eller Visa. Filtret fungerar för _DisplayName_ för princip definitionen eller princip tilldelningen.

När du väljer en princip eller tilldelning, oavsett om du väljer att använda Sök gränssnittet eller genom att markera det i trädvyn, öppnar Azure Policy-tillägget den JSON som representerar principen eller tilldelningen och alla dess egenskaps värden för Resource Manager. Tillägget kan verifiera det öppnade Azure Policy JSON-schemat.

## <a name="sign-out"></a>Logga ut

I meny raden går du till **visa** > **Command-paletten**och anger sedan **Azure: Logga ut**.

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig att [program mässigt skapa principer](programmatically-create.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).