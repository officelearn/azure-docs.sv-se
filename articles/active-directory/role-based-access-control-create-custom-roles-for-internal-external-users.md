---
title: "Skapa anpassade roller för rollbaserad åtkomstkontroll och tilldela interna och externa användare i Azure | Microsoft Docs"
description: "Tilldela anpassade RBAC-roller med hjälp av PowerShell och CLI för interna och externa användare"
services: active-directory
documentationcenter: 
author: andreicradu
manager: catadinu
editor: kgremban
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/10/2017
ms.author: a-crradu
ms.openlocfilehash: 213b02205bbe7f767b6aff6a0693bb34b97cb9ec
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="intro-on-role-based-access-control"></a>Introduktion på rollbaserad åtkomstkontroll

Rollbaserad åtkomstkontroll är en Azure portal endast funktionen som tillåter ägare av en prenumeration att tilldela detaljerade roller till andra användare som kan hantera en viss resurs-scope i sina miljöer.

RBAC kan bättre säkerhetshantering för stora organisationer och för små och medelstora företag arbetar med externa samarbetspartners, leverantörer eller freelancers som behöver åtkomst till specifika resurser i din miljö, men inte nödvändigtvis att hela infrastrukturen eller någon fakturerings-relaterade scope. RBAC kan flexibiliteten för en Azure-prenumeration som äger hanteras av administratörskontot (service administratörsrollen på en prenumerationsnivå) och har flera användare uppmanas för att arbeta under samma prenumeration, men utan några administrativa rättigheter för det. . Från en hanteringen och fakturering perspektiv bevisar RBAC-funktionen ska vara ett tids- och effektivt alternativ för att använda Azure i olika scenarier.

## <a name="prerequisites"></a>Krav
Med RBAC i Azure-miljön kräver:

* Med en fristående Azure-prenumeration tilldelas användaren som ägare (prenumeration roll)
* Har rollen som ägare av Azure-prenumeration
* Ha åtkomst till den [Azure-portalen](https://portal.azure.com)
* Se till att ha följande Resursleverantörer som registrerats för användaren prenumerationen: **Microsoft.Authorization**. Läs mer om hur du registrerar resursleverantörer [Resource Manager-providers, regioner, API-versioner och scheman](../azure-resource-manager/resource-manager-supported-services.md).

> [!NOTE]
> Office 365-prenumerationer eller Azure Active Directory-licenser (till exempel: åtkomst till Azure Active Directory) etablerats från O365 portal inte uppfyller kraven för att använda RBAC.

## <a name="how-can-rbac-be-used"></a>Hur kan RBAC användas
RBAC kan tillämpas på tre olika scope i Azure. Från området högsta till lägsta som är de följande:

* Prenumeration (högsta)
* Resursgrupp
* Resursen omfång (lägsta åtkomstnivå erbjudande riktade behörigheter till en enskild resurs i Azure omfattning)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Tilldela roller RBAC definitionsområdet prenumeration
Det finns två vanliga exempel när RBAC används (men inte begränsat till):

* Med externa användare från organisationer (inte del av användaren administratör Azure Active Directory-klient) bjudits in till att hantera vissa resurser eller hela prenumerationen
* Arbeta med användare i organisationen (det är en del av användarens Azure Active Directory-klient) men en del av olika grupper eller grupper som behöver detaljerad åtkomst till hela prenumerationen eller till vissa resursgrupp eller resurs scope i den miljö

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Bevilja åtkomst till en prenumerationsnivå för en användare utanför Azure Active Directory
RBAC-roller som kan beviljas endast av **ägare** prenumerationens därför administratörsanvändare måste vara inloggad med ett användarnamn som har rollen förinställda eller har skapats i Azure-prenumeration.

Azure-portalen när du loggar in som administratör, Välj ”prenumerationer” och välj en.
![prenumerationsbladet i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) som standard om administratören har köpt Azure-prenumeration användaren visas som **kontoadministratören**, detta är rollen prenumeration. Mer information om Azure-prenumeration roller finns [lägga till eller ändra Azure-administratörsroller som hanterar prenumerationen eller tjänster](/billing/billing-add-change-azure-subscription-administrator.md).

I det här exemplet är användaren ”alflanigan@outlook.com” är den **ägare** prenumerationen i AAD för ”utvärderingsversion” klient ”standard klient Azure”. Eftersom den här användaren är skapare av Azure-prenumeration med inledande Account ”Outlook” (Account = Outlook Live etc.) standarddomännamnet för alla andra användare som lagts till i den här klienten kommer att **”@alflaniganuoutlook.onmicrosoft.com”**. Avsiktligt syntaxen för den nya domänen bildas genom att sätta ihop användarnamn och domän namnet på användaren som skapade klienten och lägger till tillägget **”. onmicrosoft.com”**.
Dessutom kan användare kan logga in med ett anpassat domännamn i klienten efter att lägga till och verifierar för den nya innehavaren. Mer information om hur du verifierar ett anpassat domännamn i Azure Active Directory-klient finns [lägga till ett anpassat domännamn i katalogen](/active-directory/active-directory-add-domain).

I det här exemplet innehåller klientkatalogen ”standard Azure” endast användare med domännamnet ”@alflanigan.onmicrosoft.com”.

När du har valt prenumerationen admin-användaren måste klicka på **Access Control (IAM)** och sedan **lägga till en ny roll**.





![funktionen IAM för åtkomstkontroll i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![lägga till nya användare i IAM-funktionen åtkomstkontroll i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

Nästa steg är att välja rollen tilldelas och användaren som ska tilldelas rollen RBAC. I den **rollen** listrutan administratörsanvändare ser de inbyggda RBAC roller som är tillgängliga i Azure. Mer detaljerade beskrivningar av varje roll och deras tilldelningsbara scope finns [inbyggda roller för rollbaserad åtkomstkontroll i](role-based-access-built-in-roles.md).

Administratören måste sedan lägga till den externa användaren e-postadress. Förväntat beteende är externa användare kan inte visas i den befintliga klienten. När den externa användaren har bjudits han visas under **prenumerationer > Access Control (IAM)** med de aktuella användare som är tilldelade en RBAC-rollen på prenumerationsomfattningen.





![Lägg till behörigheter till den nya RBAC roll](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![lista över RBAC-roller på prenumerationsnivån](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

Användaren ”chessercarlton@gmail.com” har bjudits in till att vara en **ägare** för prenumerationen ”utvärderingsversion”. När inbjudan, får den externa användaren ett e-postbekräftelse med en aktiveringslänk.
![e-postinbjudan för RBAC roll](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

Som utanför organisationen, har den nya användaren inte några befintliga attribut i klientkatalogen ”standard Azure”. De kommer att skapas när den externa användaren har gett samtycke till att läggas till i katalogen som är kopplat till prenumerationen som har tilldelats en roll till.





![e-postmeddelande för inbjudan för RBAC roll](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

Externa användare visas i Azure Active Directory-klient hädanefter som externa användare och det kan visas både i Azure-portalen och i den klassiska portalen.





![användare bladet azure active directory Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)





![användare bladet azure active directory klassiska Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/8.png)

I den **användare** vyn i båda portaler externa användare kan identifieras av:

* Typen av olika ikoner i Azure-portalen
* Olika sourcing punkten i den klassiska portalen

Dock bevilja **ägare** eller **deltagare** åtkomst till en extern användare i den **prenumeration** omfång, tillåter inte åtkomst till katalogen för admin-användare, såvida inte den **Global administratör** tillåter. I användar-proprieties den **användartyp** som har två gemensamma parametrar, **medlem** och **gäst** kan identifieras. En medlem är en användare som har registrerats i katalogen medan gäst är en användare som bjudits in till katalogen från en extern källa. Mer information finns i [hur till B2B-samarbete användare av Azure Active Directory-administratörer](active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Kontrollera att den externa användaren väljer att logga in på rätt katalog när du har angett autentiseringsuppgifterna i portalen. Samma användare kan ha åtkomst till flera kataloger och kan välja någon av dem genom att klicka på användarnamnet i den översta högra sidan i Azure-portalen och välj sedan den aktuella katalogen i listrutan.

Samtidigt som gäst i katalogen, den externa användaren kan hantera alla resurser för Azure-prenumeration, men har inte åtkomst till katalogen.





![åtkomst begränsas till azure active directory Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

Azure Active Directory och Azure-prenumeration har inte en underordnad-överordnad relation som andra Azure-resurser (till exempel: virtuella datorer, virtuella nätverk, webbprogram, lagring etc.) med en Azure-prenumeration. Alla dessa skapas, hanteras och debiteras enligt en Azure-prenumeration medan en Azure-prenumeration som används för att hantera åtkomst till en Azure-katalog. Mer information finns i [hur Azure-prenumeration är kopplad till Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

Från alla de inbyggda RBAC rollerna, **ägare** och **deltagare** ger fullständig åtkomst till alla resurser i miljön, skillnaden är att en deltagare inte kan skapa och ta bort nya RBAC-roller . De inbyggda rollerna som **Virtual Machine-deltagare** erbjuder fullständiga endast åtkomst till resurser som anges av namnet, oberoende av den **resursgruppen** skapas i.

Tilldela rollen inbyggda RBAC för **Virtual Machine-deltagare** innebär att användaren har tilldelats rollen på en på prenumerationsnivå:

* Visa alla virtuella datorer oavsett deras distributionsdatum och resursgrupper som de tillhör
* Har fullständig åtkomst till de virtuella datorerna i prenumerationen
* Det går inte att visa alla andra typer av resurser i prenumerationen
* Fungerar inte ändringar ur fakturering

> [!NOTE]
> RBAC som en portal endast funktion i Azure, ger det inte tillgång till den klassiska portalen.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Tilldela en inbyggd roll RBAC till en extern användare
För ett annat scenario i det här testet kan den externa användaren ”alflanigan@gmail.com” läggs till som en **Virtual Machine-deltagare**.




![inbyggda deltagarrollen virtuell dator](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

Normalt beteende för den här externa användare med den här inbyggda rollen är att se och hantera endast virtuella datorer och deras intilliggande Resource Manager endast resurser som är nödvändiga vid distribution. Avsiktligt rollerna begränsad erbjuder endast åtkomst till sina motsvarande resurser som skapats i Azure-portalen, oavsett vissa kan fortfarande vara distribuerad i den klassiska portalen (till exempel: virtuella datorer).





![deltagare rollen översikt över virtuella datorer i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Bevilja åtkomst till en prenumerationsnivå för en användare i samma katalog
Processflödet är identisk med att lägga till en extern användare, både ur administratör ge RBAC-roll som användaren beviljas åtkomst till rollen. Skillnaden här är att inbjudna användare inte får någon e-inbjudningar som resursen scope i prenumerationen kommer att finnas i instrumentpanelen efter inloggningen.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Tilldela RBAC-roller på Gruppomfång resurs
Tilldela en RBAC-rollen på en **resursgruppen** scope har en identisk process för att tilldela rollen på prenumerationsnivån för båda typer av användare – extern eller intern (ingår i samma katalog). De användare som har tilldelats rollen RBAC är att se i sina miljöer endast resursgruppen de har tilldelats åtkomst från den **resursgrupper** ikon i Azure-portalen.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Tilldela RBAC-roller i omfånget för resurs
Tilldela en roll RBAC definitionsområdet resurs i Azure har en identisk process för att tilldela rollen på prenumerationsnivån eller på resursgruppsnivå, följa samma arbetsflöde för båda scenarierna. De användare som har tilldelats rollen RBAC kan finns endast de objekt som de har tilldelats åtkomst till, antingen i den **alla resurser** fliken eller direkt i sina instrumentpanelen.

En viktig aspekt för RBAC både på resursen Gruppomfång eller resurs scope är att användarna ska se till att logga in på rätt katalog.





![Directory inloggning i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Tilldela RBAC-roller för en Azure Active Directory-grupp
Alla scenarier med RBAC på tre olika scope i Azure ger behörighet för att hantera, distribuera och administrera olika resurser som tilldelats användare utan att behöva hantera en personlig prenumeration. Oavsett tilldelas rollen RBAC för en prenumeration, resursgrupp eller resurs omfång, alla resurser skapas ytterligare på av tilldelade användare debiteras under en Azure-prenumerationen där användarna har åtkomst till. På så sätt kan användare som har administratörsbehörighet för att hela Azure-prenumeration fakturering har en fullständig översikt på förbrukning, oavsett vem som hanterar resurser.

För större organisationer kan RBAC-roller tillämpas på samma sätt för Azure Active Directory-grupper med tanke på att administratören vill ge detaljerade åtkomst för team eller hela avdelningar, inte individuellt för varje användare måste därför överväger perspektiv det som ett mycket tid och hantering av effektivt alternativ. Som det här exemplet illustrerar den **deltagare** roll har lagts till någon av grupperna i klienten på prenumerationsnivån.





![Lägg till RBAC-rollen för AAD-grupper](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

Dessa grupper är säkerhetsgrupper som etableras och hanteras i Azure Active Directory.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Skapa en anpassad RBAC-roll för att öppna supportärenden med hjälp av PowerShell
De inbyggda RBAC-roller som är tillgängliga i Azure kontrollera vissa behörighetsnivåer baserat på tillgängliga resurser i miljön. Men om ingen av dessa roller passar admin-användare, finns alternativet att begränsa åtkomsten ytterligare genom att skapa anpassade RBAC-roller.

Skapa anpassade RBAC-roller kräver att ta en inbyggd roll, redigera och importera den sedan tillbaka i miljön. Ladda ned och överföra rollen som hanteras med PowerShell eller CLI.

Det är viktigt att förstå kraven för att skapa en anpassad roll som kan ge detaljerade åtkomst på prenumerationsnivå och även tillåta inbjudna användaren möjlighet att öppna supportärenden.

Det här exemplet den inbyggda rollen **Reader** som ger användare åtkomst att visa alla resurs scope men inte att redigera dem eller skapa nya har anpassats för att tillåta användaren att öppna supportärenden.

Den första åtgärden för att exportera den **Reader** roll måste utföras i PowerShell kördes med förhöjda behörigheter som administratör.

```
Login-AzureRMAccount

Get-AzureRMRoleDefinition -Name "Reader"

Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json

```





![PowerShell skärmbild för läsare RBAC roll](./media/role-based-access-control-create-custom-roles-for-internal-external-users/15.png)

Du måste sedan extrahera JSON-mall för rollen.





![JSON-mall för anpassad Reader RBAC-roll](./media/role-based-access-control-create-custom-roles-for-internal-external-users/16.png)

En typisk RBAC-rollen består av tre huvudavsnitt **åtgärder**, **NotActions** och **AssignableScopes**.

I den **åtgärd** finns i avsnittet visas alla tillåtna åtgärder för den här rollen. Det är viktigt att förstå att tilldelas varje åtgärd från en resursleverantör. I detta fall för att skapa supportärenden den **Microsoft.Support** resursprovidern måste anges.

Du kan använda PowerShell för att kunna se alla resursleverantörer tillgänglig och registrerade i din prenumeration.
```
Get-AzureRMResourceProvider

```
Dessutom kan du söka efter de alla tillgängliga PowerShell cmdletarna för hantering av resursleverantörer.
    ![PowerShell skärmbild för providern resurshantering](./media/role-based-access-control-create-custom-roles-for-internal-external-users/17.png)

Om du vill begränsa alla åtgärder för en viss roll RBAC resursproviders anges under avsnittet **NotActions**.
Senast, är det obligatoriskt att rollen RBAC innehåller explicit prenumerationen ID: N där den används. Prenumerations-ID: N visas under den **AssignableScopes**, annars du kommer inte att importera rollen i din prenumeration.

När du skapar och anpassar RBAC-rollen, behöver importeras tillbaka i miljön.

```
New-AzureRMRoleDefinition -InputFile "C:\rbacrole2.json"

```

I det här exemplet är eget namn för den här rollen RBAC ”Reader stöd biljetter åtkomstnivå” så att användaren kan visa allt i prenumerationen och också öppna supportärenden.

> [!NOTE]
> Bara två inbyggda RBAC rollerna tillåter åtgärden öppnandet av supportärenden är **ägare** och **deltagare**. För en användare för att kunna öppna supportärenden måste han tilldelas en RBAC roll bara definitionsområdet prenumeration, eftersom alla supportärenden skapas baserat på en Azure-prenumeration.

Den här nya anpassade rollen har tilldelats till en användare från samma katalog.





![Skärmbild av anpassade RBAC-roll som importeras i Azure-portalen](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)





![Skärmbild av tilldela anpassade importerade RBAC roll till användare i samma katalog](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)





![Skärmbild av behörigheter för anpassad importerade RBAC-roll](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

Exemplet har mer detaljerad att visa gränserna för den här anpassade RBAC-rollen på följande sätt:
* Kan skapa nya supportförfrågningar
* Det går inte att skapa den nya resursen scope (till exempel: virtuell dator)
* Det går inte att skapa nya resursgrupper





![Skärmbild av anpassade RBAC roll skapar supportärenden](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)





![Skärmbild av anpassad RBAC roll går inte att skapa virtuella datorer](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)





![Skärmbild av anpassad RBAC roll går inte att skapa nya RGs](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Skapa en anpassad RBAC-roll för att öppna supportärenden med Azure CLI
Körs på en Mac och utan att ha åtkomst till PowerShell, är Azure CLI att.

Stegen för att skapa en anpassad roll är desamma, med det enda undantaget att med hjälp av CLI rollen inte kan hämtas i en JSON-mall, men den kan visas i CLI.

Jag har valt den inbyggda rollen i det här exemplet **säkerhetskopiering Reader**.

```

azure role show "backup reader" --json

```





![Visa CLI Skärmbild av rollen säkerhetskopiering läsare](./media/role-based-access-control-create-custom-roles-for-internal-external-users/24.png)

Redigera rollen i Visual Studio när du har kopierat proprieties i en JSON-mall i **Microsoft.Support** resursprovidern har lagts till i den **åtgärder** avsnitt så att användaren kan öppna stöd begäranden när fortsätter att vara en läsare för säkerhetskopieringsvalv. Igen är det nödvändigt att lägga till prenumerations-ID där den här rollen ska användas i den **AssignableScopes** avsnitt.

```

azure role create --inputfile <path>

```





![CLI Skärmbild av Importera anpassad RBAC-roll](./media/role-based-access-control-create-custom-roles-for-internal-external-users/25.png)

Den nya rollen är nu tillgänglig i Azure-portalen och assignation-processen är densamma som i föregående exempel.





![Azure portal Skärmbild av anpassade RBAC-roll som skapats med hjälp av CLI 1.0](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)

Azure Cloud-gränssnittet är allmänt tillgänglig från och med den senaste Build 2017. Azure Cloud-gränssnittet är ett komplement till IDE- och Azure portal. Med den här tjänsten får du ett webbaserat gränssnitt som är autentiserad och värdbaserad i Azure och du kan använda den i stället för CLI installerat på datorn.





![Azure Cloud Shell](./media/role-based-access-control-create-custom-roles-for-internal-external-users/27.png)
