---
title: Ladda ned och installera Microsoft Authenticator-appen - Azure AD
description: Ladda ned och installera Microsoft Authenticator-appen för att verifiera din identitet när du använder tvåfaktorsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 9d8ea02c91dc6aec41a1b548eff148e85111fc32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063908"
---
# <a name="download-and-install-the-microsoft-authenticator-app"></a>Ladda ned och installera Microsoft Authenticator-appen

>[!Important]
>Innehållet är avsett för användare. Om du är administratör hittar du mer information om hur du konfigurerar och hanterar en Azure Active Directory (Azure AD)-miljö i [dokumentationen om Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

Med Microsoft Authenticator-appen kan du logga in på dina konton om du använder tvåfaktorsverifiering. Med tvåfaktorsverifiering kan du komma åt dina konton säkrare, särskilt när du visar känslig information. Eftersom lösenord kan glömmas, stjälas eller komprometteras är tvåfaktorsverifiering ett ytterligare steg som hjälper dig att skydda ditt konto genom att göra det svårare för andra att logga in.

Du kan använda Microsoft Authenticator-appen på flera sätt, däribland följande:

- **Tvåfaktorsverifiering.** Standardverifieringsmetoden, där en av faktorerna är ditt lösenord. När du har loggat in med ditt användarnamn och lösenord kan du antingen godkänna ett meddelande eller ange en medföljande verifieringskod.

- **Telefon inloggning.** En version av tvåfaktorsverifiering som gör att du kan logga in utan att kräva ett lösenord, med ditt användarnamn och din mobila enhet med ditt fingeravtryck, ansikte eller PIN-kod.

- **Kodgenerering.** Som kodgenerator för alla andra konton som stöder autentiseringsappar.

> [!Important]
> Microsoft Authenticator-appen fungerar med alla konton som använder tvåfaktorsverifiering och stöder de tidsbaserade totp-standarderna (One-time password).
>
> Din organisation kan kräva att du använder en autentiseringsapp för att logga in och komma åt organisationens data och dokument. Även om ditt användarnamn kanske visas i appen är kontot inte konfigurerat så för att fungera som verifieringsmetod förrän du har slutfört registreringsprocessen. Mer information finns i [Lägga till ditt arbets- eller skolkonto](user-help-auth-app-add-work-school-account.md).
> 
> [!NOTE]
> Om du har problem med att logga in på ditt konto kan du läsa [When you can't sign in to your Microsoft account](https://support.microsoft.com/help/12429) (När du inte kan logga in på ditt Microsoft-konto) om du behöver hjälp.  Få mer information om vad du gör när du får meddelandet [”Det Microsoft-kontot finns inte”](https://support.microsoft.com/help/13811) när du försöker logga in på ditt Microsoft-konto.

## <a name="download-and-install-the-app"></a>Ladda ned och installera appen

Installera den senaste versionen av Microsoft Authenticator-appen, baserat på ditt operativsystem:

- **Google Android.** På din Android-enhet går du till Google Play för att [ladda ned och installera Microsoft Authenticator-appen](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator).

- **Apple iOS.** På din Apple iOS-enhet går du till App Store för att [hämta och installera Microsoft Authenticator-appen](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458).

>[!Important]
>Om du inte är på din mobila enhet kan du fortfarande hämta Microsoft Authenticator-appen genom att skicka en nedladdningslänk från [sidan Microsoft Authenticator.](https://www.microsoft.com/en-us/account/authenticator)

## <a name="next-steps"></a>Nästa steg

När du har hämtat och installerat appen måste du lägga till dina olika konton. Mer information finns i:

- **Appen Autentiserare.** Ladda ned och använd en autentiseringsapp för att få antingen ett godkännandemeddelande eller en slumpmässigt genererad godkännandekod för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du konfigurerar och använder Microsoft Authenticator-appen finns i [Konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md).

- **Text för mobila enheter.** Ange ditt mobilenhetsnummer och få en text som du använder för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med ett SMS finns i [Konfigurera säkerhetsinformation för att använda SMS](security-info-setup-text-msg.md).

- **Mobil enhet eller arbetssamtal.** Ange ditt mobilenhetsnummer och få ett telefonsamtal för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [Konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **Säkerhetsnyckel.** Registrera din Microsoft-kompatibla säkerhetsnyckel och använd den tillsammans med en PIN-kod för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med en säkerhetsnyckel finns i [Konfigurera säkerhetsinformation för att använda en säkerhetsnyckel](security-info-setup-security-key.md).

- **E-postadress.** Ange din e-postadress för arbete eller skola för att få ett e-postmeddelande om återställning av lösenord. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar din e-post finns i [Konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).

- **Säkerhetsfrågor.** Svara på några säkerhetsfrågor som skapats av administratören för din organisation. Det här alternativet är endast tillgängligt för återställning av lösenord och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in dina säkerhetsfrågor finns i artikeln [Konfigurera säkerhetsinformation för att använda säkerhetsfrågor.](security-info-setup-questions.md)
