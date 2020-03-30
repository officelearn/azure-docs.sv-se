---
title: Riskprinciper - Azure Active Directory Identity Protection
description: Aktivera och konfigurera riskprinciper i Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707013"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Så här konfigurerar och aktiverar du riskprinciper

Som vi lärde oss i föregående artikel, [Identity Protection politik](concept-identity-protection-policies.md) vi har två riskpolicyer som vi kan aktivera i vår katalog. 

- Riskpolicy för inloggning
- Policy för användarrisk

![Sidan Säkerhetsöversikt för att aktivera användar- och inloggningsriskprinciper](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Båda principerna arbetar för att automatisera svaret på riskidentifieringar i din miljö och tillåta användare att själv åtgärda när risken upptäcks. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Krav 

Om din organisation vill tillåta användare att själv åtgärda när risker upptäcks, måste användare registreras för både självbetjäningslösenordsåterställning och Azure Multi-Factor Authentication. Vi rekommenderar att [du aktiverar den kombinerade säkerhetsinformationsregistreringsupplevelsen](../authentication/howto-registration-mfa-sspr-combined.md) för bästa upplevelse. Att tillåta användare att själv åtgärda får dem tillbaka till ett produktivt tillstånd snabbare utan att kräva administratörsintervention. Administratörer kan fortfarande se dessa händelser och undersöka dem i efterhand. 

## <a name="choosing-acceptable-risk-levels"></a>Välja acceptabla risknivåer

Organisationer måste bestämma vilken risknivå de är villiga att acceptera att balansera användarupplevelse och säkerhetsposition. 

Microsofts rekommendation är att ange tröskelvärdet för användarriskprincip till **Hög** och principen för inloggningsrisk till **Medel och högre**.

Om du väljer ett **högt** tröskelvärde minskar antalet gånger en princip utlöses och påverkan för användarna minimeras. Det utesluter dock **låg-** och **medelhög** riskidentifieringar från principen, som kanske inte blockerar en angripare från att utnyttja en komprometterade identitet. Om du väljer ett **lågt** tröskelvärde införs ytterligare användaravbrott, men ökad säkerhetsposition.

## <a name="exclusions"></a>Undantag

Alla principer gör det möjligt att utesluta användare, till exempel din [nödsituationsåtkomst eller administratörskonton för brytglas.](../users-groups-roles/directory-emergency-access.md) Organisationer kan bestämma att de måste utesluta andra konton från specifika principer baserat på hur kontona används. Alla undantag bör ses över regelbundet för att se om de fortfarande är tillämpliga.

Konfigurerade betrodda [nätverksplatser](../conditional-access/location-condition.md) används av Identity Protection i vissa riskidentifieringar för att minska falska positiva identifieringar.

## <a name="enable-policies"></a>Aktivera principer

Så här aktiverar du användarrisk- och inloggningsriskprinciper:

1. Navigera till [Azure-portalen](https://portal.azure.com).
1. Bläddra till**Översikt över** **Azure Active Directory** > **Security** > **Identity Protection** > .
1. Välj **Konfigurera användarriskprincip**.
   1. Under **Uppdrag**
      1. **Användare** – Välj **Alla användare** eller Välj personer **och grupper** om du begränsar distributionen.
         1. Du kan också välja att utesluta användare från principen.
      1. **Villkor** - **Användarrisk** Microsofts rekommendation är att ställa in det här alternativet till **Hög**.
   1. Under **Kontroller**
      1. **Access** - Microsofts rekommendation är att **tillåta åtkomst** och **kräv lösenordsändring**.
   1. **Framtvinga princip** - **på**
   1. **Spara** - Den här åtgärden kommer att gå tillbaka till **översiktssidan.**
1. Välj **Konfigurera inloggningsriskprincip**.
   1. Under **Uppdrag**
      1. **Användare** – Välj **Alla användare** eller Välj personer **och grupper** om du begränsar distributionen.
         1. Du kan också välja att utesluta användare från principen.
      1. **Villkor** - **Inloggningsrisk** Microsofts rekommendation är att ställa in det här alternativet till **Medium och högre**.
   1. Under **Kontroller**
      1. **Access** - Microsofts rekommendation är att **tillåta åtkomst** och **kräva multifaktorautentisering**.
   1. **Framtvinga princip** - **på**
   1. **Spara**

## <a name="next-steps"></a>Nästa steg

- [Aktivera registreringsprincipen för multifaktorautentisering i Azure](howto-identity-protection-configure-mfa-policy.md)

- [Vad är risk](concept-identity-protection-risks.md)

- [Undersöka riskidentifieringar](howto-identity-protection-investigate-risk.md)

- [Simulera riskidentifieringar](howto-identity-protection-simulate-risk.md)
