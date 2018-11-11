---
title: Kom igång med Microsoft Authenticator-appen – Azure Active Directory | Microsoft Docs
description: Lär dig hur du uppgraderar till den senaste versionen av Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: d5ea6f18fb288711bae67b6840ddb5b8bb9942c2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245451"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Kom igång med Microsoft Authenticator-appen

Förhindra obehörig åtkomst till konton och för att stoppa olagliga transaktioner genom att ge en ytterligare nivå av säkerhet för ditt arbets- eller skolkonto för Microsoft Authenticator-appen (till exempel alain@contoso.com) eller ditt personliga Microsoft-konto (för exempelvis alain@outlook.com).

När du använder appen för tvåstegsverifiering kan fungera i ett av två sätt:

- **Meddelande om.** Appen skickar ett meddelande till din enhet. Kontrollera att meddelandet är rätt och välj sedan **Kontrollera**. Om du inte ser meddelandet, Välj **neka**. När du klickar på **neka**, kan du också markera på begäran som falska.

- **Verifieringskod.** När du har skrivit ditt användarnamn och lösenord kan du öppna appen och kopiera den Verifieringskod som tillhandahålls på den **konton** skärmen in på skärmen inloggning. Verifieringskoden fungerar som en andra formen av autentisering.

## <a name="opt-in-for-two-step-verification"></a>Välja tvåstegsverifiering

Din organisation bestämmer sig för om du ska använda tvåstegsverifiering med arbets-eller skolkonto. Administratören gör att du vet vilka verifieringsmetoder som behöver ställas in och används. Mer information finns i [vad Azure Multi-Factor Authentication innebär för mig](multi-factor-authentication-end-user.md).

Du kan ställa in tvåstegsverifiering själv för ditt personliga Microsoft-konto. Mer detaljerad information och anvisningar finns i [om tvåstegsverifiering](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Du kan också använda Microsoft Authenticator-appen med icke-Microsoft-konton. Dessa konton kan anropa funktionen något annat än tvåstegsverifiering, men du ska kunna hitta den i säkerhets- eller logga in inställningar. Mer information om hur du konfigurerar dessa icke-Microsoft-konton finns i den [Microsofts kundsupport videor](https://www.youtube.com/playlist?list=PLyhj1WZ29G65QdD9NxTOAm8HwOS-OBUrX).

## <a name="install-the-app"></a>Installera appen

Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) och [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071). För att få bästa möjliga upplevelse, bör du låta appen få meddelanden när du blir tillfrågad. 

## <a name="add-accounts-to-the-app"></a>Lägga till konton i appen

Du kan lägga till arbets- eller skolkonton eller personliga konton till Microsoft Authenticator-appen. 

### <a name="add-a-personal-microsoft-account"></a>Lägg till ett personligt microsoftkonto

För ett personligt microsoftkonto (en som används för att logga in på Outlook.com, Xbox, Skype, o.s.v.), allt du behöver göra är att logga in på ditt konto i Microsoft Authenticator-appen.

### <a name="add-a-work-or-school-account"></a>Lägg till ett arbets- eller skolkonto

1. Om det är möjligt, gå till den [ytterligare säkerhetsverifiering](https://aka.ms/mfasetup) skärmen på en annan dator eller enhet. Information om hur du kommer till den här skärmen finns i [ändrar säkerhetsinställningarna](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) eller kontakta administratören.

    >[!Note]
    >Om din administratör har aktiverat förhandsgranskningsupplevelsen för security info, följer du anvisningarna i den [konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md) avsnittet.

2. Markera kryssrutan bredvid **autentiseringsapp**, och välj sedan **konfigurera**.

    ![Knappen Konfigurera på skärmen för säkerhetsinställningar verifiering](./media/microsoft-authenticator-app-how-to/auth-app-configure.png)

    Den **konfigurera mobilappen** visas med en QR-kod att skanna med autentiseringsappen.

    ![Skärmen som tillhandahåller QR-kod](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. Öppna Microsoft Authenticator-appen. På den **konton** väljer **Lägg till konto**, och välj sedan **arbets- eller skolkonto**.

4. Använd enhetens kamera för att skanna QR-koden och välj sedan **klar** att Stäng skärmen QR-kod.

    >[!Note]
    >Om din kamera inte fungerar korrekt, kan du [ange QR-koden och Webbadressen manuellt](#add-an-account-to-the-app-manually).

    Den **konton** skärmen i appen visar namnet på ditt konto och en sexsiffrig Verifieringskod. För ytterligare säkerhet ändrar verifieringskoden med 30 sekunders mellanrum hindra dig från att använda samma kod två gånger.  

    ![skärmen konton](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Lägga till ett konto i appen manuellt

1. Gå till den **ytterligare säkerhetsverifiering** skärmen. Information om hur du kommer till den här skärmen finns [ändrar säkerhetsinställningarna](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).

2. Markera kryssrutan bredvid **autentiseringsapp**, och välj sedan **konfigurera**.

    Den **konfigurera mobilappen** skärmen visas.

3. Kopiera koden och URL-information från den **konfigurera mobilappen** skärmen så att du kan manuellt ange dem i QR-skanner.

4. Öppna Microsoft Authenticator-appen. På den **konton** väljer **Lägg till konto**, och välj sedan **arbets- eller skolkonto**.

5. På skärmen QR-skanner väljer **ange koden manuellt**.

    ![Skärmen för att skanna en QR-kod](./media/microsoft-authenticator-app-how-to/auth-app-manual-code.png)
   
6. Skriv koden och Webbadressen från skärmen med QR-kod till den **Lägg till ett konto** skärmen och välj sedan **Slutför**.

    ![Skärmen för att ange koden och Webbadressen](./media/microsoft-authenticator-app-how-to/auth-app-code-url.png)

    Den **konton** skärmen i appen visar namnet på ditt konto och en sexsiffrig Verifieringskod. För ytterligare säkerhet ändrar verifieringskoden med 30 sekunders mellanrum hindra dig från att använda samma kod två gånger.

### <a name="using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Med hjälp av enhetens fingeravtryck eller funktioner för ansiktsigenkänning

Din organisation kan kräva en PIN-kod att slutföra din bekräfta din identitet. Du kan konfigurera Microsoft Authenticator-appen för att använda enhetens fingeravtrycksläsare eller funktioner för ansiktsigenkänning i stället för en PIN-kod. Du kan konfigurera detta första gången du använder authenticator-appen för att verifiera ditt konto genom att välja alternativet att använda din biometriska enhetsfunktioner som används istället för PIN-koden.

## <a name="use-the-app-when-you-sign-in"></a>Använda appen när du loggar in

Du kan använda appen för att logga in på dina konton när du lägger till dina konton till appen.

Om du väljer att använda verifieringskoder i appen, börjar du vill visa dem på den **konton** sidan. Koderna ändra med 30 sekunders mellanrum så att du alltid har en ny kod när du behöver en. Men du behöver inte göra något med dem tills du loggar in och uppmanas att ange en Verifieringskod.

## <a name="next-steps"></a>Nästa steg

- Om du har mer allmänna frågor om appen, se [Microsoft Authenticator vanliga frågor och svar](microsoft-authenticator-app-faq.md)

- Om du vill ha mer information om tvåstegsverifiering finns i [konfigurerar mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md)

- Om du vill ha mer information om säkerhetsinformation [hantera din säkerhetsinformation](security-info-manage-settings.md)
