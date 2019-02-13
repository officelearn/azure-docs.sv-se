---
title: Hantera din säkerhetsinformation - Azure Active Directory | Microsoft Docs
description: Lär dig hur du hanterar din säkerhetsinformation, inklusive hur du arbetar med dina inställningar för tvåstegsverifiering.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29c3b83ded2ef996118ebd8d37b177b0334d3bae
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211010"
---
# <a name="manage-your-security-info-preview"></a>Hantera din säkerhetsinformation (förhandsversion)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Du kan använda din säkerhetsinformation för att logga in på ditt arbets- eller skolkonto konto eller återställa ditt lösenord.

När du loggar in, beroende på inställningarna för din organisation, kan det visas en kryssruta med texten **fråga inte igen efter X dagar**. Den här kryssrutan kan du hålla loggat in till din enhet för hur många dagar som administratören tillåter utan att behöva signerat.

## <a name="change-your-info"></a>Ändra din information
Du kan uppdatera eller lägga till säkerhetsinformation eller ändra din standard, baserat på vad som är tillåtet av administratören och din organisation.

### <a name="to-change-your-info"></a>Ändra din information

1. Logga in på ditt arbets- eller skolkonto konto.

2. Gå till myapps.microsoft.com, väljer du ditt namn i övre högra hörnet på sidan och välj sedan **profil**.

3. I den **Hantera konto** Välj **redigera säkerhetsinformation**.

    ![Profil för skärmen med Redigera security info med länken markerad](media/security-info/security-info-profile.png)

4. Använda din standardmetod att godkänna åtkomst och se dina aktuella security info detaljer om din administratör har konfigurerat den här upplevelsen för din organisation.

5. På den **skydda ditt konto** sidan kan du:

    - Välj **lägga till säkerhetsinformation** att lägga till fler metoder.

    - Välj **ändra standard** att ändra din standardmetod.

    - Välj den **penna** ikonen bredvid en befintlig metod för att uppdatera din information.

    ![Security info skärmen med befintliga, redigerbara information](media/security-info/security-info-edit.png)

6. När du har gjort ändringarna kan du lämna sidan och kommer att spara ändringarna.

Om du inte ser dessa alternativ eller om det inte går att komma åt sidan myapps.microsoft.com, är det möjligt att din organisation använder anpassade alternativ eller en företagsanpassad sida. Du behöver kontakta administratören för mer hjälp.

## <a name="manage-your-security-info-for-a-lost-or-potentially-compromised-device"></a>Hantera din säkerhetsinformation för en enhet som förlorats eller potentiellt komprometterade

Om du tappar bort din enhet eller om enheten har komprometterats, kommer du behöva göra om verifieringsprocessen för alla dina tidigare betrodda enheter.

### <a name="to-manage-your-security-info-for-lost-or-potentially-compromised-devices"></a>Hantera din säkerhetsinformation för borttappade eller potentiellt komprometterade enheter

1. Logga in på ditt arbets- eller skolkonto konto.

2. Gå till myapps.microsoft.com, väljer du ditt namn i det övre högra hörnet på sidan och välj sedan **profil**.

3. I den **Hantera konto** Välj **Glöm MFA på sparade enheter**.
    
    Om du väljer det här alternativet innebär att du kommer att gå igenom processen för multi-Factor Authentication (MFA) igen när du loggar in.

    ![Profil för skärmen med Glöm länken markerad](media/security-info/security-info-forget.png)

## <a name="common-problems-and-solutions-with-your-security-info"></a>Vanliga problem och lösningar med din säkerhetsinformation

Den här artikeln hjälper dig att felsöka din säkerhetsinformation, inklusive problem med tvåstegsverifiering verifiering.

|Problem|Lösning|
|-------|--------|
|Jag har min telefon med mig|Det är möjligt att du inte har telefonen med dig när som helst, men du kommer fortfarande du vill logga in på ditt arbets- eller skolkonto. Om du vill åtgärda problemet kan du logga in med olika autentiseringsmetoder som inte kräver din telefon, till exempel ditt telefonnummer. Om du vill lägga till fler metoder till din säkerhetsinformation, kan du följa stegen i den [ändra din information](#change-your-info) avsnittet.|
|Jag min telefon eller om det var stulen|Tyvärr kan att förlora din telefon eller om den blir stulen inträffa. I så fall kan rekommenderar vi starkt att du informera din organisation så att IT-personalen kan återställa ditt lösenord och rensa alla lagrade enheter från listan med betrodda enheter. Du kan också glömmer betrodda enheter genom att följa stegen i den [hantera din säkerhetsinformation för en enhet som förlorats eller potentiellt komprometterade](#manage-your-security-info-for-a-lost-or-potentially-compromised-device) avsnittet.|
|Jag har fått ett nytt telefonnummer|Det finns två sätt att åtgärda problemet. Du kan logga in med en annan autentiseringsmetod som inte kräver ditt telefonnummer, t.ex e-post, eller om detta inte är ett alternativ kan du kontakta din organisation är IT-personal och be dem ta bort dina inställningar. Om du vill lägga till fler metoder till din säkerhetsinformation, kan du följa stegen i den [ändra din information](#change-your-info) avsnittet.|
|Min standardmetoden är fel|Du kan uppdatera din standardmetod i din säkerhetsalternativ. Mer specifik information som du kan gå till den [ändra din information](#change-your-info) avsnittet.|
|Jag kan inte ta emot en text eller anropa på min mobiltelefon|Om du har fått skickar ett SMS eller telefonsamtal till din mobila enhet tidigare, är det mest sannolika med phone-providern inte ditt konto med det här problemet. Se till att du har bra cell signal och att det går att ta emot textmeddelanden och telefonsamtal. Du kan ställa en vän samtal eller textmeddelande du som ett test.<br><br>Om du kan ta emot text och phone meddelanden, men du fortfarande inte har tagit emot meddelandet, kan du försöka med en annan metod. Du kan lägga till fler metoder till din säkerhetsinformation genom att följa stegen i den [ändra din information](#change-your-info) avsnittet. Om du inte har någon annan metod för att lägga till, kan du kontakta företagets support och be dem att rensa inställningarna så att du kan ställa in din metoder nästa gång du loggar in.<br><br>Om du ofta har problem på grund av felaktiga cell mottagningen, rekommenderar vi att du använder Microsoft Authenticator-appen på din mobila enhet. Appen kan generera slumpmässiga säkerhetskoder som används för att logga in och dessa koder kräver inte någon cell signal eller internet-anslutning. Mer information om Microsoft Authenticator-appen finns i den [Kom igång med Microsoft Authenticator-appen](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/user-help-auth-app-download-install) artikeln.|
|Inget av alternativen i den här tabellen har löst problemet|Om du har gjort de här felsökningsstegen, men är fortfarande körs till problem. Kontakta företagets support, de ska kunna hjälpa dig.|

## <a name="next-steps"></a>Nästa steg

- Mer information om säkerhetsinformation i [info (förhandsversion) Säkerhetsöversikt](user-help-security-info-overview.md).

- Lär dig mer om tvåstegsverifiering i den [tvåstegsverifiering verifiering översikt](user-help-two-step-verification-overview.md) artikeln. 

- Följ något av dessa artiklar och lär dig hur du ställer in dina enheter i området security info:

    - [Konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md)

    - [Konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md)

    - [Konfigurera säkerhetsinformation för att använda textmeddelanden](security-info-setup-text-msg.md)

    - [Konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md)

    - [Konfigurera säkerhetsinformation kan använda säkerhetsfrågor](security-info-setup-questions.md)

- Återställa lösenordet om du har förlorat den, från den [portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följer du stegen i den [återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md) artikeln.

- Hämta felsöka tips och hjälp med inloggningsproblem i den [kan inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikeln.
