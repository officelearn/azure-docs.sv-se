---
title: "Microsoft Authenticator app hjälp och support | Microsoft Docs"
description: "Visar en lista med vanliga frågor och svar om Microsoft Authentication-appen och Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: aa3c6bb9196a17ae41c05458533f73f3ec315bd8
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="microsoft-authenticator-app-faq"></a>Microsoft autentiseringsapp vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor som vi får om Microsoft Authenticator-appen. Om du inte hittar svar på din fråga, gå till den [Microsoft Authenticator app forum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Vi har också en annan vanliga frågor och svar om en specifik funktion på appen, [logga in med din telefon vanliga frågor och svar](microsoft-authenticator-app-phone-signin-faq.md).

Microsoft Authenticator-appen ersättas Azure Authenticator-appen och är den rekommenderade appen när du använder Azure Multi-Factor Authentication. Microsoft Authenticator-appen är tillgänglig för [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594), och [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>Vilka data autentiseraren lagrar för mig och hur kan jag ta bort det?

Microsoft Authenticator lagrar den kontoinformation som du skapar när du lägger till ett konto. När du använder Authenticator diagnostiska loggen har skapats för felsökning och lagrar användbara data i hjälper Microsoft att diagnosticera oförutsedda problem. Du kan komma åt informationen genom att öppna **hjälp** > **skicka loggar** > **visa loggar**.

Du kan ta bort data genom att ta bort panelen konto. Detta tar bort all kontoinformation som används av inklusive loggarna. 

Mer information om hur Microsoft använder dina data finns: https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>Vilka är de som finns i appen för? Varför har antalet keep räknat?

När du öppnar Microsoft Authenticator-appen kan se de konton som du har lagt till och ett nummer sex eller åtta siffror som var och en av dem. Du kan se en 30 sekunder timer räknat.

Dessa koder används när du loggar in på ditt konto. När du har angett ditt användarnamn och lösenord kan du bli ombedd att ange en Verifieringskod. Öppna Microsoft Authenticator-appen och kopiera koden som visas just nu. Ange koden på sidan Slutför.

Orsaken till att koderna ändra med 30 sekunders mellanrum är så att du aldrig använda samma kod två gånger. Det är inte som ett lösenord som du ska komma ihåg. Tanken är att endast användare med åtkomst till din telefon vet verifieringskoden.

Koderna kräver inte internet eller data, så att du inte behöver bry dig om att telefontjänst att logga in. När du stänger appen inte att köras i bakgrunden och det tömma inte batteri. Du kan stänga appen och ignorera förrän nästa gång du loggar in.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Jag bara får meddelanden när jag har appen öppna. Jag får inga meddelanden om appen inte är öppen.

Om du får meddelanden, men de inte gör brus eller vibrerar trots din ringer på, kontrollera först app-inställningar. Aktivera appen att använda ett ljud eller vibrerar med dess meddelanden.

Om du inte får meddelanden alls, kontrollerar du följande fall:

- Är din telefon eller stör i tyst läge? Detta läge kan behålla appar från att skicka meddelanden.
- Kan du få meddelanden från andra appar? Om inte, kan det finnas ett problem med nätverksanslutningar på din telefon eller kanalen meddelanden från Android- eller Apple. Du kan åtgärda det första alternativet i dina telefoninställningar, men du kan behöva kontakta tjänstleverantören om du behöver hjälp med det andra alternativet.
- Kan du få meddelanden för vissa konton på appen, men inte andra? Om Ja, ta bort kontot problematiska från din app och lägga till den igen för att aktivera push-meddelanden.

Om du har försökt dessa felsökning förslag men fortfarande har problem med skicka dina loggar för diagnostik. Gå till app-inställningar och sedan välja **hjälp och feedback** och **skicka loggar**. Gå sedan till den [Microsoft Authenticator app forum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) och berätta för oss vad problemet är och hur du har gjort hittills.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Jag använder redan programmet Microsoft Authenticator för verifieringskoder. Hur växla till en enda klickning push-meddelanden?
Godkänna en inloggning via push-meddelanden är endast tillgängligt för personliga Microsoft-konton eller arbeta och skola Microsoft-konton, inte för konton från tredje part, till exempel Google eller Facebook. Om du har ett arbets- eller skolkonto Microsoft-konto kan kan din organisation du inaktivera det här alternativet.

Om du använder ett Microsoft-konto för ditt eget konto och vill övergå till push-meddelanden, måste du lägga till ditt konto igen. Registrera enheten med ditt konto och konfigurera push-meddelanden.  

Om du använder Microsoft Authenticator för ditt arbets- eller skolkonto, har ditt företag avgör om du vill tillåta enkelklickning meddelanden.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Fungerar enkelklickning push-meddelanden för icke-Microsoft konton?
Nej, push-meddelanden fungerar endast med Microsoft-konton och Azure Active Directory-konton. Om ditt arbete eller skola använder Azure AD-konton, kan de inaktivera den här funktionen.  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>Jag har återställt min enhet från en säkerhetskopia och koder för min saknas eller inte fungerar. Vad hände?
Vi återställa inte konton från appen säkerhetskopior av säkerhetsskäl.  När du återställer appen kan ta bort dina konton och lägga till dem igen.

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>Jag har fått en ny enhet. Hur gör ta bort Microsoft Authenticator-appen från min gamla enhet och flytta till en ny?
Att lägga till Microsoft Authenticator-appen till en ny enhet tar inte bort automatiskt den från andra enheter. Gå till samma webbplatsen som du använder för att hantera tvåstegsverifiering och ta bort gamla appar för att hantera vilka enheter som har konfigurerats för ditt konto.

För personliga Microsoft-konton, den här webbplatsen är din [kontosäkerhet](https://account.microsoft.com/security) sidan. För arbetet eller skolan Microsoft-konton, den här webbplatsen kan antingen vara [MyApps](https://myapps.microsoft.com) eller en anpassad portal som din organisation har ställts in.

### <a name="how-do-i-remove-an-account-from-the-app"></a>Hur tar jag bort ett konto från appen?
* iOS: från huvudskärmen sveper du till vänster på ikonen för ett konto. Välj **Ta bort**.
* Windows Phone: Från huvudskärmen, klicka på menyn, sedan **redigera konton**. Tryck på den **X** bredvid namnet på kontot.
* Android: Från huvudskärmen, klicka på menyn, sedan **redigera konton**. Tryck på den **X** bredvid namnet på kontot.

Om du har en enhet som har registrerats hos din organisation kan behöva du utföra ytterligare steg för att ta bort ditt konto. På dessa enheter registreras Microsoft Authenticator-appen automatiskt som en enhetsadministratör för. Om du vill avinstallera appen måste du först avregistrera app i app-inställningar.

### <a name="why-does-the-app-request-so-many-permissions"></a>Varför begäran så många behörigheter i appen?
Här är en fullständig lista över behörigheter som vi kan fråga efter och hur de används i appen. De specifika behörigheter som visas beror på vilken typ av phone som du har.

* **Kameran**: Vi använder kameran för att skanna QR-koder när du lägger till en arbete, din skola eller icke-Microsoft-konto.
* **Kontakter och phone**: när du loggar in med ditt personliga Microsoft-konto, vi försöker förenkla processen genom att söka efter befintliga konton som du använder på din telefon.
* **SMS**: när du loggar in med ditt personliga Microsoft-konto för första gången vi måste se till att ditt telefonnummer stämmer överens av posten. Vi skickar ett SMS till telefonen dit du hämtade appen. Meddelandet innehåller en Verifieringskod för 6 – 8 siffror. I stället för ber dig att hitta den här koden och ange den i appen, vi dig att hitta det. i textmeddelandet.
* **Rita över andra appar**: när du får ett meddelande att verifiera din identitet, vi visas detta meddelande över alla andra appar som kan köras.
* **Ta emot data från internet**: den här behörigheten krävs för att skicka meddelanden.
* **Förhindra att phone viloläge**: Om du registrerar din enhet med din organisation, de kan ändra den här principen på din telefon.
* **Kontrollera vibration**: du kan välja om du vill att en vibration när du tar emot ett meddelande att verifiera din identitet.
* **Använda fingeravtryck maskinvara**: vissa arbets- och skolkonton konton kräver en ytterligare PIN-kod när du verifierar din identitet. Vi kan du använda ditt fingeravtryck istället för att ange PIN-koden för att underlätta processen.
* **Visa nätverksanslutningar**: när du lägger till ett Microsoft-konto appen kräver nätverk, internet-anslutning.
* **Läsa innehållet i lagringen**: den här behörigheten används bara när du rapporterar ett tekniskt problem via app-inställningar. Viss information från ditt lager samlas in för att diagnosticera problemet.
* **Fullständig nätverksåtkomst**: den här behörigheten krävs för att skicka meddelanden för att verifiera din identitet.
* **Kör vid start**: Om du startar om telefonen den här behörigheten garanterar att du fortsätter du få meddelanden för att verifiera din identitet.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Varför kan Microsoft Autentiseringsapp du ska godkänna begäran utan att låsa upp enheten?

Du behöver inte låsa upp enheten för att godkänna begäranden om verifiering eftersom allt du behöver bevisa att du har telefonen med dig. Tvåstegsverifiering kräver bevisar två saker – en sak som du vet och en sak som du har. Det som du vet är ditt lösenord. Det du har är att din telefon (ställs in med Microsoft Authenticator-appen och registrerad som ett bevis MFA.) Därför uppfyller har telefon och godkänna begäran villkoren för tvåfaktorsautentisering för autentisering.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>Vad innebär låsikonen i listan över användarkonton?

Hänglåsikonen visar att enheten är registrerad i Azure AD och registrerad på kontot. Registrering av enheten för iOS äger rum under Microsoft Intune-registrering.

## <a name="next-steps"></a>Nästa steg

### <a name="contact-us"></a>Kontakta oss
Om din fråga inte besvaras här, vill vi gärna höra av dig. Gå till den [Microsoft Authenticator app forum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) att ställa din fråga och få hjälp i communityn eller lämna en kommentar på den här sidan.


### <a name="related-topics"></a>Relaterade ämnen
* [Om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) för Microsoft-konton
* [Har du problem med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md) för ditt arbets- eller skolkonto konto?
* [Använd Microsoft Authenticator för att logga in från din telefon](microsoft-authenticator-app-phone-signin-faq.md)
