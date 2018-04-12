---
title: Skapa anpassade rollbaserad åtkomst kontroll roller och tilldela interna och externa användare i Azure | Microsoft Docs
description: Tilldela anpassade RBAC-roller med hjälp av PowerShell och CLI för interna och externa användare
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: kgremban
ms.assetid: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: b60b30e3a5a4f5adec4fbef8c4e981ad034a7f6c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2018
---
# <a name="intro-on-role-based-access-control"></a>Introduktion på rollbaserad åtkomstkontroll

Rollbaserad åtkomstkontroll är en Azure portal endast funktionen som tillåter ägare av en prenumeration att tilldela detaljerade roller till andra användare som kan hantera en viss resurs-scope i sina miljöer.

RBAC kan bättre säkerhetshantering för stora organisationer och för små och medelstora företag arbetar med externa samarbetspartners, leverantörer eller freelancers som behöver åtkomst till specifika resurser i din miljö, men inte nödvändigtvis att hela infrastrukturen eller någon fakturerings-relaterade scope. RBAC kan flexibiliteten för en Azure-prenumeration som äger hanteras av administratörskontot (service administratörsrollen på en prenumerationsnivå) och har flera användare uppmanas för att arbeta under samma prenumeration, men utan några administrativa rättigheter för det. . Från en hanteringen och fakturering perspektiv bevisar RBAC-funktionen ska vara ett tids- och effektivt alternativ för att använda Azure i olika scenarier.

## <a name="prerequisites"></a>Förutsättningar
Med RBAC i Azure-miljön kräver:

* Med en fristående Azure-prenumeration tilldelas användaren som ägare (prenumeration roll)
* Har rollen som ägare av Azure-prenumeration
* Ha åtkomst till den [Azure-portalen](https://portal.azure.com)
* Se till att ha följande Resursleverantörer som registrerats för användaren prenumerationen: **Microsoft.Authorization**. Läs mer om hur du registrerar resursleverantörer [Resource Manager-providers, regioner, API-versioner och scheman](../azure-resource-manager/resource-manager-supported-services.md).

> [!NOTE]
> Office 365-prenumerationer eller Azure Active Directory-licenser (till exempel: åtkomst till Azure Active Directory) etablerats från den Office 365 Admin center inte uppfyller kraven för att använda RBAC.

## <a name="how-can-rbac-be-used"></a>Hur kan RBAC användas
RBAC kan tillämpas på tre olika scope i Azure. Från området högsta till lägsta som är de följande:

* Prenumeration (högsta)
* Resursgrupp
* Resursen omfång (lägsta åtkomstnivå erbjudande riktade behörigheter till en enskild resurs i Azure omfattning)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Tilldela roller RBAC definitionsområdet prenumeration
Det finns två vanliga exempel när RBAC används (men inte begränsat till):

* Med externa användare från organisationer (inte del av användaren administratör Azure Active Directory-klient) bjudits in till att hantera vissa resurser eller hela prenumerationen
* Arbeta med användare i organisationen (det är en del av användarens Azure Active Directory-klient) men en del av olika grupper eller grupper som behöver detaljerad åtkomst till hela prenumerationen eller till vissa resursgrupp eller resurs scope i miljön

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Bevilja åtkomst till en prenumerationsnivå för en användare utanför Azure Active Directory
RBAC-roller som kan beviljas endast av **ägare** för prenumerationen. Därför måste administratören logga in som en användare som har rollen förinställda eller har skapats i Azure-prenumeration.

Azure-portalen när du loggar in som administratör, Välj ”prenumerationer” och välj en.
![prenumerationsbladet i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) som standard om administratören har köpt Azure-prenumeration användaren visas som **kontoadministratören**, detta är rollen prenumeration. Läs mer om Azure-prenumeration roller [lägga till eller ändra Azure-administratörsroller som hanterar prenumerationen eller tjänster](/billing/billing-add-change-azure-subscription-administrator.md).

I det här exemplet är användaren ”alflanigan@outlook.com” är den **ägare** prenumerationen i AAD för ”utvärderingsversion” klient ”standard klient Azure”. Eftersom den här användaren är skapare av Azure-prenumeration med inledande Account ”Outlook” (Account = Outlook Live etc.) standarddomännamnet för alla andra användare som lagts till i den här klienten kommer att **”@alflaniganuoutlook.onmicrosoft.com”**. Avsiktligt syntaxen för den nya domänen bildas genom att sätta ihop användarnamn och domän namnet på användaren som skapade klienten och lägger till tillägget **”. onmicrosoft.com”**.
Dessutom kan användare logga in med ett anpassat domännamn i klienten efter att lägga till och verifierar för den nya innehavaren. Mer information om hur du verifierar ett anpassat domännamn i Azure Active Directory-klient finns [lägga till ett anpassat domännamn i katalogen](/active-directory/active-directory-add-domain).

I det här exemplet innehåller klientkatalogen ”standard Azure” endast användare med domännamnet ”@alflanigan.onmicrosoft.com”.

När du har valt prenumerationen admin-användaren måste klicka på **Access Control (IAM)** och sedan **lägga till en ny roll**.





![funktionen IAM för åtkomstkontroll i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![lägga till nya användare i IAM-funktionen åtkomstkontroll i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

Nästa steg är att välja rollen tilldelas och användaren som ska tilldelas rollen RBAC. I den **rollen** listrutan admin-användare ser bara de inbyggda RBAC roller som är tillgängliga i Azure. Mer detaljerade beskrivningar av varje roll och deras tilldelningsbara scope finns [inbyggda roller för rollbaserad åtkomstkontroll i](role-based-access-built-in-roles.md).

Administratören måste sedan lägga till den externa användaren e-postadress. Förväntat beteende är externa användare kan inte visas i den befintliga klienten. När den externa användaren har bjudits han visas under **prenumerationer > Access Control (IAM)** med de aktuella användare som är tilldelade en RBAC-rollen på prenumerationsomfattningen.





![Lägg till behörigheter till den nya RBAC roll](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![lista över RBAC-roller på prenumerationsnivån](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

Användaren ”chessercarlton@gmail.com” har bjudits in till att vara en **ägare** för prenumerationen ”utvärderingsversion”. När inbjudan, får den externa användaren ett e-postbekräftelse med en aktiveringslänk.
![e-postinbjudan för RBAC roll](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

Som utanför organisationen, har den nya användaren inte några befintliga attribut i klientkatalogen ”standard Azure”. De kommer att skapas när den externa användaren har gett samtycke som ska registreras i den katalog som är associerade med prenumerationen har tilldelats en roll.





![e-postmeddelande för inbjudan för RBAC roll](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

Externa användare visas i Azure Active Directory-klient hädanefter som externa användare och det kan visas i Azure-portalen.





![användare bladet azure active directory Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)



I den **användare** vyn externa användare kan identifieras av typen olika ikoner i Azure-portalen.

Dock bevilja **ägare** eller **deltagare** åtkomst till en extern användare i den **prenumeration** omfång, tillåter inte åtkomst till katalogen för admin-användare, såvida inte den **Global administratör** tillåter. I användar-proprieties den **användartyp**, som har två gemensamma parametrar, **medlem** och **gäst** kan identifieras. En medlem är en användare som har registrerats i katalogen medan gäst är en användare som bjudits in till katalogen från en extern källa. Mer information finns i [hur till B2B-samarbete användare av Azure Active Directory-administratörer](active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Kontrollera att den externa användaren väljer att logga in i rätt katalog när du har angett autentiseringsuppgifterna i portalen. Samma användare kan ha åtkomst till flera kataloger och kan välja någon av dem genom att klicka på användarnamnet i den översta högra sidan i Azure-portalen och välj sedan den aktuella katalogen i listrutan.

Samtidigt som gäst i katalogen, den externa användaren kan hantera alla resurser för Azure-prenumeration, men har inte åtkomst till katalogen.





![åtkomst begränsas till azure active directory Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

Azure Active Directory och Azure-prenumeration har inte en underordnad-överordnad relation som andra Azure-resurser (till exempel: virtuella datorer, virtuella nätverk, webbprogram, lagring etc.) med en Azure-prenumeration. Alla dessa skapas, hanteras, och debiteras enligt en Azure-prenumeration medan en Azure-prenumeration som används för att hantera åtkomst till en Azure-katalog. Mer information finns i [hur Azure-prenumeration är kopplad till Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

Från alla de inbyggda RBAC rollerna, **ägare** och **deltagare** ger fullständig åtkomst till alla resurser i miljön, skillnaden är att en deltagare inte kan skapa och ta bort nya RBAC-roller . De inbyggda rollerna som **Virtual Machine-deltagare** erbjuder fullständiga endast åtkomst till resurser som anges av namnet, oberoende av den **resursgruppen** skapas i.

Tilldela rollen inbyggda RBAC för **Virtual Machine-deltagare** innebär att användaren har tilldelats rollen på en på prenumerationsnivå:

* Visa alla virtuella datorer oavsett deras distributionsdatum och resursgrupper som de tillhör
* Har fullständig åtkomst till de virtuella datorerna i prenumerationen
* Det går inte att visa alla andra typer av resurser i prenumerationen
* Fungerar inte ändringar ur fakturering

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Tilldela en inbyggd roll RBAC till en extern användare
För ett annat scenario i det här testet kan den externa användaren ”alflanigan@gmail.com” läggs till som en **Virtual Machine-deltagare**.




![inbyggda deltagarrollen virtuell dator](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

Normalt beteende för den här externa användare med den här inbyggda rollen är att se och hantera endast virtuella datorer och deras intilliggande Resource Manager endast resurser som är nödvändiga vid distribution. Rollerna begränsad erbjuder endast åtkomst till sina motsvarande resurser som skapats i Azure portal avsiktligt.



![deltagare rollen översikt över virtuella datorer i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Bevilja åtkomst till en prenumerationsnivå för en användare i samma katalog
Processflödet är identisk med att lägga till en extern användare, både ur administratör ge RBAC-roll som användaren beviljas åtkomst till rollen. Skillnaden här är att inbjudna användare inte får någon e-inbjudningar som resursen scope i prenumerationen kommer att finnas i instrumentpanelen efter inloggningen.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Tilldela RBAC-roller på Gruppomfång resurs
Tilldela en RBAC-rollen på en **resursgruppen** scope har en identisk process för att tilldela rollen på prenumerationsnivån för båda typer av användare – extern eller intern (ingår i samma katalog). Användare som har tilldelats rollen RBAC är att se i sina miljöer endast resursgruppen de har tilldelats åtkomst från den **resursgrupper** ikon i Azure-portalen.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Tilldela RBAC-roller i omfånget för resurs
Tilldela en roll RBAC definitionsområdet resurs i Azure har en identisk process för att tilldela rollen på prenumerationsnivån eller på resursgruppsnivå, följa samma arbetsflöde för båda scenarierna. Användare som har tilldelats rollen RBAC kan finns endast de objekt som de har tilldelats åtkomst till, antingen i den **alla resurser** fliken eller direkt i sina instrumentpanelen.

En viktig aspekt för RBAC både på resursen Gruppomfång eller resurs scope är för användare att se till att logga in i rätt katalog.





![Directory inloggning i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Tilldela RBAC-roller för en Azure Active Directory-grupp
Alla scenarier med RBAC på tre olika scope i Azure ger behörighet för att hantera, distribuera och administrera olika resurser som tilldelats användare utan att behöva hantera en personlig prenumeration. Oavsett RBAC-rollen tilldelas för en prenumeration, resursgrupp eller resurs scope, alla resurser skapas ytterligare på av tilldelade användare debiteras under en Azure-prenumerationen där användarna har åtkomst till. På så sätt kan användare som har administratörsbehörighet för att hela Azure-prenumeration fakturering har en fullständig översikt på förbrukning, oavsett vem som hanterar resurser.

För större organisationer kan RBAC-roller tillämpas på samma sätt för Azure Active Directory-grupper med tanke på att administratören vill ge detaljerade åtkomst för team eller hela avdelningar, inte individuellt för varje användare måste därför överväger perspektiv det som ett mycket tid och hantering av effektivt alternativ. Som det här exemplet illustrerar den **deltagare** roll har lagts till någon av grupperna i klienten på prenumerationsnivån.





![Lägg till RBAC-rollen för AAD-grupper](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

Dessa grupper är säkerhetsgrupper som etableras och hanteras i Azure Active Directory.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Skapa en anpassad RBAC-roll för att öppna supportärenden med hjälp av PowerShell
De inbyggda roller som är tillgängliga i Azure kontrollera vissa behörighetsnivåer baserat på tillgängliga resurser i miljön. Om inbyggda roller inte uppfyller dina behov, kan du skapa anpassade roller.

Om du vill skapa en anpassad roll du börja med en inbyggd roll, redigera den och sedan skapa en ny roll. I det här exemplet inbyggt **Reader** roll har anpassats för att tillåta användaren att öppna supportärenden.

I PowerShell använder den [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) kommando för att exportera den **Reader** roll i JSON-format.

```powershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

Nedan visas ett JSON-utdata för rollen läsare.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Därefter kan du redigera JSON-utdata för att skapa din egen roll.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

En typisk roll består av tre huvudavsnitt **åtgärder**, **NotActions**, och **AssignableScopes**.

Den **åtgärd** avsnitt visar en lista över tillåtna operationer för rollen. I det här fallet att skapa stöd tjänstbiljetter du, den **Microsoft.Support/&ast;**  måste läggas till igen. Det är viktigt att förstå att varje åtgärd görs tillgängliga från en resursleverantör. Du kan använda för att få en lista över åtgärder för en resursleverantör, den [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) kommando eller se [Azure Resource Manager Resource Provider operations](role-based-access-control-resource-provider-operations.md).

Om du vill begränsa alla åtgärder för en viss roll resursproviders visas under den **NotActions** avsnitt.
Det är obligatoriskt att rollen som innehåller explicita prenumerationen ID: N där den används. Prenumerations-ID: N visas under **AssignableScopes**, annars du kommer inte att importera rollen till din prenumeration.

Om du vill skapa den anpassade rollen som du använder den [ny AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) kommando och ange definitionsfilen för uppdaterade JSON-rollen.

```powershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

I det här exemplet är namnet på den här anpassade rollen ”Reader supportärenden åtkomstnivå”. Tillåter användare att visa allt i prenumerationen och även öppna supportärenden.

> [!NOTE]
> Bara två inbyggda roller som tillåter användare att öppna supportärenden är **ägare** och **deltagare**. För en användare för att kunna öppna supportärenden måste han tilldelas en roll definitionsområdet prenumerationen eftersom alla supportärenden skapas baserat på en Azure-prenumeration.

Den nya anpassa rollen är nu tillgänglig i Azure portal och kan tilldelas användare.

![Skärmbild av anpassad roll som importeras i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)

![Skärmbild av tilldela anpassade importerade roll till användare i samma katalog](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)

![Skärmbild av behörigheter för anpassad importerade roll](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

Användare med den här anpassade rollen kan nu skapa nya supportförfrågningar.

![Skärmbild av anpassade roll som skapar supportärenden](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)

Användare med den här anpassade rollen kan inte utföra andra åtgärder, exempelvis skapa virtuella datorer eller skapa resursgrupper.

![Skärmbild av anpassad roll går inte att skapa virtuella datorer](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)

![Skärmbild av anpassad roll går inte att skapa nya RGs](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Skapa en anpassad RBAC-roll för att öppna supportärenden med Azure CLI

Stegen för att skapa en anpassad roll med hjälp av Azure CLI liknar med hjälp av PowerShell, förutom att JSON-utdata är olika.

I det här exemplet kan du börjar med inbyggt **Reader** roll. Om du vill visa en lista med åtgärder för rollen Läsare, Använd den [az rollen definitionslista](/cli/azure/role/definition#az_role_definition_list) kommando.

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": []
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Skapa en JSON-fil med följande format. Den **Microsoft.Support/&ast;**  åtgärden har lagts till i den **åtgärder** avsnitt så att användaren kan öppna supportärenden när fortsätter att vara en läsare. Du måste lägga till prenumerations-ID där den här rollen ska användas i den **AssignableScopes** avsnitt.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Använd för att skapa den anpassade rollen som den [az rolldefinitionen skapa](/cli/azure/role/definition#az_role_definition_create) kommando.

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Den nya anpassa rollen är nu tillgänglig i Azure-portalen och processen för att använda den här rollen är desamma som i föregående avsnitt i PowerShell.

![Azure portal Skärmbild av anpassad roll som skapats med hjälp av CLI 1.0](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)
