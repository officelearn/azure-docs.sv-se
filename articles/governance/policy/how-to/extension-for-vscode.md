---
title: Azure Principtillägg för Visual Studio-kod
description: Lär dig hur du använder Azure Policy-tillägget för Visual Studio-kod för att slå upp Resurshanterarens alias.
ms.date: 03/07/2020
ms.topic: how-to
ms.openlocfilehash: 5e31af652f2746adbf0eda386bdb178c752f5f84
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641028"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Använda Azure-principtillägg för Visual Studio-kod

> Gäller för Azure Policy-tillägg version **0.0.21** och nyare

Lär dig hur du använder Azure Policy-tillägget för Visual Studio-kod för att slå upp [alias](../concepts/definition-structure.md#aliases) och granska resurser och principer. Först beskriver vi hur du installerar Azure Policy-tillägget i Visual Studio Code. Sen går vi igenom hur man slår upp alias.

Azure Policy-tillägg för Visual Studio-kod kan installeras på alla plattformar som stöds av Visual Studio Code. Det här stödet omfattar Windows, Linux och macOS.

> [!NOTE]
> Ändringar som görs lokalt i principer som visas i Azure Policy-tillägget för Visual Studio-kod synkroniseras inte med Azure.

## <a name="prerequisites"></a>Krav

Följande objekt krävs för att slutföra stegen i den här artikeln:

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
- [Visual Studio-kod](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Installera Azure-principtillägg

När du har uppfyllt förutsättningarna kan du installera Azure Policy-tillägg för Visual Studio-kod genom att följa dessa steg:

1. Öppna Visual Studio Code.

1. Gå till **Visa** > **tillägg**på menyraden .

1. Ange **Azure Policy**i sökrutan .

1. Välj **Azure-princip** i sökresultaten och välj sedan **Installera**.

1. Välj **Ladda om** vid behov.

## <a name="set-the-azure-environment"></a>Ange Azure-miljö

För en nationell molnanvändare följer du dessa steg för att ställa in Azure-miljön först:

1. Välj **Arkiv\Inställningar\Inställningar**.

1. Sök på följande sträng: _Azure: Cloud_

1. Välj nationmolnet i listan:

   ![Ange standardinformation från Azure-molnet för Visual Studio-kod](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Ansluta till ett Azure-konto

Om du vill utvärdera resurser och uppslagsalias måste du ansluta till ditt Azure-konto. Så här ansluter du till Azure från Visual Studio Code:

1. Logga in på Azure från Azure-principtillägget eller kommandopaletten.

   - Azure Principtillägg

     Välj **Logga in på Azure**.

     ![Azure-moln logga in för Visual Studio-kod från Azure Policy-tillägget](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Kommandopalett

     Gå till **Visa** > **kommandopalett**på menyraden och ange **Azure: Logga in**.

     ![Azure-moln logga in för Visual Studio-kod från kommandopalett](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Följ inloggningsinstruktionerna för att logga in på Azure. När du är ansluten visas ditt Azure-kontonamn i statusfältet längst ned i Visual Studio-kodfönstret.

## <a name="select-subscriptions"></a>Välj prenumerationer

När du loggar in första gången läses endast standardprenumerationsresurserna och standardprinciperna in av Azure Policy-tillägget. Så här lägger du till eller tar bort prenumerationer från att visa resurser och principer:

1. Starta prenumerationskommandot från kommandopaletten eller fönsterfoten.

   - Kommandopalett: 

     Gå till **Visa** > **kommandopalett**på menyraden och ange **Azure: Välj Prenumerationer**.

   - Fönsterfot

     I fönsterfoten längst ned på skärmen väljer du det segment som matchar **Azure: \<ditt konto\>**.

1. Använd filterrutan för att snabbt hitta prenumerationer efter namn. Kontrollera eller ta sedan bort checken från varje prenumeration för att ange prenumerationer som visas i Azure Policy-tillägget. När du har lagt till eller tagit bort prenumerationer som ska visas väljer du **OK**.

## <a name="search-for-and-view-resources"></a>Söka efter och visa resurser

Azure-principtillägget listar resurser i de valda prenumerationerna efter resursprovider och resursgrupp i **fönstret Resurser.** Trädvyn innehåller följande grupperingar av resurser inom den valda prenumerationen eller på prenumerationsnivå:

- **Resursprovidrar**
  - Varje registrerad resursprovider med resurser och relaterade underordnade resurser som har principalias
- **Resursgrupper**
  - Alla resurser efter resursgruppen de befinner sig i

Som standard filtrerar tillägget resursproviderdelen med befintliga resurser och resurser som har principalias. Ändra det här beteendet i**Azure-princip** **för inställningar** > **för** > att se alla resursleverantörer utan filtrering.

Kunder med hundratals eller tusentals resurser i en enda prenumeration kanske föredrar ett sökbart sätt att hitta sina resurser. Azure Policy-tillägget gör det möjligt att söka efter en viss resurs med följande steg:

1. Starta sökgränssnittet från Azure Policy-tillägget eller kommandopaletten.

   - Azure Principtillägg

     Från Azure Policy-tillägget hovrar du över **resurspanelen** och väljer ellipsen och väljer sedan **Sökresurser**.

   - Kommandopalett:

     Gå till **Visa** > **kommandopalett**på menyraden och ange **Resurser: Sökresurser**.

1. Om mer än en prenumeration är markerad för visning använder du filtret för att välja vilken prenumeration som ska sökas.

1. Använd filtret för att välja vilken resursgrupp som ska sökas efter som ingår i den tidigare valda prenumerationen.

1. Använd filtret för att välja vilken resurs som ska visas. Filtret fungerar för både resursnamnet och resurstypen.

## <a name="discover-aliases-for-resource-properties"></a>Identifiera alias för resursegenskaper

När en resurs har valts, antingen via sökgränssnittet eller genom att välja den i trädvyn, öppnar Azure Policy-tillägget JSON-filen som representerar resursen och alla dess Resource Manager-egenskapsvärden.

När en resurs är öppen visas Azure Policy-aliaset om det finns ett alias för Att hovra över egenskapsnamnet eller värdet i Resource Manager. I det här exemplet `Microsoft.Compute/virtualMachines` är resursen en resurstyp och egenskapen **properties.storageProfile.imageReference.offer** hovrar över. Hovring visar matchande alias.

![Azure Princip-tillägg hovrar visar egenskapsaliasal för Resurshanteraren](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Sök efter och visa principer och tilldelningar

Azure-principtillägget listar principtyper och principtilldelningar som en trädvy för de prenumerationer som valts som ska visas i fönstret **Principer.** Kunder med hundratals eller tusentals principer eller tilldelningar i en enda prenumeration kanske föredrar ett sökbart sätt att hitta sina principer eller tilldelningar. Azure Policy-tillägget gör det möjligt att söka efter en specifik princip eller tilldelning med följande steg:

1. Starta sökgränssnittet från Azure Policy-tillägget eller kommandopaletten.

   - Azure Principtillägg

     Från Azure Policy-tillägget hovrar du över **panelen Principer** och väljer ellipsen och väljer sedan **Sökprinciper**.

   - Kommandopalett:

     Gå till **Visa** > **kommandopalett**på menyraden och ange **principer: Sökregler**.

1. Om mer än en prenumeration är markerad för visning använder du filtret för att välja vilken prenumeration som ska sökas.

1. Använd filtret för att välja vilken principtyp eller tilldelning som ska sökas efter som ingår i den tidigare valda prenumerationen.

1. Använd filtret för att välja vilken princip eller vilken princip som ska visas. Filtret fungerar för _displayName_ för principdefinitionen eller principtilldelningen.

När du väljer en princip eller tilldelning, antingen via sökgränssnittet eller genom att välja den i trädvyn, öppnar Azure Policy-tillägget JSON som representerar principen eller tilldelningen och alla dess Resource Manager-egenskapsvärden. Tillägget kan validera det öppnade Azure Policy JSON-schemat.

## <a name="sign-out"></a>Logga ut

Gå till **Visa** > **kommandopalett**på menyraden och ange sedan **Azure: Logga ut**.

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Förstå hur du [programmässigt skapar principer](programmatically-create.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).