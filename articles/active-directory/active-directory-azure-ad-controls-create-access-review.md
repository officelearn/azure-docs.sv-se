---
title: Skapa en åtkomst-granskning för medlemmar i en grupp eller användare med åtkomst till ett program med Azure AD | Microsoft Docs
description: Lär dig hur du skapar en åtkomst-granskning för medlemmar i en grupp eller användare med åtkomst till ett program.
services: active-directory
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: billmath
ms.openlocfilehash: 784a461421420af403a43f944d6f63aef3ccc152
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Skapa en åtkomst-granskning av medlemmar i gruppen eller programåtkomst med Azure AD

Access-tilldelningar blir ”inaktiva” när användare har åtkomst till de inte behövs längre. Administratörer kan använda Azure Active Directory (Azure AD) att skapa en åtkomst-granskning för medlemmar i grupper eller användare som är tilldelade till ett program för att minska riskerna i samband med inaktuella åtkomst tilldelningar. Kan vara att spara tid genom att skapa återkommande åtkomst granskningar. Om du behöver granska regelbundet användare som har åtkomst till ett program eller medlemmar i en grupp, kan du definiera hur ofta dessa granskningar. Mer information om dessa scenarier finns [hantera användarnas åtkomst](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) och [hantera gästbehörighet](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Skapa en åtkomstgranskning

1. Som en global administratör, gå till den [åtkomst går igenom sidan](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), och välj **program**.

2. Välj det program som innehåller granska åtkomstkontroll du vill skapa. **Standardprogrammet** är alltid finns, eller så kan du skapa ett annat program. Exempelvis kan du ha ett program för varje kompatibilitet eller företagsmål.

3. Programmet och välj **kontroller**, och välj sedan **Lägg till** lägga till en kontroll.

4. Namnet åtkomst granska. Du kan också ge granskningen en beskrivning. Namn och beskrivning visas till granskare.

5. Ange startdatum. Som standard en åtkomst-granskning inträffar en gång, startar samtidigt som den har skapats och det avslutas med en månad. Du kan ändra start och slutdatum komma granska start i framtiden och senast många dagar som du vill.

6. Om du vill att granska åtkomst återkommande, ändra frekvensen från en gång för varje vecka, varje månad, kvartal eller år och använda skjutreglaget eller text för att definiera hur många dagar som varje granskning av serien med återkommande kommer runbe öppna för indata från granskare. Till exempel Tdet maximal varaktighet för du kan ange för en månatlig granska 27 dagar att undvika överlappande granskningar. 

7.  Serien med återkommande granska åtkomst kan avsluta 3 sätt: den kör kontinuerligt för att starta granskningar på obestämd tid, eller till ett visst datum eller efter ett angivet antal förekomster har slutförts. Du eller en annan global administratör kan sluta serien när den har skapats genom att ändra datumet i inställningar, så att den slutar på det datumet.

8. Åtkomst granskningar kan vara medlemmar i en grupp eller användare som har tilldelats ett program. Du kan ytterligare omfång åtkomsten för endast granskning granskning gästanvändare som är medlemmar (eller tilldelade till appen), i stället för att granska alla användare som är medlemmar eller som har åtkomst till programmet.

9. Välj en eller flera personer att granska alla användare i omfånget. Eller så kan du välja för att ha medlemmar granska sina egna åtkomst. Om resursen är en grupp, kan du be gruppen ägare att granska. Du kan också kräva att granskare ange en anledning när de godkänner åtkomst.

10. Om du vill tillämpa resultaten manuellt när granskning är klar klickar du på **starta**.  Annars nästa avsnitt förklarar hur du konfigurerar granska för att automatiskt tillämpa.

### <a name="configuring-an-access-review-with-auto-apply"></a>Konfigurera en åtkomst-granskning med auto-apply

1.  Expandera menyn för på slutförande inställningarna gäller andsettings och aktivera automatisk resultat för resurs. 

2.  I fall där användare inte kontrollerades av granskaren inom omprövningsperioden, har du åtkomst granska ta systemets rekommendation (om aktiverat) på Neka/godkänna användarens fortsatt åtkomst, eller lämna åtkomsten oförändrade eller ta bort deras åtkomst. Detta påverkar inte användare som har granskats granskare manuellt – om den slutliga granskare beslut neka, kan användarens åtkomst kommer att tas bort.

3.  Visa rekommendationerna i avancerade inställningar måste aktiveras om du vill aktivera alternativet för att göra rekommendationer inte bör granskare svarar.
 
4.  Klicka slutligen på **starta**.

Baserat på dina val i på slutförande inställningarna-tillämpa automatiskt kan utföras efter den granska slutdatum eller när du manuellt stoppa granskningen. Status för granska kommer att ändras från slutförd via mellanliggande tillstånd till exempel använda och slutligen till tillstånd tillämpats. Du bör se nekade användare, om sådant finns, tas bort från grupptilldelning för medlemskap eller en app några minuter.


## <a name="manage-the-access-review"></a>Hantera åtkomst-granskning

Standard skickar Azure AD ett e-postmeddelande till granskare kort efter granskningen startar. Om du inte väljer att låta Azure AD skicka e-postmeddelandet måste du meddela granskarna att en åtkomst-granskning väntar de är klara. Du kan visa dem instruktioner för hur du [granska åtkomst](active-directory-azure-ad-controls-perform-access-review.md). Om din granskning är för gäster att granska sina egna åtkomst kan visa dem instruktioner för hur du [granska din egen åtkomst](active-directory-azure-ad-controls-perform-access-review.md).

Om några av granskarna gäster, meddelas gäster via e-post endast om de har redan accepterats deras inbjudan.

För att hantera en serie åtkomst granskningar, navigera till access granska från **kontroller**, och du ska hitta kommande förekomster i schemalagda granskning och redigera slutdatumet eller Lägg till/ta bort granskare därefter. 

Du kan följa förloppet när granskare Slutför granskningarna i instrumentpanelen i Azure AD den **åtkomst granskar** avsnitt. Ingen behörighet har ändrats i katalogen tills [granskningen har slutförts](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Nästa steg

När en åtkomst-granskning har börjat skickar Azure AD automatiskt granskare ett e-postmeddelande som uppmanar dem att granska åtkomst. Om en användare inte har fått ett e-postmeddelande, kan du skicka dem instruktioner för hur du [granska åtkomst](active-directory-azure-ad-controls-perform-access-review.md). 

Om det här är en enstaka granskning när omprövningsperioden åtkomst är över eller administratören stoppar åtkomst granskning, följ instruktionerna i [slutföra en åtkomst-granskning](active-directory-azure-ad-controls-complete-access-review.md) finns och att tillämpa resultaten.  

Om detta är en serie för granskning, gå till **granska historiken** på åtkomst serien granskningssidan att välja en slutförd åtkomst granskning.  Kommande granskningar visas under **schemalagda granska**, där du kan redigera varaktighet, och Lägg till eller ta bort granskare för enskilda granskningar.


