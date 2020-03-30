---
title: Skapa samlingar för Mina appar-portaler i Azure Active Directory | Microsoft-dokument
description: Använd Mina appars samlingar för att anpassa mina appar-sidor för en enklare upplevelse för Mina appar för dina slutanvändare. Ordna program i grupper med separata flikar.
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
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120097"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Skapa samlingar på portalen Mina appar

Användarna kan använda portalen Mina appar för att visa och starta de molnbaserade program de har åtkomst till. Som standard visas alla program som en användare kan komma åt tillsammans på en enda sida. Om du vill ordna den här sidan bättre för användarna kan du konfigurera samlingar om du har en Azure AD Premium P1- eller P2-licens. Med en samling kan du gruppera program som är relaterade (till exempel efter befattning, aktivitet eller projekt) och visa dem på en separat flik. En samling tillämpar i huvudsak ett filter för de program som en användare redan kan komma åt, så användaren ser bara de program i samlingen som har tilldelats dem.

> [!NOTE]
> Den här artikeln beskriver hur en administratör kan aktivera och skapa samlingar. Information om hur du använder portalen och samlingarna i Mina appar finns i [Komma åt och använda samlingar](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-the-latest-my-apps-features"></a>Aktivera de senaste funktionerna i Mina appar

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som användaradministratör eller global administratör.

2. Gå till Azure Active > **Directory-användarinställningar**. **Azure Active Directory**

3. Under **Förhandsgranskningar av användarfunktionen**väljer du **Hantera förhandsgranskningsinställningar för användarfunktionen**.

4. Under **Användare kan använda förhandsgranskningsfunktioner för Mina appar**väljer du något av följande alternativ:
   * **Markerad** - Aktiverar funktionerna för en viss grupp. Använd alternativet **Välj en grupp** för att välja den grupp som du vill aktivera funktionerna för.  
   * **Alla** - Aktiverar funktioner för alla användare.

> [!NOTE]
> Om du vill öppna portalen Mina `https://myapps.microsoft.com` appar kan användarna använda länken `https://myapps.microsoft.com/contoso.com`eller den anpassade länken för din organisation, till exempel . När du har aktiverat den nya upplevelsen Mina appar visas **banderollen An updated My Applications** högst upp på sidan Mina appar och användarna kan välja **Prova** den för att visa den nya upplevelsen. Om du vill sluta använda den nya upplevelsen kan användarna välja **Ja** på banderollen **Lämna ny upplevelse** högst upp på sidan.

## <a name="create-a-collection"></a>Skapa en samling

För att skapa en samling måste du ha en Azure AD Premium P1- eller P2-licens.

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som administratör med en Azure AD Premium P1- eller P2-licens.

2. Gå till **Azure Active Directory** > **Enterprise Applications**.

3. Under **Hantera**väljer du **Samlingar**.

4. Välj **Ny samling**. På sidan **Ny samling** anger du ett **namn** för samlingen (vi rekommenderar att du inte använder "samling" i namnet. Ange sedan en **beskrivning**.

   ![Ny samlingssida](media/acces-panel-collections/new-collection.png)

5. Välj fliken **Program.** Välj **+ Lägg till program**och välj sedan alla program som du vill lägga till i samlingen på sidan Lägg till **program** eller använd **sökrutan** för att hitta program.

   ![Lägga till ett program i samlingen](media/acces-panel-collections/add-applications.png)

6. När du har lagt till program väljer du **Lägg till**. Listan över valda program visas. Du kan använda upppilarna för att ändra ordningen på programmen i listan. Om du vill flytta ett program nedåt eller ta bort det från samlingen väljer du **menyn Mer** (**...**).

7. Välj fliken **Ägare.** Välj **+ Lägg till användare och grupper**och välj sedan de användare eller grupper som du vill tilldela ägarskap till på sidan Lägg till användare och **grupper.** När du är klar med att välja användare och grupper väljer du **Välj**.

9. Välj fliken **Användare och grupper.** Välj **+ Lägg till användare och grupper**och välj sedan de användare eller grupper som du vill tilldela samlingen till på sidan Lägg till användare och **grupper.** Eller använd **sökrutan** för att hitta användare eller grupper. När du är klar med att välja användare och grupper väljer du **Välj**.

   ![Lägga till användare och grupper](media/acces-panel-collections/add-users-and-groups.png)

11. Välj **Granska + Skapa**. Egenskaperna för den nya samlingen visas.


## <a name="view-audit-logs"></a>Visa granskningsloggar

Granskningsloggarna registrerar åtgärder för mina appars samlingar, inklusive insamlingsskapande slutanvändaråtgärder. Följande händelser genereras från Mina appar:

* Skapa samling
* Redigera samling
* Ta bort samling
* Starta ett program (slutanvändare)
* Självbetjäningsprogram lägger till (slutanvändare)
* Borttagning av program med självbetjäning (slutanvändare)

Du kan komma åt granskningsloggar i [Azure-portalen](https://portal.azure.com) genom att välja **Azure Active Directory** > **Enterprise Applications** > **Audit loggar** i avsnittet Aktivitet. För **Tjänst**väljer du **Mina appar**.

## <a name="get-support-for-my-account-pages"></a>Få support för mina kontosidor

På sidan Mina appar kan en användare välja **Visa mitt konto** > **För** att öppna sina kontoinställningar. På sidan Azure AD **My Account** kan användare hantera sin säkerhetsinformation, sina enheter, lösenord med mera. De kan också komma åt sina Office-kontoinställningar.

Om du behöver skicka en supportbegäran för ett problem med sidan Azure AD-konto eller sidan För Office-konto följer du dessa steg så att din begäran dirigeras korrekt: 

* Om du vill ha problem med sidan **"Mitt konto" i Azure AD** öppnar du en supportbegäran från Azure-portalen. Gå till **Azure Portal** > **Azure Active Directory** > **Ny supportbegäran**.

* Om du vill ha problem med sidan **"Mitt konto" i Office** öppnar du en supportbegäran från administrationscentret för Microsoft 365. Gå till **Microsoft 365 administrationscenter** > **Support**. 

## <a name="next-steps"></a>Nästa steg
[Slutanvändarens upplevelser för program i Azure Active Directory](end-user-experiences.md)