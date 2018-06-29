---
title: Microsoft Authenticator phone inloggning - Azure och Microsoft-konton | Microsoft Docs
description: Använda telefonen för att logga in på ditt Microsoft-konto i stället för att ange lösenordet. Den här artikeln innehåller svar på vanliga frågor och svar om den här funktionen.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: ''
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/12/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 063a97fc8f5b0746517919c73094525942418719
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102675"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Logga in med din telefon inte ditt lösenord

Microsoft Authenticator-appen hjälper dig att skydda dina konton genom att utföra tvåstegsverifiering när du anger ditt lösenord. Men visste du att den kan ersätta helt lösenordet för ditt personliga Microsoft-konto?

Den här funktionen är tillgänglig på iOS och Android-enheter och fungerar med personliga Microsoft-konton.

## <a name="how-it-works"></a>Hur det fungerar

Många av du använda Microsoft Authenticator-appen för tvåstegsverifiering när du loggar in på ditt Microsoft-konto. Du skriver lösenordet och sedan gå till app att godkänna ett meddelande eller få en Verifieringskod. Med phone inloggning, hoppa över lösenordet och göra din identitetsverifiering på din telefon. Eftersom är en typ av tvåstegsverifiering telefon-inloggning kan behöver du fortfarande ange något du känner till och en sak som du behöver verifiera din identitet. Telefonen är fortfarande det som du har och din telefon PIN-kod eller biometriska nyckeln är det som du känner till.

## <a name="how-to-get-started"></a>Så här kommer du igång

Följ dessa steg om du vill logga in på ditt personliga Microsoft-konto med din telefon:

1. Aktivera phone inloggningsnamn för ditt konto.

  - Om du inte har Microsoft Authenticator-appen ännu, installerar och lägger till ditt personliga Microsoft-konto enligt stegen på den [Microsoft Authenticator sidan](../../../../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md). Konton som nyligen tillagda aktiveras automatiskt så att du är redo att börja.

  - Om du redan använder Microsoft Authenticator för tvåstegsverifiering, Välj ditt konto från startsidan app och markera **aktivera inloggning phone** från den nedrullningsbara menyn.

  >[!NOTE]
  >Vi behöver för att skydda ditt konto, PIN-kod eller biometriska lås på enheten. Om du behåller din telefon låsas upp öppnas appen en begäran som ber dig att konfigurera ett lås innan du aktiverar inloggning telefon.

3. De flesta sidor där du normalt anger lösenordet för ditt Microsoft-konto har en länk som säger **använda en app i stället**. Välj den här länken för att logga in med din telefon.

4. Microsoft skickar ett meddelande till din telefon. Godkänna meddelandet för att logga in på ditt konto.   

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>Hur loggar in med min telefon säkrare än att skriva ett lösenord?  

Idag de flesta användare logga in på webbplatser eller appar som använder ett användarnamn och lösenord.  Tyvärr är lösenord ofta förlorade, stulna eller gissa för hackare. När du konfigurerar Microsoft Authenticator-appen att logga in skapa vi en nyckel på telefonen som kan låsa upp ditt konto. Vi skydda den här nyckeln med en PIN- eller biometriska att du redan använder på din telefon.  När du loggar in med din telefon används den här nyckeln för att styrka din identitet på ett säkert sätt med två faktorer – telefon sig själv och möjligheten att låsa upp den. 

Den nyckel som används liknar de nycklar som används i Windows Hello och FIDO Alliance UAF specifikationer. Din bio data är bara används för att skydda nyckeln lokalt, och aldrig skickas till, eller lagras i molnet. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Var kan jag använda min telefon ersätta mitt lösenord och där behöver jag fortfarande lösenordet?  

Idag är fungerar funktionen för phone bara med webbprogram och tjänster som tillhandahålls av personliga Microsoft-konton, iOS eller Android-appar som använder ett personligt microsoftkonto och appar på Windows 10 som använder ett personligt microsoftkonto. När du loggar in på en av dessa webbplatser eller program på sidan där du vanligtvis ange lösenordet för det finns en länk som säger **använda en app i stället**. 

Inloggning telefon kan inte användas för att låsa upp en Windows-dator, XBOX eller fjärrskrivbord versioner av Microsoft-appar, till exempel Office-appar just nu.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>Ersätter tvåstegsverifiering? Bör jag inaktivera den?   

Ibland. Vi arbetar på expanderande omfånget för inloggning telefon, men nu finns fortfarande platser i Microsoft-ekosystemet som inte stöder den. På dessa platser använder vi fortfarande tvåstegsverifiering för säker inloggning. Därför är Nej, bör du inaktivera tvåstegsverifiering för ditt konto.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>OK, om jag behåller tvåstegsverifiering aktiverat för mitt konto behöver jag godkänner två meddelanden?

Nej, inte. Logga in på ditt Microsoft-konto med telefonen räknas som tvåstegsverifiering. I stället för att skriva lösenordet, och sedan godkänna ett meddelande styrka din identitet genom att veta hur du låser upp din telefon och sedan godkänna ett meddelande. Vi kommer inte att skicka en andra avisering att godkänna.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>Vad händer om jag tappar bort min telefon eller inte har med mig, hur kan jag använda mitt konto?  

Du kan alltid Klicka **i stället använda ett lösenord** på inloggningssidan byta tillbaka till ditt lösenord. Tänk på att om du använder tvåstegsverifiering kan du fortfarande behöver en annan metod för att verifiera din inloggning. Det är därför starkt bör du se till att du har extra, uppdaterad säkerhetsinformation på ditt konto. Du kan hantera din säkerhetsinformation på https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Hur gör sluta använda den här funktionen och gå tillbaka till att ange lösenordet?

Klicka på **i stället använda ett lösenord** när du loggar in. Vi Kom ihåg din senaste valet och ger som standard nästa gång du loggar in. Om du vill gå tillbaka till logga in med din telefon **använda en app i stället**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Kan jag använda appen för att logga in på alla konton med Microsoft?   
Den här funktionen är endast tillgängligt för personliga Microsoft-konton. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>Kan jag logga in på datorn med min telefon?  
För din dator bör du logga in med Windows Hello på Windows 10 använda din yta, fingeravtryck eller en PIN-kod.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>Kan jag logga in med min Windows Phone?  
För närvarande kan utvecklar vi inte funktionen för Microsoft-Authenticator på Windows Phone. 

## <a name="next-steps"></a>Nästa steg
Om du inte har hämtat Microsoft Authenticator-appen kan checka ut den. Appen är tillgänglig för [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), och är tillgängligt på Microsoft Authenticator-appen för inloggning phone [Android](http://go.microsoft.com/fwlink/?Linkid=825072) och [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Om du har frågor om appen i allmänhet kan ta en titt på den [Microsoft Authenticator vanliga frågor och svar](../../../../multi-factor-authentication/end-user/microsoft-authenticator-app-faq.md)
