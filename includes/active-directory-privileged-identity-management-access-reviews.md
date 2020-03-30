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
ms.openlocfilehash: d791c4ba46587ac5709d72cb31bc76f087118b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67476256"
---
## <a name="create-one-or-more-access-reviews"></a>Skapa en eller flera åtkomstgranskningar

1. Klicka på **Ny** om du vill skapa en ny åtkomstgranskning.

1. Namnge åtkomstgranskningen. Du kan också ge granskningen en beskrivning. Namnet och beskrivningen visas för granskarna.

    ![Skapa en åtkomstgranskning - Granska namn och beskrivning](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Ange **startdatum**. Som standard inträffar en åtkomstgranskning en gång, startar samtidigt som den skapas och den slutar om en månad. Du kan ändra start- och slutdatum för att få en åtkomstgranskning start i framtiden och pågå hur många dagar du vill.

    ![Startdatum, frekvens, varaktighet,, antal tider och slutdatum](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Om du vill att åtkomstgranskningen ska upprepas ändrar du inställningen **Frekvens** från **en gång** till **varje vecka,** **månadsvis** **,** **kvartalsvis**, eller **Halvårsvis**. Använd skjutreglaget **Varaktighet** eller text för att definiera hur många dagar varje granskning av den återkommande serien ska vara öppen för indata från granskare. Den maximala varaktighet som du kan ange för en månatlig granskning är till exempel 27 dagar för att undvika överlappande granskningar.

1. Använd inställningen **Slut** för att ange hur du avslutar serien för återkommande åtkomstgranskning. Serien kan sluta på tre sätt: den körs kontinuerligt för att starta granskningar på obestämd tid, tills ett visst datum eller efter att ett visst antal förekomster har slutförts. Du, en annan användaradministratör eller en annan global administratör kan stoppa serien när du har skapat den genom att ändra datumet i **Inställningar**så att det slutar på det datumet.

1. I avsnittet **Användare** väljer du en eller flera roller som du vill granska medlemskap i.

    ![Användares omfattning att granska rollmedlemskap i](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Om du väljer mer än en roll skapas flera åtkomstgranskningar. Om du till exempel väljer fem roller skapas fem separata åtkomstgranskningar.

    Om du skapar en åtkomstgranskning av Azure AD-roller visas följande ett exempel på listan Granska medlemskap.

    ![Granska medlemsfönstret med Azure AD-roller som du kan välja](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Om du skapar en åtkomstgranskning av Azure-resursroller visas följande ett exempel på listan Granska medlemskap.

    ![Granska medlemsfönstret med Azure-resursroller som du kan välja](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. I avsnittet **Granskare** väljer du en eller flera personer som ska granska alla användare. Eller så kan du välja att låta medlemmarna granska sin egen åtkomst.

    ![Granskare lista över valda användare eller medlemmar (själv)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Valda användare** – Använd det här alternativet när du inte vet vem som behöver åtkomst. Med det här alternativet kan du tilldela granskningen till en resursägare eller gruppchef för att slutföra.
    - **Medlemmar (själv)** - Använd det här alternativet om du vill att användarna ska granska sina egna rolltilldelningar.

### <a name="upon-completion-settings"></a>Efter slutförande inställningar

1. Om du vill ange vad som ska hända när en granskning är klar expanderar du avsnittet **Efter slutförandeinställningar.**

    ![Efter avslutad inställningar för att automatiskt tillämpa och bör granska inte svara](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Om du vill ta bort åtkomst automatiskt för användare som nekades anger du **Auto tillämpa resultat på resursen** för **Aktivera**. Om du vill använda resultaten manuellt när granskningen är klar ställer du in växeln **på Inaktivera**.

1. Använd listan **Bör granskare svarar inte** för att ange vad som händer för användare som inte granskas av granskaren inom granskningsperioden. Den här inställningen påverkar inte användare som har granskats av granskarna manuellt. Om den slutliga granskarens beslut är Neka tas användarens åtkomst bort.

    - **Ingen ändring** - Lämna användarens åtkomst oförändrad
    - **Ta bort åtkomst** - Ta bort användarens åtkomst
    - **Godkänn åtkomst** - Godkänn användarens åtkomst
    - **Ta rekommendationer** - Ta systemets rekommendation om att neka eller godkänna användarens fortsatta åtkomst

### <a name="advanced-settings"></a>Avancerade inställningar

1. Om du vill ange ytterligare inställningar expanderar du avsnittet **Avancerade inställningar.**

    ![Avancerade inställningar för att visa rekommendationer, kräver orsak vid godkännande, e-postmeddelanden och påminnelser](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Ange **Visa rekommendationer** **som ska aktiveras** för att visa granskarna systemrekommendationerna baserat på användarens åtkomstinformation.

1. Ange **Kräv orsak vid godkännande** för att **aktivera** för att kräva att granskaren tillhandahåller en orsak till godkännande.

1. Ange **e-postaviseringar** **så** att Azure AD skickar e-postmeddelanden till granskare när en åtkomstgranskning startar och till administratörer när en granskning är klar.

1. Ange **påminnelser** **för** att azure AD ska skicka påminnelser om åtkomstgranskningar som pågår till granskare som inte har slutfört sin granskning.
