---
title: Logga in på dina konton med hjälp av Microsoft Authenticator-appen – Azure Active Directory | Microsoft Docs
description: Använd Microsoft Authenticator-appen för att logga in på ditt arbets eller skolkonto eller ditt personliga Microsoft och icke-Microsoft-konton med hjälp av två faktorer verifiering eller telefoninloggning.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: 201487c9052ce8df9734e7e11e604cbae9102aab
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155841"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Logga in på dina konton med hjälp av Microsoft Authenticator-appen
Microsoft Authenticator-appen kan du logga in på dina konton om du använder två faktorer verifiering. Verifiering av två faktorer hjälper dig att komma åt dina konton säkrare, särskilt när du visar känslig information. Eftersom lösenord kan glömmas, stulits eller komprometterats, två faktorer är ett ytterligare steg som hjälper dig att skydda ditt konto genom att göra det svårare för andra att logga in.

Du kan använda Microsoft Authenticator-appen på flera sätt, inklusive:

- Att tillhandahålla en uppmaning för en andra verifieringsmetod när du har loggat in med ditt användarnamn och lösenord.

- Att tillhandahålla inloggning utan att kräva ett lösenord, med ditt användarnamn och din mobila enhet med ditt fingeravtryck, ett ansikte eller en PIN-kod.

 >[!Important]
 >Den här phone inloggningsmetod fungerar bara med ditt personliga Microsoft-konton. Ditt arbete eller skolkonto och icke-Microsoft-konton måste du använda standard tvåfaktors-verifieringen.

## <a name="prerequisites"></a>Förutsättningar
Innan du kan använda Microsoft Authenticator-appen, måste du:

 1. Hämta och installera Microsoft Authenticator-appen. Om du inte har gjort det redan finns i [ladda ned och installera appen](microsoft-authenticator-app-how-to.md).

 2. Lägga till ditt arbete/skola personliga och tredjeparts-konton till Microsoft Authenticator-appen. Detaljerade anvisningar finns i [Lägg till ditt arbets- eller skolkonto](microsoft-authenticator-app-add-work-account.md), [lägga till dina personliga konton](microsoft-authenticator-app-add-personal-account.md), och [lägga till din personliga icke-Microsoft-konton](microsoft-authenticator-app-add-non-microsoft-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Aktivera och använda telefoninloggning för ditt arbets- eller skolkonto konto
Telefoninloggning är en typ av tvåstegsverifiering. Du måste fortfarande verifiera din identitet genom att tillhandahålla en sak som du vet och en sak som du har, men phone inloggning kan du hoppa över att ange lösenordet för ditt konto och utför alla dina identitetsverifiering på din mobila enhet.

Innan du kan aktivera telefoninloggning, måste du aktivera tvåfaktorsautentisering verifiering. Mer information om hur du aktiverar tvåfaktorsautentisering verifiering för ett konto finns i [Lägg till ditt arbets- eller skolkonto](microsoft-authenticator-app-add-work-account.md) och [lägga till dina personliga konton](microsoft-authenticator-app-add-personal-account.md).

Telefoninloggning är endast tillgänglig på iOS och Android-enheter med Android 6.0 eller senare.

### <a name="turn-on-phone-sign-in"></a>Aktivera telefoninloggning 

- Öppna Microsoft Authenticator-appen, går du till ditt arbete eller skolkonto och aktivera telefoninloggning:

    - **Om du ser den här ikonen ![ikon som visar du har konfigurerat](media/microsoft-authenticator-app-phone-signin-faq/icon.png).** Om den här ikonen visas bredvid ditt arbete eller skola kontonamn, betyder det att du har ställt in telefoninloggning för kontot. Du kan bli ombedd att lägga till push-meddelanden för ditt konto, så du kan bli meddelad om autentiseringsbegäranden utanför appen.

    - **Om du har använt appen för verifiering av två faktorer.** Om du har redan använt appen och verifiering av två faktorer, du kan välja pilen bredvid namnet på kontot och sedan välja **aktivera telefoninloggning**.
    
    - **Om du inte hittar arbets-eller skolkonto.** Om du inte hittar ditt arbets- eller skolkonto konto på den **konton** skärmen i appen, betyder det att du inte har lagt till den i appen än. Lägg till ditt arbets- eller skolkonto genom att följa stegen i den [Lägg till ditt arbets- eller skolkonto](microsoft-authenticator-app-add-work-account.md) artikeln.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Logga in på ditt konto med telefoninloggning
När du har aktiverat telefoninloggning, kan du logga in med bara Microsoft Authenticator-appen.

1. Logga in på ditt arbets- eller skolkonto konto.

    När du har skrivit ditt användarnamn, en **Godkänn inloggning** skärm visas där du kan se två siffror och uppmanas att logga in via Microsoft Authenticator-appen. Om du inte vill använda den här inloggning i metoden kan du välja **Använd ditt lösenord istället**, och logga in med ditt lösenord.

    ![Godkänn inloggning rutan på dator](media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in.png)

2. Öppna meddelandet eller Microsoft Authenticator-appen på din enhet och tryck sedan på det nummer som matchar det nummer du ser på din dator **Godkänn inloggning** skärmen.

    ![Godkänn inloggning rutan på enheten](media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in-numbers.png)

3. Välj **Godkänn** om du känner igen inloggningsförsök. Annars väljer du **neka**.

4. Använd din telefon PIN-kod eller biometriska nyckeln för att slutföra autentiseringen.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Aktivera och använda telefoninloggning för ditt personliga Microsoft-konton
Du kan aktivera telefoninloggning för ditt personliga Microsoft-konto, till exempel kontot som används för att logga in på Outlook.com, Xbox eller Skype.

>[!NOTE]
>För att skydda ditt konto, kräver Microsoft Authenticator-appen en PIN-kod eller biometriska lås på din enhet. Om du behåller din telefon låsas upp måste appen du ställa in ett security Lås innan du aktivera telefoninloggning.

### <a name="turn-on-phone-sign-in"></a>Aktivera telefoninloggning 

- Öppna Microsoft Authenticator-appen, går du till ditt personliga Microsoft-konto och aktivera telefoninloggning:

    - **Om du ser den här ikonen ![ikon som visar du har konfigurerat](media/microsoft-authenticator-app-phone-signin-faq/icon.png).** Om den här ikonen visas bredvid namnet på ditt konto, innebär det att du har ställt in telefoninloggning för kontot. Du kan bli ombedd att lägga till push-meddelanden för ditt konto, så du kan bli meddelad om autentiseringsbegäranden utanför appen.

    - **Om du har använt appen för verifiering av två faktorer.** Om du har redan använt appen och verifiering av två faktorer, du kan välja pilen bredvid namnet på kontot och sedan välja **aktivera telefoninloggning**.
    
    - **Om du inte hittar ditt konto.** Om du inte hittar ditt konto på den **konton** skärmen i appen, betyder det att du inte har lagt till den i appen än. Lägg till ditt personliga Microsoft-konto genom att följa stegen i den [lägger du till ditt personliga Microsoft-konto](microsoft-authenticator-app-add-personal-account.md#add-your-personal-microsoft-account) artikeln.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Logga in på ditt konto med telefoninloggning

1. Gå till ditt personliga Microsoft-konto på inloggningssidan och välj sedan i stället för att skriva ditt lösenord, den **Använd Microsoft Authenticator-appen i stället** länk. 

    Microsoft skickar ett meddelande till din telefon. 

2. Godkänn meddelandet. 

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Logga in med två faktorer verifiering för ditt konto
Standard verifiering av två faktorer-metoden måste du ange ditt användarnamn och lösenord i enheten du har loggat in och välj sedan om Microsoft Authenticator-appen skickar ett meddelande eller om du vill kopiera den associera verifieringen kod från den **konton** skärmen i Microsoft Authenticator-appen. Du aktiverar tvåfaktorsautentisering verifiering för ditt konto som en del av processen för att lägga till kontot i Microsoft Authenticator-appen.

>[!Note]
>Om du inte ser ditt arbets- eller skolkonto eller ditt personliga konto på den **konton** skärm med Microsoft Authenticator-appen som det innebär att du inte har lagt till kontot till Microsoft Authenticator-appen. Om du vill lägga till ditt konto, se [Lägg till ditt arbets- eller skolkonto](microsoft-authenticator-app-add-work-account.md) eller [lägga till dina personliga konton](microsoft-authenticator-app-add-personal-account.md).

Steg som krävs för att logga in på ditt arbets- eller Skol- eller ditt personliga konto, med de olika metoderna för verifiering av två faktorer, finns i [logga in med tvåstegsverifiering verifiering eller din säkerhetsinformation](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
|**Fråga**|**Lösning**|
|--------------|-------------|
|**Hur loggar in med telefonen säkrare än att skriva ett lösenord?**|Idag de flesta användare logga in på webbplatser och appar med ett användarnamn och lösenord. Tyvärr kan lösenord vara tappas bort, blir stulen eller gissa som hackare använder.<br><br>När du har konfigurerat Microsoft Authenticator-appen skapar en nyckel på din telefon för att låsa upp ditt konto som skyddas av din telefon PIN-kod eller biometriska Lås. Den här nyckeln används sedan för att verifiera din identitet när du loggar in.<br><br>**Viktigt**<br>Dina data används endast för att skydda din nyckel lokalt. Det har aldrig skickas till, eller lagras i molnet.|
|**Telefoninloggning ersätter tvåstegsverifiering? Bör jag stänga av den?**|Telefoninloggning är en typ av två steg verifiering där de två stegen båda ske på den mobila enheten. Du bör ha två steg verifiering aktiveras för att ge ytterligare säkerhet för ditt konto.|
|**Om jag behålla tvåstegsverifiering aktiverad för mitt konto, har jag godkänna två meddelanden?**| Nej. Logga in på ditt Microsoft-konto med din telefon också räknas som tvåstegsverifiering, så det finns inga andra godkännande krävs.|
|**Vad händer om jag tappar bort min telefon eller inte har den med mig? Hur kommer jag åt mitt konto?**| Du kan alltid välja att använda ett lösenord i stället länken på sidan logga in för att växla tillbaka till ditt lösenord. Om du använder tvåstegsverifiering kommer du dock fortfarande behöver använda ett annat sätt för att verifiera din identitet.<br><br>**Viktigt**<br>Vi att starkt du kan kontrollera att du har mer än en uppdaterad verifieringsmetod som är associerat med ditt konto.<br><br>Du kan hantera din verifieringsmetoder för personliga konton från din [säkerhetsinställningar](https://account.live.com/proofs/manage) sidan. För konton för arbets- eller skolkonto, går du till din organisations [ytterligare säkerhetsverifiering](https://aka.ms/MFASetup) sidan eller **skydda ditt konto** om din administratör har aktiverat säkerhetsinformation. Läs mer om säkerhetsinformation [hantera din säkerhetsinformation](security-info-manage-settings.md).<br><br>Om du inte kan hantera din verifieringsmetoder, måste du kontakta din administratör.|
|**Hur jag för att sluta använda den här funktionen och gå tillbaka till med mitt lösenord?**|För personliga konton väljer du den **använder ett lösenord i stället** länk när de loggar in. Senaste valfri sparas och erbjuds som standard nästa gång du loggar in. Om du vill gå tillbaka till att använda telefoninloggning, Välj den **använder en app i stället** länk när de loggar in.<br><br>För arbets- eller skolkonto-konton måste du till din sida med ytterligare verifiering (t.ex, https://aka.ms/MFASetup) och ta bort Microsoft Authenticator-appen.|
|**Varför kan jag inte använda fler än ett arbets- eller skolkonto konto för inloggning via mobilen?**| En telefon måste vara registrerade i en arbets- eller skolkonto. Om du vill aktivera telefoninloggning för en annan arbets- eller skolkonto, måste du först avregistrera gamla enheten via den **inställningar** sidan.|
|**Kan jag logga in på datorn med hjälp av min telefon?**| För din dator rekommenderar vi loggar in med Windows Hello på Windows 10. Windows Hello kan du använda din ansiktsigenkänning eller fingeravtryck PIN-kod för att logga in.|
|**Kan jag använda telefoninloggning med min Windows Phone?**| Nej. Den här funktionen stöds inte av Microsoft Authenticator-appen på Windows Phone.|

## <a name="next-steps"></a>Nästa steg
- Om du har mer allmänna frågor om appen kan se den [Microsoft Authenticator vanliga frågor och svar](microsoft-authenticator-app-faq.md)

- Om du vill ha mer information om tvåstegsverifiering finns i [konfigurerar mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md)

- Om du vill ha mer information om säkerhetsinformation [hantera din säkerhetsinformation](security-info-manage-settings.md)
