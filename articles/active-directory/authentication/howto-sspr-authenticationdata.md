---
title: Azure AD SSPR-datakrav – Azure Active Directory
description: Datakrav för återställning av lösenord för Azure AD och hur du uppfyller dem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a14338e552250ac63c344365099a16f20616ea9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74964044"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Distribuera återställning av lösenord utan att kräva registrering av slutanvändare

Om du vill distribuera SSPR (Azure Active Directory) självbetjäningslösenord (SSPR) måste autentiseringsdata finnas. Vissa organisationer har sina användare ange sina autentiseringsdata själva. Andra organisationer föredrar att synkronisera med data som redan finns i Active Directory. Den här synkroniserade data görs tillgänglig för Azure AD och SSPR utan att användaren behöver interaktion om du uppfyller följande krav:

* Formatera data i den lokala katalogen på rätt sätt.
* Konfigurera [Azure AD Connect med hjälp av snabbinställningarna](../hybrid/how-to-connect-install-express.md).

För att fungera korrekt måste telefonnummer vara i formatet *+CountryCode PhoneNumber*, till exempel +1 4255551234.

> [!NOTE]
> Det måste finnas ett mellanslag mellan landskoden och telefonnumret.
>
> Återställning av lösenord stöder inte telefontillägg. Även i +1 4255551234X12345 format, tillägg tas bort innan samtalet görs.

## <a name="fields-populated"></a>Fält ifyllda

Om du använder standardinställningarna i Azure AD Connect görs följande mappningar:

| Lokalt Active Directory | Azure AD |
| --- | --- |
| telefonAntal | Arbetstelefon |
| mobil | Mobiltelefon |

När en användare har verifierat sitt mobiltelefonnummer fylls även fältet *Telefon* under **Autentiseringskontaktinformation** i Azure AD med det numret.

## <a name="authentication-contact-info"></a>Kontaktinformation för autentisering

På sidan **Autentiseringsmetoder** för en Azure AD-användare i Azure-portalen kan en global administratör manuellt ange autentiseringskontaktinformationen, vilket visas i följande exempelbildskärm:

![Autentiseringskontaktinformation för en användare i Azure AD][Contact]

* Om fältet **Telefon** är ifyllt och **mobiltelefon** är aktiverat i SSPR-principen ser användaren det numret på registreringssidan för lösenordsåterställning och under arbetsflödet för återställning av lösenord.
* Fältet **Alternativ telefon** används inte för återställning av lösenord.
* Om **e-postfältet** är ifyllt och **e-post** är aktiverat i SSPR-principen ser användaren e-postmeddelandet på registreringssidan för lösenordsåterställning och under arbetsflödet för återställning av lösenord.
* Om fältet **Alternativ e-post** är ifyllt och **e-post** är aktiverat i SSPR-principen ser användaren **inte** e-postmeddelandet på registreringssidan för lösenordsåterställning, men de ser det under arbetsflödet för återställning av lösenord.

## <a name="security-questions-and-answers"></a>Säkerhetsfrågor och svar

Säkerhetsfrågorna och svaren lagras säkert i din Azure AD-klient och är endast tillgängliga för användare via [SSPR-registreringsportalen](https://aka.ms/ssprsetup). Administratörer kan inte se, ange eller ändra innehållet i en annan användares frågor och svar.

## <a name="what-happens-when-a-user-registers"></a>Vad händer när en användare registrerar

När en användare registrerar anger registreringssidan följande fält:

* **Autentisering telefon**
* **E-post för autentisering**
* **Säkerhetsfrågor och svar**

Om du har angett ett värde för **mobiltelefon** eller **Alternativ e-post**kan användarna omedelbart använda dessa värden för att återställa sina lösenord, även om de inte har registrerat sig för tjänsten. Dessutom ser användarna dessa värden när de registrerar sig för första gången, och de kan ändra dem om de vill. När de har registrerat sig sparas dessa värden i fälten **Autentiseringstelefon** respektive **autentiseringstelefon.**

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Ange och läsa autentiseringsdata via PowerShell

Följande fält kan ställas in via PowerShell:

* **Alternativ e-postadress**
* **Mobiltelefon**
* **Office-telefon**: Kan bara ställas in om du inte synkroniserar med en lokal katalog

### <a name="use-powershell-version-1"></a>Använda PowerShell version 1

För att komma igång måste du [hämta och installera Azure AD PowerShell-modulen](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). När du har installerat den kan du använda stegen som följer för att konfigurera varje fält.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Ange autentiseringsdata med PowerShell version 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Läsa autentiseringsdata med PowerShell version 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Läs alternativen för autentiseringstelefon och autentisering e-post

Om du vill läsa **e-postadressen** **för autentiseringstelefon** och autentisering när du använder PowerShell version 1 använder du följande kommandon:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Använda PowerShell version 2

För att komma igång måste du [hämta och installera Azure AD version 2 PowerShell-modulen](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). När du har installerat den kan du använda stegen som följer för att konfigurera varje fält.

Om du snabbt vill installera från de senaste versionerna av PowerShell som stöder Install-Module kör du följande kommandon. (Den första raden kontrollerar om modulen redan är installerad.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Ange autentiseringsdata med PowerShell version 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Läsa autentiseringsdata med PowerShell version 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställa eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord med självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något är trasigt. Hur felsöker jag SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globala administratörer kan ändra en användares autentiseringskontaktinformation"
