---
title: Villkorlig åtkomst - Kräv kompatibla enheter - Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att kräva kompatibla enheter
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
ms.openlocfilehash: cc6bd486c1e8338eaf875c7026764c80d49e2f05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295205"
---
# <a name="conditional-access-require-compliant-devices"></a>Villkorlig åtkomst: Kräv kompatibla enheter

Organisationer som har distribuerat Microsoft Intune kan använda informationen som returneras från sina enheter för att identifiera enheter som uppfyller efterlevnadskraven, till exempel:

* Kräver en PIN-kod för att låsa upp
* Kräver enhetskryptering
* Kräver en minsta eller högsta operativsystemversion
* Att kräva en enhet är inte jailbroken eller rotad

Den här principefterlevnadsinformationen vidarebefordras till Azure AD där villkorlig åtkomst kan fatta beslut om att bevilja eller blockera åtkomst till resurser. Mer information om principer för enhetsefterlevnad finns i [artikeln, Ange regler på enheter för att tillåta åtkomst till resurser i organisationen med Intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa en princip för villkorlig åtkomst för att kräva att enheter som använder resurser markeras som kompatibla med organisationens Intune-efterlevnadsprinciper.

1. Logga in på **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar**väljer du **Användare och grupper**
   1. Under **Inkludera**väljer du **Alla användare**.
   1. Under **Uteslut**väljer du **Användare och grupper** och väljer organisationens konton för nödåtkomst eller glasbrott. 
   1. Välj **Done** (Klar).
1. Under **Molnappar eller åtgärder** > **Inkludera**väljer du **Alla molnappar**.
   1. Om du måste utesluta specifika program från din princip kan du välja dem på fliken **Uteslut** under **Välj uteslutna molnappar** och välja **Välj**.
   1. Välj **Done** (Klar).
1. Under **Villkor** > **klientappar (förhandsversion)** ställer du in **Konfigurera** till **Ja**och väljer **Klar**.
1. Under **Bevilja Bidrag för Åtkomst-kontroller** > **Grant**väljer du **Kräv att enheten ska markeras som kompatibel**.
   1. Välj **Välj**.
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** för att skapa för att aktivera din princip.

> [!NOTE]
> Du kan registrera dina nya enheter till Intune även om du väljer **Kräv enhet ska markeras som kompatibel** för **alla användare** och **alla molnappar** med hjälp av stegen ovan. **Kräv att enheten ska markeras som kompatibel** kontroll blockerar inte Intune-registreringen. 

### <a name="known-behavior"></a>Känt beteende

På Windows 7 identifierar iOS, Android, macOS och vissa webbläsare från tredje part Azure AD enheten med hjälp av ett klientcertifikat som etableras när enheten är registrerad med Azure AD. När en användare först loggar in via webbläsaren uppmanas användaren att välja certifikatet. Slutanvändaren måste välja det här certifikatet innan han eller hon kan fortsätta att använda webbläsaren.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Fastställa påverkan med hjälp av rapportläge med villkorsstyrd åtkomst](howto-conditional-access-report-only.md)

[Simulera inloggningsbeteende med verktyget Villkorlig åtkomst Vad händer om](troubleshoot-conditional-access-what-if.md)

[Enhetsefterlevnadsprinciper kan användas med Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
