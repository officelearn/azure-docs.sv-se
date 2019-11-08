---
title: Felsök vanliga problem med tvåstegsverifiering – Azure AD
description: Läs om möjliga lösningar på några av de vanligaste problemen med tvåstegsverifiering.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8d226d61f4b67541a6991fef3a435b4cfff500f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805306"
---
# <a name="troubleshoot-common-two-factor-verification-problems"></a>Felsök vanliga problem med tvåstegsverifiering

Din organisation har aktiverat tvåstegsverifiering, vilket innebär att ditt arbets-eller skol konto loggar nu kräver en kombination av ditt användar namn, ditt lösen ord och en mobil enhet eller telefon. Din organisation aktiverade den här extra verifieringen eftersom det är säkrare än bara ett lösen ord, som förlitar sig på två typer av autentisering: något du känner och något som du har med dig. Tvåstegsverifiering kan hjälpa till att stoppa skadliga hackare från att vara du, eftersom även om de har ditt lösen ord, är strider att de inte har din enhet.

Det finns några vanliga vanliga verifierings problem på två faktorer som verkar oftare än vad som helst av oss. Vi har samlat ihop den här artikeln lura för att lösa de vanligaste problemen och några möjliga korrigeringar.

>[!Important]
>Innehållet är avsett för användare. Om du är administratör hittar du mer information om hur du konfigurerar och hanterar en Azure Active Directory (Azure AD)-miljö i [dokumentationen om Azure Active Directory](https://docs.microsoft.com/azure/active-directory).
>
>Det här innehållet är även avsett för användning med ditt arbets-eller skol konto, kontot som du fått av din organisation (till exempel alain@contoso.com). Om du har problem med tvåstegsverifiering och ditt personliga Microsoft-konto, kan du se [Aktivera och inaktivera tvåstegsverifiering på eller av för din Microsoft-konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)om du har ett konto som du själv har konfigurerat för dig (till exempel danielle@outlook.com).

## <a name="i-forgot-my-mobile-device-at-home"></a>Jag har glömt min mobila enhet hemma

Det sker. Du har lämnat din mobila enhet hemma och nu kan du inte använda din telefon för att kontrol lera att du är den som du säger. Om du tidigare har lagt till en annan metod för att logga in på ditt konto, till exempel din arbets telefon, bör du kunna använda den metoden nu. Om du aldrig har lagt till en ytterligare verifieringsmetod måste du kontakta supportavdelningen och låta dem hjälpa dig att komma tillbaka till ditt konto.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Logga in på ditt arbets-eller skol konto med en annan verifieringsmetod

1. Logga in på ditt konto som vanligt och välj länken **Logga in på en annan väg** på sidan med **verifiering av två faktorer** .

    ![Ändra verifierings metod för inloggning](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Om du inte ser länken **Logga in på ett annat sätt** , innebär det att du inte har ställt in några andra verifierings metoder. Du måste kontakta administratören om du behöver hjälp med att logga in på ditt konto.

2. Välj den alternativa verifierings metoden och fortsätt med processen med tvåstegsverifiering.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Jag har förlorat min mobila enhet eller blivit stulen

Om du har förlorat eller haft din mobila enhet stulen kan du antingen logga in med en annan metod eller be supportavdelningen att ta bort dina inställningar. Vi rekommenderar starkt att du kontaktar supportavdelningen om din telefon tappades bort eller stulits, så att du kan göra lämpliga uppdateringar av ditt konto. När inställningarna har rensats uppmanas du att [Registrera dig för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) nästa gången du loggar in.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Jag får inte den verifierings kod som skickas till min mobila enhet

Att inte hämta verifierings koden är ett vanligt problem och det är vanligt vis relaterat till din mobila enhet och dess inställningar. Några möjliga saker att prova:

- **Starta om den mobila enheten.** Ibland behöver enheten bara en uppdatering. Att starta om enheten avslutar alla bakgrunds processer eller tjänster som för närvarande körs och kan orsaka problem, tillsammans med att uppdatera enhetens kärn komponenter, starta om dem om de kraschade vid något tillfälle.

- **Kontrol lera att din säkerhets information är korrekt.** Kontrol lera att informationen om säkerhets verifierings metoden är korrekt, särskilt dina telefonnummer. Om du anger fel telefonnummer hamnar alla aviseringar till det felaktiga numret. Den användaren är lyckligt vis inte att kunna göra något med aviseringarna, men du kan inte heller logga in på ditt konto. Om du vill kontrol lera att informationen är korrekt läser du anvisningarna i artikeln [Hantera inställningar för metod för att hantera dina inställningar](multi-factor-authentication-end-user-manage-settings.md) .

- **Kontrol lera att dina meddelanden är aktiverade.** Kontrol lera att din mobila enhet har aktiverat meddelanden och att du har valt en meddelande metod som tillåter telefonsamtal, din webbapp och din meddelande program (för textmeddelanden) att skicka synliga aviseringar till din mobila enhet.

- **Kontrol lera att du har en enhets signal och Internet anslutning.** Se till att dina telefonsamtal och textmeddelanden går igenom till din mobila enhet. Låt en vän ringa dig och skicka ett SMS till dig för att se till att du får båda. Om du inte gör det ska du först kontrol lera att din mobila enhet är påslagen. Om enheten är påslagen, men du fortfarande inte får samtalet eller texten, är det förmodligen ett problem med nätverket och du måste prata med din Provider. Om du ofta har problem med signalen rekommenderar vi att du installerar och använder Microsoft Authenticator- [appen](user-help-auth-app-download-install.md) på din mobila enhet. Authenticator-appen kan generera slumpmässiga säkerhets koder för inloggning, utan att det krävs någon cell signal eller Internet anslutning.

- **Inaktivera Stör ej.** Kontrol lera att du inte har aktiverat funktionen **stör ej** på den mobila enheten. När den här funktionen är aktive rad är aviseringar inte tillåtna för att varna dig på din mobila enhet. Information om hur du inaktiverar den här funktionen finns i hand boken för den mobila enheten.

- **Avblockera telefonnummer** I USA kommer röst samtal från Microsoft att komma från följande nummer: + 1 (866) 539 4191, + 1 (855) 330 8653 och + 1 (877) 668 6536.

- **Kontrol lera dina inställningar för batteri.** Det här verkar vara lite ojämnt på ytan, men om du har konfigurerat din batteri optimering för att stoppa appar som inte används längre än vad som är aktivt i bakgrunden har meddelande systemet förmodligen drabbats. Du kan försöka åtgärda problemet genom att inaktivera batteri optimering för din webbapp och appen för meddelande hantering och sedan försöka logga in på ditt konto igen.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Jag uppmanas inte att ange min andra verifierings information

Om du har loggat in på ditt arbets-eller skol konto med ditt användar namn och lösen ord, men inte har tillfrågats om din ytterligare säkerhets verifierings information, kan det bero på att du inte har konfigurerat din enhet ännu. Din mobila enhet måste särskilt konfigureras för att fungera med den ytterligare säkerhets verifierings metoden. För att se till att du har aktiverat din mobila enhet och att den är tillgänglig för användning med din verifierings metod, se artikeln [Hantera inställningar för metod](multi-factor-authentication-end-user-manage-settings.md) för att kontrol lera Server. Om du vet att du inte har konfigurerat din enhet eller ditt konto kan du göra det nu genom att följa stegen i artikeln [Konfigurera mitt konto för](multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering.

## <a name="i-got-a-new-phone-number-how-do-i-change-it-for-two-factor-verification"></a>Jag fick ett nytt telefonnummer, hur ändrar jag det för tvåstegsverifiering?

Om du har fått ett nytt telefonnummer måste du uppdatera information om säkerhets verifierings metoden så att verifieringen blir ombedd att gå till rätt plats. Om du vill uppdatera verifierings metoden följer du stegen i artikeln **Lägg till eller ändra ditt telefonnummer** i artikeln [Hantera inställnings metoden för en tvåfaktorautentisering](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) .

## <a name="i-got-a-new-mobile-device-how-do-i-add-it"></a>Jag fick en ny mobil enhet, hur gör jag för att lägga till den?

Om du har en ny mobil enhet måste du konfigurera den så att den fungerar med tvåstegsverifiering. Detta är en lösning med flera steg:

1. Konfigurera enheten så att den fungerar med ditt arbets-eller skol konto genom att följa stegen i artikeln [Konfigurera mitt konto för](multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering.

2. Uppdatera ditt konto och din enhets information på sidan **ytterligare säkerhets verifiering** och ta bort den gamla enheten och Lägg till din nya. Mer information finns i artikeln [Hantera inställnings metoden för tvåfaktorautentisering](multi-factor-authentication-end-user-manage-settings.md) .

3. Valfri. Hämta, installera och konfigurera Microsoft Authenticator-appen på din mobila enhet genom att följa stegen i [Hämta och installera Microsoft Authenticator app](user-help-auth-app-download-install.md) -artikeln.

4. Valfri. Aktivera tvåstegsverifiering för dina betrodda enheter genom att följa stegen i artikeln **Aktivera två faktorers verifiering på en betrodd enhet** i avsnittet [Hantera inställningar för metod](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) med tvåstegsverifiering.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Jag har problem med att logga in på min mobila enhet under resa

Det kan vara svårare att använda en metod som är relaterad till mobil enhet, t. ex. textmeddelanden, medan du är på en internationell plats. Det är också möjligt att din mobila enhet kan medföra att du debiteras för nätverks växling. I den här situationen rekommenderar vi att du använder Microsoft Authenticator-appen med alternativet att ansluta till ett Wi-Fi-hotspot. Mer information om hur du laddar ned, installerar och konfigurerar Microsoft Authenticator-appen på din mobila enhet finns i artikeln [Hämta och installera Microsoft Authenticator app](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Jag kan inte få mina applösenord att fungera

Applösenord ersätter ditt normala lösen ord för äldre Skriv bords program som inte stöder tvåstegsverifiering. Kontrol lera först att du har angett lösen ordet korrekt. Om det inte löser problemet kan du försöka med att skapa ett nytt applösenord för appen genom att följa stegen i artikeln **skapa och ta bort applösenord med hjälp av avsnittet Mina appar-portalen** i artikeln [Hantera applösenord för två steg](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="why-cant-i-turn-two-factor-verification-off"></a>Varför kan jag inte göra en tvåfaktorautentisering-verifiering?

Om du använder tvåstegsverifiering med ditt arbets-eller skol konto (till exempel alain@contoso.com), betyder det förmodligen att din organisation har valt att du måste använda den här extra säkerhetsfunktionen. Eftersom din organisation har valt att du måste använda den här funktionen kan du inaktivera den på ett och samma sätt. Om du däremot använder tvåstegsverifiering med ett personligt konto, t. ex. alain@outlook.com, kan du aktivera och inaktivera funktionen. Instruktioner för hur du styr tvåstegsverifiering för dina personliga Microsoft-konton finns i [Aktivera eller inaktivera tvåstegsverifiering på din Microsoft-konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Jag hittade inget svar på mitt problem

Om du har provat dessa steg men fortfarande har problem, kan du kontakta supportavdelningen om du behöver hjälp.

## <a name="related-articles"></a>Relaterade artiklar

- [Hantera dina inställningar för verifierings metoden med två faktorer](multi-factor-authentication-end-user-manage-settings.md)

- [Konfigurera mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md)

- [Vanliga frågor och svar om Microsoft Authenticator-appen](user-help-auth-app-faq.md)
