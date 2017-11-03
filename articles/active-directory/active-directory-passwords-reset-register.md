---
title: 'Azure AD: SSPR-registrering | Microsoft Docs'
description: "Registrera autentiseringsdata för Azure AD-lösenordet för självbetjäning återställa"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: b884f8bc3a20052fa0cb40772deef591b69d7b10
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="register-for-self-service-password-reset"></a>Registrera för återställning av lösenord med självbetjäning

> [!IMPORTANT]
> **Är du här eftersom du har problem med att logga in?** I så fall är det [här som du ser hur du kan ändra och återställa ditt eget lösenord](active-directory-passwords-update-your-own-password.md).

Du kan återställa ditt lösenord eller låsa upp ditt konto utan att behöva tala med en person som använder lösenordsåterställning via självbetjäning (SSPR) som en slutanvändare. Innan du kan använda den här funktionen, måste du registrera autentiseringsmetoder eller bekräfta de fördefinierade autentiseringsmetoderna som administratören har fyllt i.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrera eller bekräfta autentiseringsdata med SSPR

1. Öppna webbläsaren på din enhet och gå till [registreringssidan för återställning av lösenord](http://aka.ms/ssprsetup)
2. Ange ditt användarnamn och lösenord som du har fått av administratören
3. Beroende på hur din IT-personal konfigurerat saker, är en eller flera av följande alternativ tillgängliga för dig att konfigurera och verifiera. Administratören kan fylla i vissa av dessa du om de har ditt tillstånd att använda informationen.
    * Arbetstelefon kan bara anges av administratören
    * Telefon för autentisering ska anges till ett annat telefonnummer som skulle ha behörighet för att som en mobiltelefon som kan ta emot ett textmeddelande eller samtal.
    * Autentisering e-post ska anges till en alternativ e-postadress som du kan komma åt utan att du behöver återställa lösenordet.
    * Säkerhetsfrågor ger dig en lista med din administratör har godkänt för dig att besvara frågor. Du kan inte använda samma fråga eller besvara mer än en gång.
4. Ange och kontrollera informationen som din administratör begär. Om mer än ett alternativ är tillgängliga, föreslår vi att du registrerar flera metoder för att ge flexibilitet vid en annan metod är inte tillgänglig (exempel: resa och det går inte att komma åt din Arbetstelefon)

    ![Registrera autentiseringsmetoder och klicka på slutför][Register]

5. När du är klar med steg 4 väljer du **Slutför**. Nu kan du använda självbetjäning för återställning av lösenord när du behöver framöver.

Om du anger data i Telefon för autentisering eller E-post för autentisering visas detta inte i den globala katalogen. Det är endast du och dina administratörer som kan se dessa data. Endast du kan se svaren på dina säkerhetsfrågor.

Administratörer kan kräva att du bekräftar dina autentiseringsmetoder efter en viss tid för att se till att du har registrerat lämpliga metoder.

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

 Här följer tillfällen vanliga fel och lösningar:

| Fel-fall| Vilka fel ser du?| Lösning |
| --- | --- | --- |
| En ”Kontakta din administratör” sida kommer när du har angett mitt användar-ID | Kontakta administratören <br> <br> Vi har upptäckt att lösenordet till ditt användarkonto inte hanteras av Microsoft. Vi kan därför inte att återställa lösenordet automatiskt. <br> <br> Du måste kontakta din IT-personal för ytterligare hjälp. | Du ser det här meddelandet eftersom din IT-personal hanterar lösenordet i din lokala miljö och tillåter inte att återställa ditt lösenord från den kan inte komma åt ditt kontolänk. <br> <br> Kontakta din IT-personal direkt för att få hjälp om du vill återställa ditt lösenord och meddela dem om du vill återställa ditt lösenord så att de kan du aktivera funktionen för dig.|
| Jag felmeddelandet ”ditt konto har inte aktiverats för lösenordsåterställning” när du har angett mitt användar-ID | Kontot är inte aktiverat för återställning av lösenord <br> <br> Vi beklagar, men din IT-personal inte har lagt upp ditt konto för användning med den här tjänsten. <br> <br> Om du vill att kan vi kontakta en administratör i din organisation som återställer lösenordet åt dig. | Du ser det här meddelandet eftersom din IT-personal inte har aktiverats för återställning av lösenord för din organisation från den kan inte komma åt ditt kontolänk eller har inte licensierats du om du vill använda funktionen. <br> <br> Om du vill återställa ditt lösenord, klickar du på kontakten en administratör länk för att skicka ett e-postmeddelande till ditt företag har IT-personal och meddela dem om du vill återställa ditt lösenord så att de kan du aktivera funktionen för dig. |
| Jag felmeddelandet ”Det gick inte att verifiera ditt konto” när du har angett mitt användar-ID | Vi kunde inte verifiera ditt konto <br> <br> Om du vill att kan vi kontakta en administratör i din organisation som återställer lösenordet åt dig. | Du ser det här meddelandet eftersom du är aktiverade för återställning av lösenord, men du har inte registrerats för att använda tjänsten. Registrera för återställning av lösenord genom att gå till http://aka.ms/ssprsetup när du har återupprättats åtkomst till ditt konto. <br> <br> Om du vill återställa ditt lösenord, klickar du på kontakten en administratör länk för att skicka ett e-postmeddelande till ditt företags IT-personal. |

## <a name="next-steps"></a>Nästa steg

* [Ändra ditt lösenord med självbetjäning](active-directory-passwords-update-your-own-password.md)
* [Registreringssida för lösenordsåterställning](http://aka.ms/ssprsetup)
* [Portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)
* [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Registreringssidan för lösenordsåterställning visar registrerade metoder och knappen Slutför"
