---
title: Skapa en princip för villkorlig åtkomst – Azure Active Directory
description: Vilka är alla alternativ för att skapa en princip för villkorlig åtkomst och vad betyder de?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907ad8705742e4b2e38b13c3c675ebd333bd27d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295315"
---
# <a name="building-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Som förklaras i artikeln [Vad är villkorlig åtkomst](overview.md)är en princip för villkorlig åtkomst en if-then-sats, **tilldelningar** och **åtkomstkontroller**. En princip för villkorlig åtkomst sammanför signaler, för att fatta beslut och tillämpa organisationsprinciper.

Hur skapar en organisation dessa principer? Vad krävs?

![Villkorlig åtkomst (signaler + beslut + verkställighet = principer)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Tilldelningar

Tilldelningsdelen styr vem, vad och var i principen Villkorlig åtkomst.

### <a name="users-and-groups"></a>Användare och grupper

[Användare och grupper](concept-conditional-access-users-groups.md) tilldelar vem principen ska innehålla eller utesluta. Den här tilldelningen kan omfatta alla användare, specifika användargrupper, katalogroller eller externa gästanvändare. 

### <a name="cloud-apps-or-actions"></a>Molnappar eller åtgärder

[Molnappar eller -åtgärder](concept-conditional-access-cloud-apps.md) kan inkludera eller utesluta molnprogram eller användaråtgärder som omfattas av principen.

### <a name="conditions"></a>Villkor

En princip kan innehålla flera [villkor](concept-conditional-access-conditions.md).

#### <a name="sign-in-risk"></a>Inanmälningsrisk

För organisationer med [Azure AD Identity Protection](../identity-protection/overview.md)kan de riskidentifieringar som genereras där påverka dina principer för villkorlig åtkomst.

#### <a name="device-platforms"></a>Enhetsplattformar

Organisationer med flera enhetsoperativsystemplattformar kanske vill tillämpa specifika principer på olika plattformar. 

Informationen som används för att beräkna enhetsplattformen kommer från overifierade källor, till exempel användaragentsträngar som kan ändras.

#### <a name="locations"></a>Platser

Platsdata tillhandahålls av IP-geolokaliseringsdata. Administratörer kan välja att definiera platser och välja att markera vissa som betrodda som de för organisationens nätverksplatser.

#### <a name="client-apps"></a>Klientappar

Som standard gäller principer för villkorlig åtkomst för webbläsarappar, mobilappar och skrivbordsklienter som stöder modern autentisering. 

Med det här tilldelningsvillkoret kan principer för villkorlig åtkomst rikta specifika klientprogram som inte använder modern autentisering. Dessa program inkluderar Exchange ActiveSync-klienter, äldre Office-program som inte använder modern autentisering och e-postprotokoll som IMAP, MAPI, POP och SMTP.

#### <a name="device-state"></a>Enhetstillstånd

Den här kontrollen används för att utesluta enheter som är hybrid Azure AD-anslutna eller markerade en kompatibel i Intune. Det här undantaget kan göras för att blockera ohanterade enheter. 

## <a name="access-controls"></a>Åtkomstkontroller

Åtkomstkontrolldelen av principen Villkorlig åtkomst styr hur en princip tillämpas.

### <a name="grant"></a>Bevilja

[Grant](concept-conditional-access-grant.md) ger administratörer ett sätt att tvinga principen där de kan blockera eller bevilja åtkomst.

#### <a name="block-access"></a>Blockera åtkomst

Blockåtkomst gör just det, det kommer att blockera åtkomst under de angivna tilldelningarna. Blockkontrollen är kraftfull och bör utövas med lämplig kunskap.

#### <a name="grant-access"></a>Bevilja åtkomst

Bidragskontrollen kan utlösa verkställighet av en eller flera kontroller. 

- Kräv multifaktorautentisering (Azure Multi-Factor Authentication)
- Kräv att enheten ska markeras som kompatibel (Intune)
- Kräv Hybrid Azure AD-ansluten enhet
- Kräv godkänd klientapp
- Kräva appskyddsprincip

Administratörer kan välja att kräva någon av de tidigare kontrollerna eller alla markerade kontroller med hjälp av följande alternativ. Standardvärdet för flera kontroller är att kräva alla.

- Kräv alla markerade kontroller (kontroll och kontroll)
- Kräv en av de markerade kontrollerna (kontroll eller kontroll)

### <a name="session"></a>Session

[Sessionskontroller](concept-conditional-access-session.md) kan begränsa upplevelsen 

- Använda begränsningar för apptvekvering
   - Fungerar endast med Exchange Online och SharePoint Online.
      - Skickar enhetsinformation för att ge kontroll över erfarenhet som ger fullständig eller begränsad åtkomst.
- Använd appkontroll för villkorlig åtkomst
   - Använder signaler från Microsoft Cloud App Security för att göra saker som: 
      - Blockera nedladdning, klippning, kopiering och utskrift av känsliga dokument.
      - Övervaka riskfyllda sessionsbeteenden.
      - Kräv märkning av känsliga filer.
- Inloggningsfrekvens
   - Möjlighet att ändra standard inloggningsfrekvensen för modern autentisering.
- Beständig webbläsarsession
   - Tillåter användare att förbli inloggade efter att ha stängt och öppnat sitt webbläsarfönster igen.

## <a name="simple-policies"></a>Enkla policyer

En princip för villkorlig åtkomst måste innehålla minst följande för att kunna verkställas:

- **Principens namn.**
- **Tilldelningar**
   - **Användare och/eller grupper** att tillämpa principen på.
   - **Molnappar eller -åtgärder** som policyn ska tillämpas på.
- **Åtkomstkontroller**
   - **Bevilja** eller **blockera** kontroller

![Tom princip för villkorlig åtkomst](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

Artikeln [Gemensamma principer för villkorlig åtkomst](concept-conditional-access-policy-common.md) innehåller vissa principer som vi tror skulle vara användbara för de flesta organisationer.

## <a name="next-steps"></a>Nästa steg

[Simulera inloggningsbeteende med verktyget Villkorlig åtkomst Vad händer om](troubleshoot-conditional-access-what-if.md)

[Planera en molnbaserad distribution av Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Hantera enhetsefterlevnad med Intune](/intune/device-compliance-get-started)

[Säkerhet och villkorlig åtkomst för Microsoft Cloud App](/cloud-app-security/proxy-intro-aad)
