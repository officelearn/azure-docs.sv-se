---
title: Säkerhetskopiera och Återställ med Microsoft Authenticator - app i Azure Active Directory | Microsoft Docs
description: Lär dig mer om att säkerhetskopiera och återställa autentiseringsuppgifterna för ditt konto, med hjälp av Microsoft Authenticator-appen.
services: active-directory
author: eross-msft
manager: daveba
ms.component: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: bd15189a9aa70ab99971be9da1bcd1ff243c60d0
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904360"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Säkerhetskopiera och återställa kontoautentiseringsuppgifter med Microsoft Authenticator-appen

**Gäller för:**

- iOS-enheter

Microsoft Authenticator-appen säkerhetskopierar dina autentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton till molnet. Efter säkerhetskopieringen, kan du också använda appen för att återställa din information på en ny enhet potentiellt undvika komma låst upp eller att skapa konton.

>[!IMPORTANT]
> Du behöver ett personligt Microsoft-konto och en iCloud-konto för varje lagringsplats för säkerhetskopiering. Men i den lagringsplatsen du kan säkerhetskopiera flera konton. Du kan till exempel har ett personligt konto, ett skolkonto och ett tredje parts-konto som Facebook, Google, och så vidare.<br><br>Endast dina personliga och 3 part autentiseringsuppgifter lagras som inkluderar ditt användarnamn och kontoverifieringskod som krävs för att verifiera din identitet. Vi lagrar inte några andra som är associerade med dina konton, inklusive e-postmeddelanden och filer. Vi inte också koppla eller dela dina konton på något sätt eller med alla andra produkter eller tjänster. Och slutligen IT-administratören få inte någon information om något av dessa konton.

## <a name="back-up-your-account-credentials"></a>Säkerhetskopiera dina kontouppgifter
Innan du kan säkerhetskopiera dina autentiseringsuppgifter måste ha både:

- En personlig [microsoftkonto](https://account.microsoft.com/account) kan fungera som ditt återställningskonto.

- En [iCloud-konto](https://www.icloud.com/) för den faktiska lagringsplatsen. 

Kräver att du att logga in på båda kontona som tillsammans ger bättre säkerhet för din säkerhetskopieringsinformation.

**Aktivera Cloud backup**
-   På iOS-enheten, Välj **inställningar**väljer **säkerhetskopiering**, och sedan aktivera **iCloud-säkerhetskopiering**.

    Autentiseringsuppgifterna för ditt konto säkerhetskopieras till iCloud-konto.

    ![skärmen för iOS-inställningar, som visar platsen för iCloud inställningar för säkerhetskopiering](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Återställa autentiseringsuppgifterna för ditt konto på den nya enheten
Du kan återställa autentiseringsuppgifterna för ditt konto från ditt iCloud-konto med hjälp av samma Microsoft recovery kontot som du ställer in när du har säkerhetskopierat din information.

### <a name="to-recover-your-information"></a>Att återställa din information
1.  Öppna Microsoft Authenticator-appen på din iOS-enhet och välj **påbörja återställning** längst ned på skärmen.

    ![Microsoft Authenticator-appen som visar var du ska klicka Begin återställning](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-begin-recovery.png)

2.  Logga in på ditt återställningskonto med hjälp av samma personliga Microsoft-konto du använde under säkerhetskopieringen.

    Autentiseringsuppgifterna för ditt konto har återställts till den nya enheten.

När du är klar med dina recovery märker du att ditt personliga konto för Microsoft verifieringskoder i Microsoft Authenticator-appen skiljer sig mellan din gamla och nya telefoner. Koderna som skiljer sig eftersom varje enhet har en egen unik autentiseringsuppgifter, men båda är giltig och fungerar när du loggar in med associerade telefonen.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Återställa ytterligare konton som kräver ytterligare verifiering
Om du använder push-meddelanden med ditt personliga, arbete eller skola konton, får du en avisering på skärmen som säger att du måste ange ytterligare verifiering innan du kan återställa din information. Eftersom push-meddelanden kräver med hjälp av en autentiseringsuppgift som är knutna till din specifika enhet och aldrig skickas över nätverket, måste du verifiera din identitet innan autentiseringsuppgiften som har skapats på din enhet.

För personliga Microsoft-konton kan du bevisa din identitet genom att ange ditt lösenord tillsammans med ett alternativt nummer för e-post eller telefon. För arbets- eller skolkonto-konton måste du skanna en QR-kod som du fått av din kontoleverantör.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Att tillhandahålla ytterligare verifiering för personliga konton
1.  I den **konton** skärmen i Microsoft Authenticator-appen, Välj nedrullningsbara pilen bredvid det konto som du vill återställa.

    ![Microsoft Authenticator-appen som visar tillgängliga konton med deras associerade listrutepilarna](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-arrow.png)

2.  Välj **logga in för att återställa**, ange ditt lösenord och bekräfta din e-postadress eller telefonnummer nummer som ytterligare verifiering.

    ![Microsoft Authenticator-appen, så att du kan ange din inloggningsinformation](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Att tillhandahålla ytterligare verifiering för arbets-eller skolkonton
1.  I den **konton** skärmen i Microsoft Authenticator-appen, Välj nedrullningsbara pilen bredvid det konto som du vill återställa.

    ![Microsoft Authenticator-appen som visar tillgängliga konton med deras associerade listrutepilarna](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-additional-accts.png)

2.  Välj **skanna QR-kod för att återställa**, och sedan skanna QR-koden.

    ![Microsoft Authenticator-appen, så att du kan skanna QR-kod](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Mer information om hur du registrerar en QR-kod finns i [Kom igång med Microsoft Authenticator-appen](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-how-to#add-accounts-to-the-app) eller [konfigurera säkerhetsinformation för att använda en autentiseringsapp](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app#to-use-the-microsoft-authenticator-app), baserat på om din administratör har aktiverat säkerhetsinformation.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Felsökning av problem i säkerhetskopiering och återställning
Det finns flera skäl till varför säkerhetskopieringen inte kanske tillgänglig:

-   **Ändra operativsystem.** Säkerhetskopiorna lagras i molnet lagringsalternativ som tillhandahålls av operativsystemet i din telefon, vilket innebär att säkerhetskopian är inte tillgängligt om du växlar mellan Android och iOS. I så fall måste du manuellt återskapa ditt konto i appen.

-   **Problem med nätverket eller lösenord.** Kontrollera att du är ansluten till ett nätverk och loggat in på ditt iCloud-konto med hjälp av samma AppleID som du använde på din senaste iPhone.

-   **Oavsiktlig borttagning.** Det är möjligt att du tog bort kontot säkerhetskopiering från din tidigare enhet eller medan du hanterar ditt molnlagringskonto. I så fall måste du manuellt återskapa ditt konto i appen.

-   **Befintliga konton i Microsoft Authenticator.** Om du redan har konfigurerat konton i Microsoft Authenticator-appen kan appen inte att återställa dina säkerhetskopierade konton. Förhindrar återställning hjälper till att säkerställa att din kontoinformation inte skrivas över med gammal information. I så fall måste du ta bort alla befintliga kontoinformation från de befintliga konton som definierats i din autentiserare innan du kan återställa säkerhetskopian.

## <a name="next-steps"></a>Nästa steg
Nu när du har säkerhetskopierat och återställt autentiseringsuppgifterna för ditt konto till den nya enheten, kan du fortsätta att använda Microsoft Authenticator-appen för att verifiera din identitet.

## <a name="related-topics"></a>Relaterade ämnen
- [Kom igång med Microsoft Authenticator-appen](microsoft-authenticator-app-how-to.md)  
- [Vanliga frågor och svar om Microsoft Authenticator-appen](microsoft-authenticator-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
