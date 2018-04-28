---
title: Säkerhetskopiera och återställa information med Microsoft Authenticator - Azure | Microsoft Docs
description: Lär dig mer om att säkerhetskopiera och återställa autentiseringsuppgifterna för ditt konto, använder Microsoft Authenticator-appen.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: lizross
ms.reviewer: olhaun
ms.custom: end-user
ms.openlocfilehash: 0b76d2debb9814188e5551252e52bd0dae38eb50
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Säkerhetskopiera och Återställ kontoautentiseringsuppgifter med Microsoft Authenticator-appen
**Gäller för:**

- iOS-enheter

Microsoft Authenticator-appen säkerhetskopierar dina autentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton till molnet. Efter säkerhetskopieringen, du kan också använda appen för att återställa din information på en ny enhet undvika potentiellt komma låst out- eller att skapa konton.

>[!IMPORTANT]
> Du behöver ett personligt Microsoft-konto och en iCloud-konto för varje lagringsplats för säkerhetskopiering. Men i den lagringsplatsen du kan säkerhetskopiera flera konton. Du kan till exempel har ett personligt konto, en skolans konto och ett konto från tredje part som Facebook, Google, och så vidare.<br><br>Dina autentiseringsuppgifter lagras som innehåller ditt användarnamn och 8 siffror konto verifieringskoden som krävs för att styrka din identitet med Microsoft Authenticator-appen. Annan information som är kopplade till dina konton, inklusive e-post eller filer lagras inte. Vi inte också koppla eller dela dina konton på något sätt eller med en produkt eller tjänst. Och slutligen IT-administratören får inte någon information om något av dessa konton.

## <a name="back-up-your-account-credentials"></a>Säkerhetskopiera dina autentiseringsuppgifter
Innan du kan säkerhetskopiera dina autentiseringsuppgifter måste ha både:

- En personlig [Microsoft-konto](https://account.microsoft.com/account) ska fungera som kontot för återställning.

- En [iCloud-konto](https://www.icloud.com/) för den faktiska lagringsplatsen. 

Att kräva att du loggar in på båda kontona som tillsammans ger bättre säkerhet för din information om säkerhetskopieringen.

**Aktivera molnet säkerhetskopiering**
-   På iOS-enheten, Välj **inställningar**väljer **säkerhetskopiering**, och sedan aktivera **automatisk säkerhetskopiering**.

    Autentiseringsuppgifterna för ditt konto säkerhetskopieras till iCloud-konto.

    ![skärmen för iOS-inställningar, visar var automatisk inställningar för säkerhetskopiering](./media/authenticator-app-backup-and-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Återställa autentiseringsuppgifterna för ditt konto på den nya enheten
Du kan återställa autentiseringsuppgifterna för ditt konto från iCloud-konto med samma Microsoft recovery-konto du ställt in när du har säkerhetskopierat din information.

**Att återställa din information**
1.  Öppna Microsoft Authenticator-appen på din iOS-enhet och välj **börja återställa** längst ned på skärmen.

    ![Microsoft Authenticator-appen, visar var du ska klicka Begin-återställning](./media/authenticator-app-backup-and-recovery/backup-and-recovery-begin-recovery.png)

2.  Logga in på kontot återställning med hjälp av samma personliga Microsoft-konto du använde under säkerhetskopieringen.

    Autentiseringsuppgifterna för ditt konto har återställts till den nya enheten.

När du har slutfört din återställningspunkt märker du att ditt personliga Microsoft-kontokoder i appen Microsoft Authenticator skiljer sig mellan ditt gamla och nya telefoner. Koderna är olika eftersom varje enhet har en egen unik autentiseringsuppgifter, men både är giltigt och fungerar när du loggar in med hjälp av den associera telefonen.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Återställa ytterligare konton som kräver mer kontroll
Om du använder push-meddelanden med ditt personliga, arbets- eller skolkonton, får du ett på skärmen varning som säger att du måste ange ytterligare verifiering innan du kan återställa din information. Eftersom push-meddelanden kräver att du använder en autentiseringsuppgift som inte har kopplat till din enhet och aldrig skickas över nätverket, måste du verifiera din identitet innan autentiseringsuppgifter skapas på enheten.

För personliga Microsoft-konton styrka du din identitet genom att ange ditt lösenord tillsammans med ett alternativt e-postadress eller telefonnummer nummer. För arbetet eller skolan konton, måste du läsa in en QR-kod som du fått av ditt konto-providern.

**Att tillhandahålla ytterligare verifiering för personliga konton**
1.  I den **konton** skärm med Microsoft Authenticator-appen, Välj nedrullningsbara pilen bredvid det konto som du vill återställa.

    ![Microsoft Authenticator-appen, visar tillgängliga konton med deras associerade nedrullningsbara pilar](./media/authenticator-app-backup-and-recovery/backup-and-recovery-arrow.png)

2.  Välj **logga in att återställa**, ange ditt lösenord och bekräfta din e-postadress eller telefonnummer nummer som ytterligare verifiering.

    ![Microsoft Authenticator-appen, så att du kan ange din information för inloggning](./media/authenticator-app-backup-and-recovery/backup-and-recovery-sign-in.png)

**Att tillhandahålla ytterligare verifiering för arbets-eller skolkonton**
1.  I den **konton** skärm med Microsoft Authenticator-appen, Välj nedrullningsbara pilen bredvid det konto som du vill återställa.

    ![Microsoft Authenticator-appen, visar tillgängliga konton med deras associerade nedrullningsbara pilar](./media/authenticator-app-backup-and-recovery/backup-and-recovery-additonal-accts.png)

2.  Välj **skanna QR-kod för att återställa**, och sedan skanna QR-koden som tillhandahålls av administratören.

    ![Microsoft Authenticator-appen, så att du kan skanna QR-kod](./media/authenticator-app-backup-and-recovery/backup-and-recovery-scan-qr-code.png)

## <a name="troubleshooting-backup-and-recovery-problems"></a>Felsökning av säkerhetskopiering och återställning
Det finns flera skäl till säkerhetskopian inte är tillgängliga:

-   **Ändra operativsystem.** Säkerhetskopian lagras i molnet lagringsalternativ som tillhandahålls av din telefon operativsystem, vilket innebär att säkerhetskopian är inte tillgängligt om du växlar mellan Android och iOS. I den här situationen måste du manuellt återskapa ditt konto i appen.

-   **Problem med nätverket eller lösenord.** Kontrollera att du är ansluten till ett nätverk och loggat in på ditt iCloud-konto med samma AppleID som du använde på din senaste iPhone.

-   **Oavsiktlig borttagning.** Det är möjligt att du tog bort kontot för säkerhetskopiering från föregående enheten eller när hantera ditt lagringskonto i molnet. I den här situationen måste du manuellt återskapa ditt konto i appen.

-   **Befintliga Microsoft Authenticator konton.** Om du redan har konfigurerat konton i Microsoft Authenticator-appen kan appen inte återställa säkerhetskopierade-konton. Förhindra återställning säkerställer att din kontoinformation inte skrivas över med gammal information. I den här situationen måste du ta bort alla befintliga kontoinformation från de befintliga konton som definierats i autentiseringsappen innan du kan återställa säkerhetskopian.

## <a name="next-steps"></a>Nästa steg
Nu när du säkerhetskopiera och återställa autentiseringsuppgifterna för ditt konto till din nya enhet kan fortsätta du att använda Microsoft Authenticator-appen för att verifiera din identitet.

## <a name="related-topics"></a>Relaterade ämnen
- [Kom igång med Microsoft Authenticator-appen](microsoft-authenticator-app-how-to.md)  

- [Logga in med telefonen](microsoft-authenticator-app-phone-signin-faq.md)

- [Microsoft autentiseringsapp vanliga frågor och svar](microsoft-authenticator-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)