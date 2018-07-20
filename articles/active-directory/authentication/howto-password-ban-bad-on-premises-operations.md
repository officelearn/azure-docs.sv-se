---
title: Azure AD protection preview lösenordsåtgärder och rapportering
description: Azure AD lösenord protection preview efter distributionen åtgärder och rapportering
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 14aa52b6d424423f4863efa63f3e2e66b84dac70
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39164011"
---
# <a name="preview-azure-ad-password-protection-post-deployment"></a>Förhandsversion: Azure AD lösenord skydd efter distributionen

|     |
| --- |
| Azure AD-lösenordsskydd och listan över anpassade förbjudna lösenord är funktioner i offentlig förhandsversion av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

När du har slutfört den [installation av Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises.md) lokalt, det finns några objekt som måste konfigureras i Azure-portalen.

## <a name="configure-the-custom-banned-password-list"></a>Konfigurera listan med anpassade förbjudna lösenord

Följ anvisningarna i artikeln [konfigurera listan över anpassade förbjudna lösenord](howto-password-ban-bad.md) anvisningar om hur du anpassar lista med förbjudna lösenord för din organisation.

## <a name="enable-password-protection"></a>Aktivera lösenordsskydd

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory**, **autentiseringsmetoder**, sedan **lösenordsskydd (förhandsversion)**.
1. Ange **aktivera lösenordsskydd på Windows Server Active Directory** till **Ja**
1. Som vi nämnde i den [Distributionsguide](howto-password-ban-bad-on-premises.md#deployment-strategy), rekommenderar vi att du med att ange den **läge** till **granskning**
   * När du är nöjd med funktionen kan du växla den **läge** till **tvingande**
1. Klicka på **Spara**

![Aktivera Azure AD-lösenord protection komponenter i Azure portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Granskningsläge

Granskningsläget är avsedd som ett sätt att köra programvaran i ett ”vad händer om”-läge. Varje DC agent-tjänsten utvärderar ett inkommande lösenord enligt principen är aktiva för tillfället. Om den aktuella principen är konfigurerad för att vara i granskningsläge, resultera i händelseloggen ”dåliga” lösenord men accepteras. Det här är den enda skillnaden mellan gransknings- och Verkställ-läget. alla andra åtgärder kör samma.

> [!NOTE]
> Microsoft rekommenderar att den första distributionen och testar alltid börjar i granskningsläge. Händelser i händelseloggen bör sedan övervakas för att försöka förutse om alla befintliga operativa processer skulle störas när läget tvinga har aktiverats.

## <a name="enforce-mode"></a>Tvingande läge

Framtvinga läge är avsett för den slutgiltiga konfigurationen. I granskningsläget ovan utvärderar varje DC-agenttjänsten inkommande lösenord enligt principen är aktiva för tillfället. Om läget tvinga är aktiverad om ett lösenord som anses vara osäkra enligt principen avvisas.

När ett lösenord avvisas i läget tvinga av Azure AD-lösenordsskydd DC-agenten, är synliga effekten setts av en användare identiskt med vad de skulle se om sitt lösenord avvisades av tvingande för traditionella lokala lösenord komplexitet. En användare kan till exempel se följande traditionella felmeddelande visas på skärmen logon\change lösenord:

”Det gick inte att uppdatera lösenordet. Värdet som angetts för det nya lösenordet uppfyller inte längd, komplexitet eller historik enligt domänen ”.

Det här meddelandet är bara ett exempel på flera möjliga resultat. Felmeddelandet kan variera beroende på faktiska programvaran eller scenario som försöker ange ett oskyddat lösenord.

Berörda användare kan behöva arbeta med sina IT-personal att förstå de nya kraven och mer kan du välja säkra lösenord.

## <a name="usage-reporting"></a>Användningsrapport

Den `Get-AzureADPasswordProtectionSummaryReport` cmdlet kan användas för att generera en övergripande vy över aktiviter. Ett exempel på utdata från denna cmdlet är följande:

```
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Omfånget för cmdletens reporting kan påverkas med någon av parametrarna – skog, - domän eller – DomainController. Inte anger en parameter innebär – skog.

> [!NOTE]
> Denna cmdlet fungerar genom att via fjärranslutning fråga varje DC agenttjänsten Admin-händelseloggen. Om Loggboken innehåller stora mängder händelser, kan cmdlet: en ta lång tid att slutföra. Massinläsning nätverket förfrågningar av stora datauppsättningar kan också påverka domänkontrollantens prestanda. Den här cmdleten bör därför användas noggrant i produktionsmiljöer.

## <a name="next-steps"></a>Nästa steg

[Felsökning och loggningsinformation om Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-troubleshoot.md)
