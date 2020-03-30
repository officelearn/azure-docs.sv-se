---
title: Felsöka inloggningsproblem med villkorlig åtkomst - Azure Active Directory
description: I den här artikeln beskrivs vad du ska göra när dina principer för villkorlig åtkomst resulterar i oväntade resultat
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 03/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf1daca79f3ed20d9b7a89af20d74ff5f3148b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337446"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Felsöka inloggningsproblem med villkorlig åtkomst

Informationen i den här artikeln kan användas för att felsöka oväntade inloggningsresultat relaterade till villkorlig åtkomst med hjälp av felmeddelanden och Azure AD-inloggningslogg.

## <a name="conditional-access-sign-in-interrupt"></a>Inloggning för villkorlig åtkomst

Det första sättet är att granska felmeddelandet som visas. Om du har problem med att logga in när du använder en webbläsare har själva felsidan detaljerad information. Enbart den här informationen kan beskriva vad problemet är och det kan tyna på en lösning.

![Inloggningsfel – kompatibel enhet krävs](./media/troubleshoot-conditional-access/image1.png)

I ovanstående fel anger meddelandet att programmet endast kan nås från enheter eller klientprogram som uppfyller företagets hanteringsprincip för mobila enheter. I det här fallet uppfyller programmet och enheten inte den principen.

## <a name="azure-ad-sign-in-events"></a>Inloggningshändelser för Azure AD

Den andra metoden för att få detaljerad information om inloggningsavbrottet är att granska Azure AD-inloggningshändelser för att se vilka principer eller principer för villkorlig åtkomst som tillämpades och varför.

Mer information kan hittas om problemet genom att klicka på **Mer information** på den första felsidan. Om du klickar på **Mer information** visas felsökningsinformation som är användbar när du söker i Azure AD-inloggningshändelserna för den specifika felhändelse som användaren såg eller när en supportincident öppnas med Microsoft.

![Mer information från en villkorlig åtkomst avbruten webbläsare inloggning.](./media/troubleshoot-conditional-access/image2.png)

Så här tar du reda på vilken princip eller principer för villkorlig åtkomst som har tillämpats och varför göra följande.

1. Logga in på **Azure-portalen** som global administratör, säkerhetsadministratör eller global läsare.
1. Bläddra till Azure Active > **Directory-inloggningar**. **Azure Active Directory**
1. Hitta händelsen för inloggningen att granska. Lägga till eller ta bort filter och kolumner för att filtrera bort onödig information.
   1. Lägg till filter för att begränsa omfattningen:
      1. **Korrelations-ID** när du har en specifik händelse att undersöka.
      1. **Villkorlig åtkomst** för att se principfel och framgång. Scope ditt filter för att visa endast fel för att begränsa resultaten.
      1. **Användarnamn** för att se information om specifika användare.
      1. **Datum** som omfattas av tidsramen i fråga.

   ![Välja filtret Villkorlig åtkomst i inloggningsloggen](./media/troubleshoot-conditional-access/image3.png)

1. När inloggningshändelsen som motsvarar användarens inloggningsfel har hittats väljer du fliken **Villkorlig åtkomst.** På fliken Villkorlig åtkomst visas de specifika principer eller principer som resulterade i inloggningsavbrottet.
   1. Information på fliken **Felsökning och support** kan ge en tydlig anledning till varför en inloggning misslyckades, till exempel en enhet som inte uppfyllde efterlevnadskraven.
   1. Om du vill undersöka vidare går du vidare med konfigurationen av principerna genom att klicka på **principnamnet**. Om du klickar på **principnamnet** visas användargränssnittet för principkonfiguration för den valda principen för granskning och redigering.
   1. **Klientanvändare-** och **enhetsinformation** som användes för principbedömningen för villkorlig åtkomst är också tillgängliga i flikarna **Grundläggande information,** **Plats,** **Enhetsinformation,** **Autentiseringsinformation**och **Ytterligare information** för inloggningshändelsen.

   ![Fliken Logga in händelse villkorlig åtkomst](./media/troubleshoot-conditional-access/image5.png)

Om informationen i händelsen inte är tillräckligt för att förstå inloggningsresultaten eller justera principen för att få önskat resultat kan en supportincident öppnas. Navigera till inloggningshändelsens **felsökning och supportflik** och välj **Skapa en ny supportbegäran**.

![Fliken Felsökning och support för inloggningshändelsen](./media/troubleshoot-conditional-access/image6.png)

När du skickar incidenten anger du begärande-ID och tid och datum från inloggningshändelsen i information om incidentens inlämning. Den här informationen gör det möjligt för Microsoft-supporten att hitta det evenemang du är orolig för.

### <a name="conditional-access-error-codes"></a>Felkoder för villkorlig åtkomst

| Felkod för inloggning | Felsträng |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockeradAvConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Nästa steg

- [Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen](../reports-monitoring/concept-sign-ins.md)
- [Felsöka villkorlig åtkomst med verktyget Vad händer](troubleshoot-conditional-access-what-if.md)
- Metodtips för [villkorlig åtkomst i Azure Active Directory](best-practices.md)
