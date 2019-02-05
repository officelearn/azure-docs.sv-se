---
title: Översikt över Microsoft Authenticator-appen – Azure Active Directory | Microsoft Docs
description: Läs mer om Microsoft Authenticator-appen, till exempel vad det är, hur det fungerar och vilken information som ingår i det här avsnittet av innehållet.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/24/2019
ms.author: lizross
ms.openlocfilehash: 2379f1ff4fb4385015cc6077cb923cab998d1d11
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175221"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Vad är Microsoft Authenticator-appen?
Med Microsoft Authenticator-appen kan du logga in på dina konton om du använder tvåfaktorsverifiering. Med tvåfaktorsverifiering kan du komma åt dina konton säkrare, särskilt när du visar känslig information. Eftersom lösenord kan glömmas, stjälas eller komprometteras är tvåfaktorsverifiering ett ytterligare steg som hjälper dig att skydda ditt konto genom att göra det svårare för andra att logga in.

Du kan använda Microsoft Authenticator-appen på flera sätt, som att:

- Tillhandahålla en uppmaning för en andra verifieringsmetod när du har loggat in med ditt användarnamn och lösenord.

- Tillhandahålla inloggning utan att kräva ett lösenord, med ditt användarnamn och din mobila enhet med ditt fingeravtryck, ett ansikte eller en PIN-kod.

>[!Important]
>Innehållet är avsett för användare. Om du är administratör hittar du mer information om hur du konfigurerar och hanterar en Azure Active Directory (Azure AD)-miljö i [dokumentationen om Azure Active Directory](https://docs.microsoft.com/azure/active-directory).<br><br>Om du har problem med att logga in på ditt konto kan du läsa [When you can't sign in to your Microsoft account](https://support.microsoft.com/help/12429) (När du inte kan logga in på ditt Microsoft-konto) om du behöver hjälp.  Få mer information om vad du gör när du får meddelandet [”Det Microsoft-kontot finns inte”](https://support.microsoft.com/help/13811) när du försöker logga in på ditt Microsoft-konto.

## <a name="terminology"></a>Terminologi
|Period|Beskrivning|
|----|-----------|
|Tvåfaktorsverifiering |En verifieringsprocess som kräver att du endast använder två typer av verifieringsinformation, t.ex. ett lösenord och en PIN-kod. Microsoft Authenticator-appen har stöd för både tvåfaktorsverifiering som är standard och lösenordsfri inloggning.|
|Multi-Factor Authentication (MFA)|All tvåfaktorsverifiering är multifaktorautentisering, och kräver att du använder *minst* två typer av information för verifiering, baserat på din organisations behov.|
|Microsoft-konto (kallas även MSA)|Du skapar dina egna personliga konton som ger åtkomst till dina kundorienterade Microsoft-produkter och -molntjänster som Outlook, OneDrive, Xbox LIVE eller Office 365. Ditt Microsoft-konto skapas och lagras i Microsofts kontosystem för konsumentidentiteter som drivs av Microsoft.|
|Arbets- eller skolkonto|Din organisation skapar arbets-eller skolkonto (till exempel alain@contoso.com) så att du får åtkomst till interna och potentiellt begränsade resurser, till exempel Microsoft Azure, Windows Intune och Office 365.|

## <a name="how-two-factor-verification-works-with-the-app"></a>Så här fungerar tvåfaktorsverifiering med appen
Tvåfaktorsverifiering fungerar med Microsoft Authenticator-appen på följande sätt:

- **Meddelande.** Ange ditt användarnamn och lösenord i enheten som du loggar in för ditt arbets- eller skolkonto eller ditt personliga Microsoft-konto så skickar Microsoft Authenticator-appen ett meddelande som ber dig **godkänna inloggningen**. Välj **Godkänn** om du känner igen inloggningsförsöket. Annars väljer du **Neka**. Om du väljer **Neka** kan du också markera begäran som falsk.

- **Verifieringskod.** Ange ditt användarnamn och lösenord i enheten som du loggar in för ditt arbets- eller skolkonto eller ditt personliga Microsoft-konto och kopiera sedan den associerade verifieringskoden från skärmen **Konton** i Microsoft Authenticator-appen.

- **Konfiguration av lösenordsfri inloggning.** Ange ditt användarnamn och lösenord i enheten som du loggar in för ditt arbets- eller skolkonto eller ditt personliga Microsoft-konto och använd sedan din mobila enhet för att kontrollera att det är du med hjälp av ditt fingeravtryck, ansikte eller en PIN-kod. För den här metoden behöver du inte ange ditt lösenord.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Om du vill använda enhetens biometriska funktioner
Om du använder en PIN-kod för att slutföra autentiseringen kan du konfigurera Microsoft Authenticator-appen så att den i stället använder enhetens (biometriska) funktioner för fingeravtryck eller ansiktsigenkänning. Du kan konfigurera detta den första gången du använder Authenticator-appen för att verifiera ditt konto. Det gör du genom att välja alternativet för att använda enhetens biometriska enhetsfunktioner i stället för PIN-koden.

## <a name="who-decides-if-you-use-this-feature"></a>Vem bestämmer om du använder den här funktionen?
Beroende på din typ av konto kan organisationen välja att du måste använda tvåfaktorsverifiering, eller så kanske du kan välja själv.

- **Arbets- eller skolkonto.** Om du använder ett arbets- eller skolkonto (till exempel alain@contoso.com) är det upp till din organisation om du måste använda tvåfaktorsverifiering tillsammans med de specifika verifieringsmetoderna. Läs mer om multifaktorverifiering i [Vad betyder Azure Multi-Factor Authentication för mig?](multi-factor-authentication-end-user.md). Mer information om hur du ställer in säkerhetsinformation för att använda Microsoft Authentication-appen finns i [Konfigurera säkerhetsinformation för att använda en autentiseringsapp (förhandsversion)](security-info-setup-auth-app.md).

- **Personligt Microsoft-konto.** Du kan välja att ställa in tvåfaktorsverifiering för dina personliga Microsoft-konton (till exempel alain@outlook.com).

- **Personligt icke-Microsoft-konto.** Du kan välja att ställa in tvåfaktorsverifiering för dina personliga icke-Microsoft-konton (till exempel alain@gmail.com). Icke-Microsoft-konton kan inte använda termen tvåfaktorsverifiering, men du ska kunna hitta funktionen bland inställningarna för **säkerhet** eller **inloggning**.

## <a name="in-this-section"></a>I det här avsnittet

|Artikel |Beskrivning |
|------|------------|
|[Ladda ned och installera appen](microsoft-authenticator-app-how-to.md)|Beskriver var och hur du skaffar samt installerar Microsoft Authenticator-appen för enheter som kör Android, iOS och Windows Phone.|
|[Lägga till arbets- eller skolkonton](microsoft-authenticator-app-add-work-account.md)|Beskriver hur du lägger till olika arbets- eller skolkonton till Microsoft Authenticator-appen.|
|[Lägga till dina personliga konton](microsoft-authenticator-app-add-personal-account.md)|Beskriver hur du lägger till olika personliga Microsoft-konton och icke-Microsoft-konton till Microsoft Authenticator-appen.|
|[Lägga till konton manuellt](microsoft-authenticator-app-add-account-manual.md)|Beskriver hur du manuellt lägger till ditt konto i Microsoft Authenticator-appen om du inte kan skanna QR-koden som tillhandahålls.|
|[Logga in med appen](microsoft-authenticator-app-phone-signin-faq.md)|Beskriver hur du loggar in på de olika kontona med Microsoft Authenticator-appen.|
|[Säkerhetskopiera och återställa kontoautentiseringsuppgifter](microsoft-authenticator-app-backup-and-recovery.md)| Innehåller information om hur du säkerhetskopierar och återställer autentiseringsuppgifterna för ditt konto med hjälp av Microsoft Authenticator-appen.|
|[Vanliga frågor och svar om Microsoft Authenticator-appen](microsoft-authenticator-app-faq.md)|Innehåller svar på vanliga frågor om appen.|
