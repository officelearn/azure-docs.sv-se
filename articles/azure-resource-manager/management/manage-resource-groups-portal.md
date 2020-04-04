---
title: Hantera resursgrupper - Azure-portal
description: Använd Azure-portalen för att hantera dina resursgrupper via Azure Resource Manager. Visar hur du skapar, listar och tar bort resursgrupper.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 622c0cb8d58dddcdb13c7dc9412ada3f2e2d93e4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632977"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Hantera Azure Resource Manager-resursgrupper med hjälp av Azure-portalen

Lär dig hur du använder [Azure-portalen](https://portal.azure.com) med [Azure Resource Manager](overview.md) för att hantera dina Azure-resursgrupper. Information om hur du hanterar Azure-resurser finns i [Hantera Azure-resurser med hjälp av Azure-portalen](manage-resources-portal.md).

Andra artiklar om hantering av resursgrupper:

- [Hantera Azure-resursgrupper med hjälp av Azure CLI](manage-resources-cli.md)
- [Hantera Azure-resursgrupper med hjälp av Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Vad är en resursgrupp

En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. Lägg i allmänhet till resurser som delar samma livscykel i samma resursgrupp så att du enkelt kan distribuera, uppdatera och ta bort dem som en grupp.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att din data lagras inom en viss region.


## <a name="create-resource-groups"></a>Skapa resursgrupper

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **resursgrupper**

    ![lägga till resursgrupp](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Välj **Lägg till**.
4. Ange följande värden:

   - **Prenumeration**: Välj din Azure-prenumeration. 
   - **Resursgrupp**: Ange ett nytt resursgruppnamn. 
   - **Region**: Välj en Azure-plats, till exempel **Central US**.

     ![skapa resursgrupp](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Välj **recension + skapa**
6. Välj **Skapa**. Det tar några sekunder att skapa en resursgrupp.
7. Välj **Uppdatera** på den övre menyn om du vill uppdatera resursgruppslistan och välj sedan den nyskapade resursgruppen för att öppna den. Eller välj **Meddelande**(klockikonen) högst upp och välj sedan **Gå till resursgrupp** för att öppna den nyskapade resursgruppen

    ![gå till resursgrupp](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Lista resursgrupper

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Om du vill visa resursgrupper väljer du **Resursgrupper**

    ![bläddra i resursgrupper](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Om du vill anpassa informationen som visas för resursgrupperna väljer du **Redigera kolumner**. Följande skärmbild visar de tilläggskolumner som du kan lägga till i displayen:

## <a name="open-resource-groups"></a>Öppna resursgrupper

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Resursgrupper**.
3. Markera den resursgrupp som du vill öppna.

## <a name="delete-resource-groups"></a>Ta bort resursgrupper

1. Öppna den resursgrupp som du vill ta bort.  Se [Öppna resursgrupper](#open-resource-groups).
2. Välj **Ta bort resursgrupp**.

    ![ta bort azure-resursgrupp](./media/manage-resource-groups-portal/delete-group.png)

Mer information om hur Azure Resource Manager beställer borttagning av resurser finns i [Azure Resource Manager-resursgruppborttagning](delete-resource-group.md).

## <a name="deploy-resources-to-a-resource-group"></a>Distribuera resurser till en resursgrupp

När du har skapat en Resource Manager-mall kan du använda Azure-portalen för att distribuera dina Azure-resurser. Information om hur du skapar en mall finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](../templates/quickstart-create-templates-use-the-portal.md). Information om hur du distribuerar en mall med hjälp av portalen finns i [Distribuera resurser med Resource Manager-mallar och Azure-portal](../templates/deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Flytta till en annan resursgrupp eller prenumeration

Du kan flytta resurserna i gruppen till en annan resursgrupp. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Låsa resursgrupper

Låsning hindrar andra användare i organisationen från att oavsiktligt ta bort eller ändra kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

1. Öppna den resursgrupp som du vill ta bort.  Se [Öppna resursgrupper](#open-resource-groups).
2. Välj **Lås**i den vänstra rutan .
3. Om du vill lägga till ett lås i resursgruppen väljer du **Lägg till**.
4. Ange **Låsnamn,** **Låstyp**och **Anteckningar**. Låstyperna inkluderar **Skrivskyddad**och **Ta bort**.

    ![låsa azure-resursgrupp](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Mer information finns i [Låsa resurser för att förhindra oväntade ändringar](lock-resources.md).

## <a name="tag-resource-groups"></a>Tagga resursgrupper

Du kan använda taggar på resursgrupper och resurser för att logiskt ordna dina resurser. Information finns i [Använda taggar för att ordna dina Azure-resurser](tag-resources.md#portal).

## <a name="export-resource-groups-to-templates"></a>Exportera resursgrupper till mallar

Information om hur du exporterar mallar finns i [Enkel export och export med flera resurser till mallen - Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Hantera åtkomst till resursgrupper

[Rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md) är det sätt som du hanterar åtkomst till resurser i Azure. Mer information finns i [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

- Information om Azure Resource Manager finns i [Översikt över Azure Resource Manager](overview.md).
- Information om hur du lär dig mallsyntaxen för Resurshanteraren finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../templates/template-syntax.md).
- Mer information om hur du utvecklar mallar finns i [steg-för-steg-självstudier](/azure/azure-resource-manager/).
- Information om hur du visar mallscheman för Azure Resource Manager finns i [mallreferens](/azure/templates/).
