---
title: Felsöka tvåstegsverifiering - Azure AD | Microsoft Docs
description: Det här dokumentet ger användare information om vad du gör om de stöter på ett problem med Azure Multi-Factor Authentication.
services: multi-factor-authentication
keywords: Multi-Factor authentication-klienten, problem med autentisering, Korrelations-ID
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/06/2017
ms.author: lizross
ms.reviewer: richagi
ms.custom: end-user
ms.openlocfilehash: deb75c2601fa55f7cdb1681d8f73e94d6b01310a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060077"
---
# <a name="get-help-with-two-step-verification"></a>Få hjälp med tvåstegsverifiering
Den här artikeln får du svar på de vanligaste frågorna om tvåstegsverifiering.

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>Varför måste jag utför en tvåstegsverifiering? Kan jag inaktivera det?

Tvåstegsverifiering är en säkerhetsfunktion som din organisation har valt att använda för att skydda dina konton. Det är säkrare än bara ett lösenord, eftersom den är beroende av två typer av autentisering: något du känner och något du har med dig. Något du känner är ditt lösenord. Något du har med dig är en telefon eller enhet som du ofta har med dig. När ditt konto skyddas med tvåstegsverifiering, betyder det att en hackare inte kan logga in som dig om de får ditt lösenord på något sätt eftersom de inte har åtkomst till din telefon för.

Microsoft erbjuder tvåstegsverifiering, men din organisation väljer att använda funktionen. Du kan inte välja om företagets support kräver det för dig, precis som du inte kan välja bort med ett lösenord för att skydda ditt konto.

Om du har aktiverat för ditt personliga Microsoft-konto för tvåstegsverifiering och vill ändra dina inställningar genom att läsa [om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) i stället.

## <a name="i-dont-have-my-phone-with-me-today"></a>Jag har min telefon med mig i dag

Vissa dagar som du lämnar din telefon i hemmet, men ändå måste du logga in på arbetet. Det första du bör du försöka logga in med en annan verifieringsmetod. När du registrerade för tvåstegsverifiering du ställer in mer än ett telefonnummer? Följ dessa steg om du vill försöka logga in med en annan metod:

1. Logga in som vanligt.
2. När öppnas sidan tvåstegsverifiering verifiering väljer **använder ett annat verifieringsalternativ**.

   ![Olika verifiering](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Välj verifieringsalternativ som du vill använda.
4. Fortsätt med tvåstegsverifiering.

Om du inte ser den **använder ett annat verifieringsalternativ** länka sedan det innebär att du inte konfigurera alternativa metoder när du först registrerade för tvåstegsverifiering. Kontakta företagssupporten om du vill ha hjälp med att logga in på ditt konto. När du har loggat in, se till att [hantera inställningarna](multi-factor-authentication-end-user-manage-settings.md) att lägga till ytterligare verifieringsmetoder till nästa gång.

Om du ser den **använder ett annat verifieringsalternativ** länk, men du inte har tillgång till din alternativa metoder antingen, kontakta företagssupporten för att få hjälp med att logga in på ditt konto.

## <a name="i-lost-my-phone-or-got-a-new-number"></a>Jag min telefon eller om den har fått ett nytt nummer
Det finns två sätt att få tillbaka ditt konto. Först är att logga in med ditt telefonnummer för alternativa autentisering om du har konfigurerat en. Andra är att be företagets support att rensa dina inställningar.

Om telefonen har tappats bort eller blir stulen, rekommenderar vi också att du berätta för ditt företags support så att de kan återställa ditt lösenord och avmarkera sparas enheter.

### <a name="use-an-alternate-phone-number"></a>Använda ett alternativt telefonnummer
Om du har ställt in flera alternativ för verifiering, inklusive ett sekundärt telefonnummer eller en authenticator-appen på en annan enhet kan du använda en av dem för att logga in.

Följ dessa steg för att logga in med den alternativa telefonnummer:

1. Logga in som vanligt.
2. När du uppmanas att verifiera ditt konto ytterligare, välja **använder ett annat verifieringsalternativ**.

   ![Olika verifiering](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Välj det telefonnummer eller en enhet som du har åtkomst till.
4. När du är tillbaka i ditt konto, [hantera inställningarna](multi-factor-authentication-end-user-manage-settings.md) att ändra ditt telefonnummer för autentisering.

### <a name="clear-your-settings"></a>Rensa dina inställningar
Du måste be företagets support om hjälp om du inte har konfigurerat ett telefonnummer för sekundär autentisering. Låta dem Rensa inställningarna så att nästa gång du loggar in kan du uppmanas att [registrera för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) igen.

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Jag kan inte ta emot en text eller anropa på min telefon
Det finns flera orsaker till varför du kan försöka att logga in, men inte ta emot SMS eller telefonsamtal. Om du har fått skickar ett SMS eller telefonsamtal till din telefon tidigare, sedan troligen det ett problem med provider för telefon, inte ditt konto. Kontrollera att du har bra cell signal och om du vill få ett textmeddelande se till att du kan ta emot textmeddelanden. Be en vän att anropa eller textar dig som ett test.

Om du har väntat flera minuter innan en SMS eller samtal, är det snabbaste sättet att komma åt ditt konto att prova ett annat alternativ.

1. Välj **använder ett annat verifieringsalternativ** på sidan som väntar på att verifieringen.

    ![Olika verifiering](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Välj phone tal eller leverans metod som du vill använda.

    Använd det senaste om du har fått flera verifieringskoder.

Om du inte har någon annan metod som har konfigurerats kan kontakta företagets support och be dem att rensa dina inställningar. Nästa gång du loggar in uppmanas du att att [ställer in multifaktorautentisering](multi-factor-authentication-end-user-first-time.md) igen.

Om du har ofta fördröjningar på grund av felaktiga cell signal kan vi rekommenderar att du använder den [Microsoft Authenticator-appen](microsoft-authenticator-app-how-to.md) på din smartphone. Appen kan generera slumpmässiga säkerhetskoder som används för att logga in och dessa koder kräver inte någon cell signal eller internet-anslutning.

## <a name="app-passwords-are-not-working"></a>Applösenord fungerar inte
Kontrollera först att du har angett korrekt applösenordet. Det genererade appen lösenordet ersätter lösenordet normala, men endast för äldre program som inte stöder tvåstegsverifiering. Om det fortfarande inte fungerar kan du försöka logga in och [skapa ett nytt applösenord](multi-factor-authentication-end-user-app-passwords.md).  Om det fortfarande inte fungerar kontaktar du företagets support och de [ta bort dina befintliga applösenord](../authentication/howto-mfa-userdevicesettings.md) och du kan sedan skapa en ny.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Jag kunde inte hitta svar på mitt problem.
Om du har gjort de här felsökningsstegen men är fortfarande körs får problem kan du kontakta företagets support. De bör kunna hjälpa dig.

## <a name="related-topics"></a>Relaterade ämnen
* [Hantera dina inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md)  
* [Microsoft Authenticator-program vanliga frågor och svar](microsoft-authenticator-app-faq.md)
