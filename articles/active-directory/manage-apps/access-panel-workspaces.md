---
title: Skapa arbets ytor för Mina appar portaler i Azure Active Directory | Microsoft Docs
description: Använd arbets ytorna Mina appar för att anpassa Mina appar för att få en enklare upplevelse av mina appar för dina slutanvändare. Organisera program i grupper med separata flikar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a986c2032fc41bbd34230c5b0ead745f88b000f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199820"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>Skapa arbets ytor på portalen Mina appar (för hands version)

Användarna kan använda portalen Mina appar (för hands version) för att visa och starta de molnbaserade program som de har åtkomst till. Som standard visas alla program som en användare har åtkomst till tillsammans på en enda sida. Om du har en Azure AD Premium P1-eller P2-licens för att bättre organisera den här sidan för dina användare kan du konfigurera arbets ytor. Med en arbets yta kan du gruppera program som är relaterade (till exempel efter jobb roll, uppgift eller projekt) och visa dem på en separat flik. En arbets yta tillämpar i princip ett filter på de program som en användare redan har åtkomst till, så att användaren bara ser de programmen på arbets ytan som har tilldelats dem.

> [!NOTE]
> Den här artikeln beskriver hur en administratör kan aktivera och skapa arbets ytor. Information för slutanvändaren om hur du använder portalen för Mina appar och arbets ytor finns i [åtkomst till och använda arbets ytor](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-my-apps-preview-features"></a>Aktivera för hands versions funktioner för Mina appar

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som användar administratör eller global administratör.

2. Gå till **Azure Active Directory** > **användar inställningar**.

3. Under förhands **granskningar av användar funktioner**väljer du **Hantera för hands versions inställningar för användar funktionen**.

4. Under **användare kan använda för hands versions funktioner för Mina appar**väljer du något av följande alternativ:
   * **Vald** – aktiverar för hands versions funktioner för en viss grupp. Använd alternativet **Välj en grupp** för att välja den grupp som du vill aktivera för hands versions funktioner för.  
   * **Alla** – aktiverar för hands versions funktioner för alla användare.

   ![Användarens för hands versions funktioner](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> För att öppna portalen Mina appar kan användare använda länken `https://myapps.microsoft.com` eller den anpassade länken för din organisation, till exempel `https://myapps.microsoft.com/contoso.com`. Om användarna inte omdirigeras till för hands versionen av mina appar, bör användarna försöka `https://myapplications.microsoft.com` eller `https://myapplications.microsoft.com/contoso.com`.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

För att skapa en arbets yta måste du ha en Azure AD Premium P1-eller P2-licens.

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som administratör med en Azure AD Premium P1-eller P2-licens.

2. Gå till **Azure Active Directory** > **företags program**.

3. Under **Hantera**väljer du **arbets ytor (för hands version)** .

4. Välj **ny arbets yta**. På sidan **ny arbets yta** anger du ett **namn** för arbets ytan (vi rekommenderar att du inte använder "arbets yta" i namnet. Ange sedan en **Beskrivning**.

   ![Skapa en ny arbets yta](media/access-panel-workspaces/new-workspace.png)

5. Välj **Granska + Skapa**. Egenskaperna för den nya arbets ytan visas.

6. Välj fliken **program** . Under **Lägg till program**väljer du alla program som du vill lägga till i arbets ytan eller använder **sökrutan för** att hitta program. 

   ![Lägg till program i arbets ytan](media/access-panel-workspaces/add-applications.png)

7. Välj **Lägg till**. Listan med valda program visas. Du kan använda upp-och nedpilarna för att ändra ordningen på programmen i listan.

   ![Lista över program i arbets ytan](media/access-panel-workspaces/add-applications-list.png)

8. Välj fliken **användare och grupper** . Välj **Lägg till användare**om du vill lägga till en användare eller grupp. 

9. På sidan **Välj medlemmar** väljer du de användare eller grupper som du vill tilldela arbets ytan till. Eller Använd **sökrutan för** att hitta användare eller grupper.

   ![Tilldela användare och grupper till arbets ytan](media/access-panel-workspaces/add-users-and-groups.png)

10. När du är klar med att välja användare och grupper väljer du **Välj**.

11. Om du vill ändra en användares roll från **Läs behörighet** till **ägare** eller vice versa, klickar du på den aktuella rollen och väljer en ny roll.

    ![Tilldela roller till användare och grupper](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>Visa granskningsloggar

I gransknings loggarna registreras arbets ytorna Mina appar, inklusive åtgärder för att skapa arbets ytor som slutanvändare. Följande händelser genereras från Mina appar:

* Skapa arbetsyta
* Redigera arbets yta
* Ta bort arbets yta
* Starta ett program (slutanvändare)
* Lägg till självbetjänings program (slutanvändare)
* Borttagning av självbetjänings program (slutanvändare)

Du kan komma åt gransknings loggar i [Azure Portal](https://portal.azure.com) genom att välja **Azure Active Directory** > **företags program** > **gransknings loggar** i avsnittet aktivitet. För **tjänst**väljer du **Mina appar**.

   ![Tilldela roller till användare och grupper](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>Nästa steg
[Slut användar upplevelse för program i Azure Active Directory](end-user-experiences.md)