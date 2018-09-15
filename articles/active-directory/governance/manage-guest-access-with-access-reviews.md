---
title: Hantera gäståtkomst med Azure AD åtkomstgranskningar | Microsoft Docs
description: Hantera gästanvändare som medlemmar i en grupp eller tilldelats till ett program med Azure Active Directory-åtkomstgranskningar
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 7d3361b44d47fd97c14f677e5e4bbc6c1ee5ad3f
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45608300"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Hantera gäståtkomst med Azure AD åtkomstgranskningar


Med Azure Active Directory (AD Azure), kan du enkelt aktivera samarbete över organisationens gränser med hjälp av den [Azure AD B2B-funktionen](../b2b/what-is-b2b.md). Gästanvändare från andra klienter kan vara [bjuds in av administratörer](../b2b/add-users-administrator.md) eller [andra användare](../b2b/what-is-b2b.md). Den här funktionen gäller även för sociala identiteter, till exempel Microsoft-konton.

Du också kan enkelt se till att gästanvändare har lämplig åtkomst. Du kan be gästerna själva, eller en beslutsfattare, att delta i en åtkomstgranskning och certifiera om (eller intygar genomförs) till den gästers åtkomst. Granskarna kan ge sin syn på varje användares behov för kontinuerlig åtkomst, baserat på förslag från Azure AD. När en åtkomstgranskning är klar kan du göra ändringar och ta bort åtkomst för gäster som inte längre behöver den.

> [!NOTE]
> Det här dokumentet fokuserar på Granska gästanvändares åtkomst. Om du vill visa alla användares åtkomst, inte bara gäster, se [hantera användarnas åtkomst med åtkomstgranskningar](manage-user-access-with-access-reviews.md). Om du vill granska användarens medlemskap i administrativa roller som global administratör, se [starta en åtkomstgranskning i Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Förutsättningar 


Åtkomstgranskningar är tillgängliga med Premium P2-versionen av Azure AD, som ingår i Microsoft Enterprise Mobility + Security, E5. Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md). Varje användare som använder den här funktionen, inklusive för att skapa en granskning, fylla i en granskning eller bekräfta sin åtkomst, behöver en licens. 

Om du planerar att ställa gästanvändare rätt att granska sin egen åtkomst, Läs mer om användarlicensiering i gästen. Mer information finns i [licensiering av Azure AD B2B-samarbete](../b2b/licensing-guidance.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Skapa och utföra en åtkomstgranskning för gäster

Först aktivera att åtkomstgranskningar ska visas på en granskares åtkomstpaneler. Som global administratör eller användarkonto-administratör går du till [sidan med åtkomstgranskningar](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD kan flera scenarier för att granska gästanvändare.

Välj något av följande:

 - En grupp i Azure AD som har en eller flera gäster som medlemmar.
 - Ett program som är anslutna till Azure AD som har en eller flera gästanvändare tilldelade till den. 

Sedan kan du bestämma om du vill be varje gästen att granska sin egen åtkomst eller ställa en eller flera användare att granska varje gäståtkomst.

 Dessa scenarier beskrivs i följande avsnitt.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Be gäster för att granska sin egen medlemskap i en grupp

Du kan använda åtkomstgranskningar för att garantera att användare som har bjudits in och lagts till en grupp fortfarande behöver åtkomst. Du kan enkelt ställa gäster för att granska sin egen medlemskap i gruppen.

1. För att starta en åtkomstgranskning för gruppen, markerar du granska Inkludera endast gästanvändarna och att medlemmar granskar själva. Mer information finns i [Skapa en åtkomstgranskning](create-access-review.md).

2. Be varje gästen för att granska sin egen medlemskap. Som standard varje gästoperativsystem som godkänt en inbjudan tar emot ett e-postmeddelande från Azure AD med en länk till åtkomstgranskningen. Azure AD innehåller anvisningar för gäster som beskriver hur du [granska åtkomsten](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning](complete-access-review.md).

4. Förutom att användare som nekas sina egna behov för kontinuerlig åtkomst kan du ta bort användare som inte svarade. Icke-svarar användarna får eventuellt inte längre e-post.

5. Gruppen inte används för åtkomsthantering, du också ta bort användare som inte var markerat att delta i granskningen eftersom de inte accepterat sin inbjudan. Accepterar inte tyda på att den inbjudna användarens e-postadress hade ett skrivfel. Om en grupp används som en distributionslista, kanske vissa gästanvändare inte har valt att delta eftersom de kontaktobjekt.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Be en sponsor att granska en gäst medlemskap i en grupp

Du kan be en sponsorn, till exempel en grupp, ägare att granska en gäst behovet av fortsatt medlemskap i en grupp.

1. För att starta en åtkomstgranskning för gruppen, markerar du granska att inkludera endast gästanvändarna. Ange en eller flera granskare. Mer information finns i [Skapa en åtkomstgranskning](create-access-review.md).

2. Be granskarna att komma med reflektioner. Som standard får alla ett e-postmeddelande från Azure AD med en länk till åtkomstpanelen där de ska [genomföra sin åtkomstgranskning](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Be gäster för att granska sin egen åtkomst till ett program

Du kan använda åtkomstgranskningar för att garantera att användare som har bjudits in för ett visst program fortfarande behöver åtkomst. Du kan enkelt ställa sig själva gäster att granska sin egen behöver åtkomst till.

1. För att starta en åtkomstgranskning för programmet, markerar du granska Inkludera endast gäster och att användare ska granska sin egen åtkomst. Mer information finns i [Skapa en åtkomstgranskning](create-access-review.md).

2. Be varje gästen för att granska sin egen åtkomst till programmet. Som standard varje gästoperativsystem som godkänt en inbjudan tar emot ett e-postmeddelande från Azure AD med en länk till åtkomstgranskning i åtkomstpanelen för din organisation. Azure AD innehåller anvisningar för gäster som beskriver hur du [granska åtkomsten](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning](complete-access-review.md).

4. Förutom att användare som nekas sina egna måste för fortsatt åtkomst, du kan också ta bort gästanvändare som inte svarade. Icke-svarar användarna får eventuellt inte längre e-post. Du kan också ta bort gästanvändare som inte var markerat att delta, särskilt om de inte nyligen har bjudit in. Dessa användare accepterat inte sin inbjudan och det inte har åtkomst till programmet. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Be en sponsor att granska gäståtkomst till ett program

Du kan be en sponsorn, till exempel ägaren av ett program, att granska gästens behov för kontinuerlig åtkomst till programmet.

1. Välj granskningen att inkludera endast gäster för att starta en åtkomstgranskning för programmet. Ange sedan en eller flera användare som granskare. Mer information finns i [Skapa en åtkomstgranskning](create-access-review.md).

2. Be granskarna att komma med reflektioner. Som standard får alla ett e-postmeddelande från Azure AD med en länk till åtkomstpanelen där de ska [genomföra sin åtkomstgranskning](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Be gäster för att granska deras behov av åtkomst, i allmänhet

Gäster kanske inte kände till deras gruppmedlemskap i vissa organisationer.

> [!NOTE]
> Tidigare versioner av Azure-portalen tillåter inte administrativ åtkomst av användare med UserType gästen. I vissa fall kan kan en administratör i din katalog ha ändrats en gäst UserType värde till medlemmen med hjälp av PowerShell. Om den här ändringen utfördes tidigare i din katalog, kan den föregående frågan inte innehålla alla gästanvändare som tidigare har administrativa rättigheter. I det här fallet måste antingen ändra gästens UserType eller inkludera gästen manuellt i gruppmedlemskapet.

1. Skapa en säkerhetsgrupp i Azure AD med gäster som medlemmar, om en lämplig grupp inte redan finns. Du kan till exempel skapa en grupp med ett medlemskap som manuellt underhållen av gäster. Du kan också skapa en dynamisk grupp med ett namn, till exempel ”gäster av Contoso” för användare i Contoso-klienten som har UserType-attributvärdet för gäst.  Se till att gruppen är huvudsakligen gäster – inte välja en grupp med användare som behöver granskas för effektivitet.

2. Välj granskare att vara medlemmar själva för att starta en åtkomstgranskning för gruppen. Mer information finns i [Skapa en åtkomstgranskning](create-access-review.md).

3. Be varje gästen för att granska sin egen medlemskap. Som standard varje gästoperativsystem som godkänt en inbjudan tar emot ett e-postmeddelande från Azure AD med en länk till åtkomstgranskning i åtkomstpanelen för din organisation. Azure AD innehåller anvisningar för gäster som beskriver hur du [granska åtkomsten](perform-access-review.md).  Dessa gäster som inte godkände sin inbjudan visas i granskningsresultaten som ”meddelas inte”.

4. När granskarna har framfört, åtkomstgranskningen. Mer information finns i [Slutför en åtkomstgranskning](complete-access-review.md).

5. Ta bort gäståtkomst för gäster som nekades, inte slutfördes granskningen eller inte tidigare har accepterat sin inbjudan. Om några av gästerna är kontakter som har valts att delta i granskningen eller om de inte tidigare har accepterat inbjudan, kan du inaktivera sina konton med hjälp av Azure portal eller PowerShell. Om gästoperativsystemet inte längre behöver åtkomst och inte är en kontakt, kan ta bort deras användarobjektet från din katalog med hjälp av Azure portal eller PowerShell att ta bort användarobjektet gäst.

## <a name="next-steps"></a>Nästa steg

[Skapa en åtkomstgranskning för medlemmar i en grupp eller åtkomst till ett program](create-access-review.md)







