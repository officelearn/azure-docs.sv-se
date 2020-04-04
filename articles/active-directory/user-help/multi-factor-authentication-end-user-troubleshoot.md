---
title: Vanliga problem med konto tvåfaktorsautentisering - Azure AD
description: Lösningar för några av de vanligaste tvåfaktorsverifieringsproblemen och ditt arbets- eller skolkonto.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 1703853f42b52dccc80fdfadaa09b67e18a19db8
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632897"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Vanliga problem med tvåfaktorsverifiering och ditt arbets- eller skolkonto

När din Azure Active Directory-organisation (Azure AD) aktiverar tvåfaktorsverifiering kräver din inloggning för arbets- eller skolkonto en kombination av ditt användarnamn, ditt lösenord och en mobil enhet eller telefon. Det är säkrare än bara ett lösenord, förlitar sig på två former av autentisering: något du vet, och något du har med dig. Tvåfaktorsverifiering kan hjälpa till att stoppa illvilliga hackare från att låtsas vara du, för även om de har ditt lösenord är oddsen att de inte har din enhet också.

<center>

![Bild av begreppsmässiga autentiseringsmetoder](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Det finns några vanliga tvåfaktorskontroll problem som verkar hända oftare än någon av oss skulle vilja. Vi har satt ihop den här artikeln för att ta itu med de vanligaste problemen och några möjliga korrigeringar.

>[!Important]
>Om du är administratör kan du hitta mer information om hur du konfigurerar och hanterar din Azure AD-miljö i [Azure AD-dokumentationen](https://docs.microsoft.com/azure/active-directory).
>
>Det här innehållet är också endast avsett för användning med ditt arbets- eller skolkonto, alain@contoso.comvilket är det konto som din organisation tillhandahåller (till exempel ). Om du har problem med tvåfaktorsverifiering och ditt personliga Microsoft-konto, vilket är ett danielle@outlook.comkonto som du har konfigurerat själv (till exempel), läser [du Aktivera eller inaktivera tvåfaktorsverifiering för ditt Microsoft-konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Jag har inte min mobila enhet med mig

Det händer. Du lämnade din mobila enhet hemma och nu kan du inte använda telefonen för att verifiera vem du är. Om du tidigare har lagt till en annan metod för att logga in på ditt konto, till exempel din kontorstelefon, bör du kunna använda den metoden nu. Om du aldrig har lagt till ytterligare en verifieringsmetod måste du kontakta organisationens supportavdelning och låta dem hjälpa dig att komma tillbaka till ditt konto.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Så här loggar du in på ditt arbets- eller skolkonto med en annan verifieringsmetod

1. Logga in på ditt konto men välj länken Logga in på **ett annat sätt** på sidan **Tvåfaktorsverifiering.**

    ![Ändra inloggningsverifieringsmetod](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Om du inte ser länken **Logga in på ett annat sätt** betyder det att du inte har ställt in några andra verifieringsmetoder. Du måste kontakta administratören för att få hjälp med att logga in på ditt konto.

2. Välj alternativ verifieringsmetod och fortsätt med tvåfaktorsverifieringsprocessen.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Jag förlorade min mobila enhet eller den stals

Om du har tappat bort eller fått din mobila enhet stulen kan du antingen logga in med en annan metod eller be organisationens supportavdelning att rensa inställningarna. Vi rekommenderar starkt att du meddelar organisationens supportavdelning om telefonen har tappats bort eller stulits, så att lämpliga uppdateringar kan göras på ditt konto. När inställningarna har rensats uppmanas du att [registrera dig för tvåfaktorsverifiering](multi-factor-authentication-end-user-first-time.md) nästa gång du loggar in.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Jag får inte verifieringskoden skickad till min mobila enhet

Att inte få din verifieringskod är ett vanligt problem och det är vanligtvis relaterat till din mobila enhet och dess inställningar. Några möjliga saker att prova:

Prova det här | Vägledning info
--------- | ------------
Starta om den mobila enheten | Ibland behöver enheten bara en uppdatering. Om du startar om enheten avslutas alla bakgrundsprocesser eller tjänster som körs och kan orsaka problem, tillsammans med att uppdatera enhetens kärnkomponenter, starta om dem om de skulle krascha någon gång.
Kontrollera att säkerhetsinformationen är korrekt | Kontrollera att informationen om säkerhetsverifieringsmetoden är korrekt, särskilt dina telefonnummer. Om du sätter in fel telefonnummer kommer alla dina aviseringar att gå till det felaktiga numret. Lyckligtvis kommer den användaren inte att kunna göra något med aviseringarna, men det hjälper dig inte heller att logga in på ditt konto. Kontrollera att informationen är korrekt i artikeln [Hantera inställningarna för tvåfaktorsverifieringsmetoden.](multi-factor-authentication-end-user-manage-settings.md)
Kontrollera att dina aviseringar är aktiverade | Kontrollera att din mobila enhet har aviseringar påslagna och att du har valt en meddelandemetod som gör att telefonsamtal, din autentiseringsapp och meddelandeapp (för textmeddelanden) kan skicka synliga aviseringar till din mobila enhet.
Kontrollera att du har en enhetssignal och Internetanslutning | Kontrollera att dina telefonsamtal och sms kommer fram till din mobila enhet. Be en vän ringa dig och skicka ett sms till dig för att se till att du får båda. Om du inte gör det kontrollerar du först att den mobila enheten är påslagen. Om enheten är påslagen, men du fortfarande inte får samtalet eller texten, är det troligen ett problem med nätverket och du måste prata med din leverantör. Om du ofta har signalrelaterade problem rekommenderar vi att du installerar och använder [Microsoft Authenticator-appen](user-help-auth-app-download-install.md) på din mobila enhet. Autentiseringsappen kan generera slumpmässiga säkerhetskoder för inloggning, utan att kräva någon cellsignal eller Internetanslutning.
Stäng av Stör ej | Kontrollera att du inte har aktiverat funktionen **Stör ej** för din mobila enhet. När den här funktionen är aktiverad tillåts inte aviseringar att varna dig på din mobila enhet. Se din mobila enhets handbok för instruktioner om hur du inaktiverar den här funktionen.
Avblockera telefonnummer | I USA kommer röstsamtal från Microsoft från följande nummer: +1 (866) 539 4191, +1 (855) 330 8653 och +1 (877) 668 6536.
Kontrollera dina batterirelaterade inställningar | Den här verkar lite udda på ytan, men om du har ställt in batterioptimeringen för att stoppa mindre använda appar från att förbli aktiva i bakgrunden, har ditt meddelandesystem troligen påverkats. Om du vill försöka åtgärda problemet inaktiverar du batterioptimering för autentiseringsappen och meddelandeappen och försöker sedan logga in på ditt konto igen.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Jag blir inte tillfrågad om min andra verifieringsinformation

Om du har loggat in på ditt arbets- eller skolkonto med ditt användarnamn och lösenord, men inte har blivit tillfrågad om din ytterligare säkerhetsverifieringsinformation, kanske du inte har konfigurerat enheten ännu. Din mobila enhet måste vara konfigurerad för att fungera med din specifika ytterligare säkerhetsverifieringsmetod. Kontrollera att du har aktiverat din mobila enhet och att den är tillgänglig för verifiering med verifieringsmetoden finns i artikeln Hantera inställningarna för [tvåfaktorsverifieringsmetoden.](multi-factor-authentication-end-user-manage-settings.md) Om du vet att du inte har konfigurerat enheten eller ditt konto kan du göra det nu genom att följa stegen i artikeln [Konfigurera mitt konto för tvåstegsverifiering.](multi-factor-authentication-end-user-first-time.md)

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Jag fick ett nytt telefonnummer och jag vill lägga till det

Om du har fått ett nytt telefonnummer måste du uppdatera informationen om säkerhetsverifieringsmetoden så att verifieringsansanerna hamnar på rätt plats. Om du vill uppdatera verifieringsmetoden följer du stegen i avsnittet **Lägg till eller ändra ditt telefonnummer** i artikeln Hantera [inställningarna för tvåfaktorsverifieringsmetoden.](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Jag har en ny mobil enhet och jag vill lägga till den

Om du har fått en ny mobil enhet måste du konfigurera den så att den fungerar med tvåfaktorsverifiering. Detta är en lösning i flera steg:

1. Konfigurera enheten så att den fungerar med ditt arbets- eller skolkonto genom att följa stegen i artikeln [Konfigurera mitt konto för tvåstegsverifiering.](multi-factor-authentication-end-user-first-time.md)

1. Uppdatera konto- och enhetsinformationen på sidan **Ytterligare säkerhetsverifiering,** ta bort den gamla enheten och lägga till den nya. Mer information finns i artikeln [Hantera inställningarna för tvåfaktorsverifieringsmetod.](multi-factor-authentication-end-user-manage-settings.md)

Valfria steg:

- Ladda ned, installera och konfigurera Microsoft Authenticator-appen på din mobila enhet genom att följa stegen i artikeln [Hämta och installera Microsoft Authenticator-appen.](user-help-auth-app-download-install.md)

- Aktivera tvåfaktorsverifiering för dina betrodda enheter genom att följa stegen i **verifieringsansningarna aktivera tvåfaktorsverifiering på en betrodd enhet** i artikeln Hantera inställningarna för [tvåfaktorsverifieringsmetod.](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Jag har problem med att logga in på min mobila enhet när jag reser

Det kan vara svårare att använda en verifieringsmetod för mobila enheter, till exempel ett sms, när du befinner dig på en internationell plats. Det är också möjligt att din mobila enhet kan leda till att du ådrar dig roamingavgifter. I det här fallet rekommenderar vi att du använder Microsoft Authenticator-appen, med möjlighet att ansluta till en Wi-Fi-hotspot. Mer information om hur du hämtar, installerar och konfigurerar Microsoft Authenticator-appen på din mobila enhet finns i artikeln [Hämta och installera Microsoft Authenticator-appen.](user-help-auth-app-download-install.md)

## <a name="i-cant-get-my-app-passwords-to-work"></a>Jag kan inte få mina applösenord att fungera

Applösenord ersätter ditt vanliga lösenord för äldre skrivbordsprogram som inte stöder tvåfaktorsverifiering. Kontrollera först att du har skrivit lösenordet korrekt. Om det inte löser det kan du prova att skapa ett nytt applösenord för appen genom att följa stegen i avsnittet **Skapa och ta bort applösenord med** hjälp av portalavsnittet Mina appar i artikeln Hantera [applösenord för tvåstegsverifieringsartikel.](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)

## <a name="i-cant-turn-two-factor-verification-off"></a>Jag kan inte stänga av tvåfaktorsverifiering

Om du använder tvåfaktorsverifiering med ditt arbets- eller alain@contoso.comskolkonto (till exempel) betyder det troligen att din organisation har beslutat att du måste använda den här extra säkerhetsfunktionen. Eftersom din organisation har beslutat att du måste använda den här funktionen, finns det inget sätt för dig att inaktivera den individuellt. Men om du använder tvåfaktorsverifiering med ett personligt alain@outlook.comkonto, till exempel, har du möjlighet att slå på och stänga av funktionen. Instruktioner om hur du styr tvåfaktorsverifiering för dina personliga konton finns i [Aktivera eller inaktivera tvåfaktorsverifiering för ditt Microsoft-konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Om du inte kan inaktivera tvåfaktorsverifiering kan det också bero på de säkerhetsstandarder som har tillämpats på organisationsnivå. Mer information om standardvärden för säkerhet finns i [Vad är säkerhetsdefulta?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Jag hittade inget svar på mitt problem

Om du har provat de här stegen men fortfarande har problem kontaktar du organisationens supportavdelning för att få hjälp.

## <a name="related-articles"></a>Relaterade artiklar

- [Hantera inställningarna för tvåfaktorsverifieringsmetod](multi-factor-authentication-end-user-manage-settings.md)

- [Konfigurera mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md)

- [Vanliga frågor och svar om Microsoft Authenticator-appen](user-help-auth-app-faq.md)
