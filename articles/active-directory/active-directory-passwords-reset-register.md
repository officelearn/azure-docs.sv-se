---
title: "Registrera dig för lösenordsåterställning via självbetjäning - Azure Active Directory"
description: "Registrera autentiseringsdata för Azure AD-lösenordet för självbetjäning återställa"
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: barlan
ms.custom: end-user;seohack1
ms.openlocfilehash: dbb82e125381a4307ed742294f880600612ffc52
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="register-for-self-service-password-reset"></a>Registrera för återställning av lösenord med självbetjäning

> [!IMPORTANT]
> Är du här eftersom du inte logga in? I så fall, se [återställa ditt lösenord för arbetet eller skolan](active-directory-passwords-update-your-own-password.md).

Du kan återställa ditt lösenord eller låsa upp kontot själv om du använder Azure Active Directory (AD Azure) lösenordsåterställning via självbetjäning (SSPR) som en slutanvändare. Innan du kan använda den här funktionen, måste du registrera din autentiseringsmetoder eller bekräfta fördefinierade autentiseringsmetoder som administratören har fyllts i.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrera eller bekräfta autentiseringsdata med SSPR

1. Öppna webbläsaren på din enhet och gå till den [registreringssidan för lösenordsåterställning](https://aka.ms/ssprsetup).
2. Ange ditt användarnamn och lösenord som angetts av administratören.
3. Beroende på hur IT-personalen har konfigurerat saker, är en eller flera av följande alternativ tillgängliga för dig att konfigurera och verifiera. Om administratören har din tillåtelse att använda din information, de kan fylla i vissa uppgifter du.
    * **Arbetstelefon**: endast administratören kan ange det här alternativet.
    * **Telefon för autentisering**: Ange ett annat telefonnummer som du har åtkomst till. Ett exempel är en mobiltelefon som kan ta emot en text eller ett anrop.
    * **Autentisering e-post**: ange detta alternativ till en alternativ e-postadress som du kan komma åt utan att använda lösenord som du vill återställa.
    * **Säkerhetsfrågor**: administratören har godkänt den här listan med frågor som du svara. Du kan inte använda samma fråga eller besvara mer än en gång.
4. Ange och kontrollera information som krävs för din administratör. Om mer än ett alternativ är tillgängliga, föreslår vi att du registrerar flera metoder. Detta ger dig flexibilitet när någon av metoderna är inte tillgänglig. Ett exempel är när du reser och det inte går att komma åt din Arbetstelefon.

    ![Registrera autentiseringsmetoder och klicka på Slutför][Register]

5. Välj **Slutför**. Du kan nu använda SSPR när du behöver i framtiden.

Om du anger data för **telefon för autentisering** eller **autentisering e-post**, den visas inte i den globala katalogen. Det är endast du och dina administratörer som kan se dessa data. Du kan bara se svaren på dina säkerhetsfrågor.

Dina administratörer kan kräva att bekräfta din autentiseringsmetoder efter en viss tidsperiod och kontrollera att du fortfarande har lämpliga-metoder som registrerats.

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

 Här följer tillfällen vanliga fel och lösningar:

| Fel-fall| Vilka fel ser du?| Lösning |
| --- | --- | --- |
| En ”Kontakta din administratör” sida kommer när du har angett mitt användar-ID | Kontakta administratören. <br> <br> Vi har upptäckt att lösenordet till ditt användarkonto inte hanteras av Microsoft. Vi kan därför inte att återställa lösenordet automatiskt. <br> <br> Kontakta din IT-personal för att få hjälp. | Du se detta meddelande eftersom din IT-personal hanterar lösenordet i din lokala miljö och tillåter inte att återställa ditt lösenord från den **kan inte komma åt ditt konto** länk. <br> <br> Om du vill återställa ditt lösenord, kontakta din IT-personal direkt. Meddela dem om du vill återställa ditt lösenord så att de kan du aktivera funktionen för dig.|
| Jag felmeddelandet ”ditt konto har inte aktiverats för lösenordsåterställning” när du har angett mitt användar-ID | Ditt konto har inte aktiverats för återställning av lösenord. <br> <br> Vi beklagar, men din IT-personal inte har lagt upp ditt konto för användning med den här tjänsten. <br> <br> Om du vill att kan vi kontakta en administratör i din organisation som återställer lösenordet åt dig. | Du se detta meddelande eftersom din IT-personal inte har aktiverats för återställning av lösenord för din organisation från den **kan inte komma åt ditt konto** länkar eller har inte licensierats du om du vill använda funktionen. <br> <br> Om du vill återställa ditt lösenord, Välj den **kontakta en administratör** länk. Ett e-postmeddelande skickas till ditt företags IT-personal. E-postmeddelandet kan dem om du vill återställa ditt lösenord så att de kan du aktivera funktionen för dig. |
| Jag felmeddelandet ”Det gick inte att verifiera ditt konto” när du har angett mitt användar-ID | Det gick inte att verifiera ditt konto. <br> <br> Om du vill att kan vi kontakta en administratör i din organisation som återställer lösenordet åt dig. | Du se detta meddelande eftersom du har aktiverat för återställning av lösenord, men du har inte registrerats för att använda tjänsten. Om du vill registrera dig för lösenordsåterställning, gå till den [registreringssidan för lösenordsåterställning](https://aka.ms/ssprsetup) när du har återupprättats åtkomst till ditt konto. <br> <br> Om du vill återställa ditt lösenord, Välj den **kontakta en administratör** länken för att skicka ett e-postmeddelande till ditt företags IT-personal. |

## <a name="next-steps"></a>Nästa steg

* [Ändra ditt lösenord med hjälp av lösenordsåterställning via självbetjäning](active-directory-passwords-update-your-own-password.md)
* [Registreringssida för lösenordsåterställning](https://aka.ms/ssprsetup)
* [Portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)
* [När du inte logga in till ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Registrering för lösenordsåterställning lösenordssidan visar de registrerade metoderna och på Slutför"

