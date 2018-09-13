---
title: Microsoft Authenticator telefoninloggning – Azure och Microsoft-konton | Microsoft Docs
description: Använd din telefon för att logga in på ditt Microsoft-konto i stället för att skriva ditt lösenord. Den här artikeln får du svar på vanliga frågor och svar om den här funktionen.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directoary
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 08/12/2017
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 3536c5da7833c32b583f1a510be43864c9107068
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717054"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Logga in med telefonen, inte med ditt lösenord
Microsoft Authenticator-appen hjälper dig att skydda dina konton genom att utföra tvåstegsverifiering när du har angett ditt lösenord. Men visste du att den helt kan ersätta lösenordet för ditt personliga Microsoft-konto?

Den här funktionen är tillgänglig för iOS och Android-enheter och fungerar med personliga Microsoft-konton.
 
## <a name="how-it-works"></a>Hur det fungerar
Många av er Använd Microsoft Authenticator-appen för tvåstegsverifiering när du loggar in på ditt Microsoft-konto. Du skriver lösenordet och sedan gå till appen att godkänna ett meddelande eller hämta en Verifieringskod. Med telefoninloggning, hoppa över lösenordet och göra allt ditt identitetsverifiering på din telefon. Eftersom telefoninloggning är en typ av tvåstegsverifiering, behöver du fortfarande ange en sak som du vet och en sak som du behöver verifiera din identitet. Telefonens PIN-kod eller biometriska nyckeln är det som du vet telefonen fortfarande är det som du har.

## <a name="how-to-get-started"></a>Så här kommer du igång
Följ dessa steg för att logga in på ditt personliga Microsoft-konto med din telefon:

1. Aktivera telefoninloggning för ditt konto.

    - Om du inte har Microsoft Authenticator-appen ännu, installerar och lägger till ditt personliga Microsoft-konto enligt stegen på den [Microsoft Authenticator sidan](microsoft-authenticator-app-how-to.md). Nyligen tillagda konton aktiveras automatiskt så att du är redo att börja.

    - Om du redan använder Microsoft Authenticator för tvåstegsverifiering, Välj ditt konto på startsidan för appen och välj **aktivera telefoninloggning** från den nedrullningsbara menyn.

    >[!NOTE]
    >För att skydda ditt konto kan kräva vi PIN-kod eller biometriska lås på din enhet. Om du behåller din telefon låsas upp öppnas appen en begäran som ber dig att ställa in ett lås innan du aktiverar inloggning via mobilen.

2. De flesta sidor där du normalt anger lösenordet för ditt Microsoft-konto har en länk där det står **använder en app i stället**. Välj den här länken för att logga in med din telefon.
 
3. Microsoft skickar ett meddelande till din telefon. Godkänn meddelandet att logga in på ditt konto.   
 
## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>Hur loggar in med telefonen säkrare än att skriva ett lösenord?  
Idag de flesta användare logga in på webbplatser och appar med ett användarnamn och lösenord.  Tyvärr är lösenord ofta tappas bort, blir stulen eller gissa som hackare använder. När du ställer in Microsoft Authenticator-appen för att logga in, skapa vi en nyckel på din telefon som kan låsa upp ditt konto. Vi skydda den här nyckeln med PIN-kod eller biometriska som du redan använder på din telefon.  När du loggar in med din telefon, används den här nyckeln för att verifiera din identitet på ett säkert sätt med två faktorer – telefonen själva och förmågan att låsa upp den.
 
Den nyckel som används liknar de nycklar som används i Windows Hello och FIDO Alliance UAF-specifikationer. Din biografi data är endast används för att skydda nyckeln lokalt, och skickas till, eller lagras aldrig i molnet. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Var kan jag använda min telefon för att ersätta lösenord, och där behöver jag fortfarande lösenordet?  
Idag, fungerar funktionen för phone bara med webbappar och tjänster som drivs av personliga Microsoft-konton, iOS eller Android-appar som använder ett personligt microsoftkonto och appar i Windows 10 som använder ett personligt microsoftkonto. När du loggar in till någon av dessa webbplatser och appar på sidan där du vanligtvis ange lösenordet för det finns en länk med texten **använder en app i stället**. 

Telefoninloggning kan inte användas för att låsa upp en Windows-dator, XBOX eller skrivbord versioner av Microsoft-appar som Office-appar just nu.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>Ersätter tvåstegsverifiering? Bör jag stänga av den?   
Ibland. Vi arbetar på att utöka omfånget för telefoninloggning, men nu det fortfarande finns platser i Microsofts ekosystem som inte stöder den. På dessa platser använder vi fortfarande tvåstegsverifiering för säker inloggning. Därför finns inte, bör inte du inaktivera tvåstegsverifiering för ditt konto.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>OK, jag kan spara tvåstegsverifiering aktiverad för mitt konto, behöver jag godkänna två meddelanden?
Nej, du kommer inte. Logga in på ditt Microsoft-konto med din telefon räknas som tvåstegsverifiering. I stället för att skriva ditt lösenord, och sedan godkänna ett meddelande du verifiera din identitet genom att känna till hur du låser upp din telefon och sedan godkänna ett meddelande. Vi kommer inte att skicka ett meddelande som andra för att godkänna.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>Vad händer om jag tappar bort min telefon eller inte har den med mig, hur kan jag öppna mitt konto?  
Du kan alltid Klicka **använder ett lösenord i stället** på sidan logga in för att växla tillbaka till ditt lösenord. Tänk på att om du använder tvåstegsverifiering kan du fortfarande behöver ett annat sätt att verifiera din inloggning. Därför rekommenderar vi starkt att du kan se till att du har extra, uppdaterad säkerhetsinformation på ditt konto. Du kan hantera din säkerhetsinformation på https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Hur gör jag sluta använda den här funktionen och gå tillbaka till att ange lösenordet?
Klicka på **använder ett lösenord i stället** när du loggar in. Vi kommer ihåg ditt senaste val och erbjuder som standard nästa gång du loggar in. Om du vill gå tillbaka till att logga in med din telefon, klickar du på **använder en app i stället**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Kan jag använda appen för att logga in på alla konton med Microsoft?   
Den här funktionen är endast tillgänglig för personliga Microsoft-konton. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>Kan jag logga in på datorn med min telefon?  
För din dator rekommenderar vi logga in med Windows Hello i Windows 10 med ansiktet, fingeravtryck eller en PIN-kod.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>Kan jag logga in med min Windows Phone?  
För närvarande kan utvecklar vi inte den här funktionen för Microsoft Authenticator på Windows Phone. 

## <a name="next-steps"></a>Nästa steg
Om du inte har hämtat Microsoft Authenticator-appen, checka ut den. Appen är tillgänglig för [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), och är tillgängligt på Microsoft Authenticator-appen för telefoninloggning [Android](http://go.microsoft.com/fwlink/?Linkid=825072) och [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Om du har frågor om appen i allmänhet kan ta en titt på de [Microsoft Authenticator vanliga frågor och svar](microsoft-authenticator-app-faq.md)