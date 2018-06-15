---
title: Hantera Azure-resurser med Cloud Explorer | Microsoft Docs
description: Lär dig hur du använder Cloud Explorer för att bläddra och hantera Azure-resurser i Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/25/2017
ms.author: ghogen
ms.openlocfilehash: 9d8ae465e80ab38c1d7b0f6082865c110c0ff06b
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31794080"
---
# <a name="manage-the-resources-associated-with-your-azure-accounts-in-visual-studio-cloud-explorer"></a>Hantera resurser som är associerade med din Azure-konton i Visual Studio Cloud Explorer
Cloud Explorer kan du visa dina Azure-resurser och resursgrupper, granska deras egenskaper och utföra åtgärder för viktiga developer diagnostik från Visual Studio. 

Exempel på [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), Cloud Explorer bygger på Azure Resource Manager-stacken. Därför Cloud Explorer förstår resurser, t.ex Azure-resursgrupper och Azure-tjänster, till exempel Logic apps och API apps och stöder [rollbaserad åtkomstkontroll](role-based-access-control/role-assignments-portal.md) (RBAC). 

## <a name="prerequisites"></a>Förutsättningar
- [Visual Studio-2017](https://www.visualstudio.com/downloads/) med den **arbetsbelastning i Azure** valt, eller en tidigare version av Visual Studio med den [Microsoft Azure SDK för .NET 2.9](https://www.microsoft.com/en-us/download/details.aspx?id=51657).
- Microsoft Azure-konto - om du inte har ett konto kan du [registrera dig för en kostnadsfri utvärderingsversion](http://go.microsoft.com/fwlink/?LinkId=623901) eller [aktivera Visual Studio-prenumerantförmåner](http://go.microsoft.com/fwlink/?LinkId=623901).

> [!NOTE]
> Om du vill visa Cloud Explorer **visa** > **Cloud Explorer** på menyraden.   
> 
> 

## <a name="add-an-azure-account-to-cloud-explorer"></a>Lägg till en Azure-konto för att Cloud Explorer
Om du vill visa de resurser som är associerade med ett Azure-konto, måste du först lägga till kontot Cloud Explorer. 

1. I **Cloud Explorer**väljer **Azure kontoinställningar**.

    ![Ikonen för inställningar av cloud Explorer Azure-konto](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Välj **Lägg till nytt konto**. 

    ![Cloud Explorer Lägg till konto länk](media/vs-azure-tools-resources-managing-with-cloud-explorer/add-account-link.png)

1. Logga in på Azure-konto vars resurser du vill bläddra. 

1. När inloggad på ett Azure-konto, visa prenumerationer som är associerade med det kontot. Markera kryssrutorna för konto-prenumerationer som du vill bläddra och välj sedan **tillämpa**. 
 
    ![Cloud Explorer: Välj Azure-prenumerationer att visa](media/vs-azure-tools-resources-managing-with-cloud-explorer/select-subscriptions.png)

1. När du har valt prenumerationer vars resurser du vill bläddra, visas de prenumerationer och resurser i Cloud Explorer.

    ![Cloud Explorer resurs lista för ett Azure-konto](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-listed.png)

## <a name="remove-an-azure-account-from-cloud-explorer"></a>Ta bort ett Azure-konto från Cloud Explorer 

1. I **Cloud Explorer**väljer **Azure kontoinställningar**.

    ![Ikonen för inställningar av cloud Explorer Azure-konto](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Bredvid det konto som du vill ta bort, Välj **ta bort**.

    ![Ikonen för inställningar av cloud Explorer Azure-konto](media/vs-azure-tools-resources-managing-with-cloud-explorer/remove-account.png)

## <a name="view-resource-types-or-resource-groups"></a>Visa resurstyper eller resursgrupper
Om du vill visa dina Azure-resurser, kan du antingen **resurstyper** eller **resursgrupper** vyn.

1. I **Cloud Explorer**, Välj den nedrullningsbara listrutan Visa resursen.

    ![Cloud Explorer listrutan och Välj vyn önskade resurser](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-view-dropdown.png)

1. Välj önskad vy från snabbmenyn: 

    - **Resurstyper** view - vanliga vyn används på den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), visar resurserna i Azure kategoriserade efter typ, till exempel webbappar, lagringskonton och virtuella datorer. 
    - **Resursgrupper** view - kategoriserar Azure-resurser med Azure-resursgrupp som de är kopplade. En resursgrupp är en samling Azure-resurser, används vanligtvis av ett visst program. Läs mer om Azure-resursgrupper i [översikt över Azure Resource Manager](./azure-resource-manager/resource-group-overview.md).

    Följande bild visar en jämförelse av de två vyerna:

    ![Cloud Explorer resurs vyer jämförelse](media/vs-azure-tools-resources-managing-with-cloud-explorer/resource-views-comparison.png)

## <a name="view-and-navigate-resources-in-cloud-explorer"></a>Visa och navigera resurser i Cloud Explorer
Om du vill navigera till en Azure-resurs och visa informationen i Cloud Explorer, expandera objektets typ eller associerade resursgrupp och välj sedan resursen. När du väljer en resurs kan informationen som visas i två flikar - **åtgärder** och **egenskaper** - längst ned i Cloud Explorer. 

- **Åtgärder** fliken – visar en lista över de åtgärder du kan vidta i Cloud Explorer för den markerade resursen. Du kan också visa dessa alternativ genom att högerklicka på resursen om du vill visa dess snabbmenyn.

- **Egenskaper för** fliken - visar egenskaper för resurs, till exempel dess typ, språk och resurs-grupp som den är associerad med.

Följande bild visar ett exempel jämförelse av vad som visas på varje flik för en Apptjänst:

![](./media/vs-azure-tools-resources-managing-with-cloud-explorer/actions-and-properties.png)

Varje resurs har åtgärden **öppna i portalen**. När du väljer den här åtgärden Cloud Explorer visar den valda resursen i den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040). Den **öppna i portalen** funktionen är praktiskt för att navigera till djupt kapslade resurser.

Ytterligare åtgärder och egenskapsvärden kan också visas baserat på Azure-resurs. Till exempel web apps och logikappar också har åtgärderna som **öppna i webbläsare** och **koppla felsökare** förutom **öppna i portalen**. Åtgärder för att öppna redigerare visas när du väljer ett konto lagringsblob, kö eller tabell. Azure appar har **URL** och **Status** egenskaper, medan lagringsresurser har nyckeln och anslutningen egenskaperna för anslutningssträngen.

## <a name="find-resources-in-cloud-explorer"></a>Söka efter resurser i Cloud Explorer
Om du vill hitta resurser med ett visst namn i Azure-konto-prenumerationer, ange namnet i den **Sök** rutan i Cloud Explorer.

![Söka efter resurser i Cloud Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/search-for-resources.png)

När du skriver in tecken i den **Sök** , resurser som matchar de tecken som ska visas i trädet för resurser.
