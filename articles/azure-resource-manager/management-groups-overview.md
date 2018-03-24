---
title: Ordna dina resurser med Azure-Hanteringsgrupper | Microsoft Docs
description: Lär dig mer om av hanteringsgrupper och hur de används.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2018
ms.author: rithorn
ms.openlocfilehash: db472345bacda916f1b1664ed7803978ab235a2a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Ordna dina resurser med Azure-Hanteringsgrupper 

Om din organisation har många prenumerationer, kanske du behöver ett sätt att effektivt hantera åtkomst, principer och efterlevnad för dessa prenumerationer. Azure Hanteringsgrupper ger en nivå av omfång ovan prenumerationer. Du organisera prenumerationer i behållare som kallas ”hanteringsgrupper” och använda din styrning villkor för av hanteringsgrupper. Alla prenumerationer i en hanteringsgrupp samma automatiskt villkor som används för hanteringsgruppen. Av hanteringsgrupper ger företagsklass hantering på en stor skala oavsett vilken typ av prenumerationer som du kanske har.

Funktionen för hantering av grupp finns i en förhandsversion. Börja använda hantering av grupper, logga in på den [Azure-portalen](https://portal.azure.com) och Sök efter **Hanteringsgrupper** i den **alla tjänster** avsnitt. 

Exempelvis kan du tillämpa principer till en hanteringsgrupp som begränsar regionerna som är tillgängliga för att skapa en virtuell dator (VM). Den här principen skulle tillämpas på alla hanteringsgrupper, prenumerationer och resurser under den hanteringsgruppen genom att bara tillåta virtuella datorer skapas i den regionen.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarkin för av hanteringsgrupper och prenumerationer 

Du kan bygga en flexibel struktur för hanteringsgrupper och prenumerationer för att organisera dina resurser i en hierarki för enhetlig princip- och åtkomsthantering. Följande diagram visar ett exempelhierarki som består av hanteringsgrupper och prenumerationer som är ordnad efter avdelningar.    

![trädet](media/management-groups/MG_overview.png)

Genom att skapa en hierarki som är grupperade efter avdelningar, du kan tilldela [rollbaserad åtkomstkontroll (RBAC)](../active-directory/role-based-access-control-what-is.md) roller som *ärver* till avdelningar under den hanteringsgruppen. Med hjälp av hanteringsgrupper kan minska din arbetsbelastning och minskar risken för fel genom att bara behöva tilldela rollen som en gång. 

### <a name="important-facts-about-management-groups"></a>Viktiga fakta om hanteringsgrupper
- 10 000 hanteringsgrupper kan användas i en katalog. 
- En grupp för utskriftshantering har stöd för upp till sex nivåer av djup.
    - Den här gränsen innehåller inte rotnivå eller prenumerationsnivån.
- Varje hanteringsgrupp stöder bara en överordnad.
- Varje hanteringsgrupp kan ha flera underordnade objekt. 

### <a name="preview-subscription-visibility-limitation"></a>Förhandsgranska prenumeration synlighet begränsning 
Det finns en begränsning i förhandsgranskningen där du inte kan visa prenumerationer som du har ärvd åtkomst till. Ärvs åtkomst till prenumerationen, men Azure Resource Manager kan inte ta hänsyn till arv åtkomst ännu.  

Med hjälp av REST API för att hämta information om prenumerationen returnerar information som du har åtkomst, men i Azure-portalen och Azure Powershell prenumerationerna visas inte. 

Det här objektet utförs på och kommer att lösas innan Hanteringsgrupper meddelade som ”allmän tillgänglighet”.  


## <a name="root-management-group-for-each-directory"></a>Rot-hanteringsgruppen för varje katalog

Varje katalog ges en översta hantering från en enda grupp med namnet ”rot” hanteringsgruppen. Denna management group rot är inbyggd i hierarkin har alla hanteringsgrupper och prenumerationer vika till den. Denna management group rot kan för globala principer och tilldelningar RBAC som ska användas på katalog-nivå. Den [Directory-administratör behöver höjer själva](../active-directory/role-based-access-control-tenant-admin-access.md) att vara ägare till den här rotgruppen från början. När administratören ägare av gruppen kan tilldela de alla RBAC-roller till andra directory-användare eller grupper för att hantera i hierarkin.  

### <a name="important-facts-about-the-root-management-group"></a>Viktiga fakta om hanteringsgruppen rot
- Rot-hanteringsgruppen namn och ID anges i Azure Active Directory-ID som standard. Visningsnamnet kan uppdateras när som helst för att visa olika i Azure-portalen. 
- Alla prenumerationer och hanteringsgrupper vika kan en rot-hanteringsgruppen i katalogen.  
    - Vi rekommenderar att alla objekt i katalogen vikning upp till hanteringsgruppen rot för hantering av globala.  
    - Den offentliga förhandsversionen görs alla prenumerationer i katalogen automatiskt inte underordnade objekt för roten.   
    - Den offentliga förhandsversionen nya prenumerationer får inte automatiskt som standard hanteringsgruppen rot. 
- Hanteringsgruppen rot kan inte flyttas eller tas bort, till skillnad från andra hanteringsgrupper. 
  
## <a name="management-group-access"></a>Åtkomst till Hanteringsgruppen

Har stöd för Azure Hanteringsgrupper [rollbaserad åtkomstkontroll (RBAC)](../active-directory/role-based-access-control-what-is.md) för alla resurs har åtkomst och rolldefinitioner. Dessa behörigheter ärvs till underordnade resurser som finns i hierarkin.   

När någon [inbyggda RBAC rollen](../active-directory/role-based-access-control-what-is.md#built-in-roles) kan tilldelas till en hanteringsgrupp, det finns fyra roller som är vanliga: 
- **Ägare** har fullständig åtkomst till alla resurser som bland annat att delegera åtkomst till andra. 
- **Deltagare** kan skapa och hantera alla typer av Azure-resurser, men det går inte att bevilja åtkomst till andra.
- **Resursen princip deltagare** kan skapa och hantera principer i katalogen på resurser.     
- **Läsaren** kan visa befintliga Azure-resurser. 


## <a name="next-steps"></a>Nästa steg 
Mer information om hanteringsgrupper finns: 
- [Skapa hanteringsgrupper för att organisera Azure-resurser](management-groups-create.md)
- [Ändra, ta bort eller hantera dina hanteringsgrupper](management-groups-manage.md)
- [Installera Azure PowerShell-modulen](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Granska REST API-specifikationen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Installera tillägg för Azure CLI](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

