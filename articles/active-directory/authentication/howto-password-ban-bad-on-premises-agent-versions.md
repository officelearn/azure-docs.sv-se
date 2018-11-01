---
title: Versionshistorik för Azure AD lösenord protection agent på plats
description: Dokument-versionen och beteende ändringshistorik
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: c52c84a1311c30c19356bb8a1287b203faf476fc
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50743263"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>Förhandsversion: Azure AD lösenord protection agent-versionshistorik

|     |
| --- |
| Azure AD-lösenordsskydd är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12100"></a>1.2.10.0

Utgivningsdatum: 8/17/2018

Korrigeringar:

* Registrera AzureADPasswordProtectionProxy och registrera AzureADPasswordProtectionForest stöder nu multifaktorautentisering
* Registrera AzureADPasswordProtectionProxy kräver en WS2012 eller senare domänkontrollanten i domänen för att undvika krypteringsfel.
* DC-agenttjänsten är mer tillförlitligt om hur du begär en ny lösenordsprincip för från Azure vid start.
* DC-agenttjänsten begär en ny lösenordsprincip för från Azure varje timme om det behövs, men kommer nu att göra det på ett slumpmässigt valda starttiden.
* DC-agenttjänsten kommer inte längre en obestämd fördröjning i ny DC-annons när installeras på en server innan dess befordran som en replik.
* DC-agenttjänsten följer nu Konfigurationsinställningen ”aktivera lösenordsskydd på Windows Server Active Directory”
* Installationsprogram för både DC-agenten och proxy har nu stöd för uppgradering på plats när du uppgraderar till framtida versioner.

> [!WARNING]
> Uppgradering från version 1.1.10.3 stöds inte och leder till installationsfel. Till uppgradera till version 1.2.10 eller senare, du måste först helt avinstallera programvaran för DC-agenten och proxy-tjänsten och sedan installera den nya versionen från grunden. Omregistrering av Azure AD-lösenordsskydd proxytjänsten krävs.  Det krävs inte registrera skogen.

> [!NOTE]
> Uppgraderingar på plats av DC-agentprogramvaran kräver en omstart.

* DC-agenten och proxy-tjänsten nu kan du köra på en server som konfigurerats att bara använda FIPS-kompatibla algoritmer.
* Förbättrad loggning
* Lägre prestanda och stabilitet korrigeringar

## <a name="11103"></a>1.1.10.3

Utgivningsdatum: 6/15/2018

Första offentliga förhandsversionen

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
