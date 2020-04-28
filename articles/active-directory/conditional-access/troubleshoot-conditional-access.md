---
title: Felsöka inloggnings problem med villkorlig åtkomst – Azure Active Directory
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337446"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Felsöka inloggnings problem med villkorlig åtkomst

Informationen i den här artikeln kan användas för att felsöka oväntade inloggnings resultat relaterade till villkorlig åtkomst med fel meddelanden och inloggnings loggen för Azure AD.

## <a name="conditional-access-sign-in-interrupt"></a>Inloggnings avbrott för villkorlig åtkomst

Det första sättet är att granska fel meddelandet som visas. För problem med att logga in när du använder en webbläsare innehåller fel sidan detaljerad information. Den här informationen kan innehålla en beskrivning av vad problemet är och som kan föreslå en lösning.

![Inloggnings fel – kompatibel enhet krävs](./media/troubleshoot-conditional-access/image1.png)

I ovanstående fel anger meddelandet att programmet endast kan nås från enheter eller klient program som uppfyller företagets princip för hantering av mobila enheter. I det här fallet uppfyller programmet och enheten inte principen.

## <a name="azure-ad-sign-in-events"></a>Inloggnings händelser för Azure AD

Den andra metoden för att få detaljerad information om inloggnings avbrott är att granska inloggnings händelser för Azure AD för att se vilken princip för villkorlig åtkomst som tillämpades och varför.

Mer information finns på problemet genom att klicka på **Mer information** på sidan inledande fel. Om du klickar på **Mer information** visas felsöknings information som är användbar när du söker efter inloggnings händelser i Azure AD för den särskilda fel händelsen som användaren såg eller när du öppnar en support incident med Microsoft.

![Mer information från en villkorlig åtkomst avbruten webb läsar inloggning.](./media/troubleshoot-conditional-access/image2.png)

Så här tar du reda på vilka principer för villkorlig åtkomst som tillämpas och varför:

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller global läsare.
1. Bläddra till **Azure Active Directory** > **inloggningar**.
1. Hitta händelsen för inloggningen som ska granskas. Lägg till eller ta bort filter och kolumner för att filtrera bort onödig information.
   1. Lägg till filter för att begränsa omfånget:
      1. **Korrelations-ID** när du har en speciell händelse att undersöka.
      1. **Villkorlig åtkomst** för att se princip haveri och lyckad. Omfånget av filtret så att du endast kan visa felen för att begränsa resultaten.
      1. **Användar namn** för att se information om vissa användare.
      1. **Datum** som är begränsat till tids ramen i fråga.

   ![Välja filter för villkorlig åtkomst i inloggnings loggen](./media/troubleshoot-conditional-access/image3.png)

1. När inloggnings händelsen som motsvarar användarens inloggnings försök har hittats väljer du fliken **villkorlig åtkomst** . På fliken villkorsstyrd åtkomst visas en eller fler principer som resulterade i inloggnings avbrott.
   1. Information på fliken **fel sökning och support** kan ge en tydlig anledning till varför en inloggning misslyckades, till exempel en enhet som inte uppfyller kraven för efterlevnad.
   1. Om du vill undersöka ytterligare kan du öka detalj nivån i konfigurationen av principerna genom att klicka på **namnet på principen**. Om du klickar på **princip namnet** visas användar gränssnittet för princip konfiguration för den valda principen för granskning och redigering.
   1. **Klientens användar** -och **enhets information** som användes för utvärderingen av principen för villkorlig åtkomst är också tillgänglig i flikarna **grundläggande information**, **plats**, **enhets information**, **autentiseringsinformation**och **Ytterligare information** för inloggnings händelsen.

   ![Logga in på fliken villkorlig åtkomst för händelser](./media/troubleshoot-conditional-access/image5.png)

Om informationen i händelsen inte räcker till för att förstå inloggnings resultatet eller justera principen för att få önskade resultat, kan en support incident öppnas. Navigera till fliken **fel sökning och support** i inloggnings händelsen och välj **skapa en ny supportbegäran**.

![Fliken fel sökning och support i inloggnings händelsen](./media/troubleshoot-conditional-access/image6.png)

När du skickar in incidenten ska du ange begäran-ID och tid och datum från inloggnings händelsen i informationen om incident sändning. Med den här informationen kan Microsoft-supporten hitta den händelse som du är bekymrad om.

### <a name="conditional-access-error-codes"></a>Fel koder för villkorlig åtkomst

| Felkod för inloggning | Fel sträng |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Nästa steg

- [Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen](../reports-monitoring/concept-sign-ins.md)
- [Felsöka villkorlig åtkomst med hjälp av What If-verktyget](troubleshoot-conditional-access-what-if.md)
- Metod tips för [villkorlig åtkomst i Azure Active Directory](best-practices.md)
