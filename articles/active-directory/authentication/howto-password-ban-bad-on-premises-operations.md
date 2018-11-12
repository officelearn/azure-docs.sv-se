---
title: Azure AD protection preview lösenordsåtgärder och rapportering
description: Azure AD lösenord protection preview efter distributionen åtgärder och rapportering
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 92c8de0961f64eea8eef830ad99c7baa268099d9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007594"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>Förhandsversion: Azure AD lösenord protection operativa procedurer

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

![Aktivera Azure AD-lösenord protection komponenter i Azure portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Granskningsläge

Granskningsläget är avsedd som ett sätt att köra programvaran i ett ”vad händer om”-läge. Varje DC agent-tjänsten utvärderar ett inkommande lösenord enligt principen är aktiva för tillfället. Om den aktuella principen är konfigurerad för att vara i granskningsläge, resultera i händelseloggen ”dåliga” lösenord men accepteras. Det här är den enda skillnaden mellan gransknings- och Verkställ-läget. alla andra åtgärder kör samma.

> [!NOTE]
> Microsoft rekommenderar att den första distributionen och testar alltid börjar i granskningsläge. Händelser i händelseloggen bör sedan övervakas för att försöka förutse om alla befintliga operativa processer skulle störas när läget tvinga har aktiverats.

## <a name="enforce-mode"></a>Tvingande läge

Framtvinga läge är avsett för den slutgiltiga konfigurationen. I granskningsläget ovan utvärderar varje DC-agenttjänsten inkommande lösenord enligt principen är aktiva för tillfället. Om läget tvinga är aktiverad om ett lösenord som anses vara osäkra enligt principen avvisas.

När ett lösenord avvisas i läget tvinga av Azure AD-lösenordsskydd DC-agenten, är synliga effekten setts av en användare identiskt med vad de skulle se om sitt lösenord avvisades av tvingande för traditionella lokala lösenord komplexitet. En användare kan till exempel se följande traditionella felmeddelande visas på skärmen Windows logon\change lösenord:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Det här meddelandet är bara ett exempel på flera möjliga resultat. Felmeddelandet kan variera beroende på faktiska programvaran eller scenario som försöker ange ett oskyddat lösenord.

Berörda användare kan behöva arbeta med sina IT-personal att förstå de nya kraven och mer kan du välja säkra lösenord.

## <a name="enable-mode"></a>Aktivera läge

Den här inställningen bör normalt lämnas i aktiverad (Ja) standardtillståndet. Konfigurerar den här inställningen inaktiverad (Nej) gör alla distribuerade Azure AD-lösenord skyddsagenter DC försättas i ett overksamt läget där alla lösenord godkänns som – är, och inga aktiviteter för verifiering kommer att utförda helst (till exempel inte ens granskningshändelser ska genereras).

## <a name="usage-reporting"></a>Användningsrapport

Den `Get-AzureADPasswordProtectionSummaryReport` cmdlet kan användas för att generera en övergripande vy över aktiviter. Ett exempel på utdata från denna cmdlet är följande:

```PowerShell
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
> Denna cmdlet fungerar genom att öppna en PowerShell-session till varje domänkontrollant. För att lyckas, stöd för PowerShell-fjärrsession måste vara aktiverat på varje domänkontrollant och klienten måste ha tillräcklig behörighet. Mer information om kraven för PowerShell-fjärrsession kör du ”Get-Help about_Remote_Troubleshooting” i ett PowerShell-fönster.

> [!NOTE]
> Denna cmdlet fungerar genom att via fjärranslutning fråga varje DC agenttjänsten Admin-händelseloggen. Om Loggboken innehåller stora mängder händelser, kan cmdlet: en ta lång tid att slutföra. Massinläsning nätverket förfrågningar av stora datauppsättningar kan också påverka domänkontrollantens prestanda. Den här cmdleten bör därför användas noggrant i produktionsmiljöer.

## <a name="dc-agent-discovery"></a>Identifiering av DC-Agent

Den `Get-AzureADPasswordProtectionDCAgent` cmdlet kan användas för att visa grundläggande information om de olika DC-agenter som körs i en domän eller skog. Den här informationen hämtas från serviceConnectionPoint-objekt som har registrerats av körs DC agenten tjänster. Ett exempel på utdata från denna cmdlet är följande:

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

De olika egenskaperna uppdateras med varje DC-agenttjänsten ungefärliga timme. Data kan fortfarande komma replikeringsfördröjning för Active Directory.

Omfånget för cmdletens fråga kan påverkas med hjälp av antingen parametrarna – skog eller -domän.

## <a name="next-steps"></a>Nästa steg

[Felsökning och övervakning för Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-troubleshoot.md)
