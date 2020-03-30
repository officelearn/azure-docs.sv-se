---
title: Visa och sök i din senaste inloggningsaktivitet från sidan Min inloggning (förhandsversion) - Azure Active Directory | Microsoft-dokument
description: Information om hur du visar och söker efter din senaste inloggningsaktivitet från sidan Mina inloggningar på portalen Mitt konto.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064027"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Visa och sök i din senaste inloggningsaktivitet från sidan Mina inloggningar (förhandsgranska)

Du kan visa alla dina senaste **inloggningsaktiviteter** för arbets- eller skolkonto, från sidan Mina inloggningar på portalen **Mitt konto.** Genom att granska din inloggningshistorik kan du söka efter ovanlig aktivitet genom att hjälpa dig att se:

- Om någon försöker gissa ditt lösenord.

- Om en angripare har loggat in på ditt konto och från vilken plats.

- Vilka appar angriparen försökte komma åt.

## <a name="view-your-recent-sign-in-activity"></a>Visa din senaste inloggningsaktivitet

1. Logga in på ditt arbets- eller https://myprofile.microsoft.com/ skolkonto och gå sedan till din sida.

2. Välj **Mina inloggningar (förhandsgranskning) i** det vänstra navigeringsfönstret eller välj länken Granska **senaste aktivitet** i blocket Mina **inloggningar (förhandsgranskning).**

    ![Sidan Mitt konto, som visar markerade Senaste aktivitetslänkar](media/my-account-portal/my-account-portal-sign-ins.png)

3. Expandera och granska var och en av inloggningsobjekten och se till att du känner igen var och en. Om du hittar ett inloggningsobjekt som inte ser bekant ut rekommenderar vi starkt att du ändrar ditt lösenord för att skydda ditt konto om det komprometterades.

    ![Senaste aktivitetssida med utökad inloggningsinformation](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Om du ser en lyckad inloggning

Du bör känna igen din egen aktivitet som normal. Men om du upptäcker en lyckad inloggning från konstig plats, webbläsare eller operativsystem kan det innebära att en angripare har fått åtkomst till ditt konto. I det här fallet rekommenderar vi att du omedelbart ändrar ditt lösenord och sedan går till sidan [Säkerhetsinformation](https://mysignins.microsoft.com/security-info) för att uppdatera dina säkerhetsinställningar.

Innan du bestämmer dig för att något är felaktigt, se till att du inte ser ett falskt positivt (där objektet ser tveksamt ut, men är okej). Vi bestämmer till exempel din ungefärliga plats och karta baserat på din IP-adress. Mobilnät är särskilt svåra att sätta fingret på eftersom de ibland dirigerar trafik genom avlägsna platser. Så om du har loggat in med din mobila enhet i delstaten Washington kan platsen visa inloggningen från Kalifornien. På grund av detta rekommenderar vi starkt att du kontrollerar mer information, utöver bara platsen. Du bör också se till att operativsystemet, webbläsaren och appen alla vettigt också.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Om du ser en misslyckad inloggning

En misslyckad inloggning, utan sessionsaktivitet, innebär att din primära verifieringsmetod (användarnamn/lösenord) misslyckades. Detta kan innebära att du skrivit fel användarnamn eller lösenord, men det kan också innebära att en angripare försökte gissa ditt lösenord. Om du tror att det var angriparen som försökte gissa ditt lösenord utan framgång behöver du inte ändra ditt lösenord, men vi rekommenderar starkt att du registrerar dig för Azure Multi-Factor Authentication (MFA). Med MFA, även om hackaren så småningom gissar ditt lösenord, kommer det inte att räcka för att komma åt ditt konto.

Om du ser en misslyckad inloggning, med en anteckning under Sessionsaktivitet som säger, **Ytterligare verifiering misslyckades, ogiltig kod**, betyder det att din primära autentisering (användarnamn / lösenord) lyckades, men MFA misslyckades. Om detta var en angripare, gissade de korrekt ditt lösenord men kunde fortfarande inte passera MFA utmaning. I det här fallet rekommenderar vi att du fortfarande ändrar ditt lösenord, eftersom angriparen fick den delen rätt, och sedan gå till sidan [Säkerhetsinformation](https://mysignins.microsoft.com/security-info) för att uppdatera dina säkerhetsinställningar.

## <a name="search-for-specific-sign-in-activity"></a>Sök efter specifik inloggningsaktivitet

Du kan söka i din senaste inloggningsaktivitet med någon av de tillgängliga uppgifterna. Du kan till exempel söka efter din senaste inloggningsaktivitet efter operativsystem, plats, app och så vidare.

1. På sidan **Granska senaste aktivitet** skriver du den information du vill söka efter i **sökfältet.** Skriv `My Account` till exempel om du vill söka efter all aktivitet som samlas in av appen Mitt konto.

2. Välj knappen **Sök** för att börja söka.

    ![Sidan Senaste aktivitet, som visar markerat sökfält, sökknapp och resultat](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Nästa steg

När du har tittat på din senaste inloggningsaktivitet kan du:

- Visa eller hantera [din säkerhetsinformation](user-help-security-info-overview.md).

- Visa eller hantera dina anslutna [enheter](my-account-portal-devices-page.md).

- Visa eller hantera dina [organisationer](my-account-portal-organizations-page.md).

- Visa hur din organisation [använder dina sekretessrelaterade data](my-account-portal-privacy-page.md).
