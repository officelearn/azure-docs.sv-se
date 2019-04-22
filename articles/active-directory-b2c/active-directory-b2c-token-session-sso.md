---
title: Sessionen och enkel inloggning-konfiguration – Azure Active Directory B2C | Microsoft Docs
description: Sessionen och enkel inloggning-konfiguration i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 674a20fc96cf5b86219222d746525a3559ae9d09
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681105"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Sessionen och enkel inloggning-konfiguration i Azure Active Directory B2C

Den här funktionen ger dig detaljerad kontroll på en [flow användarnivå](active-directory-b2c-reference-policies.md), av:

- Livslängd för web application sessioner som hanteras av Azure AD B2C.
- Enkel inloggning (SSO) beteende för flera appar och användarflöden i din Azure AD B2C-klient.

## <a name="session-behavior"></a>Sessionsbeteende

Azure AD-B2C stöder den [autentiseringsprotokollet OpenID Connect](active-directory-b2c-reference-oidc.md) för att aktivera säker inloggning till webbprogram. Du kan använda följande egenskaper för att hantera web application sessioner:

- **Webbappssession (minuter)** – livslängden för Azure AD B2C-sessioncookie som lagras i användarens webbläsare efter lyckad autentisering.
    - Standard = 1 440 minuter.
    - Minsta (inklusivt) = 15 minuter.
    - Maximalt (inklusivt) = 1 440 minuter.
- **Webbappssession** – om den här växeln anges till **absolut**, om användaren tvingas att autentisera igen efter hur lång tid som anges av **webbappssession (minuter)** nätverksförbindelse. Om den här växeln anges till **rullande** (standardinställningen), användaren förblir inloggad så länge användaren är aktiv kontinuerligt i webbprogrammet.

Följande användningsfall aktiveras med hjälp av dessa egenskaper:

- Uppfyller kraven för din bransch säkerhet och efterlevnad genom att ange rätt program webbsessionen livslängd.
- Tvinga autentisering efter en viss tidsperiod under användaren interagerar med en hög säkerhet som en del av ditt webbprogram. 

De här inställningarna är inte tillgängliga för att återställa lösenord användarflöden.

## <a name="single-sign-on-sso-configuration"></a>Enkel inloggning (SSO) konfiguration

Om du har flera program och användarflöden i din B2C-klient kan du kan hantera användarinteraktioner över dem med hjälp av den **konfigurationen för enkel inloggning** egenskapen. Du kan ange egenskapen till någon av följande inställningar:

- **Klient** – det här är standardinställningen. Med den här inställningen kan flera program och användare flöden i din B2C-klient för att dela samma användarsessionen. Till exempel när en användare loggar in i ett program, användaren kan även sömlöst logga in på en annan en, Contoso-farmaci, vid åtkomst till den.
- **Programmet** – den här inställningen kan du behålla en användarsession exklusivt för ett program, oberoende av andra program. Till exempel om du vill att användaren att logga in på Contoso farmaci (med samma autentiseringsuppgifter), även om användaren är redan inloggad på Contoso Shopping, ett annat program på samma B2C-klient. 
- **Principen** – den här inställningen kan du behålla en användarsession exklusivt för ett användarflöde, oberoende av de program som använder. Till exempel om användaren har redan loggat in och slutföra ett steg med multi factor authentication (MFA), kan användaren få tillgång till högre säkerhet delar av flera program så länge som den session som är kopplad till användarflödet inte upphör att gälla.
- **Inaktiverad** – den här inställningen Tvingar användaren att köra genom hela användarflödet på varje körning av principen.

De här inställningarna är inte tillgängliga för att återställa lösenord användarflöden. 

