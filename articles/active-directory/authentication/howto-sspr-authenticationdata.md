---
title: Data krav för Azure AD SSPR – Azure Active Directory
description: Data krav för återställning av lösen ord för självbetjäning i Azure AD och hur du kan uppfylla dem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42f7e120745357d3bd5735cca568bdd6971ea061
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80652359"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Distribuera lösen ords återställning utan att kräva slut användar registrering

För att distribuera Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord (SSPR) måste autentiseringsdata vara tillgängligt. Vissa organisationer har sina användare inmatat sina autentiseringsdata själva. Andra organisationer föredrar att synkronisera med data som redan finns i Active Directory. Dessa synkroniserade data görs tillgängliga för Azure AD och SSPR utan användar interaktion om du uppfyller följande krav:

* Formatera data korrekt i din lokala katalog.
* Konfigurera [Azure AD Connect med hjälp av inställningarna för Express](../hybrid/how-to-connect-install-express.md).

För att fungera korrekt måste telefonnumret vara i formatet *+ CountryCode telefonnummer*, till exempel + 1 4255551234.

> [!NOTE]
> Det måste finnas ett blank steg mellan lands koden och telefonnumret.
>
> Lösen ords återställning stöder inte telefon tillägg. Även i formatet + 1 4255551234X12345 tas tilläggen bort innan anropet placeras.

## <a name="fields-populated"></a>Ifyllda fält

Om du använder standardinställningarna i Azure AD Connect görs följande mappningar:

| Lokalt Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | Arbetstelefon |
| mobil | Mobiltelefon |

När en användare har verifierat mobiltelefon numret fylls *telefon* fältet under **information om kontakt information** i Azure AD också i det numret.

## <a name="authentication-contact-info"></a>Information om autentisering av kontakt

På sidan **autentiseringsmetoder** för en Azure AD-användare i Azure Portal kan en global administratör manuellt ange autentiseringens kontakt information, som du ser i följande exempel skärm bild:

![Kontakt information för autentisering på en användare i Azure AD][Contact]

* Om **telefon** fältet är ifyllt och **mobil telefonen** är aktiverat i SSPR-principen, ser användaren det numret på registrerings sidan för lösen ords återställning och under arbets flödet för lösen ords återställning.
* Det **alternativa telefon** fältet används inte för lösen ords återställning.
* Om **e-** postfältet är ifyllt och **e-postmeddelandet** är aktiverat i SSPR-principen ser användaren den e-postadressen på registrerings sidan för lösen ords återställning och under arbets flödet för lösen ords återställning.
* Om det **alternativa e-** postfältet är ifyllt och **e-postmeddelandet** är aktiverat i SSPR-principen visas **inte** e-postmeddelandet på registrerings sidan för lösen ords återställning, men de visas under arbets flödet för lösen ords återställning.

## <a name="security-questions-and-answers"></a>Säkerhets frågor och svar

Säkerhets frågorna och svaren lagras på ett säkert sätt i din Azure AD-klient och är bara tillgängliga för användare via [registrerings portalen för SSPR](https://aka.ms/ssprsetup). Administratörer kan inte se, ange eller ändra innehållet i en annan användares frågor och svar.

## <a name="what-happens-when-a-user-registers"></a>Vad händer när en användare registrerar sig

När en användare registrerar, anger registrerings sidan följande fält:

* **Telefon för autentisering**
* **E-postautentisering**
* **Säkerhets frågor och svar**

Om du har angett ett värde för **mobil telefon** eller **alternativt e-postmeddelande**kan användarna omedelbart använda dessa värden för att återställa sina lösen ord, även om de inte har registrerats för tjänsten. Dessutom ser användarna dessa värden när de registreras för första gången och de kan ändra dem om de vill. När de har registrerats sparas de här värdena i fälten för **autentiserings** **-och e-postadresserna** .

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Ange och läsa autentiseringsdata via PowerShell

Följande fält kan ställas in via PowerShell:

* **Alternativ e-postadress**
* **Mobiltelefon**
* **Arbets telefon**: kan bara anges om du inte synkroniserar med en lokal katalog

### <a name="use-powershell-version-1"></a>Använd PowerShell version 1

För att komma igång måste du [Ladda ned och installera Azure AD PowerShell-modulen](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). När du har installerat det kan du använda stegen nedan för att konfigurera varje fält.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Ange autentiseringsdata med PowerShell version 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Läs data för autentisering med PowerShell version 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Läs e-postalternativen för autentisering via telefon och autentisering

Om du vill läsa **e-postmeddelandet** för **autentisering via telefon** och autentisering när du använder PowerShell version 1, använder du följande kommandon:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Använd PowerShell version 2

För att komma igång måste du [Ladda ned och installera Azure AD version 2 PowerShell-modulen](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). När du har installerat det kan du använda stegen nedan för att konfigurera varje fält.

Om du snabbt vill installera från de senaste versionerna av PowerShell som stöder install-module kör du följande kommandon. (Den första raden kontrollerar om modulen redan är installerad.)

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

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Läs autentiseringsdata med PowerShell version 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösen ord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord med självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något är brutet. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globala administratörer kan ändra kontakt information för en användares autentisering"
