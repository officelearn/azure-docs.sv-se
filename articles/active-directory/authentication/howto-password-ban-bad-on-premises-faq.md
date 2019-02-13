---
title: Den lokala Azure AD lösenordsskydd vanliga frågor och svar
description: Den lokala Azure AD lösenordsskydd vanliga frågor och svar
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
ms.openlocfilehash: 0a4528cde92c5f738fa3fa7f4a649d84b1e79431
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175956"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Förhandsversion: Azure AD-lösenordsskydd lokalt – vanliga frågor och svar

|     |
| --- |
| Azure AD-lösenordsskydd är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Allmänna frågor

**F: När uppnår allmän tillgänglighet (GA) i Azure AD-lösenordsskydd?**

Allmän tillgänglighet planeras Q1 CY2019 (före slutet av mars 2019). Tack för alla som har skickat feedback om funktionen på datum - vi uppskattar den!

**F: Är den lokala Azure AD-lösenord Protection stöds i icke-offentlig moln?**

Nej, lokala Azure AD-lösenord skydd stöds endast i det offentliga molnet.

**F: Hur kan jag använda Azure AD-lösenordsskydd fördelar till en delmängd av Mina lokala användare?**

Stöds ej. När distribuerats och aktiverats, Azure AD-lösenordsskydd skilja inte – alla användare får lika säkerhetsfördelarna.

**F: Finns det stöd för att installera Azure AD-lösenord Protection sida vid sida med andra filter-lösenordsbaserade produkter?**

Ja. Stöd för flera filter DLL-filer för registrerade lösenord är en core Windows-funktion och inte specifikt för Azure AD-lösenordsskydd. Alla registrerade lösenord filter DLL: er måste godkänna innan ett lösenord har godkänts.

**F: Varför krävs DFSR för sysvol-replikering?**

FRS (föregångare till DFSR) har många kända problem och är helt och hållet stöds inte i senare versioner av Windows Server Active Directory. Noll testning av Azure AD-lösenordsskydd görs på FRS-konfigurerade domäner.

Mer information finns i följande artiklar:

[Migrera sysvol-replikering till DFSR fallet](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Slutet är Nigh för FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**F: Varför är en omstart krävs för att installera eller uppgradera DC-agentprogramvaran?**

Det här kravet orsakas av core Windows beteende.

**F: Finns det något sätt att konfigurera en DC-agenten för att använda en särskild proxyserver?**

Nej.

## <a name="next-steps"></a>Nästa steg

Om du har en lokal Azure AD-lösenordsskydd fråga som inte besvaras här kan du skicka in ett feedbackobjekt nedan – tack!

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
