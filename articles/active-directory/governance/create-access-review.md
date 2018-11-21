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
ms.component: compliance
ms.date: 11/15/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: c1ae6e8c67ab42207aea73932bb2e887db727f16
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263392"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Skapa en åtkomstgranskning för medlemmar i gruppen eller programmet åtkomst med Azure AD

Åtkomst till grupper och program för anställda och gäster ändras med tiden. För att minska riskerna med inaktuella åtkomsttilldelningar kan kan administratörer använda Azure Active Directory (AD Azure) för att skapa åtkomstgranskningar för gruppmedlemmar eller programåtkomst. Om du vill granska regelbundet tillgång kan skapa du också återkommande åtkomstgranskningar. Mer information om dessa scenarier finns i [hantera användaråtkomst](manage-user-access-with-access-reviews.md) och [hantera gäståtkomst](manage-guest-access-with-access-reviews.md).

Den här artikeln beskriver hur du skapar en ny åtkomstgranskning för medlemmar i gruppen eller programåtkomst.

## <a name="prerequisites"></a>Förutsättningar

- [Åtkomstgranskningar aktiverat](access-reviews-overview.md)
- Global administratör eller kontoadministratör

## <a name="create-an-access-review"></a>Skapa en åtkomstgranskning

1. Logga in på Azure-portalen och öppna den [Åtkomstgranskningar sidan](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Klicka på **kontroller**.

1. Klicka på **ny åtkomstgranskning** att skapa en ny åtkomstgranskning.

    ![Åtkomstgranskning - kontroller](./media/create-access-review/controls.png)

1. Namnet åtkomstgranskningen. Du kan också ge granskningen en beskrivning. Namn och beskrivning visas granskarna.

    ![Skapa en åtkomstgranskning - granska namn och beskrivning](./media/create-access-review/name-description.png)

1. Ange den **startdatum**. Som standard en åtkomstgranskning sker en gång, startar samtidigt som den har skapats och slutar i en månad. Du kan ändra start- och slutdatum komma granska start i framtiden och senast hur många dagar som du vill.

    ![Skapa en åtkomstgranskning - Start- och slutdatum](./media/create-access-review/start-end-dates.png)

1. För att göra åtkomsten granska återkommande, ändra den **frekvens** från **en gång** till **veckovisa**, **månatliga**,  **Kvartalsvis** eller **årligen**, och använda den **varaktighet** skjutreglaget eller text om du vill definiera hur många dagar som varje granskning av serien med återkommande kommer att vara öppna för indata från granskare. Maximal varaktighet som du kan ange för den månatliga granskningen är till exempel 27 dagar att undvika överlappande granskningar.

1. Använd den **slutet** inställningen för att specificera hur du avslutar återkommande åtkomst granska serien. Serien kan sluta på tre sätt: det körs kontinuerligt för att starta granskningar på obestämd tid tills ett visst datum eller efter ett angivet antal förekomster har slutförts. Du ett annat användarkonto-administratör eller en annan Global administratör kan stoppa serien när du har skapat genom att ändra datumet i **inställningar**, så att den slutar på det datumet.

1. I den **användare** anger du de användare som använder granska gäller för. Åtkomstgranskningar kan vara medlemmar i en grupp eller användare som har tilldelats till ett program. Du kan ytterligare begränsa åtkomsten endast granskning till granskning gästanvändare som är medlemmar (eller tilldelats programmet), i stället för att granska alla användare som är medlemmar eller som har åtkomst till programmet.

    ![Skapa en åtkomstgranskning - användare](./media/create-access-review/users.png)

1. I den **granskare** väljer du en eller flera personer att granska alla användare inom området. Eller du kan välja för att ha medlemmar granska sin egen åtkomst. Om resursen är en grupp, kan du be gruppägare att granska. Du kan också kräva att granskarna ska ange en anledning när de godkänner åtkomst.

    ![Skapa en åtkomstgranskning - granskare](./media/create-access-review/reviewers.png)

1. I den **program** väljer du det program som du vill använda. Du kan förenkla spåra och samla in åtkomstgranskningar för olika ändamål genom att ordna dem i program. **Standardprogrammet** är alltid finns, eller så kan du skapa ett annat program. Du kan till exempel välja att ha ett program för varje efterlevnad eller företagsmål.

    ![Skapa en åtkomstgranskning - program](./media/create-access-review/programs.png)

### <a name="upon-completion-settings"></a>Vid slutförande-inställningar

1. Om du vill ange vad som händer när en granskning är klar, expandera den **vid slutförande-inställningar** avsnittet.

    ![Vid slutförande-inställningar](./media/create-access-review/upon-completion-settings.png)

1. Om du vill att automatiskt ta bort åtkomst för användare som nekades anger **tillämpa automatiskt resultaten på resursen** till **aktivera**. Om du vill tillämpa resultaten manuellt när granskningen har slutförts kan du ange växeln **inaktivera**.

1. Använd den **granskaren inte svarar** listan för att ange vad som händer för användare som inte har setts över av den inom granskningsperioden. Den här inställningen påverkar inte användare som har granskats av granskarna manuellt. Om den slutliga granskare beslut neka, bort användarens åtkomst.

    - **Ingen ändring** -lämna användarens åtkomst har inte ändrats
    - **Ta bort programåtkomst** -ta bort användarens åtkomst
    - **Bevilja åtkomst** -godkänna användarens åtkomst
    - **Anta rekommendationerna** – ta systemets rekommendation neka eller godkänna användaren är fortsatt åtkomst

### <a name="advanced-settings"></a>Avancerade inställningar

1. Om du vill ange ytterligare inställningar, expandera den **avancerade inställningar** avsnittet.

    ![Avancerade inställningar](./media/create-access-review/advanced-settings.png)

1. Ange **visa rekommendationer** till **aktivera** för att visa granskarna systemet rekommendationer baserat information om användaren åtkomst.

1. Ange **Kräv orsak vid godkännande** till **aktivera** att kräva att granskaren anger skäl för godkännande.

1. Ange **e-postaviseringar** till **aktivera** till har Azure AD skickar e-postmeddelanden till granskare när en åtkomstgranskning startar och till administratörer när en granskning är klar.

1. Ange **påminnelser** till **aktivera** har Azure AD skickar påminnelser om åtkomstgranskningar pågår till granskare som inte har slutfört sina granskningar.

## <a name="start-the-access-review"></a>Starta åtkomstgranskningen

När du har angett inställningarna för en åtkomstgranskning, klickar du på **starta**.

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter det att granskningen startar. Om du väljer att inte har Azure AD skickar e-postmeddelandet, måste du meddela granskarna att en åtkomstgranskning väntar dem att slutföra. Visa instruktioner för hur du [granska åtkomst](perform-access-review.md). Om din granskning är för gäster att granska sin egen åtkomst, visas instruktioner för hur du [granska dina egna åtkomst](perform-access-review.md).

Om några av granskarna gäster, Gäster får ett meddelande via e-post endast om de redan har accepterat sin inbjudan.

## <a name="manage-the-access-review"></a>Hantera åtkomstgranskningen

Du kan följa förloppet när granskarna har slutfört sina granskningar i Azure AD-instrumentpanelen i den **Åtkomstgranskningar** avsnittet. Ingen behörighet har ändrats i katalogen tills [granskningen har slutförts](complete-access-review.md).

Om det här är en enstaka granskning efter åtkomstgranskningsperiod är över eller administratören stoppar åtkomstgranskning, Följ stegen i [Slutför en åtkomstgranskning](complete-access-review.md) att se och tillämpa resultaten.  

För att hantera en serie med åtkomstgranskningar, navigera till åtkomstgranskning från **kontroller**, och du ska hitta kommande förekomster i schemalagd granskningar och redigera slutdatumet eller Lägg till/ta bort granskare i enlighet med detta. 

Baserat på dina val i vid slutförande-inställningar, autotillämpad ska köras efter slutet granskningsdatum eller när du manuellt stoppa granskningen. Status för granskningen kommer att ändras från slutförd via mellanliggande tillstånd, till exempel använda och slutligen till tillstånd tillämpas. Du bör förvänta dig att se nekade användare, om sådant finns, tas bort från grupptilldelning för medlemskap eller ett program på några få minuter.

## <a name="create-reviews-via-apis"></a>Skapa granskningar via API: er

Du kan också skapa åtkomstgranskningar med API: er. Vad du gör för att hantera åtkomst går igenom i grupper och användare i Azure-portalen kan också göras med hjälp av Microsoft Graph API: er. Mer information finns i den [Azure AD-Åtkomstgranskningar API-referens](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/accessreviews_root). Finns ett kodexempel i [exempel för att hämta Azure AD-Åtkomstgranskningar via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomstgranskning med Azure AD-Åtkomstgranskningar](perform-access-review.md)
- [Slutför en åtkomstgranskning för medlemmar i en grupp eller användare åtkomst till ett program i Azure AD](complete-access-review.md)
