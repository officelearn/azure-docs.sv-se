---
title: "Azure Active Directory Domain Services: Felsökningsguide | Microsoft Docs"
description: "Felsökningsguide för Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: maheshu
ms.openlocfilehash: 5b094ab27d9d11828b0818a6024ff9b108d6cddb
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services - guide för felsökning
Den här artikeln innehåller tips för felsökning för problem som kan uppstå när du konfigurerar eller administrera Azure Active Directory (AD) Domain Services.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Du kan inte aktivera Azure AD Domain Services för din Azure AD-katalog
Det här avsnittet hjälper dig att felsöka när du försöker aktivera Azure AD Domain Services för din katalog.

Välj felsökningsstegen som motsvarar att du får felmeddelandet.

| **Felmeddelande** | **Lösning** |
| --- |:--- |
| *Namnet contoso100.com används redan i nätverket. Ange ett namn som inte används.* |[Domän namnkonflikt i virtuella nätverk](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Tjänsten har inte tillräcklig behörighet för programmet ”Azure AD Domain Services Sync”. Ta bort programmet ”Azure AD Domain Services Sync” och försök sedan aktivera Domain Services för din Azure AD-klient.* |[Domain Services har inte tillräcklig behörighet för att programmet Azure AD Domain Services-synkronisering](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Programmet Domain Services i din Azure AD-klient har inte tillräcklig behörighet för att aktivera Domain Services. Ta bort programmet med programidentifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64 och försök sedan aktivera Domain Services för din Azure AD-klient.* |[Domain Services-tjänstprogrammet har inte konfigurerats korrekt i din klientorganisation](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Programmet Microsoft Azure AD är inaktiverat i din Azure AD-klient. Aktivera programmet med programidentifieraren 00000002-0000-0000-c000-000000000000 och försök aktivera Domain Services för din Azure AD-klient.* |[Programmet Microsoft Graph har inaktiverats i Azure AD-klient](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Namnkonflikt i domänen
**Ett felmeddelande visas:**

*Namnet contoso100.com används redan i nätverket. Ange ett namn som inte används.*

**Reparation:**

Se till att du inte har en befintlig domän med samma domännamn tillgänglig i det virtuella nätverket. Anta exempelvis att det redan finns en domän som heter ”contoso.com” i det valda virtuella nätverket. Senare, försök att aktivera en Azure AD Domain Services-hanterad domän med samma domännamn (dvs, contoso.com) i det virtuella nätverket. Det uppstår ett fel vid försök att aktivera Azure AD Domain Services.

Det här felet beror på namnkonflikter för domännamnet i det virtuella nätverket. I den här situationen måste du använda ett annat namn för att ställa in din Azure AD Domain Services-hanterade domän. Du kan också avetablera den befintliga domänen och sedan aktivera Azure AD Domain Services.

### <a name="inadequate-permissions"></a>Otillräckliga behörigheter
**Ett felmeddelande visas:**

*Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Tjänsten har inte tillräcklig behörighet för programmet ”Azure AD Domain Services Sync”. Ta bort programmet ”Azure AD Domain Services Sync” och försök sedan aktivera Domain Services för din Azure AD-klient.*

**Reparation:**

Kontrollera om det finns ett program med namnet 'Azure AD Domain Services Sync' i Azure AD-katalogen. Om programmet finns, ta bort den och sedan återaktivera Azure AD Domain Services.

Utför följande steg för att kontrollera förekomst av programmet och ta bort den om programmet redan finns:

1. Navigera till den **program** avsnitt i Azure AD-katalogen i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Välj **alla program** i den **visa** listrutan. Välj **alla** i den **program status** listrutan. Välj **alla** i den **programmet synlighet** listrutan.
3. Typen **Azure AD Domain Services Sync** i sökrutan. Om programmet redan finns, klicka på den och klickar på den **ta bort** i verktygsfältet för att ta bort den.
4. När du har tagit bort programmet, försök att aktivera Azure AD Domain Services igen.

### <a name="invalid-configuration"></a>Ogiltig konfiguration
**Ett felmeddelande visas:**

*Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Programmet Domain Services i din Azure AD-klient har inte tillräcklig behörighet för att aktivera Domain Services. Ta bort programmet med programidentifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64 och försök sedan aktivera Domain Services för din Azure AD-klient.*

**Reparation:**

Kontrollera om du har ett program med namnet 'AzureActiveDirectoryDomainControllerServices' (med en identifierare för d87dcbc6-a371-462e-88e3-28ad15ec4e64) i Azure AD-katalogen. Om det här programmet finns måste du ta bort den och sedan återaktivera Azure AD Domain Services.

Använd följande PowerShell-skript för att hitta programmet och ta bort den.

> [!NOTE]
> Det här skriptet använder **Azure AD PowerShell version 2** cmdlets. En fullständig lista över alla tillgängliga cmdletar och för att hämta modulen kan läsa den [AzureAD PowerShell referensdokumentationen](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph inaktiverad
**Ett felmeddelande visas:**

Det gick inte att aktivera Domain Services i Azure AD-klient. Programmet Microsoft Azure AD är inaktiverat i din Azure AD-klient. Aktivera programmet med 00000002-0000-0000-c000-000000000000 för program-ID och försök sedan att aktivera Domain Services för din Azure AD-klient.

**Reparation:**

Kontrollera om du har inaktiverat ett program med identifierare 00000002-0000-0000-c000-000000000000. Det här programmet är Microsoft Azure AD-program och ger Graph API-åtkomst till Azure AD-klienten. Azure AD Domain Services måste det här programmet måste vara aktiverat för att synkronisera din Azure AD-klient till din hanterade domän.

Aktivera det här programmet och försök sedan att aktivera Domain Services för din Azure AD-klient för att lösa det här felet.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Användarna kan inte logga in på den hanterade domänen för Azure AD Domain Services
Om en eller flera användare i Azure AD-klienten inte kan logga in på den nyligen skapade hanterade domänen, utför du följande felsökningssteg:

* **Logga in med UPN-format:** försöker logga in med UPN-format (till exempel ”joeuser@contoso.com”) i stället för SAMAccountName format (CONTOSO\joeuser). SAMAccountName kan genereras automatiskt för användare vars UPN-prefixet är alltför lång eller är detsamma som en annan användare på den hanterade domänen. UPN-format är säkert att vara unika inom en Azure AD-klient.

> [!NOTE]
> Vi rekommenderar att du använder UPN-format för att logga in på den hanterade domänen med Azure AD Domain Services.
>
>

* Kontrollera att du har [aktiverat lösenordssynkronisering](active-directory-ds-getting-started-password-sync.md) i enlighet med anvisningarna i guiden Komma igång.
* **Externa konton:** Kontrollera att användarkontot påverkas inte är ett externt konto i Azure AD-klient. Exempel på externa konton är Microsoft-konton (till exempel 'joe@live.com') eller användarkonton från en extern Azure AD-katalog. Eftersom Azure AD Domain Services inte har autentiseringsuppgifter för dessa konton kan kan inte dessa användare logga in på den hanterade domänen.
* **Synkroniserats konton:** om de berörda användarkontona synkroniseras från en lokal katalog, kontrollerar du att:

  * Du har distribuerat eller uppdateras till den [senaste rekommenderade versionen av Azure AD Connect](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
  * Du har konfigurerat Azure AD Connect till [utför en fullständig synkronisering](active-directory-ds-getting-started-password-sync.md).
  * Beroende på storleken på din katalog kan det ta en stund för användarkonton och autentiseringsuppgifter hash-värden ska vara tillgängliga i Azure AD Domain Services. Se till att du vänta tillräckligt länge innan du försöker autentisering.
  * Om problemet kvarstår efter verifiering av ovanstående steg kan du prova att starta om tjänsten Microsoft Azure AD Sync. Starta en kommandotolk och kör följande kommandon från datorn synkronisering:

    1. net stop ”Microsoft Azure AD Sync'
    2. net start ”Microsoft Azure AD Sync'
* **Endast molnbaserad konton**: om berörda användarkontot är en molnbaserad användarkonto, ska du kontrollera att användaren har ändrat sitt lösenord när du har aktiverat Azure AD Domain Services. Det här steget gör att de autentiseringshashvärden som krävs för Azure AD Domain Services genereras.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Användare har tagits bort från Azure AD-klienten tas inte bort från din hanterade domän
Azure AD skyddar dig mot oavsiktlig borttagning av användarobjekt. När du tar bort ett användarkonto från Azure AD-klienten flyttas motsvarande användarobjekt till papperskorgen. När den här åtgärden synkroniseras till din hanterade domän gör motsvarande användarkonto markeras som inaktiverade. Den här funktionen hjälper dig att återställa eller ångra borttagning användarkontot senare.

Användarkontot förblir inaktiverad i din hanterade domän, även om du återskapa ett användarkonto med samma UPN i Azure AD-katalogen. Om du vill ta bort användarkontot från den Hantera domänen måste framtvingar ta bort det från din Azure AD-klient.

Om du vill ta bort användarkontot helt från din hanterade domän, ta bort användaren permanent från Azure AD-klienten. Använd den `Remove-MsolUser` PowerShell-cmdlet med den `-RemoveFromRecycleBin` alternativ, enligt beskrivningen i det här [MSDN-artikel](https://msdn.microsoft.com/library/azure/dn194132.aspx).

## <a name="contact-us"></a>Kontakta oss
Kontakta produktteamet Azure Active Directory Domain Services för att [dela feedback eller support](active-directory-ds-contact-us.md).
