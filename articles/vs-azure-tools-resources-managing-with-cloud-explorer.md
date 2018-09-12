---
title: Hantera Azure-resurser med Cloud Explorer | Microsoft Docs
description: Lär dig hur du använder Cloud Explorer för att söka efter och hantera Azure-resurser i Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/25/2017
ms.author: ghogen
ms.openlocfilehash: 596db56a71d4cc73517b9a9cbfe9b97e536aa83a
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378854"
---
# <a name="manage-the-resources-associated-with-your-azure-accounts-in-visual-studio-cloud-explorer"></a>Hantera resurser som är associerade med dina Azure-konton i Visual Studio Cloud Explorer
Cloud Explorer kan du visa dina Azure-resurser och resursgrupper, granska deras egenskaper och utföra åtgärder för viktiga developer diagnostik från Visual Studio. 

Som den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), Cloud Explorer bygger på Azure Resource Manager-stacken. Därför Cloud Explorer förstår resurser, till exempel Azure-resursgrupper och Azure-tjänster som Logic apps och API apps och stöder [rollbaserad åtkomstkontroll](role-based-access-control/role-assignments-portal.md) (RBAC). 

## <a name="prerequisites"></a>Förutsättningar
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) med den **arbetsbelastning för Azures** valt, eller en tidigare version av Visual Studio med den [Microsoft Azure SDK för .NET 2.9](https://www.microsoft.com/en-us/download/details.aspx?id=51657).
- Microsoft Azure-konto – om du inte har ett konto, kan du [registrera dig för en kostnadsfri utvärderingsversion](http://go.microsoft.com/fwlink/?LinkId=623901) eller [aktivera Visual Studio-prenumerantförmåner](http://go.microsoft.com/fwlink/?LinkId=623901).

> [!NOTE]
> Om du vill visa Cloud Explorer, Välj **visa** > **Cloud Explorer** på menyraden.   
> 
> 

## <a name="add-an-azure-account-to-cloud-explorer"></a>Lägga till en Azure-konto till Cloud Explorer
Om du vill visa de resurser som är associerade med ett Azure-konto, måste du först lägga till kontot Cloud Explorer. 

1. I **Cloud Explorer**väljer **Azure kontoinställningar**.

    ![Ikon för inställningar av cloud Explorer Azure-konto](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Välj **hantera konton**. 

    ![Cloud Explorer Lägg till konto länk](media/vs-azure-tools-resources-managing-with-cloud-explorer/manage-accounts-link.png)

1. Logga in på Azure-konto vars resurser som du vill bläddra. 

1. När du loggat in på en Azure-konto kan visa de prenumerationer som är kopplade till kontot. Markera kryssrutorna för konto-prenumerationer som du vill bläddra och välj sedan **tillämpa**. 
 
    ![Cloud Explorer: Välj Azure-prenumerationer att visa](media/vs-azure-tools-resources-managing-with-cloud-explorer/select-subscriptions.png)

1. När du har valt prenumerationer vars resurser som du vill bläddra, visa dessa prenumerationerna och resurserna i Cloud Explorer.

    ![Cloud Explorer resurs för ett Azure-konto](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-listed.png)

## <a name="remove-an-azure-account-from-cloud-explorer"></a>Ta bort ett Azure-konto från Cloud Explorer 

1. I **Cloud Explorer**väljer **kontohantering**.

    ![Ikon för inställningar av cloud Explorer Azure-konto](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Markera bredvid det konto som du vill ta bort **hantera konton**.

    ![Ikon för inställningar av cloud Explorer Azure-konto](media/vs-azure-tools-resources-managing-with-cloud-explorer/remove-account.png)

1. Välj **ta bort** att ta bort ett konto.

    ![Cloud Explorer hantera konton dialogrutan](media/vs-azure-tools-resources-managing-with-cloud-explorer/accountmanage.PNG)

## <a name="view-resource-types-or-resource-groups"></a>Visa typer av resurser eller resursgrupper
Om du vill visa dina Azure-resurser, kan du välja något **resurstyper** eller **resursgrupper** vy.

1. I **Cloud Explorer**, Välj i listrutan för resurs-vy.

    ![Cloud Explorer listrutan att välja vyn önskade resurser](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-view-dropdown.png)

1. Välj önskad vy från snabbmenyn: 

    - **Resurstyper** view - vyn vanliga används på den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), visar Azure-resurser efter typ, t.ex web apps, lagringskonton och virtuella datorer. 
    - **Resursgrupper** view - kategoriserar Azure-resurser med Azure-resursgruppen som de är associerade. En resursgrupp är ett paket med Azure-resurser, vanligtvis används av ett visst program. Läs mer om Azure-resursgrupper i [översikt över Azure Resource Manager](./azure-resource-manager/resource-group-overview.md).

    Följande bild visar en jämförelse av de två vyerna:

    ![Cloud Explorer resource vyer jämförelse](media/vs-azure-tools-resources-managing-with-cloud-explorer/resource-views-comparison.png)

## <a name="view-and-navigate-resources-in-cloud-explorer"></a>Visa och navigera till resurserna i Cloud Explorer
För att navigera till en Azure-resurs och visa dess information i Cloud Explorer, expandera objektets typ eller associerade resursgruppen och välj sedan resursen. När du väljer en resurs kan informationen visas i två flikar: **åtgärder** och **egenskaper** – längst ned i Cloud Explorer. 

- **Åtgärder** fliken – visar en lista över de åtgärder du kan vidta i Cloud Explorer för den valda resursen. Du kan också visa dessa alternativ genom att högerklicka på resursen om du vill visa dess snabbmenyn.

- **Egenskaper för** fliken – visar egenskaperna för resursen, till exempel dess typ, språk och resurs-grupp som den är associerad.

Följande bild visar ett exempel jämförelse av vad som visas på varje flik för en App Service:

![](./media/vs-azure-tools-resources-managing-with-cloud-explorer/actions-and-properties.png)

Alla resurser har åtgärden **öppna i portalen**. När du väljer den här åtgärden Cloud Explorer visar den valda resursen i den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040). Den **öppna i portalen** funktionen är praktiskt för att navigera till djupt kapslade resurser.

Ytterligare åtgärder och egenskapsvärden kan också visas baserat på Azure-resursen. Till exempel web apps och logic apps också ha åtgärderna **öppna i webbläsare** och **koppla felsökare** förutom **öppna i portalen**. Åtgärder för att öppna redigerare visas när du väljer en logglagringskontots blob, kö eller tabell. Azure-appar har **URL** och **Status** egenskaper, medan lagringsresurser har strängegenskaper nyckel och anslutning.

## <a name="find-resources-in-cloud-explorer"></a>Hitta resurser i Cloud Explorer
Om du vill hitta resurser med ett visst namn i dina prenumerationer i Azure-konto, anger du namn i den **Search** i Cloud Explorer.

![Söka efter resurser i Cloud Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/search-for-resources.png)

När du anger tecken i den **Search** rutan endast de resurser som matchar de tecken som visas i trädet för resurser.
