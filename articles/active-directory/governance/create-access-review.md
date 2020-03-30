---
title: Skapa en åtkomstgranskning av grupper & program - Azure AD
description: Lär dig hur du skapar en åtkomstgranskning av gruppmedlemmar eller programåtkomst i Azure Active Directory-åtkomstgranskningar.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3b8f2cf7db474f2a67ebaed818a0a440a195326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128848"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Skapa en åtkomstgranskning av grupper och program i Azure AD-åtkomstgranskningar

Tillgången till grupper och applikationer för anställda och gäster ändras med tiden. För att minska risken för inaktuella åtkomsttilldelningar kan administratörer använda Azure Active Directory (Azure AD) för att skapa åtkomstgranskningar för gruppmedlemmar eller programåtkomst. Om du behöver granska åtkomsten rutinmässigt kan du också skapa återkommande åtkomstgranskningar. Mer information om dessa scenarier finns i [Hantera användaråtkomst](manage-user-access-with-access-reviews.md) och [Hantera gäståtkomst](manage-guest-access-with-access-reviews.md).

I den här artikeln beskrivs hur du skapar en eller flera åtkomstgranskningar för gruppmedlemmar eller programåtkomst.

## <a name="prerequisites"></a>Krav

- Azure AD Premium P2
- Global administratör eller användaradministratör

Mer information finns i [Licenskrav](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Skapa en eller flera åtkomstgranskningar

1. Logga in på Azure-portalen och öppna [sidan Identitetsstyrning](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Klicka på **Access-recensioner**på den vänstra menyn .

1. Klicka på **Ny åtkomstgranskning** om du vill skapa en ny åtkomstgranskning.

    ![Fönstret Granska access i fönstret Identitetsstyrning](./media/create-access-review/access-reviews.png)

1. Namnge åtkomstgranskningen. Du kan också ge granskningen en beskrivning. Namnet och beskrivningen visas för granskarna.

    ![Skapa en åtkomstgranskning - Granska namn och beskrivning](./media/create-access-review/name-description.png)

1. Ange **startdatum**. Som standard inträffar en åtkomstgranskning en gång, startar samtidigt som den skapas och den slutar om en månad. Du kan ändra start- och slutdatum för att få en åtkomstgranskning start i framtiden och pågå hur många dagar du vill.

    ![Skapa en åtkomstgranskning - Start- och slutdatum](./media/create-access-review/start-end-dates.png)

1. Om du vill att åtkomstgranskningen ska upprepas ändrar du inställningen **Frekvens** från **En gång** till **Varje vecka,** **Månadsvis**, **Kvartal,** **Halvår**eller **Årligen**. Använd skjutreglaget **Varaktighet** eller text för att definiera hur många dagar varje granskning av den återkommande serien ska vara öppen för indata från granskare. Den maximala varaktighet som du kan ange för en månatlig granskning är till exempel 27 dagar för att undvika överlappande granskningar.

1. Använd inställningen **Slut** för att ange hur du avslutar serien för återkommande åtkomstgranskning. Serien kan sluta på tre sätt: den körs kontinuerligt för att starta granskningar på obestämd tid, tills ett visst datum eller efter att ett visst antal förekomster har slutförts. Du, en annan användaradministratör eller en annan global administratör kan stoppa serien när du har skapat den genom att ändra datumet i **Inställningar**så att det slutar på det datumet.

1. I avsnittet **Användare** anger du vilka användare som åtkomstgranskningen gäller för. Åtkomstgranskningar kan vara för medlemmarna i en grupp eller för användare som har tilldelats ett program. Du kan ytterligare begränsa åtkomstgranskningen så att den endast granskar gästanvändarna som är medlemmar (eller har tilldelats programmet) i stället för att granska alla användare som är medlemmar eller som har åtkomst till programmet.

    ![Skapa en åtkomstgranskning – användare](./media/create-access-review/users.png)

1. I avsnittet **Grupp** väljer du en eller flera grupper som du vill granska medlemskap i.

    > [!NOTE]
    > Om du väljer mer än en grupp skapas flera åtkomstgranskningar. Om du till exempel väljer fem grupper skapas fem separata åtkomstgranskningar.
    
    ![Skapa en åtkomstgranskning - Välj grupp](./media/create-access-review/select-group.png)

1. I avsnittet **Program** (om du har valt **Tilldelad till ett program** i steg 8) väljer du de program som du vill granska åtkomsten till.

    > [!NOTE]
    > Om du väljer mer än ett program skapas flera åtkomstgranskningar. Om du till exempel väljer fem program skapas fem separata åtkomstgranskningar.
    
    ![Skapa en åtkomstgranskning - Välj program](./media/create-access-review/select-application.png)

1. I avsnittet **Granskare** väljer du antingen en eller flera personer som ska granska alla användare i omfånget. Eller så kan du välja att låta medlemmarna granska sin egen åtkomst. Om resursen är en grupp kan du be gruppägarna att granska. Du kan också kräva att granskarna anger en orsak när de godkänner åtkomst.

    ![Skapa en åtkomstgranskning – granskare](./media/create-access-review/reviewers.png)

1. Välj det program du vill använda i avsnittet **Program.** **Standardprogram** finns alltid.

    ![Skapa en åtkomstgranskning - Program](./media/create-access-review/programs.png)

    Du kan förenkla hur du spårar och samlar in åtkomstrecensioner för olika ändamål genom att organisera dem i program. Varje åtkomstgranskning kan länkas till ett program. När du sedan förbereder rapporter för en revisor kan du fokusera på åtkomstgranskningarna i omfånget för ett visst initiativ. Program och åtkomstgranskningsresultat är synliga för användare i rollen Global administratör, Användaradministratör, Säkerhetsadministratör eller Säkerhetsläsare.

    Om du vill se en lista över program går du till sidan för åtkomstgranskningar och väljer **Program**. Om du är i en global administratörs- eller användaradministratörsroll kan du skapa ytterligare program. Du kan till exempel välja att ha ett program för varje efterlevnadsinitiativ eller affärsmål. Om du inte längre behöver ett program och det inte har några kontroller kopplade till det, kan du ta bort det.

### <a name="upon-completion-settings"></a>Efter slutförande inställningar

1. Om du vill ange vad som ska hända när en granskning är klar expanderar du avsnittet **Efter slutförandeinställningar.**

    ![Skapa en åtkomstgranskning - Inställningar för efter slutförande](./media/create-access-review/upon-completion-settings.png)

1. Om du vill ta bort automatiskt, åtkomst för användare som nekades, anger **du Automatiskt tillämpa resultat på resursen** för **Aktivera**. Om du vill använda resultaten manuellt när granskningen är klar ställer du in växeln **på Inaktivera**.

1. Använd listan **Bör granskare svarar inte** för att ange vad som händer för användare som inte granskas av granskaren inom granskningsperioden. Den här inställningen påverkar inte användare som har granskats av granskarna manuellt. Om den slutliga granskarens beslut är Neka tas användarens åtkomst bort.

    - **Ingen ändring** - Lämna användarens åtkomst oförändrad
    - **Ta bort åtkomst** - Ta bort användarens åtkomst
    - **Godkänn åtkomst** - Godkänn användarens åtkomst
    - **Ta rekommendationer** - Ta systemets rekommendation om att neka eller godkänna användarens fortsatta åtkomst

### <a name="advanced-settings"></a>Avancerade inställningar

1. Om du vill ange ytterligare inställningar expanderar du avsnittet **Avancerade inställningar.**

    ![Skapa en åtkomstgranskning - Avancerade inställningar](./media/create-access-review/advanced-settings.png)

1. Ange **Visa rekommendationer** **som ska aktiveras** för att visa granskarna systemrekommendationerna baserat på användarens åtkomstinformation.

1. Ange **Kräv orsak vid godkännande** för att **aktivera** för att kräva att granskaren tillhandahåller en orsak till godkännande.

1. Ange **e-postaviseringar** **så** att Azure AD skickar e-postmeddelanden till granskare när en åtkomstgranskning startar och till administratörer när en granskning är klar.

1. Ange **påminnelser** **för** att azure AD ska skicka påminnelser om åtkomstgranskningar som pågår till granskare som inte har slutfört sin granskning.

    Som standard skickar Azure AD automatiskt en påminnelse när halva tiden före slutdatumet har gått till granskarna som ännu inte har svarat.

## <a name="start-the-access-review"></a>Starta åtkomstgranskningen

När du har angett inställningarna för en åtkomstgranskning klickar du på **Start**. Åtkomstgranskningen visas i listan med en indikator på dess status.

![Lista över åtkomstgranskningar och deras status](./media/create-access-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter att granskningen har startat. Om du väljer att inte låta Azure AD skicka e-postmeddelandet måste du informera granskarna om att en åtkomstgranskning väntar på att de ska slutföras. Du kan visa dem instruktionerna för hur du [granskar åtkomsten till grupper eller program](perform-access-review.md). Om din recension är för gäster att granska sin egen tillgång, visa dem instruktionerna för hur du [granskar åtkomst för dig själv till grupper eller applikationer](review-your-access.md).

Om du har tilldelat gäster som granskare och de inte har accepterat inbjudan, kommer de inte att få ett e-postmeddelande från åtkomstrecensioner eftersom de först måste acceptera inbjudan innan de granskar.

## <a name="access-review-status-table"></a>Statustabell för åtkomstgranskning

| Status | Definition |
|--------|------------|
|Inte startad | Granskning har skapats, användaridentifiering väntar på att starta. |
|Initierar   | Användaridentifiering pågår för att identifiera alla användare som ingår i granskningen. |
|Startar | Granskningen startar. Om e-postmeddelanden är aktiverade skickas e-postmeddelanden till granskare. |
|InProgress (InProgress) | Granskningen har startat. Om e-postmeddelanden är aktiverade har e-postmeddelanden skickats till granskare. Granskare kan skicka beslut fram till förfallodatumet. |
|Slutföra | Granskningen slutförs och e-postmeddelanden skickas till granskningsägaren. |
|Granskning av automatiskt | Granskningen är i ett system som granskar. Systemet registrerar beslut för användare som inte granskades baserat på rekommendationer eller förkonfigurerade beslut. |
|Visas automatiskt | Beslut har registrerats av systemet för alla användare som inte har granskats. Granskningen är klar att fortsätta till **Tillämpa** om Automatisk tillämpning är aktiverat. |
|Tillämpa | Åtkomsten för användare som har godkänts kommer inte att ändras. |
|Tillämpat | Nekade användare, om sådana finns, har tagits bort från resursen eller katalogen. |

## <a name="create-reviews-via-apis"></a>Skapa recensioner via API:er

Du kan också skapa åtkomstgranskningar med API:er. Vad du gör för att hantera åtkomstgranskningar av grupper och programanvändare i Azure-portalen kan också göras med hjälp av Microsoft Graph API:er. Mer information finns i [API-referensen för Azure AD-åtkomstgranskningar](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Ett kodexempel finns i [Exempel på att hämta Azure AD-åtkomstgranskningar via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomsten till grupper eller program](perform-access-review.md)
- [Granska åtkomsten för dig själv till grupper eller program](review-your-access.md)
- [Slutföra en åtkomstgranskning av grupper eller program](complete-access-review.md)
