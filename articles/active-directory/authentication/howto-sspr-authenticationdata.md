---
title: Azure AD SSPR-datakrav | Microsoft Docs
description: Uppgifter som krävs för Azure AD-lösenord via Självbetjäning för återställning och hur du uppfyller dem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccf665147c8fea0e2ce44161ef0b5df085649a0b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179052"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Distribuera lösenordsåterställning utan registrerad slutanvändare

Autentiseringsdata måste finnas för att distribuera lösenordsåterställning för Azure Active Directory (Azure AD) via självbetjäning (SSPR). Vissa organisationer har användarna ange sina autentiseringsdata själva. Men många organisationer vill synkronisera med data som redan finns i Active Directory. Synkroniserade data görs tillgänglig för Azure AD och SSPR utan användaråtgärder om du:
   * Korrekt formatera data i din lokala katalog.
   * Konfigurera [Azure AD Connect genom att använda standardinställningarna](../hybrid/how-to-connect-install-express.md).

För att fungera korrekt telefonnummer måste vara i formatet *+ CountryCode PhoneNumber*, till exempel + 1 4255551234.

> [!NOTE]
> Det måste finnas ett blanksteg mellan landskoden och telefonnumret.
>
> Återställning av lösenord har inte stöd för anknytningsnummer. Även i formatet + 1 4255551234 X 12345 tas tillägg bort innan de görs.

## <a name="fields-populated"></a>Fält

Om du använder standardinställningarna i Azure AD Connect, görs följande mappningar:

| Lokalt Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | Arbetstelefon |
| mobila | Mobiltelefon |

När en användare har verifierat sitt mobiltelefonnummer, fylls också fältet Phone under autentiseringskontakt i Azure AD med det numret.

## <a name="authentication-contact-info"></a>Information om autentiseringskontakt

En Global administratör kan manuellt ange kontaktinformation för autentisering för en användare som visas i följande skärmbild.

![Kontakta][Contact]

Om fältet Phone fylls och mobiltelefon är aktiverat i SSPR-principen, kan användaren ser att antalet på registreringssidan för återställning av lösenord och under lösenordet återställning av arbetsflöde.

Alternativ telefon fältet används inte för återställning av lösenord.

Om fylls fältet för e-post och e-post är aktiverat i SSPR-principen, ser användaren att e-post på registreringssidan för återställning av lösenord och under lösenordet återställning av arbetsflöde.

Om fylls fältet alternativ e-post och e-post är aktiverat i SSPR-princip, kommer användaren **inte** se att registreringssidan för återställning av e-post på lösenordet, men visas den under lösenordet återställning av arbetsflöde.

## <a name="security-questions-and-answers"></a>Säkerhetsfrågor och svar

Säkerhetsfrågor och svar lagras på ett säkert sätt i Azure AD-klienten och är endast tillgängliga för användare via den [SSPR-registreringsportalen](https://aka.ms/ssprsetup). Administratörer kan inte se Ange eller ändra innehållet i en annan användare frågor och svar.

## <a name="what-happens-when-a-user-registers"></a>Vad händer när en användare registrerar

När en användare registrerar, anger registreringssidan följande fält:

* **Telefon för autentisering**
* **E-post för autentisering**
* **Säkerhetsfrågor och svar**

Om du har angett ett värde för **mobiltelefon** eller **alternativ e-postadress**, omedelbart fjärranvändning av dessa värden kan återställa sina lösenord, även om de inte har registrerat dig för tjänsten. Dessutom kan se användare dessa värden när de registrerar sig för första gången, och de kan ändra dem om de vill. När de registrerar sig har dessa värden kommer att sparas i den **Autentiseringstelefon** och **e-post för autentisering** respektive fält.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Ange och läsa autentiseringsdata via PowerShell

Följande fält kan ställas in via PowerShell:

* **Alternativ e-postadress**
* **Mobiltelefon**
* **Arbetstelefon**: Kan bara anges om du inte synkroniserar med en lokal katalog

### <a name="use-powershell-version-1"></a>Använda PowerShell version 1

Om du vill komma igång kan du behöva [ladda ned och installera Azure AD PowerShell-modulen](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). När du har installerat kan använda du stegen nedan för att konfigurera varje fält.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Ange autentisering med PowerShell version 1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Läsa in autentiseringsdata med PowerShell-version 1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Läsa Alternativ telefon för autentisering och autentisering e-post

Att läsa den **Autentiseringstelefon** och **e-post för autentisering** när du använder PowerShell version 1 kan du använda följande kommandon:

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Använda PowerShell version 2

Om du vill komma igång kan du behöva [ladda ned och installera Azure AD-modul version 2 PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). När du har installerat kan använda du stegen nedan för att konfigurera varje fält.

Kör följande kommandon för att snabbt vill installera från de senaste versionerna av PowerShell som har stöd för Install-Module. (Den första raden kontrollerar om modulen har installerats.)

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Ange autentisering med PowerShell version 2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Läs autentiseringsdata med PowerShell version 2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globala administratörer kan ändra en användares information"
