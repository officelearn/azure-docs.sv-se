---
title: Anpassade kontroller i Azure AD-villkorlig åtkomst
description: Lär dig hur anpassade kontroller i Azure Active Directory Villkorlig åtkomst fungerar.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c149279a755eb186a3fdc7891e9b511d18c7f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050540"
---
# <a name="custom-controls-preview"></a>Anpassade kontroller (förhandsgranskning)

Anpassade kontroller är en förhandsgranskningsfunktion i Azure Active Directory. När du använder anpassade kontroller omdirigeras användarna till en kompatibel tjänst för att uppfylla autentiseringskrav utanför Azure Active Directory. För att uppfylla den här kontrollen omdirigeras en användares webbläsare till den externa tjänsten, utför all nödvändig autentisering och omdirigeras sedan tillbaka till Azure Active Directory. Azure Active Directory verifierar svaret och om användaren har autentiserats eller validerats fortsätter användaren i flödet Villkorlig åtkomst.

> [!NOTE]
> Mer information om ändringar som vi planerar i funktionen Anpassad kontroll finns i den [nya uppdateringen](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls)från februari 2020 .

## <a name="creating-custom-controls"></a>Skapa anpassade kontroller

Anpassade kontroller fungerar med en begränsad uppsättning godkända autentiseringsleverantörer. Om du vill skapa en anpassad kontroll bör du först kontakta den leverantör som du vill använda. Varje icke-Microsoft-leverantör har sin egen process och krav för att registrera dig, prenumerera eller på annat sätt bli en del av tjänsten, och för att ange att du vill integrera med villkorlig åtkomst. Då kommer leverantören att förse dig med ett datablock i JSON-format. Dessa data gör det möjligt för providern och villkorlig åtkomst att arbeta tillsammans för din klientorganisation, skapar den nya kontrollen och definierar hur villkorlig åtkomst kan avgöra om dina användare har utfört verifiering med leverantören.

Kopiera JSON-data och klistra sedan in dem i den relaterade textrutan. Gör inga ändringar i JSON om du inte uttryckligen förstår vilken ändring du gör. Om du gör några ändringar kan anslutningen mellan leverantören och Microsoft brytas och du och användarna eventuellt inte har gjort något.

Alternativet att skapa en anpassad kontroll finns i avsnittet **Hantera** på sidan **Villkorlig åtkomst.**

![Kontroll](./media/controls/82.png)

Om du klickar på **Ny anpassad kontroll**öppnas ett blad med en textruta för JSON-data för kontrollen.  

![Kontroll](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Ta bort anpassade kontroller

Om du vill ta bort en anpassad kontroll måste du först se till att den inte används i någon princip för villkorlig åtkomst. När du är klar:

1. Gå till listan Anpassade kontroller
1. Klicka...  
1. Välj **Ta bort**.

## <a name="editing-custom-controls"></a>Redigera anpassade kontroller

Om du vill redigera en anpassad kontroll måste du ta bort den aktuella kontrollen och skapa en ny kontroll med den uppdaterade informationen.

## <a name="known-limitations"></a>Kända begränsningar

Anpassade kontroller kan inte användas med Identity Protections automatisering som kräver Azure Multi-Factor Authentication, Azure AD self-service password reset (SSPR), uppfyller krav på anspråk för multifaktorautentisering eller för att höja roller i Privilegierad Identitetshanteraren (PIM).

## <a name="next-steps"></a>Nästa steg

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

- [Läget Endast rapport](concept-conditional-access-report-only.md)

- [Simulera inloggningsbeteende med verktyget Villkorlig åtkomst Vad händer om](troubleshoot-conditional-access-what-if.md)
