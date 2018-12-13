---
title: Azure AD-självbetjäning princip för lösenordsåterställning
description: Konfigurera alternativ för princip för återställning av lösenord för självbetjäning Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 3a16977f2d3bc7321de2d2a1538276f182e7fa10
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191000"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Lösenordsprinciper och begränsningar i Azure Active Directory

Den här artikeln beskriver lösenordsprinciper och krav på komplexitet som är associerade med användarkonton som lagrats i din Azure Active Directory (Azure AD)-klient.

## <a name="administrator-reset-policy-differences"></a>Administratören återställa principen skillnader

**Microsoft tillämpar en stark standardprincip *två gate* för återställning av lösenord för alla Azure-administratörsroller** den här principen kan skilja sig från det du har definierat för dina användare och kan inte ändras. Du bör alltid kontrollera att återställa lösenord som en användare utan någon Azure-administratörsroller som tilldelats.

Med en princip för två gate **administratörer inte har möjlighet att använda säkerhetsfrågor**.

Två gate-principen kräver två typer av autentiseringsdata, till exempel en **e-postadress**, **autentiseringsapp**, eller en **telefonnummer**. En två-gate-princip tillämpas under följande omständigheter:

* Alla följande Azure-administratörsroller som påverkas:
  * Supportavdelningsadministratör
  * Tjänstsupportadministratör
  * Faktureringsadministratör
  * Partnersupport, nivå 1
  * Partnersupport, nivå 2
  * Exchange-tjänstadministratör
  * Lync-tjänstadministratör
  * Användarkonto-administratör
  * Katalogskrivare
  * Global administratör eller företagsadministratör
  * SharePoint-tjänstadministratör
  * Efterlevnadsadministratör
  * Programadministratör
  * Säkerhetsadministratör
  * Privilegierad rolladministratör
  * Microsoft Intune-tjänstadministratör
  * Administratör för tjänsten webbprogramproxy
  * CRM-tjänstadministratör
  * Power BI-tjänstadministratör

* Om 30 dagar har i en utvärderingsprenumeration; eller
* En anpassad domän finns, t.ex contoso.com; eller
* Azure AD Connect synkroniserar identiteter från din lokala katalog

### <a name="exceptions"></a>Undantag

En princip för en gate kräver en typ av autentiseringsdata, till exempel en e-postadress *eller* telefonnummer. En princip för en gate gäller under följande omständigheter:

* Det är inom de första 30 dagarna av en utvärderingsprenumeration; eller
* En anpassad domän inte finns (*. onmicrosoft.com); och
* Azure AD Connect synkroniserar inte identiteter

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName-principer som gäller för alla användarkonton

Alla användarkonton som krävs för att logga in på Azure AD måste ha en unik användare huvudnamn (UPN) attribut-värde som är associerad med deras konto. I följande tabell visas de principer som gäller för både den lokala Active Directory-användarkonton som synkroniseras till molnet samt för endast molnbaserade användarkonton:

| Egenskap  | UserPrincipalName-krav |
| --- | --- |
| Tecken som tillåts |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Tecken som tillåts inte |<ul> <li>Alla ”\@ \" tecken som inte är att ange användarnamnet från domänen.</li> <li>Får inte innehålla en punkt ””. omedelbart före den ”\@ \" symbol</li></ul> |
| Längdkravet |<ul> <li>Den totala längden får inte överskrida 113 tecken</li><li>Det kan vara upp till 64 tecken innan den ”\@ \" symbol</li><li>Det kan vara upp till 48 tecken efter den ”\@ \" symbol</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>De principer som gäller endast för cloud-användarkonton

I följande tabell beskrivs de tillgängliga lösenordsprincip som kan tillämpas på konton som skapas och hanteras i Azure AD:

| Egenskap  | Krav |
| --- | --- |
| Tecken som tillåts |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ " ( ) ;</li></ul> |
| Tecken som tillåts inte |<ul><li>Unicode-tecken.</li><li>Blanksteg.</li><li> Starka lösenord</li></ul> |
| Begränsningar för lösenord |<ul><li>Minst 8 tecken och högst 16 tecken.</li><li>Starka lösenord: Kräver tre av fyra av följande:<ul><li>Gemener.</li><li>Versaler.</li><li>Siffror (0-9).</li><li>Symboler (se föregående begränsningar för lösenord).</li></ul></li></ul> |
| Lösenordet upphör att gälla varaktighet |<ul><li>Standardvärde: **90** dagar.</li><li>Värdet kan konfigureras med hjälp av den `Set-MsolPasswordPolicy` cmdlet från Azure Active Directory-modulen för Windows PowerShell.</li></ul> |
| Förfallodatum lösenordsmeddelande |<ul><li>Standardvärde: **14** dagar (tills lösenordet upphör att gälla).</li><li>Värdet kan konfigureras med hjälp av den `Set-MsolPasswordPolicy` cmdlet.</li></ul> |
| Lösenordet förfaller |<ul><li>Standardvärde: **FALSKT** dagar (anger att lösenordet förfaller är aktiverad).</li><li>Värdet kan konfigureras för enskilda användarkonton med hjälp av den `Set-MsolUser` cmdlet.</li></ul> |
| Ändra lösenord |Det sista lösenordet *kan inte* användas igen när användaren ändrar ett lösenord. |
| Historik för återställning av lösenord | Det sista lösenordet *kan* användas igen när du återställer en glömda lösenord. |
| Kontoutelåsning |Efter 10 misslyckade inloggningsförsök med fel lösenord utelåsta användaren för en minut. Ytterligare inloggningsförsök felaktiga utelåst från användaren för att öka varaktigheten för tid. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Ange lösenord upphör att gälla principer i Azure AD

En global administratör för en molntjänst från Microsoft kan använda Microsoft Azure AD-modulen för Windows PowerShell för att ange lösenord inte upphör att gälla. Du kan också använda Windows PowerShell-cmdletar för att ta bort den-upphör aldrig konfiguration, eller för att se vilken användare lösenord ställs in att aldrig upphöra. 

Den här vägledningen gäller för andra leverantörer, till exempel Intune och Office 365, som också förlitar sig på Azure AD för identitets- och directory services. Lösenordets giltighetstid är den enda delen av den princip som kan ändras.

> [!NOTE]
> Endast lösenord för användarkonton som inte synkroniseras via katalogsynkronisering kan konfigureras för att inte upphör för att gälla. Läs mer om katalogsynkronisering [Connect AD med Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Ange eller kontrollera principer för lösenord med hjälp av PowerShell

Om du vill komma igång kan du behöva [ladda ned och installera Azure AD PowerShell-modulen](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). När du har installerat kan använda du följande steg för att konfigurera varje fält.

### <a name="check-the-expiration-policy-for-a-password"></a>Kontrollera förfalloprincipen för lösenord

1. Ansluta till Windows PowerShell med hjälp av företagets administratörsautentiseringsuppgifter.
1. Kör något av följande kommandon:

   * Om du vill se om en användares lösenord har angetts att aldrig upphöra, kör du följande cmdlet med hjälp av UPN-namnet (till exempel *aprilr@contoso.onmicrosoft.com*) eller användar-ID för den användare som du vill kontrollera: `Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}`
   * Se den **lösenordet upphör aldrig att gälla** inställningen för alla användare kör du följande cmdlet: `Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}`

### <a name="set-a-password-to-expire"></a>Ange ett lösenord att gälla

1. Ansluta till Windows PowerShell med hjälp av företagets administratörsautentiseringsuppgifter.
1. Kör något av följande kommandon:

   * Kör följande cmdlet för att ange lösenordet för en användare så att lösenordet upphör att gälla, med hjälp av UPN-namnet eller det användar-ID för användaren: `Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None`
   * Om du vill ange lösenorden för alla användare i organisationen så att de går ut, använder du följande cmdlet: `Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None`

### <a name="set-a-password-to-never-expire"></a>Ange ett lösenord aldrig upphör att gälla

1. Ansluta till Windows PowerShell med hjälp av företagets administratörsautentiseringsuppgifter.
1. Kör något av följande kommandon:

   * Kör följande cmdlet för att ange lösenordet för en användare att aldrig upphöra, med hjälp av UPN-namnet eller det användar-ID för användaren: `Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration`
   * Om du vill ange lösenorden för alla användare i en organisation att aldrig upphöra, kör du följande cmdlet: `Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration`

   > [!WARNING]
   > Lösenord har angetts till `-PasswordPolicies DisablePasswordExpiration` fortfarande ålder baserat på den `pwdLastSet` attribut. Om du ställer in användarlösenord aldrig upphör att gälla och sedan 90 dagar sedan genom att lösenorden går ut. Utifrån den `pwdLastSet` attributet, om du ändrar förfallotiden att `-PasswordPolicies None`, alla lösenord som har en `pwdLastSet` äldre än 90 dagar kräver att användaren ändrar dem nästa gång de loggar in. Den här ändringen kan påverka ett stort antal användare. 

## <a name="next-steps"></a>Nästa steg

I följande artiklar innehåller ytterligare information om lösenordsåterställning via Azure AD:

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)
