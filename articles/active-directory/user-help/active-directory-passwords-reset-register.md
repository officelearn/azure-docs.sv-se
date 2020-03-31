---
title: Registrera autentiseringsinformation för att återställa ditt eget lösenord - Azure AD
description: Registrera information om verifieringsmetoden för azure ad-självbetjäningslösenordsåterställning, så att du kan återställa ditt eget lösenord utan administratörshjälp.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 397e1fd7695fd7e74e1f22959d0f9f24af7d1ea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062650"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registrera information om verifieringsmetoden för att återställa ditt eget lösenord

Om du har glömt ditt arbets- eller skollösenord, aldrig fått något lösenord från din organisation eller har låsts ute från ditt konto kan du använda din säkerhetsinformation och din mobila enhet för att återställa ditt arbets- eller skollösenord.

Administratören måste aktivera den här funktionen för att du ska kunna registrera din information och återställa ditt eget lösenord. Om alternativet **Glömt mitt lösenord** inte visas betyder det att administratören inte har aktiverat funktionen för din organisation. Om du anser att detta är felaktigt kontaktar du din helpdesk för att få hjälp.

>[!Important]
>Den här artikeln är avsedd för användare som försöker använda registrera dig för återställning av lösenord med självbetjäning. Det innebär att du kan återställa ditt eget arbets- eller alain@contoso.comskollösenord (till exempel ) utan att administratören behöver hjälp. Om du är administratör och letar efter information om hur du aktiverar återställning av lösenord för självbetjäning för anställda eller andra användare läser [du återställningen av självbetjäningslösenordet i Azure AD och andra artiklar](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="set-up-your-password-reset-verification-method"></a>Konfigurera verifieringsmetoden för återställning av lösenord

1. Öppna webbläsaren på enheten och gå till [sidan säkerhetsinformation](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. Beroende på hur administratören har konfigurerat din organisation är ett eller flera av följande alternativ tillgängliga för dig att konfigurera som din säkerhetsverifieringsmetod. Om flera alternativ är tillgängliga rekommenderar vi starkt att du använder mer än ett som din säkerhetsverifieringsmetod, om någon av dina metoder blir otillgänglig.

    - **Autentiseringsapp.** Välj att använda Microsoft Authenticator-appen eller annan autentiseringsapp som säkerhetsverifieringsmetod. Mer information om hur du konfigurerar appen finns i [Konfigurera Microsoft Authenticator-appen som verifieringsmetod](security-info-setup-auth-app.md).

    - **Sms.** Välj att skicka textmeddelanden till din mobila enhet. Mer information om hur du konfigurerar textmeddelanden finns i [Konfigurera textmeddelanden som verifieringsmetod](security-info-setup-text-msg.md).

    - **Telefonsamtal.** Välj att få ett telefonsamtal till ditt registrerade telefonnummer. Mer information om hur du konfigurerar telefonsamtal finns i [Konfigurera ett telefonnummer som verifieringsmetod](security-info-setup-phone-number.md).

    - **Säkerhetsnyckel.** Välj att använda en Microsoft-kompatibel säkerhetsnyckel. Mer information finns i [Konfigurera en säkerhetsnyckel som verifieringsmetod](security-info-setup-security-key.md).

    - **E-postadress.** Välj att använda en alternativ e-postadress som kan användas utan att kräva ditt glömda eller saknade lösenord. Detta fungerar bara för återställning av lösenord, inte som en säkerhetsverifieringsmetod. Mer information om hur du konfigurerar en e-postadress finns i [Konfigurera en e-postadress som verifieringsmetod](security-info-setup-email.md).

    - **Säkerhetsfrågor.** Välj att ställa in och svara på fördefinierade säkerhetsfrågor som konfigurerats av administratören. Detta fungerar bara för återställning av lösenord, inte som en säkerhetsverifieringsmetod. Mer information om säkerhetsfrågor finns i [Konfigurera säkerhetsfrågor som verifieringsmetod](security-info-setup-questions.md).

3. När du har valt och ställt in metoderna väljer du **Slutför** för att slutföra processen.

    > [!Note]
    > Information som läggs till för ditt telefonnummer eller din e-postadress delas inte med organisationens globala katalog. De enda som kan se den här informationen är du och din administratör. Det är bara du som kan se svaren på dina säkerhetsfrågor.

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

 Här är några vanliga felfall och deras lösningar:

| Felmeddelande |  Möjlig lösning |
| --- | --- | --- |
| Kontakta din administratör.<br>Vi har upptäckt att lösenordet för ditt användarkonto inte hanteras av Microsoft. Därför kan vi inte automatiskt återställa ditt lösenord.<br>Kontakta din IT-personal för ytterligare hjälp.| Om du får det här felmeddelandet när du har skrivit ditt användar-ID betyder det att din organisation internt hanterar ditt lösenord och inte vill att du ska återställa lösenordet från länken **Kan inte komma åt ditt konto.** Om du vill återställa lösenordet i den här situationen måste du kontakta organisationens support eller administratör för att få hjälp. |
| Ditt konto är inte aktiverat för återställning av lösenord.<br>Vi beklagar, men din IT-personal har inte konfigurerat ditt konto för användning med den här tjänsten.<br>Om du vill kan vi kontakta en administratör i organisationen för att återställa lösenordet åt dig. | Om du får det här felmeddelandet när du har skrivit ditt användar-ID betyder det att antingen har organisationen inte aktiverat funktionen för återställning av lösenord eller så får du inte använda det. Om du vill återställa lösenordet i den här situationen måste du välja länken **Kontakta en administratör.** När du har klickat på länken skickas ett e-postmeddelande till organisationens support eller administratör så att de vet att du vill återställa lösenordet. |
| Vi kunde inte verifiera ditt konto.<br>Om du vill kan vi kontakta en administratör i organisationen för att återställa lösenordet åt dig. | Om du får det här felmeddelandet när du har skrivit ditt användar-ID betyder det att din organisation har aktiverat återställning av lösenord och att du kan använda det, men att du inte har registrerat dig för tjänsten. I det här fallet måste du kontakta organisationens support eller administratör för att återställa lösenordet. Information om hur du registrerar dig för återställning av lösenord när du är tillbaka på enheten finns i processen ovan i den här artikeln. |

## <a name="next-steps"></a>Nästa steg

- [Ändra ditt lösenord med hjälp av återställning av lösenord för självbetjäning](active-directory-passwords-update-your-own-password.md)

- [Sida för säkerhetsinformation](https://mysignins.microsoft.com/security-info)

- [Portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)

- [När du inte kan logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
