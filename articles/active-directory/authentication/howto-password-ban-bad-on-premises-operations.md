---
title: Azure AD-lösenordsskydd operations och rapportering
description: Azure AD-lösenordsskydd efter distributionen-åtgärder och rapportering
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac91a70465d79b235ae43681a1375d47e7691efe
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285810"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Azure AD-lösenordsskydd operativa procedurer

När du har slutfört den [installation av Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md) lokalt, det finns några objekt som måste konfigureras i Azure-portalen.

## <a name="configure-the-custom-banned-password-list"></a>Konfigurera listan med anpassade förbjudna lösenord

Följ anvisningarna i artikeln [konfigurera listan över anpassade förbjudna lösenord](howto-password-ban-bad-configure.md) anvisningar om hur du anpassar lista med förbjudna lösenord för din organisation.

## <a name="enable-password-protection"></a>Aktivera lösenordsskydd

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory**, **autentiseringsmetoder**, sedan **lösenordsskydd**.
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
