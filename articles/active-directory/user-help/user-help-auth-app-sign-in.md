---
title: Logga in med Microsoft Authenticator-appen - Azure AD
description: Använd Microsoft Authenticator-appen för att logga in på ditt arbets- eller skolkonto eller dina personliga Microsoft- och icke-Microsoft-konton, med hjälp av antingen tvåfaktorsverifiering eller telefon inloggning.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 9b8c44f99953d4518f0bc3f558f396250657c632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138951"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Logga in på dina konton med hjälp av Microsoft Authenticator-appen

Microsoft Authenticator-appen hjälper dig att logga in på dina konton om du använder tvåfaktorsverifiering. Med tvåfaktorsverifiering kan du komma åt dina konton säkrare, särskilt när du visar känslig information. Eftersom lösenord kan glömmas, stjälas eller komprometteras är tvåfaktorsverifiering ett ytterligare steg som hjälper dig att skydda ditt konto genom att göra det svårare för andra att logga in.

Du kan använda Microsoft Authenticator-appen på flera sätt, däribland följande:

- Tillhandahålla en uppmaning för en andra verifieringsmetod när du har loggat in med ditt användarnamn och lösenord.

- Tillhandahålla inloggning utan att kräva ett lösenord, med ditt användarnamn och din mobila enhet med ditt fingeravtryck, ett ansikte eller en PIN-kod.

  >[!Important]
  >Den här inloggningsmetoden för telefon fungerar bara med dina arbets-, skol- och personliga Microsoft-konton. Dina konton som inte kommer från Microsoft kräver att du använder standardverifieringsprocessen med två faktorer.

## <a name="prerequisites"></a>Krav

Innan du kan använda Microsoft Authenticator-appen måste du:

 1. Ladda ned och installera Microsoft Authenticator-appen. Om du inte har gjort detta ännu läser du [Ladda ned och installera appen](user-help-auth-app-download-install.md).

 2. Lägg till dina arbets-/skol-, personliga och tredjepartskonton i Microsoft Authenticator-appen. Detaljerade steg finns i [Lägga till ditt arbets- eller skolkonto,](user-help-auth-app-add-work-school-account.md) [Lägg till dina personliga konton](user-help-auth-app-add-personal-ms-account.md)och Lägg till dina konton som inte kommer [från Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Aktivera och använda telefon inloggning för ditt arbete eller ditt skolkonto

Telefon inloggning är en typ av tvåstegsverifiering. Du måste fortfarande verifiera din identitet genom att ange en sak du vet och en sak du har, men telefoninmatning gör att du kan hoppa över att ange ditt kontolösenord och utföra all din identitetsverifiering på din mobila enhet.

Innan du kan aktivera telefon inloggning, måste du aktivera tvåfaktorsverifiering. Mer information om hur du aktiverar tvåfaktorsverifiering för ett konto finns i [Lägga till ditt arbets- eller skolkonto](user-help-auth-app-add-work-school-account.md) och [Lägg till dina personliga konton](user-help-auth-app-add-personal-ms-account.md).

Telefon inloggning är endast tillgänglig på iOS och Android-enheter som kör Android 6.0 eller högre.

### <a name="turn-on-phone-sign-in"></a>Aktivera telefon inloggning

Öppna Microsoft Authenticator-appen, gå till ditt arbets- eller skolkonto och aktivera telefon inloggning.

På en Android-enhet:

- **Om den här ![ikonen visas visar](media/user-help-auth-app-sign-in/icon.png)att du är konfigurerad .** Om den här ikonen visas bredvid ditt arbets- eller skolkontonamn betyder det att du redan har konfigurerat telefon inloggning för kontot. Du kan bli ombedd att lägga till push-meddelanden för ditt konto, så att du kan få ett meddelande om autentiseringsbegäranden utanför appen.
- **Om du redan använder appen för tvåfaktorsverifiering.** Om du redan har använt appen och tvåfaktorsverifiering kan du välja pilen bredvid kontonamnet och sedan välja **Aktivera telefon inloggning**.
- **Om du inte kan hitta ditt arbets- eller skolkonto.** Om du inte hittar ditt arbets- eller skolkonto på skärmen **Konton** i appen betyder det att du inte har lagt till det i appen ännu. Lägg till ditt arbets- eller skolkonto genom att följa stegen i artikeln [Lägg till ditt arbets- eller skolkonto.](user-help-auth-app-add-work-school-account.md)

På en iOS-enhet:

- **När du trycker på kontopanelen**öppnas kontot i en helskärmsvy av kontot. Om du ser **Telefon inloggning aktiverat** det innebär att du är helt inställd på att logga in utan ditt lösenord. Om du ser **Aktivera telefon inloggning**trycker du på den för att aktivera telefon inloggning.
- **Om du redan har använt appen för tvåfaktorsverifiering**kan du trycka på kontopanelen, som sedan expanderar till en helskärmsvy av kontot. Tryck sedan på **Aktivera telefon inloggning** för att aktivera telefon inloggning.
- **Om du inte hittar ditt arbets- eller skolkonto** på skärmen **Konton** i appen betyder det att du inte har lagt till det i appen ännu. Lägg till ditt arbets- eller skolkonto genom att följa stegen i artikeln Lägg till ditt arbets- eller skolkonto.

När du har aktiverat telefon inloggning kan du logga in med bara Microsoft Authenticator app.

1. Logga in på ditt arbets- eller skolkonto.

    När du har skrivit ditt användarnamn visas en **inloggningsskärm för godkänn inloggning** som visar ett tvåsiffrigt nummer och ber dig logga in via Microsoft Authenticator-appen. Om du inte vill använda den här inloggningsmetoden kan du välja **Använd ditt lösenord i stället**och logga in med ditt lösenord.

    ![Godkänn inloggningsruta på datorn](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Öppna meddelandet eller Microsoft **Authenticator-appen** på enheten och tryck sedan på numret som matchar numret som visas på datorns godkänn inloggningsskärm.

    ![Godkänn inloggningsruta på enheten](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Välj **Godkänn** om du känner igen inloggningsförsöket. Annars väljer du **Neka**.

4. Använd telefonens PIN-kod eller biometriska nyckeln för att slutföra autentiseringen.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Aktivera och använda telefon inloggning för dina personliga Microsoft-konton

Du kan aktivera telefon inloggning för ditt personliga Microsoft-konto, till exempel det konto du använder för att logga in på Outlook.com, Xbox eller Skype.

>[!NOTE]
>För att skydda ditt konto kräver Microsoft Authenticator-appen en PIN-kod eller ett biometriskt lås på enheten. Om du håller telefonen olåst kräver appen att du konfigurerar ett säkerhetslås innan du aktiverar telefon inloggning.

### <a name="turn-on-phone-sign-in"></a>Aktivera telefon inloggning 

Öppna Microsoft Authenticator-appen, gå till ditt arbets- eller skolkonto och aktivera telefon inloggning.

På en Android-enhet:

- **Om den här ![ikonen visas visar](media/user-help-auth-app-sign-in/icon.png)att du är konfigurerad .** Om den här ikonen visas bredvid ditt personliga Microsoft-kontonamn betyder det att du redan har konfigurerat telefon inloggning för kontot. Du kan bli ombedd att lägga till push-meddelanden för ditt konto, så att du kan få ett meddelande om autentiseringsbegäranden utanför appen.
- **Om du har använt appen för tvåfaktorsverifiering.** Om du redan har använt appen och tvåfaktorsverifiering kan du välja pilen bredvid kontonamnet och sedan välja **Aktivera telefon inloggning**.
- **Om du inte kan hitta ditt arbets- eller skolkonto.** Om du inte hittar ditt konto på skärmen **Konton** i appen betyder det att du inte har lagt till det i appen ännu. Lägg till ditt konto genom att följa stegen i artikeln [Lägg till personliga Microsoft-konton.](user-help-auth-app-add-personal-ms-account.md)

På en iOS-enhet:

- **När du trycker på kontopanelen**öppnas kontot i en helskärmsvy av kontot. Om du ser **Telefon inloggning aktiverat** det innebär att du är helt inställd på att logga in utan ditt lösenord. Om du ser **Aktivera telefon inloggning**trycker du på den för att aktivera telefon inloggning.
- **Om du redan använder appen för tvåfaktorsverifiering**kan du trycka på kontopanelen, som sedan expanderar till en helskärmsvy av kontot. Tryck sedan på **Aktivera telefon inloggning** för att aktivera telefon inloggning.
- **Om du inte hittar ditt konto** på skärmen **Konton** i appen betyder det att du inte har lagt till det i appen ännu. Lägg till ditt personliga Microsoft-konto genom att följa stegen i artikeln [Lägg till personliga Microsoft-konton.](user-help-auth-app-add-personal-ms-account.md)

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Logga in på ditt konto med hjälp av telefon inloggning

1. Gå till din personliga inloggningssida för Microsoft-kontot och välj sedan länken **Använd microsoft authenticator-appen i stället för** att skriva ditt lösenord.

    Microsoft skickar ett meddelande till telefonen.

2. Godkänn meddelandet.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Logga in med tvåfaktorsverifiering för ditt konto

Standardverifieringsmetoden med två faktorer kräver att du anger ditt användarnamn och lösenord i enheten du loggar in på. Sedan öppnar du din Microsoft Authenticator-app och skriver den slumpmässigt genererade koden för ditt konto i rutan **Ange kod.** På en Android-enhet finns dessa verifieringskoder på skärmen **Konton.** På en iOS-enhet finns dessa verifieringskoder på **skärmen Konton** eller i helskärmsläge för ett konto, beroende på vilken typ av konto det är. Du aktiverar tvåfaktorsverifiering för ditt konto som en del av processen för att lägga till kontot i Microsoft Authenticator-appen.

>[!Note]
>Om du inte ser ditt arbets- eller skolkonto eller ditt personliga konto på skärmen **Konton** i Microsoft Authenticator-appen betyder det att du inte har lagt till kontot i Microsoft Authenticator-appen. Information om hur du lägger till ditt konto finns i [Lägga till ditt arbets- eller skolkonto](user-help-auth-app-add-work-school-account.md) eller [Lägg till dina personliga konton](user-help-auth-app-add-personal-ms-account.md).

De steg som krävs för att logga in på ditt arbete eller din skola eller ditt personliga konto, med hjälp av de olika metoderna för tvåfaktorsverifiering, finns [i Logga in med tvåstegsverifiering eller säkerhetsinformation](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

| Fråga | Lösning |
| -------- | -------- |
| Hur är det säkrare att logga in med telefonen än att skriva ett lösenord? | Idag loggar de flesta in på webbplatser eller appar med ett användarnamn och lösenord. Tyvärr kan lösenord gå förlorade, stulna eller gissas av hackare.<br><br>När du har konfigurerat Microsoft Authenticator-appen skapas en nyckel på telefonen för att låsa upp ditt konto som skyddas av telefonens PIN-kod eller biometriska lås. Den här nyckeln används sedan för att bevisa din identitet när du loggar in.<br><br>**Viktigt**<br>Dina data används bara för att skydda din nyckel lokalt. Det skickas aldrig till, eller lagras i, molnet. |
| Ersätter telefon inloggning tvåstegsverifiering? Ska jag stänga av den? | Telefon inloggning är en typ av två steg verifiering där de två stegen både sker på den mobila enheten. Du bör ha två steg verifiering aktiverat för att ge ytterligare säkerhet för ditt konto. |
| Måste jag godkänna två meddelanden om jag har aktiverat tvåstegsverifiering för mitt konto? | Nej. Att logga in på ditt Microsoft-konto med telefonen räknas också som tvåstegsverifiering, så det krävs inget andra godkännande. |
| Vad händer om jag tappar bort telefonen eller inte har den med mig? Hur kommer jag åt mitt konto? | Du kan alltid välja länken Använd ett lösenord i stället på inloggningssidan för att växla tillbaka till att använda ditt lösenord. Men om du använder tvåstegsverifiering måste du fortfarande använda en andra metod för att verifiera din identitet.<br><br>**Viktigt**<br>Vi rekommenderar starkt att du ser till att du har mer än en, uppdaterad, verifieringsmetod kopplad till ditt konto.<br><br>Du kan hantera dina verifieringsmetoder för personliga konton från sidan [Säkerhetsinställningar.](https://account.live.com/proofs/manage) För arbets- eller skolkonton kan du gå till organisationens [sida För ytterligare säkerhetsverifiering](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) eller sidan **Skydda ditt konto** om administratören har aktiverat säkerhetsinformation. Mer information om säkerhetsinformation finns i [Översikt över säkerhetsinformation (förhandsversion).](user-help-security-info-overview.md)<br><br>Om du inte kan hantera dina verifieringsmetoder måste du kontakta administratören. |
| Hur slutar jag använda den här funktionen och går tillbaka till att använda mitt lösenord? | För personliga konton väljer du länken **Använd ett lösenord i stället** när du loggar in. Ditt senaste val kommer ihåg och erbjuds som standard nästa gång du loggar in. Om du någonsin vill gå tillbaka till att använda telefon inloggning, välj **länken Använd en app istället** under inloggning.<br><br>För arbets- eller skolkonton måste du antingen avregistrera enheten från sidan **Inställningar** i Microsoft Authenticator-appen eller inaktivera enheten från aktivitetsområdet **Enheter & i** din profil. Mer information om hur du inaktiverar enheten från din profil finns i [Uppdatera din profil och kontoinformation från portalen Mina appar](my-apps-portal-end-user-update-profile.md#view-your-organization-related-profile-information). |
| Varför kan jag inte använda mer än ett arbets- eller skolkonto för telefon inloggning? | En telefon måste vara registrerad på ett enda arbets- eller skolkonto. Om du vill aktivera telefon inloggning för ett annat arbets- eller skolkonto måste du avregistrera ditt konto från den här enheten via sidan **Inställningar.** |
| Kan jag logga in på datorn med telefonen? | För din dator rekommenderar vi att du loggar in med Windows Hello på Windows 10. Med Windows Hello kan du använda ditt ansikte, fingeravtryck eller din PIN-kod för att logga in. |

## <a name="next-steps"></a>Nästa steg

- Om du har problem med att få din verifieringskod för ditt personliga Microsoft-konto läser du avsnittet **Felsöka verifieringskodproblem** i [säkerhetsinformationen för Microsoft-kontot & verifieringskoder.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- Om du har fler allmänna frågor om appen läser du vanliga frågor om [Microsoft Authenticator](user-help-auth-app-faq.md)

- Om du vill ha mer information om tvåstegsverifiering läser du [Konfigurera mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md)

- Om du vill ha mer information om säkerhetsinformation läser du [Översikt över säkerhetsinformation (förhandsversion)](user-help-security-info-overview.md)
