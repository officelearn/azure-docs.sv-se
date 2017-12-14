---
title: "Hantera gästbehörighet med Azure AD åtkomst till granskningar | Microsoft Docs"
description: "Hantera gästanvändare som medlemmar i en grupp eller ett program med Azure Active Directory access granskningar som tilldelats"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 8d5cc8035d085ac9c8fc46077376836726afbb1a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Hantera gästbehörighet med Azure AD åtkomst till granskningar


Med Azure Active Directory (Azure AD), kan du enkelt aktivera samarbete över organisationens gränser med hjälp av den [Azure AD B2B-funktionen](active-directory-b2b-what-is-azure-ad-b2b.md). Gästanvändare från andra klienter kan vara [inbjuden administratörer](active-directory-b2b-admin-add-users.md) eller [andra användare](active-directory-b2b-how-it-works.md). Den här funktionen gäller även för sociala identiteter, till exempel Microsoft-konton.

Du också kan enkelt se till att gästanvändare har lämplig åtkomst. Du kan be gästerna sig själva eller en beslutsfattare att delta i ett åtkomst granska och Godkänn (eller intygar) till den gäster åtkomst. Granskarna kan ge sin syn på varje användares behov för kontinuerlig åtkomst, baserat på förslag från Azure AD. När en åtkomst-granskning är klar kan du gör ändringar och ta bort åtkomst för gäster som inte längre behövs.

> [!NOTE]
> Det här dokumentet fokuserar på att granska gästanvändare åtkomst. Om du vill granska alla användare åtkomst, inte bara gäster finns [hantera användarnas åtkomst med åtkomst granskningar](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md). Om du vill granska användarnas medlemskap i administrativa roller som global administratör, se [startar en åtkomst-granskning i Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Krav 

Åtkomstgranskningar är tillgängliga med Premium P2-versionen av Azure AD, som ingår i Microsoft Enterprise Mobility + Security, E5. Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md). Varje användare som använder den här funktionen för att skapa en granskning, få åtkomst till en granskning eller tillämpa en granskning kräver en licens.

Om du planerar att be gästen användaren att granska sina egna åtkomst kan du läsa om gäst licenser. Mer information finns i [licensiering av Azure AD B2B-samarbete](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Skapa och utföra en åtkomst-granskning för gäster

Först aktivera åtkomst granskningar ska visas på en granskare åtkomst paneler. Som global administratör går du till [sidan med åtkomstgranskningar](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD kan flera scenarier för att granska gästanvändare.

Välj något av följande:

 - En grupp i Azure AD som har en eller flera gäster som medlemmar.
 - Ett program som är anslutna till Azure AD som har en eller flera gästanvändare som tilldelats. 

Sedan kan du bestämma om du vill be varje gäst att granska sina egna åtkomst eller be en eller flera användare att granska varje gästbehörighet.

 Dessa scenarier beskrivs i följande avsnitt.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Be gäster att granska sina egna medlemskap i en grupp

Du kan använda åtkomst granskningar så att användare som har bjudits in och lagts till i en grupp fortsätta behöver åtkomst. Du kan enkelt be gäster att granska sina egna medlemskap i gruppen.

1. Om du vill starta en åtkomst-granskning för gruppen, Välj granska gästen användaren medlemmar och medlemmar Läs sig själva. Mer information finns i [Skapa en åtkomstgranskning](active-directory-azure-ad-controls-create-access-review.md).

2. Be varje gästen för att granska sina egna medlemskap. Som standard varje Gäst som har accepterat inbjudan tar emot ett e-postmeddelande från Azure AD med en länk till granskningen åtkomst. Azure AD innehåller anvisningar för gäster som beskriver hur du [granska deras åtkomst](active-directory-azure-ad-controls-perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning](active-directory-azure-ad-controls-complete-access-review.md).

4. Förutom användare som nekas sina egna behov av fortsatt åtkomst kan du ta bort användare som inte svarar. Icke-svarar användarna får eventuellt inte längre e-post.

5. Om gruppen inte används för åtkomsthantering av bort du också användare som inte har valt att delta i granskningen eftersom de inte acceptera inbjudan. Accepterar inte kan indikera att inbjudna användarens e-postadress hade ett skrivfel. Om en grupp används som en distributionslista, inte var kanske vissa gästanvändare markerat delta eftersom de kontaktobjekt.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Be en sponsor att granska en gäst medlemskap i en grupp

Du kan begära en sponsor som ägare till en grupp att granska behovet av en gäst för fortsatt medlemskap i en grupp.

1. Om du vill starta en åtkomst-granskning för gruppen, Välj granska gästen användaren medlemmar ska inkluderas. Ange en eller flera granskare. Mer information finns i [Skapa en åtkomstgranskning](active-directory-azure-ad-controls-create-access-review.md).

2. Be granskarna att komma med reflektioner. Som standard får alla ett e-postmeddelande från Azure AD med en länk till åtkomstpanelen där de ska [genomföra sin åtkomstgranskning](active-directory-azure-ad-controls-perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Be gäster att granska sina egna åtkomst till ett program

Du kan använda åtkomst granskningar så att användare som bjudits in för ett visst program fortsätta behöver åtkomst. Du kan enkelt fråga gäster sig att granska sina egna behöver för åtkomst.

1. Välj granska att inkludera endast gäster och att användarna granska sina egna åtkomst för att starta en åtkomst-granskning för programmet. Mer information finns i [Skapa en åtkomstgranskning](active-directory-azure-ad-controls-create-access-review.md).

2. Be varje gästen för att granska sina egna åtkomst till programmet. Som standard varje Gäst som har accepterat inbjudan tar emot ett e-postmeddelande från Azure AD med en länk till access granskningen i åtkomstpanelen för din organisation. Azure AD innehåller anvisningar för gäster som beskriver hur du [granska deras åtkomst](active-directory-azure-ad-controls-perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning](active-directory-azure-ad-controls-complete-access-review.md).

4. Utöver användare som nekas sina egna behöver för fortsatt åtkomst, du kan också ta bort gästanvändare som inte svarar. Icke-svarar användarna får eventuellt inte längre e-post. Du kan också ta bort gästanvändare som inte var markerat delta, särskilt om de nyligen har bjudits in. Dessa användare accepterade sina inbjudan och kan därför inte tillgång till programmet. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Be en sponsor att granska gästbehörighet till ett program

Du kan begära en sponsor som ägare till ett program för att granska behovet av en gäst för fortsatt åtkomst till programmet.

1. Välj granska att inkludera gäster endast om du vill starta en åtkomst-granskning för programmet. Ange en eller flera användare som granskare. Mer information finns i [Skapa en åtkomstgranskning](active-directory-azure-ad-controls-create-access-review.md).

2. Be granskarna att komma med reflektioner. Som standard får alla ett e-postmeddelande från Azure AD med en länk till åtkomstpanelen där de ska [genomföra sin åtkomstgranskning](active-directory-azure-ad-controls-perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Be gäster att granska deras behov av åtkomst i allmänhet

I vissa organisationer kan gäster inte medveten om deras gruppmedlemskap.

> [!NOTE]
> Tidigare versioner av Azure portal som tillåter inte administrativ åtkomst av användare med UserType gäst. I vissa fall kan kan en administratör i din katalog ha ändrats en gäst UserType värde till medlem med hjälp av PowerShell. Om den här ändringen utfördes tidigare i din katalog, kanske inte den föregående frågan innehåller alla gästanvändare som tidigare har administrativa rättigheter. I detta fall behöver du ändra gästens UserType eller inkludera gästen manuellt i gruppmedlemskap.

1. Skapa en säkerhetsgrupp i Azure AD med gäster som medlemmar, om en lämplig grupp inte redan finns. Du kan exempelvis skapa en grupp med manuellt behålla medlemskap för gäster. Eller så kan du skapa en dynamisk grupp med ett namn, till exempel ”gäster för Contoso” för användare i Contoso-klienten har UserType-attributvärdet för gäst.

2. Om du vill starta en åtkomst-granskning för gruppen, Välj granskare att vara medlemmar i själva. Mer information finns i [Skapa en åtkomstgranskning](active-directory-azure-ad-controls-create-access-review.md).

3. Be varje gästen för att granska sina egna medlemskap. Som standard varje Gäst som har accepterat inbjudan tar emot ett e-postmeddelande från Azure AD med en länk till access granskningen i åtkomstpanelen för din organisation. Azure AD innehåller anvisningar för gäster som beskriver hur du [granska deras åtkomst](active-directory-azure-ad-controls-perform-access-review.md).

4. När granskare ger indata kan stoppa åtkomst granska. Mer information finns i [Slutför en åtkomstgranskning](active-directory-azure-ad-controls-complete-access-review.md).

5. Ta bort gästbehörighet för gäster som nekades, inte slutföras granskningen eller accepterade tidigare deras inbjudan. Om några av gästerna kontakter som har valts för att delta i granskningen eftersom de inte har tidigare accepterar en inbjudan kan inaktivera du sina konton med hjälp av Azure-portalen eller PowerShell. Om gästen inte längre behöver åtkomst och inte är en kontakt, kan ta bort deras användarobjektet från katalogen med hjälp av Azure-portalen eller PowerShell.

## <a name="next-steps"></a>Nästa steg

[Skapa en åtkomstgranskning för medlemmar i en grupp eller åtkomst till ett program](active-directory-azure-ad-controls-create-access-review.md)







