---
title: Hjälp med Microsoft Authenticator - app i Azure AD | Microsoft Docs
description: Visar en lista med vanliga frågor och svar som rör Microsoft Authentication-appen och Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: d86bc84653e38a9b64a336b8ce9ed7e657129e8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059832"
---
# <a name="microsoft-authenticator-app-faq"></a>Microsoft Authenticator-appen vanliga frågor och svar

Den här artikeln innehåller vanliga frågor och svar om Microsoft Authenticator-appen. Om du inte hittar svar på din fråga, går du till den [Microsoft Authenticator-appen forum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Dessutom kan du granska en annan vanliga frågor och svar om en specifik funktion i appen, [logga in med telefonen vanliga frågor och svar](microsoft-authenticator-app-phone-signin-faq.md).

Microsoft Authenticator-appen ersätts Azure Authenticator-appen och är den rekommenderade appen när du använder Azure Multi-Factor Authentication. Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) och [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>Vilka data autentiseraren lagrar åt mig och hur kan jag ta bort det?

Microsoft Authenticator lagrar uppgifterna för ett konto som du skapar när du lägger till ett konto. När du använder Authenticator, en diagnostiklogg har skapats för felsökning och lagrar användbara data gäller att hjälpa Microsoft att diagnostisera eventuella oförutsedda problem. Du kan komma åt loggdata genom att öppna **hjälpa** > **skicka loggar** > **visa loggar**.

Du kan ta bort data genom att ta bort konto-panelen. Panelen konto också tar du bort alla uppgifterna för ett konto som används av programmet loggarna. 

Mer information om hur Microsoft använder dina data finns: https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>Vilka är de som finns i appen för? Varför fungerar nummer jag vill räkna?

När du öppnar Microsoft Authenticator-appen, visas de konton som du har lagt till och ett nummer sex eller åtta siffror i de olika versionerna. Du kan se en 30 sekunder timer räkna.

Dessa koder används när du loggar in på ditt konto. När du har angett ditt användarnamn och lösenord kan du bli ombedd att ange en Verifieringskod. Öppna Microsoft Authenticator-appen och kopiera koden som visas för närvarande. Ange den kod på sidan logga in för att slutföra.

Orsaken till att koderna ändra med 30 sekunders mellanrum är så att du aldrig använda samma kod två gånger. Det är inte som ett lösenord som du ska komma ihåg. Tanken är att endast användare med åtkomst till telefonen vet verifieringskoden.

Koderna som kräver inte internet eller data, så att du inte behöver bekymra dig om att du har telefontjänst att logga in. När du stänger appen inte att köra i bakgrunden och det att tömma inte batteriet. Du kan stänga appen och ignorera tills nästa gång du loggar in.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Jag bara få meddelanden när jag har appen öppen. Om appen inte är öppen, jag inte några meddelanden.

Om du får meddelanden, men de inte gör brus eller vibrationer trots din tyst mot, kontrollera först appinställningarna. Aktivera appen att använda ett ljud eller vibrationer med dess meddelanden.

Om du inte får meddelanden alls kan du kontrollera följande fall:

- Är din telefon i stör ej eller tyst läge? Detta läge kan se appar från att skicka meddelanden.
- Du får ett meddelande från andra appar? Om inte, det kan finnas ett problem med nätverksanslutningarna på din telefon eller kanalen meddelanden från Android- eller Apple. Du kan lösa det första alternativet i telefoninställningarna för din, men du kan behöva tala med din tjänstleverantör för att få hjälp med det andra alternativet.
- Får du ett meddelande för vissa konton för appen, men inte för andra? Om Ja, ta bort det problematiska kontot från appen och Lägg till den igen för att aktivera push-meddelanden.

Om du har försökt förslagen felsökning men fortfarande har problem kan skicka du dina loggar för diagnostik. Gå till appinställningar och välj sedan **hjälp och feedback** och **skicka loggar**. Gå sedan till den [Microsoft Authenticator-appen forum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) och berätta vad du får problem och vilka steg som du har gjort hittills.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Jag använder redan Microsoft Authenticator-program för verifieringskoder. Hur växlar jag till ett klick push-meddelanden?
Godkänna en inloggning via push-meddelande är endast tillgänglig för personliga Microsoft-konton eller arbeta och skolan Microsoft-konton, inte för tredje parts-konton som Google eller Facebook. Om du har ett arbets- eller din skola Microsoft-konto kan kan din organisation du inaktivera det här alternativet.

Om du använder ett Microsoft-konto för ditt personliga konto och vill växla till push-meddelanden, måste du lägga till ditt konto igen. Omregistrera enheten med ditt konto och konfigurera push-meddelanden.  

Om du använder Microsoft Authenticator för ditt arbets- eller skolkonto, har ditt företag avgör om du vill tillåta klick meddelanden.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Fungerar en push-meddelanden för icke-Microsoft-konton?
Nej, push-meddelanden fungerar bara med Microsoft-konton och Azure Active Directory-konton. Om ditt arbete eller skola använder du Azure AD-konton, kan de inaktivera den här funktionen.  

### <a name="i-got-a-new-device-or-restored-my-device-from-a-backup-how-do-i-set-up-my-accounts-in-the-microsoft-authenticator-app-again"></a>Jag har fått en ny enhet eller återställts min enhet från en säkerhetskopia. Hur ställer jag in min i Microsoft Authenticator-appen igen?
Om du kör en iOS-enhet, har aktiverat **automatiska säkerhetskopieringen**, och har skapat en säkerhetskopia av dina konton på enheten gamla; du kan använda säkerhetskopian för att återställa autentiseringsuppgifterna för ditt konto på den nya enheten. Mer information finns i den [säkerhetskopiera och återställa kontoautentiseringsuppgifter med Microsoft Authenticator-appen](microsoft-authenticator-app-backup-and-recovery.md) artikeln. 

### <a name="i-lost-my-device-or-moved-on-to-a-new-device-how-do-i-make-sure-notifications-dont-continue-to-go-to-my-old-device"></a>Jag har tappats bort min enhet eller gått vidare till en ny enhet. Hur kan jag vara säker på att meddelanden inte fortsätta att gå till min gamla enhet?  
Om du lägger till Microsoft Authenticator-appen till den nya iOS-enheten bort inte automatiskt appen från din gamla enhet. Även ta bort appen från din gamla enhet räcker inte. Du måste både tar bort appen från din gamla enhet och visa Microsoft eller din organisation om du glömmer bort den gamla enheten och avregistrera från ditt konto.
- **Ta bort appen från en enhet med ett personligt microsoftkonto.** Gå till området tvåstegsverifiering för verifiering av din [kontosäkerhet](https://account.microsoft.com/security) och väljer att inaktivera verifiering för din gamla enhet.  
- **Att ta bort appen från en enhet med ett arbets- eller skolkonto för Microsoft.** Gå till området tvåstegsverifiering för verifiering av din [MyApps](https://myapps.microsoft.com/) sidan eller till din organisations anpassade portal och väljer att inaktivera verifiering för din gamla enhet. 



### <a name="how-do-i-remove-an-account-from-the-app"></a>Hur tar jag bort ett konto från appen?
* iOS: från huvudskärmen, sveper du vänster på en panel för kontot. Välj **Ta bort**.
* Windows Phone: Från huvudskärmen väljer på menyknappen sedan **redigera konton**. Tryck på den **X** bredvid namnet på kontot.
* Android: Från huvudskärmen väljer på menyknappen sedan **redigera konton**. Tryck på den **X** bredvid namnet på kontot.

Om du har en enhet som har registrerats hos din organisation kan behöva du utföra ett extra steg för att ta bort ditt konto. På dessa enheter registreras automatiskt Microsoft Authenticator-appen som enhetsadministratör. Om du vill avinstallera appen helt måste du först avregistrera appen i appinställningar.

### <a name="why-does-the-app-request-so-many-permissions"></a>Varför begära så många behörigheter i appen?
Här är en fullständig lista över behörigheter som kan be dig om och hur de används i appen. De specifika behörigheter som visas beror på vilken typ av telefonen som du har.

* **Kamera**: används för att skanna QR-koder när du lägger till ett arbets-, Skol- eller icke-Microsoft-konto.
* **Kontakter och phone**: används för att förenkla processen genom att söka efter befintliga konton på din telefon när du loggar in med ditt personliga Microsoft-konto.
* **SMS**: används för att kontrollera ditt telefonnummer matchar numret på posten. När du loggar in med ditt personliga Microsoft-konto för första gången.  Vi skicka ett SMS till telefonen som du laddade ned appen som innehåller en 6 – 8-siffriga Verifieringskod. I stället för att visas som ber dig att hitta den här koden och ange den i appen, verkligen den finns i på SMS: et för dig.
* **Rita över andra appar**: du får meddelandet som verifierar din identitet visas även på andra appar som kan vara igång.
* **Ta emot data från internet**: den här behörigheten krävs för att skicka meddelanden.
* **Förhindra att phone viloläge**: Om du registrerar enheten med organisationen kan din organisation kan ändra den här principen på din telefon.
* **Kontrollera vibrationer**: du kan välja om du vill ha en vibrationer när du tar emot en avisering om att verifiera din identitet.
* **Använda fingeravtryck maskinvara**: vissa arbets- och skolkonton konton kräver en ytterligare PIN-kod när du verifierar din identitet. Vi kan du använda ditt fingeravtryck istället för att ange PIN-koden för att underlätta processen.
* **Visa nätverksanslutningar**: när du lägger till ett Microsoft-konto appen kräver nätverk/internet-anslutning.
* **Läsa innehållet i din lagring**: den här behörigheten används bara när du rapporterar ett tekniskt problem via inställningarna för appen. Information från din lagring som samlas in för att diagnostisera problemet.
* **Fullständig nätverksåtkomst**: den här behörigheten krävs för att skicka meddelanden för att verifiera din identitet.
* **Körs vid start**: Om du startar om din telefon den här behörigheten säkerställer att du fortsätter du får meddelanden att verifiera din identitet.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Varför Microsoft Authenticator-appen kan du godkänna en begäran utan att låsa upp enheten?

Du behöver att låsa upp enheten för att godkänna begäranden eftersom allt du behöver för att bevisa att du har telefonen med dig. Tvåstegsverifiering kräver att bevisa sin skicklighet två saker – en sak som du vet och en sak som du har. Det som du vet är ditt lösenord. Det du behöver är din telefon (Ställ in med Microsoft Authenticator-appen och registreras som ett funktionstest MFA.) Därför uppfyller har telefonen och godkänna begäran villkoren för den andra faktorn för autentisering.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>Vad betyder låsikon i listan över användarkonton?

Hänglåsikonen anger att enheten är registrerad i Azure AD och registrerad till kontot. Enhetsregistrering för iOS sker under Microsoft Intune-registrering.

## <a name="next-steps"></a>Nästa steg

### <a name="contact-us"></a>Kontakta oss
Om din fråga inte besvaras här, som vi vill höra från dig. Gå till den [Microsoft Authenticator-appen forum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) att publicera din fråga och få hjälp från communityn eller lämna en kommentar på den här sidan.


### <a name="related-topics"></a>Relaterade ämnen
* [Om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) för Microsoft-konton
* [Har du problem med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md) för arbets-eller skolkonto?
* [Använd Microsoft Authenticator för att logga in från din telefon](microsoft-authenticator-app-phone-signin-faq.md)
