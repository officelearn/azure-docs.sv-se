---
title: Microsoft Authenticator telefoninloggning - Azure Active Directory | Microsoft Docs
description: Innehåller detaljerad information och instruktioner om hur du använder din telefon för att logga in på ditt arbete och skola och personliga Microsoft-konton i stället för att skriva ditt lösenord.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 3303a0fb1ca4c4a8f6695c2e4338c1b0a0fc9f40
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358640"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Logga in med din telefon, inte ditt lösenord

Microsoft Authenticator-appen inte bara kan du skydda dina konton genom att utföra tvåstegsverifiering när du ange ditt lösenord, men nu den kan ändra lösenordet för ditt Microsoft-konto helt och hållet.

Den här funktionen är tillgänglig på iOS och Android-enheter med Android 6.0 eller senare.

## <a name="how-it-works"></a>Hur det fungerar

Många av er Använd Microsoft Authenticator-appen för tvåstegsverifiering när du loggar in på ditt Microsoft-konto. Du skriver lösenordet och sedan gå till appen antingen godkänna ett meddelande eller för att hämta en Verifieringskod. Med telefoninloggning, hoppa över lösenordet och göra alla identitetsverifiering på din telefon. Eftersom telefoninloggning är en typ av tvåstegsverifiering kan behöver du fortfarande verifiera din identitet genom att tillhandahålla en sak som du vet och en sak som du har. Telefonen fortfarande är det som du har, men din telefon PIN-kod eller biometriska nyckeln nu är det som du vet.

## <a name="turn-on-phone-sign-in-for-your-work-or-school-account"></a>Aktivera phone inloggning för arbets-eller skolkonto

Om din organisation använder Azure Active Directory för Identitetshantering och autentisering, kan du använda telefoninloggning när du har aktiverat det i Microsoft Authenticator-appen. 

### <a name="to-turn-on-phone-sign-in-for-your-account"></a>Aktivera phone logga in för ditt konto

- **Om du har den här ikonen ![inloggning telefonikonen](./media/microsoft-authenticator-app-phone-signin-faq/icon.png) på din telefon.** Du har redan konfigurerat telefoninloggning. Du kan bli ombedd att uppgradera ditt konto för att lägga till push-meddelanden, så att du kan bli meddelad om autentiseringsbegäranden utanför appen.

- **Om du redan använder Microsoft Authenticator-appen för tvåstegsverifiering.** Gå till den **konton** skärmen i appen, Välj listrutepilen för arbets-eller skolkonto och välj sedan **aktivera telefoninloggning**.

- **Om du använder Microsoft Authenticator-appen för ditt personliga konto, men inte för arbets-eller skolkonto.** Följ organisationens tvåstegsverifiering verifieringsinstruktionerna, och sedan aktivera telefoninloggning. Mer information finns i [Kom igång med Microsoft Authenticator-appen](microsoft-authenticator-app-how-to.md)

### <a name="to-use-phone-sign-in-to-sign-in-to-your-work-or-school-account"></a>Att använda telefoninloggning för att logga in på ditt arbets- eller skolkonto konto

1.  Logga in på ditt arbets- eller skolkonto konto som vanligt.

    När du har skrivit ditt användarnamn, visas en sida med en 2-siffror ber dig godkänna inloggningen via Microsoft Authenticator-appen. Om du inte vill använda den här inloggning i metoden kan du välja **Använd ditt lösenord istället**, och logga in med ditt lösenord.

    ![På inloggningssidan](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in.png)
 
2. I Microsoft Authentication-appen, du får ett meddelande som ber dig **Godkänn inloggning**. Välj **Godkänn** om du känner igen inloggningsförsök. Annars väljer du **neka**.

3. Tryck på samma nummer som visas på den **Godkänn inloggning** skärmen. Använd din telefon PIN-kod eller biometriska nyckeln för att slutföra autentiseringen.
 
    ![Godkänn inloggningssidan](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in-numbers.png)

## <a name="turn-on-phone-sign-in-for-your-personal-account"></a>Aktivera phone inloggning för ditt personliga konto

Du kan aktivera telefoninloggning för ditt personliga Microsoft-konto, till exempel kontot som används för att logga in på Outlook.com, Xbox eller Skype.

### <a name="to-turn-on-phone-sign-in-for-your-personal-account"></a>Aktivera phone inloggning för ditt personliga konto

- **Om du inte använder Microsoft Authenticator-appen.** Hämta och installera Microsoft Authenticator-appen och Lägg sedan till ditt personliga Microsoft-konto genom att logga in på kontot från de **lägga till konton** skärmen i Microsoft Authenticator-appen. Mer information finns i [Kom igång med Microsoft Authenticator-appen](microsoft-authenticator-app-how-to.md).
Nya konton som är automatiskt aktiverad så att du kan omedelbart börja använda telefoninloggning.

- **Om du redan använder Microsoft Authenticator-appen för tvåstegsverifiering.** Gå till den **konton** skärmen i appen, Välj listrutepilen för arbets-eller skolkonto och välj sedan **aktivera telefoninloggning**.

>[!NOTE]
>För att skydda ditt konto, kräver appen en PIN-kod eller biometriska lås på din enhet. Om du behåller din telefon låsas upp måste appen du ställa in ett security Lås innan du aktivera telefoninloggning.

### <a name="to-use-phone-sign-in-to-sign-in-to-your-personal-account"></a>Att använda telefoninloggning för att logga in på ditt personliga konto

1.  Gå till ditt personliga Microsoft-konto på inloggningssidan och välj sedan i stället för att skriva ditt lösenord, den **använder en app i stället** länk.

    Microsoft skickar ett meddelande till din telefon. 

2.  Godkänn meddelandet.

## <a name="phone-sign-in-faq"></a>Phone inloggning FAQ

|**Fråga**|**Lösning**|
|--------------|-------------|
|**Hur loggar in med telefonen säkrare än att skriva ett lösenord?**|Idag de flesta användare logga in på webbplatser och appar med ett användarnamn och lösenord. Tyvärr kan lösenord vara tappas bort, blir stulen eller gissa som hackare använder.<br><br>När du har konfigurerat Microsoft Authenticator-appen skapar en nyckel på din telefon för att låsa upp ditt konto som skyddas av din telefon PIN-kod eller biometriska Lås. Den här nyckeln används sedan för att verifiera din identitet när du loggar in.<br><br>**Viktigt**<br>Dina data används endast för att skydda din nyckel lokalt. Det har aldrig skickas till, eller lagras i molnet.|
|**Ersätter tvåstegsverifiering? Bör jag stänga av den?**| Vi arbetar för att expandera omfattningen för telefoninloggning, men nu det fortfarande finns platser i Microsofts ekosystem som inte stöder den. På dessa platser används tvåstegsverifiering fortfarande för säker inloggning. Därför finns inte, bör inte du inaktivera tvåstegsverifiering för ditt konto.|
|**Om jag behålla tvåstegsverifiering aktiverad för mitt konto, har jag godkänna två meddelanden?**| Nej. Logga in på ditt Microsoft-konto med din telefon också räknas som tvåstegsverifiering, så det finns inga andra godkännande krävs.|
|**Vad händer om jag tappar bort min telefon eller inte har den med mig? Hur kommer jag åt mitt konto?**| Du kan alltid välja att använda ett lösenord i stället länken på sidan logga in för att växla tillbaka till ditt lösenord. Om du använder tvåstegsverifiering kommer du dock fortfarande behöver använda ett annat sätt för att verifiera din identitet.<br><br>**Viktigt**<br>Är du rekommenderas att ha mer än en uppdaterad verifieringsmetod som är associerat med ditt konto.<br><br>Du kan hantera din verifieringsmetoder för personliga konton från din [säkerhetsinställningar](https://account.live.com/proofs/manage) sidan. För konton för arbets- eller skolkonto, går du till din organisations [ytterligare säkerhetsverifiering](https://aka.ms/MFASetup) sidan eller **skydda ditt konto** om din administratör har aktiverat säkerhetsinformation. Läs mer om säkerhetsinformation [hantera din säkerhetsinformation](security-info-manage-settings.md).<br><br>Om du inte kan hantera din verifieringsmetoder, måste du kontakta din administratör.|
|**Hur jag för att sluta använda den här funktionen och gå tillbaka till med mitt lösenord?**|För personliga konton väljer du den **använder ett lösenord i stället** länken under inloggning. Senaste valfri sparas och erbjuds som standard nästa gång du loggar in. Om du vill gå tillbaka till att använda telefoninloggning, Välj den **använder en app i stället** länken under inloggning.<br><br>För arbets- eller skolkonto-konton måste du ta bort kontot genom att öppna Microsoft Authenticator-appen att välja den **redigera konton** menyn och sedan ta bort ditt konto.|
|**Varför kan jag inte använda fler än ett arbets- eller skolkonto konto för inloggning via mobilen?**| En telefon måste vara registrerade i en arbets- eller skolkonto. Om du vill aktivera telefoninloggning för en annan arbets- eller skolkonto, måste du först avregistrera det gamla kontot via den **inställningar** sidan.|
|**Kan jag logga in på datorn med hjälp av min telefon?**| För din dator rekommenderar vi loggar in med Windows Hello på Windows 10. Windows Hello kan du använda din ansiktsigenkänning eller fingeravtryck PIN-kod för att logga in.|
|**Kan jag använda telefoninloggning med min Windows Phone?**| Nej. Den här funktionen stöds inte av Microsoft Authenticator-appen på Windows Phone.|

## <a name="next-steps"></a>Nästa steg

-   Om du har mer allmänna frågor om appen, se [Microsoft Authenticator vanliga frågor och svar](microsoft-authenticator-app-faq.md)

-   Om du vill ha mer information om tvåstegsverifiering finns i [konfigurerar mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md)

-   Om du vill ha mer information om säkerhetsinformation [hantera din säkerhetsinformation](security-info-manage-settings.md)