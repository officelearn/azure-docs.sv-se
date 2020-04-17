---
title: Villkorlig åtkomst - Kombinerad säkerhetsinformation - Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för registrering av säkerhetsinformation
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8081bb8145a6654c168fb2d664e1666b32dc18
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457917"
---
# <a name="conditional-access-securing-security-info-registration"></a>Villkorlig åtkomst: Skydda registrering av säkerhetsinformation

Det är nu möjligt att skydda när och hur användare registrerar sig för Azure Multi-Factor Authentication och självbetjäningslösenordsåterställning med användaråtgärder i principen villkorlig åtkomst. Den här förhandsgranskningsfunktionen är tillgänglig för organisationer som har aktiverat den [kombinerade förhandsversionen av registreringen](../authentication/concept-registration-mfa-sspr-combined.md). Den här funktionen kan aktiveras i organisationer där de vill använda villkor som betrodd nätverksplats för att begränsa åtkomsten till registrering för Azure Multi-Factor Authentication och självbetjäningslösenordsåterställning (SSPR). Mer information om användbara villkor finns i artikeln [Villkorlig åtkomst: Villkor](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Skapa en princip som kräver registrering från en betrodd plats

Följande princip gäller för alla valda användare som försöker registrera sig med den kombinerade registreringsupplevelsen och blockerar åtkomst om de inte ansluter från en plats som markerats som ett betrott nätverk.

1. I **Azure-portalen**bläddrar du till **Azure Active Directory** > **Security** > Conditional**Access**.
1. Välj **Ny princip**.
1. Ange ett namn för den här principen i Namn. **Kombinerad registrering av säkerhetsinformation i betrodda nätverk**.
1. Under **Tilldelningar**väljer du **Användare och grupper**och väljer de användare och grupper som du vill att den här principen ska gälla för.

   > [!WARNING]
   > Användare måste vara aktiverade för den [kombinerade registreringen](../authentication/howto-registration-mfa-sspr-combined.md).

1. Under **Molnappar eller -åtgärder**väljer du **Användaråtgärder**och kontrollerar **Registrera säkerhetsinformation**.
1. Under **förhållanden** > **platser**.
   1. Konfigurera **Ja**.
   1. Inkludera **valfri plats**.
   1. Uteslut **alla betrodda platser**.
   1. Välj **Klar** på bladet Platser.
   1. Välj **Klar** på bladet Villkor.
1. Under **Villkor** > **klientappar (förhandsversion)** ställer du in **Konfigurera** till **Ja**och väljer **Klar**.
1. Under **Åtkomstkontroller** > **Bevilja**.
   1. Välj **Blockera åtkomst**.
   1. Klicka sedan på **Välj**.
1. Ange **Aktivera princip** till **På**.
1. Välj sedan **Spara**.

I steg 6 i den här principen har organisationer val de kan göra. Principen ovan kräver registrering från en betrodd nätverksplats. Organisationer kan välja att använda alla tillgängliga villkor i stället **för platser**. Kom ihåg att den här principen är en blockeringsprincip så allt som ingår blockeras och allt som inte matchar inbegripa är tillåtet. 

Vissa kan välja att använda enhetstillstånd i stället för plats i steg 6 ovan:

6. Under **Villkor** > **Enhetstillstånd (förhandsgranskning)**.
   1. Konfigurera **Ja**.
   1. Inkludera **alla enhetstillstånd**.
   1. Exkludera **Device Hybrid Azure AD-sammanfogad** och/eller **enhet som är markerad som kompatibel**
   1. Välj **Klar** på bladet Platser.
   1. Välj **Klar** på bladet Villkor.

> [!WARNING]
> Om du använder enhetstillstånd som ett villkor i din princip kan detta påverka gästanvändare i katalogen. [Endast rapportläge](concept-conditional-access-report-only.md) kan hjälpa till att avgöra effekten av politiska beslut.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Fastställa påverkan med hjälp av rapportläge med villkorsstyrd åtkomst](howto-conditional-access-report-only.md)

[Simulera inloggningsbeteende med verktyget Villkorlig åtkomst Vad händer om](troubleshoot-conditional-access-what-if.md)
