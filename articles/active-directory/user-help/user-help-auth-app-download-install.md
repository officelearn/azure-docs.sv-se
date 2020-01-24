---
title: Hämta och installera Microsoft Authenticator app – Azure AD
description: Hämta och installera appen Microsoft Authenticator för att verifiera din identitet när du använder tvåstegsverifiering.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: ba6f4d8d7c8bae73d998ee09de3adba7601e25b8
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704722"
---
# <a name="download-and-install-the-microsoft-authenticator-app"></a>Ladda ned och installera appen Microsoft Authenticator

>[!Important]
>Innehållet är avsett för användare. Om du är administratör hittar du mer information om hur du konfigurerar och hanterar en Azure Active Directory (Azure AD)-miljö i [dokumentationen om Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

Med Microsoft Authenticator-appen kan du logga in på dina konton om du använder tvåfaktorsverifiering. Med tvåfaktorsverifiering kan du komma åt dina konton säkrare, särskilt när du visar känslig information. Eftersom lösenord kan glömmas, stjälas eller komprometteras är tvåfaktorsverifiering ett ytterligare steg som hjälper dig att skydda ditt konto genom att göra det svårare för andra att logga in.

Du kan använda Microsoft Authenticator-appen på flera sätt, däribland följande:

- **Tvåstegsverifiering.** Standard verifierings metoden, där en av faktorerna är ditt lösen ord. När du har loggat in med ditt användar namn och lösen ord kan du antingen godkänna ett meddelande eller ange en angiven verifierings kod.

- **Telefonin loggning.** En version av tvåstegsverifiering som du kan använda för att logga in utan att kräva ett lösen ord, med ditt användar namn och din mobila enhet med ditt finger avtryck, ansikte eller PIN-kod.

- **Kodgenerering.** Som kod generator för andra konton som stöder Authenticator-appar.

> [!Important]
> Microsoft Authenticator-appen fungerar med ett konto som använder tvåstegsverifiering och som stöder tidsbaserad eng ång slö sen ord (TOTP mobilapp).
>
> Din organisation kan kräva att du använder en autentiseringsapp för att logga in och komma åt organisationens data och dokument. Även om ditt användarnamn kanske visas i appen är kontot inte konfigurerat så för att fungera som verifieringsmetod förrän du har slutfört registreringsprocessen. Mer information finns i [Lägga till ditt arbets- eller skolkonto](user-help-auth-app-add-work-school-account.md).
> 
> [!NOTE]
> Om du har problem med att logga in på ditt konto kan du läsa [When you can't sign in to your Microsoft account](https://support.microsoft.com/help/12429) (När du inte kan logga in på ditt Microsoft-konto) om du behöver hjälp.  Få mer information om vad du gör när du får meddelandet [”Det Microsoft-kontot finns inte”](https://support.microsoft.com/help/13811) när du försöker logga in på ditt Microsoft-konto.

## <a name="download-and-install-the-app"></a>Ladda ned och installera appen

Installera den senaste versionen av Microsoft Authenticator-appen, baserat på operativ systemet:

- **Google Android.** På din Android-enhet går du till Google Play för att [Ladda ned och installera Microsoft Authenticator-appen](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator).

- **Apple iOS.** På din Apple iOS-enhet går du till App Store för att [Ladda ned och installera Microsoft Authenticator-appen](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458).

>[!Important]
>Om du inte redan har en mobil enhet kan du fortfarande få Microsoft Authenticator-appen genom att skicka en nedladdnings länk från [Microsoft Authenticator sidan](https://www.microsoft.com/en-us/account/authenticator).

## <a name="next-steps"></a>Nästa steg

När du har laddat ned och installerat appen måste du lägga till dina olika konton. Mer information finns här:

- **Authenticator-app.** Hämta och Använd en Authenticator-app för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännande kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner om hur du konfigurerar och använder Microsoft Authenticator-appen finns i [Konfigurera säkerhets information så att en autentiserare används](security-info-setup-auth-app.md).

- **Mobil enhets text.** Ange ditt mobila enhets nummer och få en text kod som du kan använda för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett textmeddelande (SMS) finns i [Konfigurera säkerhets information för att använda SMS (text messaging)](security-info-setup-text-msg.md).

- **Mobil enhets-eller arbets telefon samtal.** Ange ditt mobila enhets nummer och få ett telefonsamtal för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett telefonnummer finns i [Konfigurera säkerhets information så att telefonsamtal används](security-info-setup-phone-number.md).

- **Säkerhets nyckel.** Registrera din Microsoft-kompatibla säkerhets nyckel och Använd den tillsammans med en PIN-kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med en säkerhets nyckel finns i [Konfigurera säkerhets information för att använda en säkerhets nyckel](security-info-setup-security-key.md).

- **E-postadress.** Ange din e-postadress till arbetet eller skolan för att få ett e-postmeddelande om lösen ords återställning. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar din e-post finns i [Konfigurera säkerhets information för att använda e-post](security-info-setup-email.md).

- **Säkerhets frågor.** Svara på vissa säkerhets frågor som har skapats av administratören för din organisation. Det här alternativet är bara tillgängligt för lösen ords återställning och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar dina säkerhets frågor finns i artikeln [Konfigurera säkerhets information för att använda säkerhets frågor](security-info-setup-questions.md) .
