---
title: Hantera åtkomst för externa användare med RBAC i Azure | Microsoft Docs
description: Lär dig mer om att hantera åtkomst för användare utanför en organisation som använder rollbaserad åtkomstkontroll (RBAC) i Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: aec12e6dc7d331b2610546d0b0c92fa6ce0789ee
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284531"
---
# <a name="manage-access-for-external-users-using-rbac"></a>Hantera åtkomst för externa användare med RBAC

Rollbaserad åtkomstkontroll (RBAC) gör bättre säkerhetshantering för stora organisationer och för små och medelstora företag att arbeta med externa samarbetspartner, leverantörer eller freelancers som behöver åtkomst till specifika resurser i din miljö, men inte nödvändigtvis för hela infrastruktur eller eventuella faktureringsrelaterade scope. RBAC kan flexibiliteten i ägande en Azure-prenumeration som hanteras av administratörskontot (administratörsrollen för tjänsten på en prenumerationsnivå) och har flera användare bjuds in för att arbeta i samma prenumeration, men utan några administrativa rättigheter för den .

> [!NOTE]
> Office 365-prenumerationer eller Azure Active Directory-licenser (till exempel: åtkomst till Azure Active Directory) etablerats från center är inte kvalificerade för att använda RBAC Administrationscenter för Office 365.

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Tilldela RBAC-roller prenumerationsområde

Det finns två vanliga exempel när RBAC används (men inte begränsat till):

* Med externa användare från organisationer (inte del av administratören användarens Azure Active Directory-klient) bjuds in för att hantera vissa resurser eller hela prenumerationen
* Arbeta med användare i organisationen (de är en del av användarens Azure Active Directory-klient) men en del av olika team eller grupper som behöver detaljerad åtkomst till hela prenumerationen eller att vissa resursgrupper eller resurs scope i miljön

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Bevilja åtkomst på prenumerationsnivå för en användare utanför Azure Active Directory

RBAC-roller som kan beviljas endast av **ägare** för prenumerationen. Därför kan måste administratören vara inloggad som en användare som har den här rollen tilldelas i förväg eller har skapats i Azure-prenumeration.

Azure-portalen när du har loggat in som administratör, Välj ”prenumerationer” och välj den du vill använda.
![prenumerationsbladet i Azure-portalen](./media/role-assignments-external-users/0.png) som standard om administratören har köpt Azure-prenumerationen för användaren visas som **kontoadministratören**, detta är rollen prenumeration. Läs mer om Azure-prenumeration roller [Lägg till eller ändra Azure-prenumerationsadministratörer](../billing/billing-add-change-azure-subscription-administrator.md).

I det här exemplet är användaren ”alflanigan@outlook.com” är den **ägare** av ”kostnadsfri utvärdering” prenumerationen i AAD-klient ”standard klient Azure”. Eftersom den här användaren är skapare av Azure-prenumeration med inledande Account ”Outlook” (Account = Outlook, Live osv) standarddomännamnet för alla andra användare som har lagts till i den här klienten kommer att **”\@ alflaniganuoutlook.onmicrosoft.com ”**. Avsiktligt bildas syntaxen för den nya domänen genom att sätta ihop användarnamn och namnet på användaren som skapade klienten och att lägga till tillägget **”. onmicrosoft.com”**.
Dessutom kan användare logga in med ett anpassat domännamn i klienten efter att lägga till och verifierar den för den nya innehavaren. Mer information om hur du verifierar ett anpassat domännamn i Azure Active Directory-klient finns i [lägga till ett anpassat domännamn i katalogen](../active-directory/fundamentals/add-custom-domain.md).

I det här exemplet innehåller katalogen ”standard-klientorganisation Azure” endast användare med domännamnet ”\@alflanigan.onmicrosoft.com”.

När du har valt prenumerationen administratörsanvändaren måste klicka på **åtkomstkontroll (IAM)** och sedan **lägga till en ny roll**.

![IAM åtkomstfunktion i Azure-portalen](./media/role-assignments-external-users/1.png)

![Lägg till ny användare i IAM åtkomstfunktion i Azure-portalen](./media/role-assignments-external-users/2.png)

Nästa steg är att välja rollen tilldelas och användaren som ska tilldelas den RBAC-rollen. I den **rollen** listrutan admin-användare ser bara de inbyggda RBAC-roller som är tillgängliga i Azure. Mer detaljerade förklaringar av varje roll och deras tilldelningsbara scope finns i [inbyggda roller](built-in-roles.md).

Administratören måste sedan lägga till den externa användaren e-postadress. Det är förväntat beteende för den externa användaren inte visas i den befintliga klienten. När den externa användaren har bjudits han kommer att visas under **prenumerationer > åtkomstkontroll (IAM)** med de aktuella användarna som är tilldelade en RBAC-roll prenumerationsområde.

![lägga till behörigheter till nya RBAC-roll](./media/role-assignments-external-users/3.png)

![lista över RBAC-roller på prenumerationsnivån](./media/role-assignments-external-users/4.png)

Användaren ”chessercarlton@gmail.com” har blivit inbjuden att vara en **ägare** för prenumerationen ”kostnadsfri utvärdering”. När du har skickat inbjudan, får den externa användaren en e-postbekräftelse med en aktiveringslänk.
![e-postinbjudan för RBAC-roll](./media/role-assignments-external-users/5.png)

Är utanför organisationen, har den nya användaren inte några befintliga attribut i katalogen ”standard-klientorganisation Azure”. De kommer att skapas efter den externa användaren har gett ditt medgivande till att läggas till i den katalog som är associerade med prenumerationen har tilldelats en roll.

![e-postinbjudan meddelande för RBAC-roll](./media/role-assignments-external-users/6.png)

Den externa användare visas som i Azure Active Directory-klient hädanefter som externa användare och detta kan visas i Azure-portalen.

![användare bladet azure active directory Azure-portalen](./media/role-assignments-external-users/7.png)

I den **användare** vyn, externa användare kan erkännas av typen annan ikon i Azure-portalen.

Beviljar dock **ägare** eller **deltagare** åtkomst till en extern användare i den **prenumeration** omfång, tillåter inte åtkomst till admin-användarens katalog, såvida inte den **Global administratör** innebär att det. I användare-proprieties den **användartyp**, som har två parametrarna **medlem** och **gäst** kan identifieras. Medlem är en användare som har registrerats i katalogen gäst är en användare som bjuds in till katalogen från en extern källa. Mer information finns i [hur lägger Azure Active Directory-administratörer till B2B-samarbetare](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Kontrollera att den externa användaren väljer att logga in på rätt katalog när du har angett autentiseringsuppgifterna i portalen. Samma användare kan ha åtkomst till flera kataloger och kan välja någon av dem genom att klicka på användarnamnet i överst till höger i Azure-portalen och välj sedan den aktuella katalogen i listrutan.

När du är en gäst i katalogen, den externa användaren kan hantera alla resurser för Azure-prenumeration, men kan inte komma åt katalogen.

![åtkomst begränsad till azure active directory Azure-portalen](./media/role-assignments-external-users/9.png)

Azure Active Directory och en Azure-prenumeration inte har en underordnad-överordnad relation som andra Azure-resurser (till exempel: virtuella datorer, virtuella nätverk, webbappar, lagring osv) har med en Azure-prenumeration. Alla dessa skapas, hanteras och debiteras enligt en Azure-prenumeration medan en Azure-prenumeration används för att hantera åtkomst till en Azure-katalog. Mer information finns i [hur en Azure-prenumeration är kopplad till Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Från alla de inbyggda RBAC-rollerna, **ägare** och **deltagare** ger fullständig åtkomst till alla resurser i miljön, skillnaden är att deltagare inte kan skapa eller ta bort nya RBAC-roller . De inbyggda rollerna som **virtuell Datordeltagare** erbjuder fullständig hantering endast åtkomst till de resurser som anges av namnet, oavsett den **resursgrupp** de som skapas i.

Tilldela den inbyggda RBAC-rollen för **virtuell Datordeltagare** innebär att användaren tilldelas rollen på en prenumerationsnivå:

* Kan visa alla virtuella datorer oavsett deras distributionsdatum och de ingår i resursgrupper
* Har fullständig åtkomst till de virtuella datorerna i prenumerationen
* Det går inte att visa alla andra typer av resurser i prenumerationen
* Det går inte att fungera ändringar från ett

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Tilldela en inbyggd RBAC-roll till en extern användare

För ett annat scenario i det här testet, den externa användaren ”alflanigan@gmail.com” har lagts till som en **virtuell Datordeltagare**.

![inbyggda deltagarrollen virtuell dator](./media/role-assignments-external-users/11.png)

Det normala beteendet för den här externa användaren med det här inbyggd roll är att se och hantera endast virtuella datorer och deras intilliggande Resource Manager endast resurser som krävs för när du distribuerar. Enligt design erbjuder rollerna begränsad endast åtkomst till sina motsvarande resurser som skapats i Azure-portalen.

![deltagare rollen översikt över virtuella datorer i Azure-portalen](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Bevilja åtkomst på prenumerationsnivå för en användare i samma katalog

Processflödet är identisk med att lägga till en extern användare, både ur administratör bevilja RBAC-roll som användaren beviljas åtkomst till rollen. Skillnaden är att inbjuden användare inte får någon e-postinbjudningar som alla resurs scope inom prenumerationen kommer att finns på instrumentpanelen när du loggar in.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Tilldela RBAC-roller i resursgruppomfånget

Tilldela en RBAC-rollen på en **resursgrupp** omfång har en identisk process för att tilldela rollen på prenumerationsnivå för båda typerna av användare – extern eller intern (ingår i samma katalog). De användare som har tilldelats rollen RBAC är att se i sina miljöer endast resursgruppen de har tilldelats åtkomst från den **resursgrupper** ikonen i Azure-portalen.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Tilldela RBAC-roller i resurs-omfånget

Tilldela en RBAC-roller i ett resurs-omfång i Azure har en identisk process för att tilldela rollen på prenumerationsnivån eller på resursgruppsnivå, följa samma arbetsflöde för båda scenarierna. Igen, de användare som har tilldelats rollen RBAC ser bara de objekt som de har tilldelats åtkomst till, antingen i den **alla resurser** fliken eller direkt i sin instrumentpanel.

Det är en viktig aspekt för RBAC både på resursen Gruppomfång eller resurs omfång för användare att se till att logga in på rätt katalog.

![Directory-inloggning i Azure-portalen](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Tilldela RBAC-roller för en Azure Active Directory-grupp

Alla scenarier med RBAC i tre olika omfång i Azure erbjuder privilegiet hantera, distribuera och administrera olika resurser som en tilldelad användare utan att behöva hantera en personlig prenumeration. Oavsett RBAC-roll tilldelas för en prenumeration, resursgrupp eller resurs omfång, alla resurser som skapas ytterligare på av tilldelade användare, faktureras i en Azure-prenumeration där användarna har åtkomst till. På så sätt kan användare som har fakturering administratörsbehörighet för att hela Azure-prenumeration har en fullständig översikt på förbrukning, oavsett vem som hanterar resurserna.

För stora organisationer kan RBAC-roller tillämpas på samma sätt för Azure Active Directory-grupper som överväger att administratören vill ge detaljerad åtkomst för team eller hela avdelningar, inte individuellt för varje användare måste därför överväger perspektiv det som ett mycket tid och hantering av effektiv alternativ. För att illustrera det här exemplet är den **deltagare** roll har lagts till någon av grupperna i klienten på prenumerationsnivån.

![Lägg till RBAC-roll för AAD-grupper](./media/role-assignments-external-users/14.png)

Dessa grupper är säkerhetsgrupper, det vill säga etableras och hanteras endast i Azure Active Directory.

