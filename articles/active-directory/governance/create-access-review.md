---
title: Skapa en åtkomst granskning av grupper & program – Azure AD
description: Lär dig hur du skapar en åtkomst granskning av grupp medlemmar eller program åtkomst i Azure Active Directory åtkomst granskningar.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/15/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18f0627b809f56b813052cc763e6ff961f31aa02
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697143"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Skapa en åtkomst granskning av grupper och program i åtkomst granskningar för Azure AD

Åtkomst till grupper och program för medarbetare och gäster förändras över tid. För att minska risken som är kopplad till inaktuella åtkomst tilldelningar kan administratörer använda Azure Active Directory (Azure AD) för att skapa åtkomst granskningar för grupp medlemmar eller program åtkomst. Om du behöver granska åtkomst rutinmässigt kan du också skapa återkommande åtkomst granskningar. Mer information om dessa scenarier finns i [hantera användar åtkomst](manage-user-access-with-access-reviews.md) och [Hantera gäst åtkomst](manage-guest-access-with-access-reviews.md).

Du kan se en snabb video som talar om hur du aktiverar åtkomst granskningar:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Den här artikeln beskriver hur du skapar en eller flera åtkomst granskningar för grupp medlemmar eller program åtkomst.

## <a name="prerequisites"></a>Förutsättningar

- Azure AD Premium P2
- Global administratör eller användar administratör
- Förhandsgranskningsvyn Resurs ägare i Microsoft 365 grupper kan skapa recensioner i de Microsoft 365 grupper som de äger
- Förhandsgranskningsvyn Resurs ägare till Azure AD-säkerhetsgrupper kan skapa recensioner i de Azure AD-säkerhetsgrupper som de äger

Mer information finns i [licens krav](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Skapa en eller flera åtkomst granskningar

1. Logga in på Azure Portal och öppna [sidan identitets styrning](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Klicka på **åtkomst granskningar** på den vänstra menyn.

1. Klicka på **ny åtkomst granskning** för att skapa en ny åtkomst granskning.

    ![Fönstret åtkomst granskningar i identitets styrning](./media/create-access-review/access-reviews.png)

1. Ge åtkomst granskningen namnet. Du kan också ange en beskrivning av granskningen. Namnet och beskrivningen visas för granskarna.

    ![Skapa en åtkomst granskning – granska namn och beskrivning](./media/create-access-review/name-description.png)

1. Ange **start datum**. Som standard sker en åtkomst granskning en gång, startas samma gång som den skapas och den avslutas om en månad. Du kan ändra start-och slutdatumen så att en åtkomst granskning börjar i framtiden och det senaste antalet dagar som du vill.

    ![Skapa en åtkomst granskning – start-och slutdatum](./media/create-access-review/start-end-dates.png)

1. Om du vill göra åtkomst granskningen återkommande ändrar du **frekvens** inställningen **från en gång** till **varje vecka**, **varje månad**, **kvartals vis**, **halvårs** vis eller **varje år**. Använd skjutreglaget **varaktighet** eller text rutan för att definiera hur många dagar varje granskning av den återkommande serien ska vara öppen för inmatade i granskare. Den längsta tid som du kan ange för en månatlig granskning är till exempel 27 dagar för att undvika överlappande granskningar.

1. Använd **End** -inställningen för att ange hur du ska avsluta gransknings serien för återkommande åtkomst. Serien kan sluta på tre sätt: 
    1. Den körs kontinuerligt för att starta recensioner på obestämd tid
    1. Fram till ett visst datum,
    1. Tills ett definierat antal förekomster har slutförts. 
  
    En annan användar administratör eller en annan global administratör kan stoppa serien när den har skapats genom att ändra datumet i **Inställningar**, så att det slutar på det datumet.

1. I avsnittet **användare** anger du de användare som åtkomst granskningen gäller för. Åtkomst granskningar kan göras för medlemmar i en grupp eller för användare som har tilldelats till ett program. Du kan ytterligare ange omfånget för åtkomst granskningen för att granska de gäst användare som är medlemmar (eller tilldelade till programmet), i stället för att granska alla användare som är medlemmar eller som har åtkomst till programmet.

    ![Skapa en åtkomst granskning – användare](./media/create-access-review/users.png)

1. I avsnittet **grupp** väljer du en eller flera grupper som du vill granska medlemskap i.

    > [!NOTE]
    > Om du markerar fler än en grupp skapas flera åtkomst granskningar. Om du till exempel väljer fem grupper skapas fem olika åtkomst granskningar.
    
    ![Skapa en åtkomst granskning – Välj grupp](./media/create-access-review/select-group.png)

1. I avsnittet **program** (om du har valt **tilldelad till ett program** i steg 8) väljer du de program som du vill granska åtkomst till.

    > [!NOTE]
    > Om du väljer mer än ett program skapas flera åtkomst granskningar. Om du till exempel väljer fem program skapas fem olika åtkomst granskningar.
    
    ![Skapa en åtkomst granskning – Välj program](./media/create-access-review/select-application.png)

1. I avsnittet **granskare väljer du antingen** en eller flera personer för att granska alla användare i omfånget. Alternativt kan du välja att låta medlemmarna granska sin egen åtkomst. Om resursen är en grupp kan du be grupp ägarna att granska. Du kan också kräva att granskarna anger en anledning när de godkänner åtkomsten.

    ![Skapa en åtkomst granskning – granskare](./media/create-access-review/reviewers.png)

1. I avsnittet **program** väljer du det program som du vill använda. **Standard programmet** är alltid tillgängligt.

    ![Skapa en åtkomst granskning – program](./media/create-access-review/programs.png)

    Du kan förenkla insamling och spårning av åtkomst granskningar genom att organisera dem i program. Varje åtkomst granskning kan länkas till ett program. När du förbereder rapporter för en granskare kan du fokusera på åtkomst granskningarna i omfattningen för ett visst initiativ. Program och åtkomst gransknings resultat visas för användare i rollen global administratör, användar administratör, säkerhets administratör eller säkerhets läsare.

    Om du vill se en lista över program går du till sidan åtkomst granskningar och väljer **program**. Om du har rollen som global administratör eller användar administratör kan du skapa ytterligare program. Du kan till exempel välja att ha ett program för varje initiativ för regelefterlevnad eller affärs mål. När du inte längre behöver ett program och det inte finns några kontroller som är länkade till det kan du ta bort det.

### <a name="upon-completion-settings"></a>Vid slut för ande inställningar

1. Om du vill ange vad som händer när en granskning har slutförts expanderar du avsnittet **när inställningarna slutförs** .

    ![Skapa en åtkomst granskning-när inställningarna slutförs](./media/create-access-review/upon-completion-settings-new.png)

2. Om du vill ta bort åtkomsten för nekade användare automatiskt anger du **Använd automatiskt tillämpa resultat till resurs** för att **Aktivera**. Om du vill tillämpa resultaten manuellt när granskningen är klar ställer du in växeln på **inaktivera**.

3. Använd listan **om granskare inte svarar** för att ange vad som händer för användare som inte granskas av granskaren under gransknings perioden. Den här inställningen påverkar inte användare som har granskats manuellt av granskarna. Om den sista granskaren av beslutet är neka tas användarens åtkomst bort.

    - **Ingen ändring** – lämna användarens åtkomst oförändrad
    - **Ta bort åtkomst** – ta bort användares åtkomst
    - **Godkänn åtkomst** – Godkänn användarens åtkomst
    - **Ta rekommendationer** – beakta systemets rekommendation om att neka eller godkänna användarens fortsatta åtkomst

    ![Skapa en åtkomst granskning – avancerade inställningar](./media/create-access-review/advanced-settings-preview-new.png)

4. Förhandsgranskningsvyn Använd åtgärden som ska tillämpas på nekade användare för att ange vad som händer med gäst användare om de nekas.
    - **Alternativ 1** tar bort nekad användares åtkomst till gruppen eller programmet som granskas, men de kommer fortfarande att kunna logga in till klienten. 
    - **Alternativ 2** blockerar nekade användare från att logga in på klienten, oavsett om de har åtkomst till andra resurser. Om det var fel eller om en administratör bestämmer sig för att återaktivera en åtkomst, kan de göra det inom 30 dagar efter att användaren har inaktiverats. Om ingen åtgärd vidtas för de inaktiverade användarna tas de bort från klienten.

Om du vill veta mer om metod tips för att ta bort gäst användare som inte längre har åtkomst till resurser i organisationen läser du artikeln [med rubriken använd Azure AD Identity Governance för att granska och ta bort externa användare som inte längre har resurs åtkomst.](access-reviews-external-users.md).

>[!NOTE]
> Åtgärd som ska tillämpas på nekade användare fungerar bara om du tidigare har begränsat en granskning till gäst användare (se avsnittet **skapa en eller flera åtkomst granskningar** steg 8)

### <a name="advanced-settings"></a>Avancerade inställningar

1. Om du vill ange ytterligare inställningar expanderar du avsnittet **Avancerade inställningar** .

1. Ange **Visa rekommendationer** för att **Aktivera** för att Visa granskarna system rekommendationer baserat på användarens åtkomst information.

1. Ange **Kräv orsak för godkännande** för att **göra det möjligt** för granskaren att ange en orsak till godkännande.

1. Ange **e-postaviseringar** för att **tillåta** att Azure AD skickar e-postmeddelanden till granskare när en åtkomst granskning startar och till administratörer när en granskning har slutförts.

1. Ställ in **påminnelser** för att **tillåta** att Azure AD skickar påminnelser om åtkomst granskningar till granskare som inte har slutfört sin granskning. 

    >[!NOTE]
    > Som standard skickar Azure AD automatiskt en påminnelse som är halvvägs till slutdatumet för granskare som ännu inte har svarat

1. Förhandsgranskningsvyn Innehållet i e-postmeddelandet som skickas till granskare genereras automatiskt baserat på gransknings informationen, till exempel gransknings namn, resurs namn, förfallo datum osv. Om du behöver ett sätt att kommunicera ytterligare information, till exempel ytterligare instruktioner eller kontakt information, kan du ange den här informationen i det **extra innehållet för e-postmeddelandet i e-** postmeddelandet som ska ingå i inbjudan och påminnelse e-postmeddelanden som skickas till tilldelade granskare. Det markerade avsnittet nedan visar var den här informationen kommer att visas.

    ![Granska en användares åtkomst till en grupp](./media/create-access-review/review-users-access-group.png)

## <a name="start-the-access-review"></a>Starta åtkomst granskningen

När du har angett inställningarna för åtkomst granskning klickar du på **Start**. Åtkomst granskningen visas i listan med en indikator för dess status.

![Lista över åtkomst granskningar och deras status](./media/create-access-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter att granskningen startar. Om du väljer att inte låta Azure AD skicka e-postmeddelandet måste du meddela granskarna att en åtkomst granskning väntar på att de ska slutföras. Du kan visa dem i anvisningarna för hur du [granskar åtkomst till grupper eller program](perform-access-review.md). Om granskningen är till för gäster för att granska sin egen åtkomst, visar du dem i anvisningarna för att [Granska åtkomsten för dig själv till grupper eller program](review-your-access.md).

Om du har tilldelat gäster som granskare och de inte har accepterat inbjudan får han eller hon ingen e-post från åtkomst granskningar eftersom de först måste godkänna inbjudan innan de kan granskas.

## <a name="access-review-status-table"></a>Status tabell för åtkomst granskning

| Status | Definition |
|--------|------------|
|NotStarted | Granskningen skapades, användar identifieringen väntar på att starta. |
|Initierar   | Användar identifiering pågår för att identifiera alla användare som ingår i granskningen. |
|Startar | Granskningen startas. Om e-postaviseringar har Aktiver ATS skickas e-postmeddelanden till granskare. |
|Pågår | Granskning har startat. Om e-postaviseringar har Aktiver ATS har e-postmeddelanden skickats till granskarna. Granskare kan skicka beslut fram till förfallo datumet. |
|Slutföra | Granskningen slutförs och e-postmeddelanden skickas till gransknings ägaren. |
|Automatisk visning | Granskning är i ett system gransknings steg. Systemet registrerar beslut för användare som inte har granskats utifrån rekommendationer eller förkonfigurerade beslut. |
|Automatiskt granskad | Beslut har registrerats av systemet för alla användare som inte har granskats. Granskning är redo att fortsätta att **tillämpa** om Auto-Apply har Aktiver ATS. |
|Lägger | Det kommer inte att gå att komma åt användare som har godkänts. |
|Tillämpat | Nekade användare, om sådana finns, har tagits bort från resursen eller katalogen. |
|Misslyckad | Granskningen kunde inte fortsätta. Det här felet kan bero på borttagning av klienten, en ändring i licenser eller andra interna klient ändringar. |

## <a name="create-reviews-via-apis"></a>Skapa recensioner via API: er

Du kan också skapa åtkomst granskningar med hjälp av API: er. Vad du gör för att hantera åtkomst granskningar av grupper och program användare i Azure Portal kan också göras med hjälp av Microsoft Graph-API: er. Mer information finns i API- [referens för Azure AD Access-granskning](/graph/api/resources/accessreviews-root?view=graph-rest-beta). Ett kod exempel finns i [exempel på hämtning av åtkomst granskningar för Azure AD via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomst till grupper eller program](perform-access-review.md)
- [Granska åtkomst för dig själv till grupper eller program](review-your-access.md)
- [Slutför en åtkomst granskning av grupper eller program](complete-access-review.md)