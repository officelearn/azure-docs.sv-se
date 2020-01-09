---
title: Registrera autentiseringsinformation för att återställa ditt eget lösen ord – Azure AD
description: Registrera din verifierings metod information för återställning av lösen ord i Azure AD, så att du kan återställa ditt eget lösen ord utan Administratörs hjälp.
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
ms.openlocfilehash: a6dddd13f31366e5a99d6c68ab82b048de4064b2
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681369"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registrera din verifierings metod information för att återställa ditt eget lösen ord

> [!IMPORTANT]
> Är du här eftersom du inte kan logga in? I så fall, se [återställa lösen ordet för ditt arbets-eller skol](active-directory-passwords-update-your-own-password.md)konto.

Som slutanvändare kan du återställa ditt lösen ord eller låsa upp ditt konto om du använder Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord (SSPR). Innan du kan använda den här funktionen måste du registrera dina verifierings metoder eller bekräfta de fördefinierade verifierings metoderna som administratören har fyllt i.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrera eller bekräfta autentiseringsdata med SSPR

1. Öppna webbläsaren på din enhet och gå till [registrerings sidan för lösen ords återställning](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).
2. Ange ditt användar namn och lösen ordet som administratören har angett.
3. Beroende på hur din IT-personal har konfigurerat saker är ett eller flera av följande alternativ tillgängliga för dig att konfigurera och verifiera. Om din administratör har behörighet att använda din information kan de fylla i en del av informationen.
    * **Arbets telefon**: endast administratören kan ställa in det här alternativet.
    * **Telefon för autentisering**: Ange det här alternativet till ett annat telefonnummer som du har åtkomst till. Ett exempel är en mobil telefon som kan ta emot en text eller ett samtal.
    * **E-postautentisering**: Ange det här alternativet till en alternativ e-postadress som du kan komma åt utan att använda lösen ordet som du vill återställa.
    * **Säkerhets frågor**: administratören har godkänt den här listan med frågor som du kan besvara. Du kan inte använda samma fråga eller svara mer än en gång.
4. Ange och verifiera den information som administratören behöver. Om fler än ett alternativ är tillgängligt rekommenderar vi att du registrerar flera metoder. Detta ger dig flexibilitet när någon av metoderna inte är tillgänglig. Ett exempel är när du reser och du inte har åtkomst till din arbets telefon.

    ![Registrera verifieringsmetoder och välj Slutför][Register]

5. Välj **Slutför**. Du kan nu använda SSPR när du behöver i framtiden.

Om du anger data för **autentisering via telefon** eller **e-postautentisering**är det inte synligt i den globala katalogen. Det är endast du och dina administratörer som kan se dessa data. Du kan bara se svaren på dina säkerhets frågor.

Dina administratörer kan kräva att du bekräftar dina verifierings metoder efter en viss tids period för att se till att du fortfarande har rätt registrerade metoder.

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

 Här följer några vanliga fel och deras lösningar:

| Fel fall| Vilket fel ser du?| Lösning |
| --- | --- | --- |
| Jag får sidan Kontakta administratören när du har angett mitt användar-ID | Kontakta din administratör. <br> <br> Vi har upptäckt att lösen ordet för ditt användar konto inte hanteras av Microsoft. Därför kan vi inte automatiskt återställa ditt lösen ord. <br> <br> Kontakta IT-personalen om du vill ha mer hjälp. | Du ser det här meddelandet eftersom IT-personalen hanterar ditt lösen ord i din lokala miljö och inte tillåter att du återställer ditt lösen ord från länken **kan inte komma åt ditt konto** . <br> <br> Om du vill återställa ditt lösen ord kontaktar du IT-personalen direkt för hjälp. Berätta för dem att du vill återställa lösen ordet så att du kan aktivera den här funktionen.|
| Jag får fel meddelandet "ditt konto har inte Aktiver ATS för lösen ords återställning" när du har angett mitt användar-ID | Ditt konto har inte Aktiver ATS för lösen ords återställning. <br> <br> Din IT-personal har tyvärr inte konfigurerat ditt konto för användning med den här tjänsten. <br> <br> Om du vill kan vi kontakta en administratör i din organisation för att återställa lösen ordet åt dig. | Du ser det här meddelandet eftersom IT-personalen inte har aktiverat lösen ords återställning för din organisation från det **inte går att komma åt din konto** länk eller inte har licens för att använda funktionen. <br> <br> Om du vill återställa lösen ordet väljer du länken **kontakta en administratör** . Ett e-postmeddelande skickas till företagets IT-personal. Med e-postmeddelandet vet du att du vill återställa lösen ordet så att du kan aktivera den här funktionen. |
| Jag får ett fel meddelande om att vi inte kunde verifiera ditt konto när jag har angett mitt användar-ID | Vi kunde inte verifiera ditt konto. <br> <br> Om du vill kan vi kontakta en administratör i din organisation för att återställa lösen ordet åt dig. | Du ser det här meddelandet eftersom du är aktive rad för återställning av lösen ord, men du inte har registrerat dig för att använda tjänsten. Om du vill registrera dig för lösen ords återställning går du till [registrerings sidan för lösen ords återställning](https://aka.ms/ssprsetup) när du har fått åtkomst till ditt konto igen. <br> <br> Om du vill återställa lösen ordet väljer du länken **kontakta en administratör** för att skicka ett e-postmeddelande till företagets IT-personal. |

## <a name="next-steps"></a>Nästa steg

* [Ändra lösen ordet med hjälp av lösen ords återställning via självbetjäning](active-directory-passwords-update-your-own-password.md)
* [Registreringssida för lösenordsåterställning](https://aka.ms/ssprsetup)
* [Portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)
* [När du inte kan logga in på Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Registrerings sidan för lösen ords återställning visar registrerade metoder och slutför-knappen"

