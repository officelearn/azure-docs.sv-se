---
title: Ordna dina resurser med Azure hanteringsgrupper | Microsoft Docs
description: Lär dig mer om av hanteringsgrupper och hur de används.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/28/2018
ms.author: rithorn
ms.openlocfilehash: 611faef7e4b94b1734896fb64ca29540b12bc057
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102715"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Ordna dina resurser med Azure hanteringsgrupper

Om din organisation har många prenumerationer, kanske du behöver ett sätt att effektivt hantera åtkomst, principer och efterlevnad för dessa prenumerationer. Azure hanteringsgrupper ger en nivå av omfång ovan prenumerationer. Du organisera prenumerationer i behållare som kallas ”hanteringsgrupper” och använda din styrning villkor för av hanteringsgrupper. Alla prenumerationer i en hanteringsgrupp samma automatiskt villkor som används för hanteringsgruppen. Av hanteringsgrupper ger företagsklass hantering på en stor skala oavsett vilken typ av prenumerationer som du kanske har.

Funktionen för hantering av grupp finns i en förhandsversion. Om du vill börja använda hanteringsgrupper, logga in på den [Azure-portalen](https://portal.azure.com) och Sök efter **Hanteringsgrupper** i den **alla tjänster** avsnitt.

Du kan till exempel använda principer till en hanteringsgrupp som begränsar regionerna som är tillgängliga för att skapa en virtuell dator (VM). Den här principen skulle tillämpas på alla hanteringsgrupper, prenumerationer och resurser under den hanteringsgruppen genom att bara tillåta virtuella datorer skapas i den regionen.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarkin för av hanteringsgrupper och prenumerationer

Du kan bygga en flexibel struktur för hanteringsgrupper och prenumerationer för att organisera dina resurser i en hierarki för enhetlig princip- och åtkomsthantering.
Följande diagram visar ett exempelhierarki som består av hanteringsgrupper och prenumerationer som är ordnad efter avdelningar.

![Trädet](media/management-groups/MG_overview.png)

Genom att skapa en hierarki som är grupperade efter avdelningar, kan du tilldela [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) roller som *ärver* till avdelningar under den hanteringsgruppen. Med hjälp av hanteringsgrupper kan minska din arbetsbelastning och minskar risken för fel genom att bara behöva tilldela rollen som en gång.

### <a name="important-facts-about-management-groups"></a>Viktiga fakta om hanteringsgrupper

- 10 000 hanteringsgrupper kan användas i en katalog.
- En grupp för utskriftshantering har stöd för upp till sex nivåer av djup.
  - Den här gränsen innehåller rotnivå eller prenumerationsnivån.
- Varje hanteringsgrupp och prenumeration stöder bara en överordnad.
- Varje hanteringsgrupp kan ha flera underordnade objekt.
- Alla prenumerationer och hanteringsgrupper ingår i en hierarki i varje katalog. Se [viktiga fakta om hanteringsgruppen rot](#important-facts-about-the-root-management-group) för undantag under förhandsgranskningen.

### <a name="preview-subscription-visibility-limitation"></a>Förhandsgranska prenumeration synlighet begränsning

Det finns en begränsning i förhandsgranskningen om du inte kunna visa prenumerationer som du har ärvd åtkomst till. Ärvs åtkomst till prenumerationen, men Azure Resource Manager inte kan ta hänsyn till arv åtkomst ännu.  

Med hjälp av REST API för att hämta information om prenumerationen returnerar information som du har åtkomst, men i Azure-portalen och Azure Powershell prenumerationerna visas inte.

Det här objektet utförs på och kommer att lösas innan grupper annonseras som ”allmän tillgänglighet”.  

### <a name="cloud-solution-provider-csp-limitation-during-preview"></a>Cloud Solution Providers (CSP)-begränsning under förhandsgranskning

Det finns en aktuell begränsning för Cloud Solution Providers (CSP) partner där de inte kan du skapa och hantera sina kunder hanteringsgrupper i sina kunder katalog.  
Det här objektet utförs på och kommer att lösas innan grupper annonseras som ”allmän tillgänglighet”.

## <a name="root-management-group-for-each-directory"></a>Rot-hanteringsgruppen för varje katalog

Varje katalog ges en översta hantering från en enda grupp med namnet ”rot” hanteringsgruppen. Denna management group rot är inbyggd i hierarkin har alla hanteringsgrupper och prenumerationer vika till den. Denna management group rot kan för globala principer och tilldelningar RBAC som ska användas på katalog-nivå. Den [Directory-administratör behöver höjer själva](../role-based-access-control/elevate-access-global-admin.md) att vara ägare till den här rotgruppen från början. När administratören ägare av gruppen kan tilldela de alla RBAC-roller till andra directory-användare eller grupper för att hantera i hierarkin.  

### <a name="important-facts-about-the-root-management-group"></a>Viktiga fakta om hanteringsgruppen rot

- Rot-hanteringsgruppen namn och ID anges som standard. Visningsnamnet kan uppdateras när som helst för att visa olika i Azure-portalen.
  - Namnet ska vara ”klient rot grupp”.
  - ID som kommer att Azure Active Directory-ID.
- Hanteringsgruppen rot kan inte flyttas eller tas bort, till skillnad från andra hanteringsgrupper.  
- Alla prenumerationer och hanteringsgrupper vika till en rot-hanteringsgrupp i katalogen.
  - Alla resurser i katalogen vika till rot-hanteringsgruppen för hantering av globala.
  - Nya prenumerationer får automatiskt som standard hanteringsgruppen rot när de skapas.
- Alla Azure-kunder kan se hanteringsgruppen rot, men alla kunder har inte tillgång till att hantera den rot-hanteringsgruppen.
  - Alla som har åtkomst till en prenumeration kan se kontext där den prenumerationen är i hierarkin.  
  - Ingen ges åtkomst till hanteringsgruppen rot. Directory globala administratörer är de bara användare som kan höja sig för att få åtkomst.  När de har åtkomst till kan katalogadministratörer tilldela alla RBAC-roller till andra användare att hantera.  

>[!NOTE]
>Om din katalog har startats med hjälp av grupper hanteringstjänsten innan 6/25/2018 kan katalogen inte konfigureras med alla prenumerationer i hierarkin. Gruppen ledningen uppdaterar retroaktivt varje katalog som igång med hanteringsgrupper i Public Preview innan det datumet i juli 2018. Alla prenumerationer i kataloger kommer att göras barn under roten hanteringsgruppen tidigare.  
>
>Om du har frågor om denna retroaktiv process kontakta: managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>Installationen av av hanteringsgrupper

Finns en inledande installationsprocessen som händer när en användare startar med hanteringsgrupper. Det första steget är att rot-hanteringsgruppen har skapats i katalogen. När den här gruppen har skapats görs underordnade rot-hanteringsgruppen med alla befintliga prenumerationer som finns i katalogen.  Orsaken till den här processen är att kontrollera att det finns en enda grupphierarkin i en katalog.  Samma hierarki i katalogen kan använda global åtkomst och principer som andra kunder i katalogen inte kan kringgå administrativa kunder. Något har tilldelats roten gäller över alla hanteringsgrupper, prenumerationer, resursgrupper och resurser i katalogen genom att ha en hierarki i katalogen.  

> [!IMPORTANT]
> Tilldelningar av åtkomst eller princip Användartilldelning i hanteringsgruppen rot **gäller för alla resurser i katalogen**. Därför bör alla kunder utvärdera behovet av objekt som definierats i detta scope.  Användaren åtkomst och principtilldelningar ska vara ”måste ha” endast i det här omfånget.  
  
## <a name="management-group-access"></a>Hantering av åtkomst

Stöd för Azure hanteringsgrupper [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) för alla resurs har åtkomst och rolldefinitioner. Dessa behörigheter ärvs till underordnade resurser som finns i hierarkin. Någon inbyggda RBAC-roll kan tilldelas till en hanteringsgrupp som ärver nedåt i hierarkin till resurserna.  Till exempel kan RBAC roll VM deltagare tilldelas till en hanteringsgrupp. Den här rollen har ingen åtgärd på hanteringsgruppen, men ärver till alla virtuella datorer under den hanteringsgruppen.  

Följande diagram visar en lista över roller och åtgärder som stöds på hanteringsgrupper.

| RBAC rollnamn             | Skapa | Byt namn | Flytta | Ta bort | Tilldela åtkomst | Tilldela princip | Läsa  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Ägare                       | X      | X      | X    | X      | X             |               | X     |
|Deltagare                 | X      | X      | X    | X      |               |               | X     |
|Läsare                      |        |        |      |        |               |               | X     |
|Deltagare för resursprincip |        |        |      |        |               | X             |       |
|Administratör för användaråtkomst   |        |        |      |        | X             |               |       |

### <a name="custom-rbac-role-definition-and-assignment"></a>Anpassade RBAC rolldefinition och tilldelning

Anpassade RBAC-roller stöds inte på hanteringsgrupper just nu.  Finns det [management group Feedbackforum](https://aka.ms/mgfeedback) att visa status för det här objektet.

## <a name="next-steps"></a>Nästa steg

Mer information om hanteringsgrupper finns:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](management-groups-create.md)
- [Ändra, ta bort eller hantera dina hanteringsgrupper](management-groups-manage.md)
- [Installera Azure PowerShell-modulen](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Granska REST API-specifikationen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Installera tillägg för Azure CLI](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
