---
title: Azure AD-lösenordsskydd preview-åtgärder och rapportering
description: Efter distributionen driften av Azure AD-lösenordsskydd förhandsversionen och rapportering
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: a77a6dd8b408fd8151cb12b7d0269b8890ef929b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662422"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>Förhandsversion: Azure AD-lösenordsskydd operativa procedurer

|     |
| --- |
| Azure AD-lösenordsskydd är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

När du har slutfört den [installation av Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md) lokalt, det finns några objekt som måste konfigureras i Azure-portalen.

## <a name="configure-the-custom-banned-password-list"></a>Konfigurera listan med anpassade förbjudna lösenord

Följ anvisningarna i artikeln [konfigurera listan över anpassade förbjudna lösenord](howto-password-ban-bad-configure.md) anvisningar om hur du anpassar lista med förbjudna lösenord för din organisation.

## <a name="enable-password-protection"></a>Aktivera lösenordsskydd

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory**, **autentiseringsmetoder**, sedan **lösenordsskydd (förhandsversion)**.
1. Ange **aktivera lösenordsskydd på Windows Server Active Directory** till **Ja**
1. Som vi nämnde i den [Distributionsguide](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), rekommenderar vi att du med att ange den **läge** till **granskning**
   * När du är nöjd med funktionen kan du växla den **läge** till **tvingande**
1. Klicka på **Spara**

![Aktivera Azure AD-lösenordsskydd komponenter i Azure portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Granskningsläge

Granskningsläget är avsedd som ett sätt att köra programvaran i ett ”vad händer om”-läge. Varje DC agent-tjänsten utvärderar ett inkommande lösenord enligt principen är aktiva för tillfället. Om den aktuella principen är konfigurerad för att vara i granskningsläge, resultera i händelseloggen ”dåliga” lösenord men accepteras. Det här är den enda skillnaden mellan gransknings- och Verkställ-läget. alla andra åtgärder kör samma.

> [!NOTE]
> Microsoft rekommenderar att den första distributionen och testar alltid börjar i granskningsläge. Händelser i händelseloggen bör sedan övervakas för att försöka förutse om alla befintliga operativa processer skulle störas när läget tvinga har aktiverats.

## <a name="enforce-mode"></a>Tvingande läge

Framtvinga läge är avsett för den slutgiltiga konfigurationen. I granskningsläget ovan utvärderar varje DC-agenttjänsten inkommande lösenord enligt principen är aktiva för tillfället. Om läget tvinga är aktiverad om ett lösenord som anses vara osäkra enligt principen avvisas.

När ett lösenord avvisas i läget tvinga av Azure AD-lösenord Protection DC-agenten, är synliga effekten setts av en användare identiskt med vad de skulle se om sitt lösenord avvisades av tvingande för traditionella lokala lösenord komplexitet. En användare kan till exempel se följande traditionella felmeddelande visas på skärmen Windows logon\change lösenord:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Det här meddelandet är bara ett exempel på flera möjliga resultat. Felmeddelandet kan variera beroende på faktiska programvaran eller scenario som försöker ange ett oskyddat lösenord.

Berörda användare kan behöva arbeta med sina IT-personal att förstå de nya kraven och mer kan du välja säkra lösenord.

## <a name="enable-mode"></a>Aktivera läge

Den här inställningen bör normalt lämnas i aktiverad (Ja) standardtillståndet. Konfigurerar den här inställningen inaktiverad (Nej) gör alla distribuerade Azure AD-lösenord Protection DC-agenter att komma in i ett overksamt läge där alla lösenord godkänns som – är, och inga aktiviteter för verifiering kommer att utförda helst (till exempel inte ens granskningshändelser ska genereras).

## <a name="next-steps"></a>Nästa steg

[Övervakning för Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-monitor.md)
