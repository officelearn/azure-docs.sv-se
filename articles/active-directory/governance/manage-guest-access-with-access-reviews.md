---
title: Hantera gäståtkomst med åtkomstgranskningar - Azure AD
description: Hantera gästanvändare som medlemmar i en grupp eller tilldelas ett program med Azure Active Directory-åtkomstgranskningar
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/13/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bf2f9503ae704110786a1e73aec3da18c17e4ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932431"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Hantera gäståtkomst med Azure AD-åtkomstgranskningar


Med Azure Active Directory (Azure AD) kan du enkelt aktivera samarbete över organisationsgränser med hjälp av [Azure AD B2B-funktionen](../b2b/what-is-b2b.md). Gästanvändare från andra klienter kan [bjudas in av administratörer](../b2b/add-users-administrator.md) eller [andra användare.](../b2b/what-is-b2b.md) Den här funktionen gäller även för sociala identiteter som Microsoft-konton.

Du kan också enkelt se till att gästanvändare har lämplig åtkomst. Du kan be gästerna själva eller en beslutsfattare att delta i en åtkomstgranskning och certifiera om (eller intyga) till gästernas tillgång. Granskarna kan ge sin syn på varje användares behov för kontinuerlig åtkomst, baserat på förslag från Azure AD. När en åtkomstgranskning är klar kan du sedan göra ändringar och ta bort åtkomst för gäster som inte längre behöver den.

> [!NOTE]
> Det här dokumentet fokuserar på att granska gästanvändarnas åtkomst. Om du vill granska alla användares åtkomst, inte bara gäster, se [Hantera användaråtkomst med åtkomstgranskningar](manage-user-access-with-access-reviews.md). Om du vill granska användarnas medlemskap i administrativa roller, till exempel global administratör, läser [du Starta en åtkomstgranskning i Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Krav

- Azure AD Premium P2

För mer information, [licenskrav](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Skapa och gör en åtkomstgranskning för gäster

Gå först till [sidan Identitetsstyrning](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) som global administratör eller användaradministratör för att se till att åtkomstgranskningar är klara för din organisation.

Azure AD möjliggör flera scenarier för granskning av gästanvändare.

Du kan granska antingen:

 - En grupp i Azure AD som har en eller flera gäster som medlemmar.
 - Ett program som är anslutet till Azure AD och som har en eller flera gästanvändare tilldelade till det. 

Du kan sedan bestämma om du vill be varje gäst att granska sin egen åtkomst eller be en eller flera användare att granska varje gästs åtkomst.

 Dessa scenarier beskrivs i följande avsnitt.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Be gästerna att granska sitt eget medlemskap i en grupp

Du kan använda åtkomstgranskningar för att säkerställa att användare som har bjudits in och lagts till i en grupp fortsätter att behöva åtkomst. Du kan enkelt be gästerna att granska sitt eget medlemskap i den gruppen.

1. Om du vill skapa en åtkomstgranskning för gruppen väljer du granskningen om du bara vill inkludera gästanvändarmedlemmar och att medlemmarna granskar sig själva. Mer information finns i [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md).

2. Be varje gäst att granska sitt eget medlemskap. Som standard får varje gäst som accepterade en inbjudan ett e-postmeddelande från Azure AD med en länk till åtkomstgranskningen. Azure AD har instruktioner för gäster om hur du [granskar åtkomst till grupper eller program](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning av grupper eller program](complete-access-review.md).

4. Förutom de användare som förnekade sitt eget behov av fortsatt åtkomst kan du också ta bort användare som inte svarade. Användare som inte svarar kan inte längre få e-post.

5. Om gruppen inte används för åtkomsthantering kan du också ta bort användare som inte har valts för att delta i granskningen eftersom de inte accepterade sin inbjudan. Om du inte accepterar det kan det tyda på att den inbjudna användarens e-postadress hade ett stavfel. Om en grupp används som en distributionslista kanske vissa gästanvändare kanske inte har valts för att delta eftersom de är kontaktobjekt.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Be en sponsor att granska en gästs medlemskap i en grupp

Du kan be en sponsor, till exempel ägaren av en grupp, att granska en gästs behov av fortsatt medlemskap i en grupp.

1. Om du vill skapa en åtkomstgranskning för gruppen väljer du granskningen om du endast vill inkludera gästanvändarmedlemmar. Ange sedan en eller flera granskare. Mer information finns i [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md).

2. Be granskarna att komma med reflektioner. Som standard får de var och en ett e-postmeddelande från Azure AD med en länk till åtkomstpanelen, där de [granskar åtkomsten till grupper eller program](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning av grupper eller program](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Be gästerna att granska sin egen tillgång till en ansökan

Du kan använda åtkomstgranskningar för att säkerställa att användare som har bjudits in för ett visst program fortsätter att behöva åtkomst. Du kan enkelt be gästerna själva att se över sitt eget behov av tillgång.

1. Om du vill skapa en åtkomstgranskning för programmet väljer du granskningen så att endast gäster inkluderas och att användarna granskar sin egen åtkomst. Mer information finns i [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md).

2. Be varje gäst att granska sin egen tillgång till programmet. Som standard får varje gäst som har accepterat en inbjudan ett e-postmeddelande från Azure AD. E-postmeddelandet har en länk till åtkomstgranskningen i organisationens åtkomstpanel. Azure AD har instruktioner för gäster om hur du [granskar åtkomst till grupper eller program](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning av grupper eller program](complete-access-review.md).

4. Förutom användare som nekat till sitt eget behov av fortsatt åtkomst kan du också ta bort gästanvändare som inte svarade. Användare som inte svarar kan inte längre få e-post. Du kan också ta bort gästanvändare som inte har valts ut för att delta, särskilt om de inte nyligen har bjudits in. Dessa användare accepterade inte sin inbjudan och hade därför inte tillgång till programmet. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Be en sponsor att granska en gästs tillgång till en ansökan

Du kan be en sponsor, till exempel ägaren av en ansökan, att granska gästens behov av fortsatt tillgång till programmet.

1. Om du vill skapa en åtkomstgranskning för programmet väljer du granskningen om du bara vill inkludera gäster. Ange sedan en eller flera användare som granskare. Mer information finns i [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md).

2. Be granskarna att komma med reflektioner. Som standard får de var och en ett e-postmeddelande från Azure AD med en länk till åtkomstpanelen, där de [granskar åtkomsten till grupper eller program](perform-access-review.md).

3. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [Slutför en åtkomstgranskning av grupper eller program](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Be gästerna att se över sitt behov av tillgång, i allmänhet

I vissa organisationer kanske gästerna inte är medvetna om sina gruppmedlemskap.

> [!NOTE]
> Tidigare versioner av Azure-portalen tillät inte administrativ åtkomst av användare med UserType of Guest. I vissa fall kan en administratör i katalogen ha ändrat en gästs UserType-värde till Medlem med powershell. Om den här ändringen tidigare har skett i katalogen kanske den föregående frågan inte innehåller alla gästanvändare som historiskt har haft administratörsbehörighet. I det här fallet måste du antingen ändra gästens UserType eller manuellt inkludera gästen i gruppmedlemskapet.

1. Skapa en säkerhetsgrupp i Azure AD med gästerna som medlemmar, om det inte redan finns en lämplig grupp. Du kan till exempel skapa en grupp med ett manuellt underhållet medlemskap för gäster. Du kan också skapa en dynamisk grupp med ett namn som "Gäster på Contoso" för användare i Contoso-klienten som har attributevärdet UserType för Gäst.  För effektivitet, se till att gruppen är övervägande gäster - välj inte en grupp som har medlemsanvändare, eftersom medlemsanvändare inte behöver granskas.  Tänk också på att en gästanvändare som är medlem i gruppen kan se de andra medlemmarna i gruppen.

2. Om du vill skapa en åtkomstgranskning för den gruppen väljer du granskarna som medlemmar själva. Mer information finns i [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md).

3. Be varje gäst att granska sitt eget medlemskap. Som standard får varje gäst som accepterade en inbjudan ett e-postmeddelande från Azure AD med en länk till åtkomstgranskningen i organisationens åtkomstpanel. Azure AD har instruktioner för gäster om hur du [granskar åtkomst till grupper eller program](perform-access-review.md).  De gäster som inte accepterade sin inbjudan visas i granskningsresultaten som "Ej anmäld".

4. När granskarna har gett indata stoppar du åtkomstgranskningen. Mer information finns i [Slutför en åtkomstgranskning av grupper eller program](complete-access-review.md).

5. Ta bort gäståtkomst för gäster som nekades, inte slutförde granskningen eller inte tidigare accepterade inbjudan. Om några av gästerna är kontakter som har valts ut för att delta i granskningen eller om de inte tidigare har accepterat en inbjudan kan du inaktivera deras konton med hjälp av Azure-portalen eller PowerShell. Om gästen inte längre behöver åtkomst och inte är en kontakt kan du ta bort deras användarobjekt från katalogen med hjälp av Azure-portalen eller PowerShell för att ta bort gästanvändarobjektet.

## <a name="next-steps"></a>Nästa steg

[Skapa en åtkomstgranskning av grupper eller program](create-access-review.md)







