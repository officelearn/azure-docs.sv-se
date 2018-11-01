---
title: Lokala Azure AD-lösenordsskydd vanliga frågor och svar
description: Lokala Azure AD-lösenordsskydd vanliga frågor och svar
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 59c89c81f618876de48de66a38e1063eb658fba4
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50743298"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Förhandsversion: Azure AD lösenord skydd lokalt – vanliga frågor och svar

|     |
| --- |
| Azure AD-lösenordsskydd är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Allmänna frågor

**F: när uppnår allmän tillgänglighet (GA) i Azure AD-lösenordsskydd?**

Vi har inte meddelat allmän tillgänglighet datum än.

**F: är den lokala Azure AD-lösenordsskydd som stöds i icke-offentlig moln?**

Nej, den lokala Azure AD-lösenordsskydd stöds bara i det offentliga molnet.

**F: hur kan jag använda fördelarna med Azure AD lösenord skydd till en delmängd av Mina lokala användare?**

Stöds ej. När distribuerats och aktiverats, Azure AD-lösenordsskydd skilja inte – alla användare får lika säkerhetsfördelarna.

**F: finns det stöd för att installera Azure AD-lösenordsskydd sida vid sida med andra filter-lösenordsbaserade produkter?**

Ja. Stöd för flera filter DLL-filer för registrerade lösenord är en core Windows-funktion och inte specifikt för Azure AD-lösenordsskydd. Alla registrerade lösenord filter DLL: er måste godkänna innan ett lösenord har godkänts.

**F: Varför krävs DFSR för sysvol-replikering?**

FRS (föregångare till DFSR) har många kända problem och är helt och hållet stöds inte i senare versioner av Windows Server Active Directory. Noll testning av Azure AD-lösenordsskydd görs på FRS-konfigurerade domäner.

Mer information finns i följande artiklar:

[Migrera sysvol-replikering till DFSR fallet](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Slutet är Nigh för DRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**F: Varför är en omstart krävs för att installera eller uppgradera DC-agentprogramvaran?**

Det här kravet orsakas av core Windows beteende.

**F: finns det något sätt att konfigurera en DC-agenten för att använda en särskild proxyserver?**

Nej.

## <a name="next-steps"></a>Nästa steg

Om du har en lokal Azure AD protection lösenordsfråga som inte besvaras här kan du skicka in ett feedbackobjekt nedan – tack!

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
