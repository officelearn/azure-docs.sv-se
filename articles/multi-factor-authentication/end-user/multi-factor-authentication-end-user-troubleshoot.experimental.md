---
title: Felsöka tvåstegsverifiering | Microsoft Docs
description: Det här dokumentet ger användare information om vad du gör om de stöter på ett problem med Azure Multi-Factor Authentication.
services: multi-factor-authentication
keywords: flerfunktionsautentisering klienten, problem med autentisering, Korrelations-ID
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: barlan
ms.reviewer: richagi
ms.custom: end-user
ms.openlocfilehash: 736a1f03ef87850fdaaee7ce636d8dd0f3ae1a84
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792060"
---
# <a name="get-help-with-two-step-verification"></a>Få hjälp med tvåstegsverifiering
Den här artikeln innehåller svar på de vanligaste frågorna om tvåstegsverifiering. 

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>Varför måste jag göra tvåstegsverifiering? Kan jag inaktivera den?

Tvåstegsverifiering är en säkerhetsfunktion som din organisation har valt att använda för att skydda dina konton. Det är säkrare än bara ett lösenord, eftersom den är beroende av två typer av autentisering: något du känner till och något du har med dig. Det är något du känner till lösenordet. Något du har med dig är en telefon eller en enhet som du ofta har med dig. När ditt konto skyddas med tvåstegsverifiering logga hackare inte in dig även om de komma åt ditt lösenord. De kan inte logga in eftersom de inte har åtkomst till din telefon. 

Microsoft erbjuder tvåstegsverifiering, men din organisation väljer att använda funktionen. Du kan välja om ditt företag stöd kräver av du, precis som du inte kan välja bort med ett lösenord för att skydda ditt konto. 

Om du har aktiverat för ditt personliga Microsoft-konto för tvåstegsverifiering och vill ändra dina inställningar, läsa [om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) i stället. 

## <a name="i-dont-have-my-phone-with-me-today"></a>Jag har inte min telefon med mig idag

Vissa dagar du lämnar din telefon hemma, men fortfarande behöver logga in på arbetet. Det första du ska försöka logga in med en annan verifieringsmetod. När du registrerat för tvåstegsverifiering du ställa in mer än ett telefonnummer? Följ dessa steg om du vill försöka logga in med en annan metod:

1. Logga in som vanligt.
2. När öppnas sidan tvåstegsverifiering verifiering väljer **använder ett annat verifieringsalternativ**.

   ![Olika verifiering](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Välj verifieringsalternativ som du vill använda. 
  - Om du inte har åtkomst till din alternativa metoder antingen, kontakta stöd för ditt företag för att få hjälp att logga in på ditt konto.
  - Om du har åtkomst till din alternativa metoder, fortsätter du med tvåstegsverifiering.

Om du inte ser den **använder ett annat verifieringsalternativ** länkar, och det innebär att du inte konfigurera alternativa metoder när du först registrerade för tvåstegsverifiering. Kontakta företagets support för att få hjälp att logga in på ditt konto. När du har loggat in, se till att [hantera dina inställningar](multi-factor-authentication-end-user-manage-settings.md) att lägga till ytterligare verifieringsmetoderna för nästa gång. 

## <a name="i-lost-my-phone-or-got-a-new-number"></a>Jag tappar bort min telefon eller har fått ett nytt nummer
Det finns två sätt att få tillbaka ditt konto. Först är att logga in med ditt alternativa autentisering telefonnummer om du har skapat en. Andra är att kontakta företagets support att rensa dina inställningar.

Om telefonen har tappas bort eller blir stulen, rekommenderar vi också att anger du ditt företags-stöd. De behöver återställa ditt lösenord och ta bort alla lagrade enheter. 

### <a name="use-an-alternate-phone-number"></a>Använda ett alternativt telefonnummer
Om du konfigurerar flera alternativ för verifiering, t.ex. ett sekundärt telefonnummer eller en autentiseringsapp på en annan enhet kan du använda en av dem för att logga in.

Följ dessa steg för att logga in med alternativa telefonnummer:

1. Logga in som vanligt.
2. När du uppmanas att verifiera ditt konto ytterligare, välja **använder ett annat verifieringsalternativ**.
   
   ![Olika verifiering](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Välj det telefonnummer eller en enhet som du har åtkomst till.
4. När du är tillbaka i ditt konto, [hantera dina inställningar](multi-factor-authentication-end-user-manage-settings.md) ändra telefonnummer för autentisering.

### <a name="clear-your-settings"></a>Rensa dina inställningar
Du måste kontakta företagets support för hjälp om du inte har konfigurerat ett telefonnummer för sekundära autentiseringen. Be dem ta bort inställningarna så nästa gång du loggar in, uppmanas du att [registrera dig för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) igen.

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Jag kan inte ta emot en text eller anropa på min telefon
Det finns flera skäl till varför du kan försöka logga in, men inte ta emot text eller telefonsamtal. Om du har fått text eller samtal till din telefon tidigare, sedan det här problemet är förmodligen ett problem med phone-providern inte ditt konto. Kontrollera att du har en bra cell signal. Och om du vill få ett textmeddelande, se till att du ska kunna ta emot textmeddelanden. Be en vän att anropa du eller text du som ett test. 

Om du har väntat flera minuter innan ett textmeddelande eller samtal är det snabbaste sättet att komma åt ditt konto du prova ett annat alternativ.

1. Välj **använder ett annat verifieringsalternativ** på sidan som väntar på att verifieringen.
   
    ![Olika verifiering](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Välj phone tal eller leverans metod som du vill använda.
   
    Använd det senaste om du har fått flera verifieringskoder.

Om du inte har någon annan metod som har konfigurerats, kontakta företagets support och be dem ta bort dina inställningar. Nästa gång du loggar in, uppmanas du att [konfigurera multifaktorautentisering](multi-factor-authentication-end-user-first-time.md) igen.

Om du har ofta fördröjningar på grund av felaktiga cell signal, vi rekommenderar att du använder den [Microsoft Authenticator-appen](microsoft-authenticator-app-how-to.md) på din smartphone. Appen kan generera slumpmässigt säkerhetskoder som du använder för att logga in och dessa koder kräver inte någon cell signal eller internet-anslutning.

## <a name="app-passwords-are-not-working"></a>Applösenord fungerar inte
Kontrollera först att du har angett rätt applösenordet. Genererade applösenordet ersätter lösenordet normal, men endast för äldre program som inte stöder tvåstegsverifiering. Om det fortfarande inte fungerar, försök logga in och [skapa ett nytt applösenord](multi-factor-authentication-end-user-app-passwords.md).  Om det fortfarande inte fungerar kan kontakta företagets support och ha dem [ta bort dina befintliga applösenord](../../active-directory/authentication/howto-mfa-userdevicesettings.md) och du kan sedan skapa en ny.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Det gick inte att hitta ett svar till mitt problem.
Om du har gjort dessa felsökningssteg men är fortfarande körs problem, kontakta företagets support. De ska kunna hjälpa dig.

## <a name="related-topics"></a>Relaterade ämnen
* [Hantera inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md)  
* [Microsoft Authenticator program vanliga frågor och svar](microsoft-authenticator-app-faq.md)

