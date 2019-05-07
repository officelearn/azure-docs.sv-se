---
title: ta med fil
description: ta med fil
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: 364d4a11772e6bb72e2e258503f3cce49dc61453
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141671"
---
## <a name="create-one-or-more-access-reviews"></a>Skapa en eller flera åtkomstgranskningar

1. Klicka på **New** att skapa en ny åtkomstgranskning.

1. Namnet åtkomstgranskningen. Du kan också ge granskningen en beskrivning. Namn och beskrivning visas granskarna.

    ![Skapa en åtkomstgranskning - granska namn och beskrivning](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Ange den **startdatum**. Som standard en åtkomstgranskning sker en gång, startar samtidigt som den har skapats och slutar i en månad. Du kan ändra start- och slutdatum komma granska start i framtiden och senast hur många dagar som du vill.

    ![Skapa en åtkomstgranskning - Start- och slutdatum](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. För att göra åtkomsten granska återkommande, ändra den **frekvens** från **en gång** till **veckovisa**, **månatliga**,  **Kvartalsvis**, **årligen**, eller **delvis annually**. Använd den **varaktighet** skjutreglaget eller text om du vill definiera hur många dagar som varje granskning av serien med återkommande kommer att vara öppna för indata från granskare. Maximal varaktighet som du kan ange för den månatliga granskningen är till exempel 27 dagar att undvika överlappande granskningar.

1. Använd den **slutet** inställningen för att specificera hur du avslutar återkommande åtkomst granska serien. Serien kan sluta på tre sätt: det körs kontinuerligt för att starta granskningar på obestämd tid tills ett visst datum eller efter ett angivet antal förekomster har slutförts. Du administratören för en annan eller en annan Global administratör kan stoppa serien när du har skapat genom att ändra datumet i **inställningar**, så att den slutar på det datumet.

1. I den **användare** väljer du en eller flera roller som du vill granska medlemskap.

    ![Skapa en åtkomstgranskning - användare](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Att välja mer än en roll skapar flera åtkomstgranskningar. Till exempel skapar att välja fem roller fem separata åtkomstgranskningar.

    Om du skapar en åtkomstgranskning av Azure AD-roller, visas nedan ett exempel på medlemskapslistan för granskning.

    ![Skapa en åtkomstgranskning - granska rollmedlemskap](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Om du skapar en åtkomstgranskning i Azure-resursroller visas nedan ett exempel på medlemskapslistan för granskning.

    ![Skapa en åtkomstgranskning - granska rollmedlemskap](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. I den **granskare** väljer du en eller flera personer att granska alla användare. Eller du kan välja för att ha medlemmar granska sin egen åtkomst.

    ![Skapa en åtkomstgranskning - granskare](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Markerade användare** – Använd det här alternativet om du inte vet vilka som behöver åtkomst. Med det här alternativet kan du tilldela granskningen till en resursägaren eller gruppansvarig för att slutföra.
    - **Medlemmar (själv)** – Använd det här alternativet för att be användarna granska sin egen rolltilldelningar.

### <a name="upon-completion-settings"></a>Vid slutförande-inställningar

1. Om du vill ange vad som händer när en granskning är klar, expandera den **vid slutförande-inställningar** avsnittet.

    ![Vid slutförande-inställningar](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Om du vill att automatiskt ta bort åtkomst för användare som nekades anger **tillämpa automatiskt resultaten på resursen** till **aktivera**. Om du vill tillämpa resultaten manuellt när granskningen har slutförts kan du ange växeln **inaktivera**.

1. Använd den **granskaren inte svarar** listan för att ange vad som händer för användare som inte har setts över av den inom granskningsperioden. Den här inställningen påverkar inte användare som har granskats av granskarna manuellt. Om den slutliga granskare beslut neka, bort användarens åtkomst.

    - **Ingen ändring** -lämna användarens åtkomst har inte ändrats
    - **Ta bort åtkomst** -ta bort användarens åtkomst
    - **Bevilja åtkomst** -godkänna användarens åtkomst
    - **Anta rekommendationerna** – ta systemets rekommendation neka eller godkänna användaren är fortsatt åtkomst

### <a name="advanced-settings"></a>Avancerade inställningar

1. Om du vill ange ytterligare inställningar, expandera den **avancerade inställningar** avsnittet.

    ![Avancerade inställningar](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Ange **visa rekommendationer** till **aktivera** för att visa granskarna systemet rekommendationer baserat information om användaren åtkomst.

1. Ange **Kräv orsak vid godkännande** till **aktivera** att kräva att granskaren anger skäl för godkännande.

1. Ange **e-postaviseringar** till **aktivera** till har Azure AD skickar e-postmeddelanden till granskare när en åtkomstgranskning startar och till administratörer när en granskning är klar.

1. Ange **påminnelser** till **aktivera** har Azure AD skickar påminnelser om åtkomstgranskningar pågår till granskare som inte har slutfört sina granskningar.
