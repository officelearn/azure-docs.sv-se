---
title: Organisera dina resurser med Azure-hanteringsgrupper | Microsoft Docs
description: Läs om hanteringsgrupperna och hur de används.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/26/2018
ms.author: rithorn
ms.openlocfilehash: f9554c058fbebc215aa61979fa03280553597afc
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308324"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organisera dina resurser med Azure-hanteringsgrupper

Om din organisation har många prenumerationer kan det behövas ett effektivt sätt att hantera åtkomst, principer och efterlevnad för prenumerationerna. Azure-hanteringsgrupper ger en nivå av omfång över prenumerationer. Du organiserar prenumerationer i behållare som kallas ”hanteringsgrupper” och använda dina styrning villkor på hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de villkor som gäller för hanteringsgruppen. Av hanteringsgrupper ger företagsklass i stor skala oavsett vilken typ av prenumerationer som du kan ha.

Hantering av grupp-funktionen är tillgänglig i en offentlig förhandsversion. Om du vill börja använda hanteringsgrupper, logga in på den [Azure-portalen](https://portal.azure.com) och Sök efter **Hanteringsgrupper** i den **alla tjänster** avsnittet.

Du kan till exempel tillämpa principer på en hanteringsgrupp som begränsar regionerna som är tillgängliga för att skapa en virtuell dator (VM). Den här principen ska tillämpas på alla hanteringsgrupper, prenumerationer och resurser under den hanteringsgruppen genom att bara tillåta virtuella datorer som ska skapas i den regionen.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarki för hanteringsgrupper och -prenumerationer

Du kan skapa en flexibel struktur för hanteringsgrupper och -prenumerationer för att organisera dina resurser i en hierarki för enhetlig princip- och åtkomsthantering.
Följande diagram visar en exempelhierarki som består av hanteringsgrupper och prenumerationer som är ordnade efter avdelningar.

![trädet](media/management-groups/MG_overview.png)

Genom att skapa en hierarki som är grupperade efter avdelningar, kan du tilldela [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) roller som *ärver* till avdelningar under den hanteringsgruppen. Genom att använda hanteringsgrupper kan du minska din arbetsbelastning och minskar risken för fel genom att bara behöva tilldela rollen som en gång.

### <a name="important-facts-about-management-groups"></a>Viktiga fakta om hanteringsgrupper

- 10 000 hanteringsgrupper kan användas i en enskild katalog.
- En grupp Utskriftshantering har stöd för upp till sex nivåer för djup.
  - Den här gränsen omfattar inte rotnivå eller prenumerationsnivå.
- Varje hanteringsgrupp och en prenumeration har endast stöd för en överordnad.
- Varje hanteringsgrupp kan ha flera underordnade objekt.
- Alla prenumerationer och hanteringsgrupper ingår i en hierarki i varje katalog. Se [viktiga fakta om hanteringsgruppen rot](#important-facts-about-the-root-management-group) för undantag i förhandsversionen.

### <a name="cloud-solution-provider-csp-limitation-during-preview"></a>Cloud Solution Provider (CSP)-begränsning i förhandsversionen

Det finns en aktuell begränsning för Cloud Solution Provider (CSP)-partner där de inte kan du skapa och hantera sina kunders hanteringsgrupper i sina kunders directory.  
Det här objektet arbetades på och kommer att lösas innan grupper har meddelats vara ”Allmänt”.

## <a name="root-management-group-for-each-directory"></a>Rot-hanteringsgruppen för varje katalog

Varje katalog får en enskild översta hanteringsgrupp som kallas ”rot”-hanteringsgruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Den här rot-hanteringsgruppen kan globala principer och RBAC-uppgifter som ska tillämpas på nivån för katalogen. Den [Directory-administratören måste att upphöja själva](../role-based-access-control/elevate-access-global-admin.md) initialt rot gruppens ägare. När administratören är ägare av gruppen kan tilldela de alla RBAC-roller till andra directory-användare eller grupper för att hantera i hierarkin.  

### <a name="important-facts-about-the-root-management-group"></a>Viktiga fakta om hanteringsgruppen rot

- Rot-hanteringsgruppens namn och ID anges som standard. Visningsnamnet kan uppdateras när som helst för att visa olika i Azure-portalen.
  - Namnet ska vara ”klient rot grupp”.
  - ID: T blir Azure Active Directory-ID.
- Rot-hanteringsgruppen kan inte flyttas eller tas bort, till skillnad från andra hanteringsgrupper.  
- Alla prenumerationer och hanteringsgrupper som vikbar till en rot-hanteringsgrupp i katalogen.
  - Alla resurser i katalogen som vikbar till rot-hanteringsgruppen för global hantering.
  - Nya prenumerationer får automatiskt som standard till rot-hanteringsgruppen när du skapade.
- Alla Azure-kunder kan se rot-hanteringsgrupp, men inte alla kunder har åtkomst till att hantera den rot-hanteringsgruppen.
  - Alla som har åtkomst till en prenumeration kan se filens kontext där den aktuella prenumerationen är i hierarkin.  
  - Ingen får åtkomst till rot-hanteringsgruppen. Directorys globala administratörer är de bara användare som kan utöka själva för att få åtkomst.  När de har åtkomst till kan directory-administratörer tilldela alla RBAC-roller till andra användare att hantera.  

>[!NOTE]
>Om din katalog har startats med hjälp av hanteringstjänsten för grupper innan du 6/25/2018 kan din katalog inte konfigureras med alla prenumerationer i hierarkin. Gruppen ledningen uppdaterar retroaktivt alla kataloger som startats med hjälp av hanteringsgrupper i Public Preview före det datumet i juli/augusti 2018. Alla prenumerationer i kataloger kommer att göras barn under roten hanteringsgruppen tidigare.  
>
>Om du har frågor om den här retroaktiv processen kan du kontakta: managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>Den initiala konfigurationen av hanteringsgrupper

När en användare startar med hjälp av hanteringsgrupper, finns det en installationen process som sker. Det första steget är roten hanteringsgruppen skapas i katalogen. När den här gruppen har skapats görs underordnade rot-hanteringsgruppens med alla befintliga prenumerationer som finns i katalogen.  Orsaken till den här processen är att kontrollera att det finns endast en grupphierarki för hantering i en katalog.  Hierarki i katalogen kan administrativa kunderna kan använda global åtkomst och principer som andra kunder i katalogen inte kan kringgå. Något tilldelade rot gäller i alla hanteringsgrupper, prenumerationer, resursgrupper och resurser i katalogen genom att ha en hierarki i katalogen.  

> [!IMPORTANT]
> Alla tilldelningen av åtkomst eller princip Användartilldelning på rot-hanteringsgruppen **gäller för alla resurser i katalogen**. Därför bör alla kunder utvärdera behovet av objekten som definierats för det här området.  Användaren åtkomst och principtilldelningar ska vara ”måste ha” endast i den här omfattningen.  
  
## <a name="management-group-access"></a>Åtkomst till hanteringsgruppen

Azure-hanteringsgrupper stöd [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) för alla åtkomst till resursen och rolldefinitioner. Dessa behörigheter ärvs till underordnade resurser som finns i hierarkin. Alla inbyggda RBAC-rollen kan tilldelas till en hanteringsgrupp som ärver nedåt i hierarkin till resurserna.  VM-deltagare RBAC-rollen kan till exempel tilldelas till en hanteringsgrupp. Den här rollen har ingen åtgärd på hanteringsgruppen, men ärver för alla virtuella datorer under den hanteringsgruppen.  

Följande diagram visar en lista över roller och åtgärder som stöds på hanteringsgrupper.

| RBAC rollnamn             | Skapa | Byt namn | Flytta | Ta bort | Tilldela åtkomst | Tilldela princip | Läsa  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Ägare                       | X      | X      | X    | X      | X             |               | X     |
|Deltagare                 | X      | X      | X    | X      |               |               | X     |
|MG deltagare *             | X      | X      | X    | X      |               |               | X     |
|Läsare                      |        |        |      |        |               |               | X     |
|MG läsare *                  |        |        |      |        |               |               | X     |
|Deltagare för resursprincip |        |        |      |        |               | X             |       |
|Administratör för användaråtkomst   |        |        |      |        | X             |               |       |

*: MG-deltagare och läsare MG endast tillåta användare att utföra dessa åtgärder på management-Gruppomfång.  

### <a name="custom-rbac-role-definition-and-assignment"></a>Anpassad RBAC-rolldefinition och tilldelning

Anpassad RBAC-roller stöds inte på hanteringsgrupper just nu.  Se den [management group feedback-forum](https://aka.ms/mgfeedback) att visa status för det här objektet.

## <a name="next-steps"></a>Nästa steg

Mer information om hanteringsgrupper finns:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](management-groups-create.md)
- [Ändra, ta bort eller hantera dina hanteringsgrupper](management-groups-manage.md)
- [Installera Azure PowerShell-modulen](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Granska REST API-specifikationen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Installera Azure CLI-tillägg](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
