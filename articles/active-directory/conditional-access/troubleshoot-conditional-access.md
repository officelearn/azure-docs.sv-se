---
title: Felsöka inloggnings problem med villkorlig åtkomst – Azure Active Directory
description: I den här artikeln beskrivs vad du ska göra när dina principer för villkorlig åtkomst resulterar i oväntade resultat
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6778b556795f4e079100f1a7bcbb8b9465e9e315
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88032976"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Felsöka inloggningsproblem med villkorsstyrd åtkomst

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
1. Bläddra till **Azure Active Directory**  >  **inloggningar**.
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

### <a name="policy-details"></a>Princip information

Om du väljer ellipsen på höger sida om principen i en inloggnings händelse öppnas princip information. Detta ger administratörer ytterligare information om varför en princip har tillämpats eller inte.

   ![Logga in på fliken villkorlig åtkomst för händelser](./media/troubleshoot-conditional-access/image5.png)

   ![Princip information (för hands version)](./media/troubleshoot-conditional-access/policy-details.png)

Den vänstra sidan innehåller information som samlas in vid inloggning och den högra sidan innehåller information om huruvida dessa uppgifter uppfyller kraven för de tillämpade principerna för villkorlig åtkomst. Principer för villkorlig åtkomst gäller endast när alla villkor är uppfyllda eller inte har kon figurer ATS.

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
