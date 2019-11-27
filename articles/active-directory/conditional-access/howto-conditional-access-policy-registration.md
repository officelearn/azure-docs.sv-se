---
title: Villkorlig åtkomst – kombinerad säkerhets information – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att kräva en betrodd plats för registrering av säkerhets information
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5864424f003ce9254a6452d8374d78c54516f2bc
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322741"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>Villkorlig åtkomst: Kräv betrodd plats för MFA-registrering

Att skydda när och hur användare registrerar sig för Azure Multi-Factor Authentication och återställning av lösen ord för självbetjäning är nu möjligt med användar åtgärder i princip för villkorlig åtkomst. Den här förhands gransknings funktionen är tillgänglig för organisationer som har aktiverat den [kombinerade förhands granskningen](../authentication/concept-registration-mfa-sspr-combined.md). Den här funktionen kan vara aktive rad i organisationer där de vill att användarna ska kunna registrera sig för Azure Multi-Factor Authentication och SSPR från en central plats, till exempel en betrott nätverks plats under en inledande registrering. Mer information om hur du skapar betrodda platser i villkorlig åtkomst finns i artikeln [Vad är plats villkoret i Azure Active Directory villkorlig åtkomst?](../conditional-access/location-condition.md#named-locations)

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Skapa en princip för att kräva registrering från en betrodd plats

Följande princip gäller för alla valda användare, som försöker registrera sig med den kombinerade registrerings upplevelsen och blockerar åtkomsten om de inte ansluter från en plats som har marker ATS som ett betrott nätverk.

1. I **Azure Portal**bläddrar du till **Azure Active Directory** > **villkorlig åtkomst**.
1. Välj **ny princip**.
1. I namn anger du ett namn för den här principen. Till exempel **kombinerad säkerhets informations registrering på betrodda nätverk**.
1. Under **tilldelningar**klickar du på **användare och grupper**och väljer de användare och grupper som du vill att den här principen ska tillämpas på.

   > [!WARNING]
   > Användare måste aktive ras för för [hands versionen av den kombinerade registreringen](../authentication/howto-registration-mfa-sspr-combined.md).

1. Under **molnappar eller åtgärder**väljer du **användar åtgärder**, kryss rutan **Registrera säkerhets information (för hands version)** .
1. Under **villkor** > **platser**.
   1. Konfigurera **Ja**.
   1. Ta med **vilken plats som helst**.
   1. Undanta **alla betrodda platser**.
   1. Klicka på **Done** på bladet platser.
   1. Klicka på **färdig** på bladet villkor.
1. Under **åtkomst kontroller** > **bevilja**.
   1. Klicka på **blockera åtkomst**.
   1. Klicka sedan på **Välj**.
1. Ange **Aktivera princip** till **på**.
1. Klicka sedan på **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
