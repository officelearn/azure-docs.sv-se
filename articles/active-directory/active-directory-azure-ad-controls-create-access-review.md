---
title: Skapa en åtkomstgranskning för medlemmar i en grupp eller användare med åtkomst till ett program med Azure AD | Microsoft Docs
description: Lär dig mer om att skapa en åtkomstgranskning för medlemmar i en grupp eller användare med åtkomst till ett program.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 853d8f09a94e46db218553500a50dc4ef1ec3d23
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448279"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Skapa en åtkomstgranskning för medlemmar i gruppen eller programmet åtkomst med Azure AD

Åtkomsttilldelningar blir ”inaktuell” när användare har åtkomst inte behöver de längre. Administratörer kan använda Azure Active Directory (Azure AD) skapar du en åtkomstgranskning för medlemmar i gruppen eller användare som tilldelats till ett program för att minska riskerna med inaktuella åtkomsttilldelningar. Skapa återkommande åtkomstgranskningar kan vara tidsbesparande. Om du vill granska regelbundet användare som har åtkomst till ett program eller är medlemmar i en grupp kan du definiera frekvensen för dessa granskningar. Mer information om dessa scenarier finns i [hantera användaråtkomst](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) och [hantera gäståtkomst](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Skapa en åtkomstgranskning

1. Som en global administratör eller Användaradministratör konto går du till den [sidan med åtkomstgranskningar](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), och välj **program**.

2. Välj det program som innehåller åtkomstkontroll för granskning som du vill skapa. **Standardprogrammet** är alltid finns, eller så kan du skapa ett annat program. Du kan till exempel välja att ha ett program för varje efterlevnad eller företagsmål.

3. Starta programmet och välj **kontroller**, och välj sedan **Lägg till** Lägg till en kontroll.

4. Namnet åtkomstgranskningen. Du kan också ge granskningen en beskrivning. Namn och beskrivning visas granskarna.

5. Ange startdatum. Som standard en åtkomstgranskning sker en gång, startar samtidigt som den har skapats och slutar i en månad. Du kan ändra start- och slutdatum komma granska start i framtiden och senast hur många dagar som du vill.

6. Ändra hur ofta från en gång för varje vecka, månadsvis, Kvartalsvis eller årligen för att göra åtkomsten granska återkommande, och Använd skjutreglaget eller text om du vill definiera hur många dagar varje granskning av serien med återkommande kommer att vara öppen för indata från granskare. Till exempel är maximal varaktighet för du kan ange för månatliga granskningen 27 dagar att undvika överlappande granskningar. 

7.  Återkommande åtkomstgranskningsserien kan avslutas med 3 sätt: det körs kontinuerligt för att starta granskningar på obestämd tid tills ett visst datum eller efter ett angivet antal förekomster har slutförts. Du ett annat användarkonto-administratör eller en annan global administratör kan stoppa serien när du har skapat genom att ändra datumet i inställningar, så att den slutar på det datumet.

8. Åtkomstgranskningar kan vara medlemmar i en grupp eller användare som har tilldelats till ett program. Du kan ytterligare begränsa åtkomsten endast granskning till granskning gästanvändare som är medlemmar (eller tilldelade till appen), i stället för att granska alla användare som är medlemmar eller som har åtkomst till programmet.

9. Välj en eller flera personer att granska alla användare inom området. Eller du kan välja för att ha medlemmar granska sin egen åtkomst. Om resursen är en grupp, kan du be gruppägare att granska. Du kan också kräva att granskarna ska ange en anledning när de godkänner åtkomst.

10. Om du vill tillämpa resultaten manuellt när granskningen har slutförts klickar du på **starta**.  I annat fall i nästa avsnitt förklarar hur du konfigurerar granskning för att automatiskt tillämpa.

### <a name="configuring-an-access-review-with-auto-apply"></a>Konfigurera en åtkomstgranskning med auto-apply

1.  Expandera menyn för vid slutförande-inställningar och aktivera automatisk gäller resultat till resursen. 

2.  I fall där användarna inte har granskas av granskaren inom granskningsperioden, du kan ha åtkomstgranskning ta systemets rekommendation (om aktiverat) på Neka/godkännande av användarens fortsatt åtkomst, lämna deras åtkomst har inte ändrats eller ta bort deras åtkomst. Detta påverkar inte användare som har granskats av granskarna manuellt – om den slutliga granskare beslut är Neka, användarens åtkomst kommer att tas bort.

3.  Om du vill aktivera alternativet att anta rekommendationerna inte bör granskare svarar, expandera avancerade inställningar och aktivera visa rekommendationer.
 
4.  Klicka slutligen på **starta**.

Baserat på dina val i vid slutförande-inställningar, autotillämpad ska köras efter slutet granskningsdatum eller när du manuellt stoppa granskningen. Status för granskningen kommer att ändras från slutförd via mellanliggande tillstånd, till exempel använda och slutligen till tillstånd tillämpas. Du bör förvänta dig att se nekade användare, om sådant finns, tas bort från grupptilldelning för medlemskap eller Maps-app på några få minuter.


## <a name="manage-the-access-review"></a>Hantera åtkomstgranskningen

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter det att granskningen startar. Om du väljer att inte har Azure AD skickar e-postmeddelandet, måste du meddela granskarna att en åtkomstgranskning väntar dem att slutföra. Visa instruktioner för hur du [granska åtkomst](active-directory-azure-ad-controls-perform-access-review.md). Om din granskning är för gäster att granska sin egen åtkomst, visas instruktioner för hur du [granska dina egna åtkomst](active-directory-azure-ad-controls-perform-access-review.md).

Om några av granskarna gäster, Gäster får ett meddelande via e-post endast om de redan har accepterat sin inbjudan.

För att hantera en serie med åtkomstgranskningar, navigera till åtkomstgranskning från **kontroller**, och du ska hitta kommande förekomster i schemalagd granskningar och redigera slutdatumet eller Lägg till/ta bort granskare i enlighet med detta. 

Du kan följa förloppet när granskarna har slutfört sina granskningar i Azure AD-instrumentpanelen i den **Åtkomstgranskningar** avsnittet. Ingen behörighet har ändrats i katalogen tills [granskningen har slutförts](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Nästa steg

När en åtkomstgranskning har startat har Azure AD skickar granskare automatiskt ett e-postmeddelande som uppmanar dem att granska åtkomst. Om en användare inte får ett e-postmeddelande kan du skicka dem instruktioner för hur du [granska åtkomst](active-directory-azure-ad-controls-perform-access-review.md). 

Om det här är en enstaka granskning efter åtkomstgranskningsperiod är över eller administratören stoppar åtkomstgranskning, Följ stegen i [Slutför en åtkomstgranskning](active-directory-azure-ad-controls-complete-access-review.md) att se och tillämpa resultaten.  

Om det här är en granskningsserien gå sedan till **granska historiken** på Granska serien visas, och välj en slutförd åtkomstgranskning.  Kommande granskningar visas under **schemalagd granskning**, där du kan redigera varaktighet, och lägga till eller ta bort granskare för enskilda granskning.
