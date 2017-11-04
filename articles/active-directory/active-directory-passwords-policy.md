---
title: 'Princip: Azure AD SSPR | Microsoft Docs'
description: "Alternativ för återställning av lösenord för självbetjäning av Azure AD"
services: active-directory
keywords: "Hantering av Active directory-lösenord, lösenordshantering, Azure AD self service för lösenordsåterställning"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 5c33f08e54d522e0eea13a3e267f14f407fc59b6
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Lösenordsprinciper och begränsningar i Azure Active Directory

Den här artikeln beskriver lösenordsprinciper och krav på komplexitet som är associerade med användarkonton lagras i Azure AD-klienten.

## <a name="administrator-password-policy-differences"></a>Administratören lösenord princip skillnader

Microsoft tillämpar en stark standardprincip för lösenordsåterställning med **två portar** för alla Azure-administratörsroller (till exempel Global administratör, Supportavdelningsadministratör och Lösenordsadministratör).

Detta inaktiverar administratörer från att använda säkerhetsfrågor och tillämpar följande.

Två gate-principen, som kräver två typer av autentiseringsdata (e-postadress **och** telefonnummer), gäller följande omständigheter

* Alla Azure-administratörsroller
  * Supportavdelningen administratör
  * Tjänstsupportadministratör
  * Faktureringsadministratör
  * Support för partner Tier1
  * Support för partner Tier2
  * Tjänstadministratör för Exchange
  * Lync tjänstadministratör
  * Kontoadministratör för användaren
  * Directory-skrivare
  * Global administratör/företagsadministratör
  * Tjänstadministratör för SharePoint
  * Kompatibilitet administratör
  * Programadministratör
  * Säkerhetsadministratör
  * Administratör för privilegierade roller
  * Intune-tjänstadministratören
  * Tjänstadministratör för Application Proxy
  * CRM-tjänstadministratör
  * Tjänstadministratör för Power BI
  
* 30 dagar har i en utvärderingsversion **eller**
* Alternativa domänen finns (contoso.com) **eller**
* Azure AD Connect synkroniserar identiteter från din lokala katalog

### <a name="exceptions"></a>Undantag
En gate-principen, som kräver en dataenhet autentisering (e-postadress **eller** telefonnummer), gäller följande omständigheter

* Första 30 dagar från en utvärderingsversion **eller**
* Alternativa domänen finns inte (*. onmicrosoft.com) **och** Azure AD Connect inte synkroniserar identiteter


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName principer som gäller för alla användarkonton

Varje användarkonto som behöver logga in på Azure AD måste ha ett unikt huvudnamn (UPN)-attributvärde som associeras med deras konto. Tabellen nedan beskrivs de principer som gäller för både lokala Active Directory-användarkonton synkroniseras till molnet och endast molnbaserad användarkonton.

| Egenskap | UserPrincipalName krav |
| --- | --- |
| Tecken som tillåts |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Tecken som tillåts inte |<ul> <li>Alla ' @-tecken som inte avgränsa användarnamnet från domänen.</li> <li>Får inte innehålla en punkt '.' omedelbart före den ”@” symbol</li></ul> |
| Längden begränsningar |<ul> <li>Den totala längden får inte överskrida 113 tecken</li><li>64 tecken innan den ”@” symbol</li><li>48 tecken efter den ”@” symbol</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>De principer som gäller endast för användarkonton i molnet

I följande tabell beskrivs de tillgängliga lösenordsprincip som kan tillämpas på konton som skapas och hanteras i Azure AD.

| Egenskap | Krav |
| --- | --- |
| Tecken som tillåts |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Tecken som tillåts inte |<ul><li>Unicode-tecken</li><li>Blanksteg</li><li> **Starka lösenord**: får inte innehålla en punkttecknet '.' omedelbart före den ”@” symbol</li></ul> |
| Begränsningar för lösenord |<ul><li>minsta-8 tecken och högst 16 tecken</li><li>**Starka lösenord**: kräver 3 utanför 4 av följande:<ul><li>Gemener</li><li>Versaler</li><li>Siffror (0-9)</li><li>Symboler (Se ovanstående begränsningar för lösenord)</li></ul></li></ul> |
| Giltighetstiden för lösenord |<ul><li>Standardvärde: **90** dagar </li><li>Värdet kan konfigureras med Set-MsolPasswordPolicy cmdlet från Azure Active Directory-modulen för Windows PowerShell.</li></ul> |
| Meddelande om lösenords upphör att gälla |<ul><li>Standardvärde: **14** dagar (tills lösenordet upphör att gälla)</li><li>Värdet kan konfigureras med cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Lösenordet upphör att gälla |<ul><li>Standardvärde: **FALSKT** dagar (anger att lösenordet upphör att gälla är aktiverad) </li><li>Värdet kan konfigureras för enskilda användarkonton med cmdlet Set-MsolUser. </li></ul> |
| Lösenordet **ändra** historik |Lösenordet **kan** användas igen när **ändra** ett lösenord. |
| Lösenordet **återställa** historik | Lösenordet **kan** användas igen när **återställer** glömt lösenordet. |
| Kontoutelåsning |Efter 10 misslyckade inloggningsförsök (fel lösenord), kommer användaren utelåst under en minut. Ytterligare inloggningsförsök felaktiga Lås ut användaren för att öka varaktighet. |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>Ange lösenordet upphör att gälla principer i Azure Active Directory

En global administratör för en Microsoft-molntjänst kan använda i Microsoft Azure Active Directory-modulen för Windows PowerShell för att ställa in lösenord inte upphör att gälla. Du kan också använda Windows PowerShell-cmdlets för att ta bort den-upphör aldrig konfiguration eller för att se vilka användare lösenord ställs in inte upphör. Den här vägledningen gäller andra leverantörer som Microsoft Intune och Office 365 som också förlitar sig på Microsoft Azure Active Directory för identitets- och directory services.

> [!NOTE]
> Endast lösenord för användarkonton som inte är synkroniserade med katalogsynkronisering kan konfigureras för att inte upphör för att gälla. Läs mer om katalogsynkronisering[Anslut AD med Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>Ange eller kontrollera lösenordsprinciper med hjälp av PowerShell

Om du vill komma igång behöver du [ladda ned och installera Azure AD PowerShell-modulen](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). När du har installerat kan följa du stegen nedan för att konfigurera varje fält.

### <a name="how-to-check-expiration-policy-for-a-password"></a>Hur du kontrollerar förfalloprincipen för lösenord
1. Ansluta till Windows PowerShell med administratörsbehörighet för ditt företag.
2. Kör något av följande kommandon:

   * Kör följande cmdlet för att se om en enskild användares lösenord har angetts att aldrig upphöra, med hjälp av användarens huvudnamn (UPN) (till exempel aprilr@contoso.onmicrosoft.com) eller användar-ID för den användare som du vill kontrollera:`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Om du vill se inställningen ”lösenordet upphör aldrig att gälla” för alla användare kör du följande cmdlet:`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Ange ett lösenord ska upphöra att gälla

1. Ansluta till Windows PowerShell med administratörsbehörighet för ditt företag.
2. Kör något av följande kommandon:

   * Kör följande cmdlet för att ange lösenordet för en användare så att lösenordet upphör att gälla, med hjälp av användarens huvudnamn (UPN) eller användar-ID för användaren:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * För att ange lösenord för alla användare i organisationen så att de upphör att gälla, använder du följande cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Ange ett lösenord aldrig upphör att gälla

1. Ansluta till Windows PowerShell med administratörsbehörighet för ditt företag.
2. Kör något av följande kommandon:

   * Kör följande cmdlet för att ange lösenordet för en användare att aldrig upphöra, med hjälp av användarens huvudnamn (UPN) eller användar-ID för användaren:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * För att ange lösenord för alla användare i en organisation aldrig upphör att gälla, kör du följande cmdlet:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Nästa steg

Följande länkar ger ytterligare information om lösenordsåterställning med Azure AD

* [Hur jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställa eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md).
* [Registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md).
* [Har du en fråga med licensiering?](active-directory-passwords-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder som är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vad är tillbakaskrivning av lösenord och varför jag är intresserad av den?](active-directory-passwords-writeback.md)
* [Hur rapporterar på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad de betyder?](active-directory-passwords-how-it-works.md)
* [Jag tror att något har brutits. Hur felsöker SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte var motsvarar någon annan](active-directory-passwords-faq.md)
