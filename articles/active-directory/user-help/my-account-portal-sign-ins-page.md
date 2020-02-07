---
title: Visa och Sök efter din senaste inloggnings aktivitet från sidan min inloggning (för hands version) – Azure Active Directory | Microsoft Docs
description: Information om hur du visar och söker efter din senaste inloggnings aktivitet från sidan mina inloggningar i mitt konto-portalen.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: b68e7b517ddaa9b2aaef00cf87d5b6e63871654b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064027"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Visa och Sök efter din senaste inloggnings aktivitet från sidan mina inloggningar (för hands version)

Du kan visa all din senaste inloggnings aktivitet för arbets-eller skol konton från sidan **Mina inloggningar** i **mitt konto** Portal. Genom att granska inloggnings historiken kan du söka efter ovanlig aktivitet genom att hjälpa dig att se:

- Om någon försöker gissa ditt lösen ord.

- Om en angripare har loggat in på ditt konto och från vilken plats.

- Vilka appar angriparen försökte få åtkomst till.

## <a name="view-your-recent-sign-in-activity"></a>Visa din senaste inloggningsaktivitet

1. Logga in på ditt arbets-eller skol konto och gå sedan till din https://myprofile.microsoft.com/ sida.

2. Välj **Mina inloggningar (förhands granskning)** i det vänstra navigerings fönstret eller Välj länken **Granska senaste aktivitet** från blockeringen **mitt inloggnings program (för hands version)** .

    ![Sidan mitt konto, som visar markerade senaste aktivitets länkar](media/my-account-portal/my-account-portal-sign-ins.png)

3. Expandera och granska vart och ett av inloggnings objekten och se till att du känner igen dem. Om du hittar ett inloggnings objekt som inte ser bekant ut rekommenderar vi starkt att du ändrar ditt lösen ord för att skydda ditt konto om det skulle ha komprometterats.

    ![Sidan senaste aktivitet med utökad inloggnings information](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Om du ser en lyckad inloggning

Du bör känna igen din egen aktivitet som normal. Men om du antecknar en lyckad inloggning från en konstig plats, webbläsare eller operativ system kan det betyda att en angripare har fått åtkomst till ditt konto. I den här situationen rekommenderar vi att du omedelbart ändrar ditt lösen ord och sedan går till sidan [säkerhets information](https://mysignins.microsoft.com/security-info) för att uppdatera säkerhets inställningarna.

Innan du fastställer något är felaktigt kontrollerar du att du inte ser ett falskt positivt objekt (där objektet ser tveksamt ut, men är OK). Vi fastställer till exempel din ungefärliga plats och mappar baserat på din IP-adress. Mobila nätverk är särskilt svåra att hitta eftersom de ibland dirigerar trafik genom avlägsna platser. Så om du har loggat in med din mobila enhet i Washington-tillstånd kan platsen visa att inloggningen kommer från Kalifornien. Därför rekommenderar vi starkt att du markerar mer information, utöver bara platsen. Du bör också se till att operativ systemet, webbläsaren och appen är så bra.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Om du ser en misslyckad inloggning

En misslyckad inloggning, med ingen session-aktivitet, innebär att den primära verifierings metoden (username/Password) misslyckades. Detta kan betyda att du felaktigt skrev ditt användar namn eller lösen ord, men det kan också betyda att en angripare försökte gissa ditt lösen ord. Om du tror att en angripare försöker att gissa ditt lösen ord behöver du inte ändra ditt lösen ord, men vi rekommenderar starkt att du registrerar dig för Azure Multi-Factor Authentication (MFA). Med MFA, även om hackaren till sist gissar ditt lösen ord, räcker det inte att komma åt ditt konto.

Om du ser en misslyckad inloggning, med en anteckning under session-aktivitet som säger att **Ytterligare verifiering misslyckades, ogiltig kod**, innebär det att din primära autentisering (username/Password) lyckades, men MFA misslyckades. Om detta var en angripare gissade de sig korrekt till ditt lösen ord, men kunde fortfarande inte skicka MFA-utmaningen. I det här fallet rekommenderar vi att du fortfarande ändrar ditt lösen ord, eftersom angriparen får den delen till höger och sedan går till sidan [säkerhets information](https://mysignins.microsoft.com/security-info) för att uppdatera dina säkerhets inställningar.

## <a name="search-for-specific-sign-in-activity"></a>Sök efter en speciell inloggnings aktivitet

Du kan söka efter din senaste inloggnings aktivitet med någon av de tillgängliga uppgifterna. Du kan till exempel söka efter din senaste inloggnings aktivitet efter operativ system, plats, app och så vidare.

1. På sidan **Granska senaste aktivitet** anger du den information som du vill söka efter i **Sök** fältet. Skriv till exempel `My Account` för att söka efter all aktivitet som samlas in av appen mitt konto.

2. Välj **Sök** knappen för att börja söka.

    ![Sidan senaste aktivitet, visar markerat Sök fält, Sök knapp och resultat](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Nästa steg

När du har visat din senaste inloggnings aktivitet kan du:

- Visa eller hantera din [säkerhets information](user-help-security-info-overview.md).

- Visa eller hantera dina anslutna [enheter](my-account-portal-devices-page.md).

- Visa eller hantera dina [organisationer](my-account-portal-organizations-page.md).

- Se hur din organisation [använder dina sekretess-relaterade data](my-account-portal-privacy-page.md).
