---
title: Registrera dig för lösenordsåterställning via självbetjäning – Azure Active Directory | Microsoft Docs
description: Registrera autentiseringsdata för Azure AD-självbetjäning lösenord återställa
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: f113c42ff45811f31eb77a92873fb187dd51ea6b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56184320"
---
# <a name="register-for-self-service-password-reset"></a>Registrera för återställning av lösenord med självbetjäning

> [!IMPORTANT]
> Är du här eftersom du inte logga in? I så, fall Se [återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

Du kan återställa ditt lösenord eller låsa upp kontot själv om du använder Azure Active Directory (Azure AD) för återställning av lösenord via självbetjäning (SSPR) som en slutanvändare. Innan du kan använda den här funktionen måste behöva du registrera dina autentiseringsmetoder eller bekräfta de fördefinierade autentiseringsmetoderna som administratören har fyllt i.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrera eller bekräfta autentiseringsdata med SSPR

1. Öppna webbläsaren på din enhet och gå till den [registreringssidan för lösenordsåterställning](https://aka.ms/ssprsetup).
2. Ange ditt användarnamn och lösenord som din administratör.
3. Beroende på hur din IT-avdelning har konfigurerat saker, är en eller flera av följande alternativ tillgängliga för dig att konfigurera och verifiera. Om din administratör har din behörighet att använda din information, de kan fylla i en del av information för dig.
    * **Arbetstelefon**: Endast administratören kan ange detta alternativ.
    * **Telefon för autentisering**: Ange det här alternativet om du till ett annat telefonnummer som du har åtkomst till. Ett exempel är en mobiltelefon som kan ta emot en text eller ett anrop.
    * **E-post för autentisering**: Ange det här alternativet om du till en alternativ e-postadress som du kan komma åt utan att använda lösenord som du vill återställa.
    * **Säkerhetsfrågor**: Din administratör har godkänt den här listan med frågor att besvara. Du kan inte använda samma fråga eller svara på mer än en gång.
4. Ange och kontrollera informationen som din administratör kräver. Om mer än ett alternativ är tillgängliga, föreslår vi att du registrerar flera metoder. Detta ger dig flexibilitet när någon av metoderna är inte tillgänglig. Ett exempel är när du reser och det inte går att komma åt din Arbetstelefon.

    ![Registrera autentiseringsmetoder och välj Slutför][Register]

5. Välj **Slutför**. Du kan nu använda SSPR när du behöver i framtiden.

Om du anger data för **Autentiseringstelefon** eller **e-post för autentisering**, den syns inte i den globala katalogen. Det är endast du och dina administratörer som kan se dessa data. Du kan bara se svaren på dina säkerhetsfrågor.

Dina administratörer kräver att du bekräftar dina autentiseringsmetoder efter en tid för att kontrollera att du fortfarande har registrerat lämpliga metoder.

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

 Här följer några vanliga fel och sina lösningar:

| Felsituation| Vilka fel ser du?| Lösning |
| --- | --- | --- |
| Jag får en ”Kontakta administratören”-sida när du har angett mitt användar-ID | Kontakta administratören. <br> <br> Vi har upptäckt att lösenordet till ditt användarkonto inte hanteras av Microsoft. Vi kan därför inte att automatiskt återställa ditt lösenord. <br> <br> Kontakta din IT-personal behöver mer hjälp. | Du får det här meddelandet eftersom din IT-personal hanterar ditt lösenord i din lokala miljö och tillåter inte att återställa ditt lösenord från den **kan inte komma åt ditt konto** länk. <br> <br> Om du vill återställa ditt lösenord, kontakta din IT-personal direkt om du behöver hjälp. Informera dem om du vill återställa lösenordet så att de kan aktivera den här funktionen för dig.|
| Jag får felet ”ditt konto har inte aktiverats för återställning av lösenord” när du har angett mitt användar-ID | Ditt konto har inte aktiverats för återställning av lösenord. <br> <br> Vi beklagar, men IT-avdelningen har inte konfigurerat ditt konto för användning med den här tjänsten. <br> <br> Om du vill kan kontakta vi en administratör i din organisation som återställer lösenordet åt dig. | Du får det här meddelandet eftersom din IT-avdelningen inte har aktiverat återställning av lösenord för din organisation från den **kan inte komma åt ditt konto** länkar eller har inte licensierats du om du vill använda funktionen. <br> <br> Om du vill återställa ditt lösenord, Välj den **kontakta en administratör** länk. Ett e-postmeddelande skickas till ditt företags IT-personal. E-postmeddelandet informerar dem om du vill återställa ditt lösenord så att de kan aktivera den här funktionen för dig. |
| Jag får felet ”Det gick inte att verifiera ditt konto” när du har angett mitt användar-ID | Det gick inte att verifiera ditt konto. <br> <br> Om du vill kan kontakta vi en administratör i din organisation som återställer lösenordet åt dig. | Du får det här meddelandet eftersom du har aktiverats för återställning av lösenord, men du har inte registrerats för användning av tjänsten. Registrera för återställning av lösenord genom att gå till den [registreringssidan för lösenordsåterställning](https://aka.ms/ssprsetup) när du har återupprättats åtkomst till ditt konto. <br> <br> Om du vill återställa ditt lösenord, Välj den **kontakta en administratör** länk för att skicka ett e-postmeddelande till ditt företags IT-personal. |

## <a name="next-steps"></a>Nästa steg

* [Ändra ditt lösenord genom att använda lösenordsåterställning via självbetjäning](active-directory-passwords-update-your-own-password.md)
* [Registreringssida för lösenordsåterställning](https://aka.ms/ssprsetup)
* [Portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)
* [När du inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Lösenord: registrering för lösenordsåterställning sidan som visar registrerade metoder och knappen Slutför"

