---
title: Så här konfigurerar du registreringsprincip för multi-Factor authentication i Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du konfigurerar registreringsprincip för multifaktorautentisering i Azure AD Identity Protection.
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: e626260dba3155ef56ee4a784aab2c6fd6897295
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581365"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Så här: Konfigurera registreringsprincip för multi-Factor authentication

Azure AD Identity Protection hjälper dig att hantera lansering av registrering för multifaktorautentisering (MFA) genom att konfigurera en princip. Den här artikeln förklarar vad principen kan användas för ett så här konfigurerar du den.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Vad är registreringsprincip för multi-Factor authentication?

Azure Multi-Factor authentication är en metod för att verifiera vem du är som kräver användning av mer än bara ett användarnamn och lösenord. Det ger ett andra säkerhetslager till användarinloggningar och transaktioner.  
Vi rekommenderar att du Kräv Azure Multi-Factor authentication för användarinloggningar eftersom den:

* Du får stark autentisering med en rad enkla verifieringsalternativ
* Spelar en viktig roll i att förbereda din organisation för att skydda och återställa från kontot kompromisser

![Princip för användarrisk](./media/howto-mfa-policy/1019.png "riskprincip för användare")

Mer information finns i [vad är Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="configuration"></a>Konfiguration

**Att öppna dialogrutan tillhörande konfigurationer**:

- På den **Azure AD Identity Protection** bladet i den **konfigurera** klickar du på **registrering av multifaktorautentisering**.

    ![MFA-principen](./media/howto-mfa-policy/1019.png "MFA-principen")

### <a name="settings"></a>Inställningar

* Ange de användare och grupper som principen gäller för:

    ![MFA-principen](./media/howto-mfa-policy/1020.png "MFA-principen")
* Ange kontroller som ska tillämpas när principen utlöser::  

    ![MFA-principen](./media/howto-mfa-policy/1021.png "MFA-principen")
* Växla tillståndet för din princip:

    ![MFA-principen](./media/howto-mfa-policy/403.png "MFA-principen")
* Visa den aktuella registreringsstatusen:

    ![MFA-principen](./media/howto-mfa-policy/1022.png "MFA-principen")

En översikt över relaterade användarupplevelsen finns:

* [Flöde för registrering av multifaktorautentisering](flows.md#multi-factor-authentication-registration).  
* [Logga in som inträffar med Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection kan se den [översikt över Azure AD Identity Protection](overview.md).
