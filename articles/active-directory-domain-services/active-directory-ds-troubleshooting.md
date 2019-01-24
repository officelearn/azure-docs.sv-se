---
title: 'Azure Active Directory Domain Services: Felsökningsguide för | Microsoft Docs'
description: Felsökningsguide för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: ergreenl
ms.openlocfilehash: 8b752585fc72b7f4be8e7b9320290f8ad56f53c2
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844661"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services - felsökningsguide
Den här artikeln innehåller tips för felsökning för problem som kan uppstå när du konfigurerar eller administrera domäntjänster för Azure Active Directory (AD).

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Du kan inte aktivera Azure AD Domain Services för din Azure AD-katalog
Det här avsnittet hjälper dig att felsöka fel vid försök att aktivera Azure AD Domain Services för din katalog.

Välj felsökningsstegen som relaterar till det felmeddelande som uppstår.

| **Felmeddelande** | **Lösning** |
| --- |:--- |
| *Namnet contoso100.com används redan i nätverket. Ange ett namn som inte används.* |[Domän namnkonflikt i det virtuella nätverket](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Tjänsten har inte tillräcklig behörighet för programmet ”Azure AD Domain Services Sync”. Ta bort programmet ”Azure AD Domain Services Sync” och försök sedan aktivera Domain Services för din Azure AD-klient.* |[Domain Services har inte tillräcklig behörighet för Azure AD Domain Services Sync-programmet](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Programmet Domain Services i din Azure AD-klient har inte tillräcklig behörighet för att aktivera Domain Services. Ta bort programmet med programidentifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64 och försök sedan aktivera Domain Services för din Azure AD-klient.* |[Programmet Domain Services har inte konfigurerats korrekt i din klient](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Programmet Microsoft Azure AD är inaktiverat i din Azure AD-klient. Aktivera programmet med programidentifieraren 00000002-0000-0000-c000-000000000000 och försök aktivera Domain Services för din Azure AD-klient.* |[Microsoft Graph-programmet har inaktiverats i Azure AD-klienten](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Domän namnkonflikt
**Felmeddelande:**

*Namnet contoso100.com används redan i nätverket. Ange ett namn som inte används.*

**Reparation:**

Kontrollera att du inte har en befintlig domän med samma domännamn i det virtuella nätverket. Anta exempelvis att det redan finns en domän som heter ”contoso.com” i det valda virtuella nätverket. Senare kan försöker du aktivera en Azure AD Domain Services-hanterad domän med samma domännamn (det vill säga ”contoso.com”) i det virtuella nätverket. Det uppstår ett fel vid försök att aktivera Azure AD Domain Services.

Det här felet beror på namnkonflikter för domännamnet i det virtuella nätverket. I den här situationen måste du använda ett annat namn för att ställa in din Azure AD Domain Services-hanterade domän. Du kan också avetablera den befintliga domänen och sedan aktivera Azure AD Domain Services.

### <a name="inadequate-permissions"></a>Otillräckliga behörigheter
**Felmeddelande:**

*Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Tjänsten har inte tillräcklig behörighet för programmet ”Azure AD Domain Services Sync”. Ta bort programmet ”Azure AD Domain Services Sync” och försök sedan aktivera Domain Services för din Azure AD-klient.*

**Reparation:**

Kontrollera om det finns ett program med namnet ”Azure AD Domain Services Sync” i Azure AD-katalogen. Om det här programmet finns, ta bort den och sedan återaktivera Azure AD Domain Services.

Utför följande steg för att söka efter förekomst av programmet och ta bort den, om programmet redan finns:

1. Navigera till den **program** för din Azure AD-katalog i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Välj **alla program** i den **visa** listrutan. Välj **alla** i den **programstatus** listrutan. Välj **alla** i den **synlighet för program** listrutan.
3. Typ **Azure AD Domain Services Sync** i sökrutan. Om programmet redan finns, klicka på den och klickar på den **ta bort** i verktygsfältet för att ta bort den.
4. När du har tagit bort programmet, kan du försöka aktivera Azure AD Domain Services igen.

### <a name="invalid-configuration"></a>Ogiltig konfiguration
**Felmeddelande:**

*Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Programmet Domain Services i din Azure AD-klient har inte tillräcklig behörighet för att aktivera Domain Services. Ta bort programmet med programidentifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64 och försök sedan aktivera Domain Services för din Azure AD-klient.*

**Reparation:**

Kontrollera om du har ett program med namnet ”AzureActiveDirectoryDomainControllerServices” (med en programidentifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64) i Azure AD-katalogen. Om det här programmet finns, måste du ta bort det och sedan återaktivera Azure AD Domain Services.

Använd följande PowerShell-skript för att hitta programmet och tar bort den.

> [!NOTE]
> Det här skriptet använder **Azure AD PowerShell version 2** cmdletar. En fullständig lista över alla tillgängliga cmdlet: ar och för att hämta modulen kan du läsa den [AzureAD PowerShell-referensdokumentation](https://msdn.microsoft.com/library/azure/mt757189.aspx).
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
**Felmeddelande:**

Det gick inte att aktivera Domain Services i den här Azure AD-klient. Programmet Microsoft Azure AD är inaktiverat i din Azure AD-klient. Aktivera programmet med programidentifieraren 00000002-0000-0000-c000-000000000000 och försök sedan aktivera Domain Services för din Azure AD-klient.

**Reparation:**

Kontrollera om du har inaktiverat ett program med identifieraren 00000002-0000-0000-c000-000000000000. Det här programmet är Microsoft Azure AD-program och ger Graph API-åtkomst till Azure AD-klienten. Azure AD Domain Services måste det här programmet ska vara aktiverat för att synkronisera Azure AD-klienten till din hanterade domän.

Aktivera det här programmet och försök sedan aktivera Domain Services för din Azure AD-klient för att lösa det här felet.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Användarna kan inte logga in på den hanterade domänen för Azure AD Domain Services
Om en eller flera användare i Azure AD-klienten inte kan logga in på den nyligen skapade hanterade domänen, utför du följande felsökningssteg:

* **Logga in med UPN-format:** Försök logga in med UPN-formatet (till exempel ”joeuser@contoso.com”) istället för formatet SAMAccountName format (”CONTOSO\joeuser”). SAMAccountName kan genereras automatiskt för användare vars UPN-prefix är alltför långa eller är samma som en annan användare i den hanterade domänen. UPN-formatet är säkert att vara unika inom en Azure AD-klient.

> [!NOTE]
> Vi rekommenderar att du använder UPN-formatet för att logga in på den hanterade domänen i Azure AD Domain Services.
>
>

* Kontrollera att du har [aktiverat lösenordssynkronisering](active-directory-ds-getting-started-password-sync.md) i enlighet med anvisningarna i guiden Komma igång.
* **Externa konton:** Kontrollera att det användarkonto som påverkas inte är ett externt konto i Azure AD-klienten. Exempel på externa konton är Microsoft-konton (till exempel ”joe@live.com”) eller användarkonton från en extern Azure AD-katalog. Eftersom Azure AD Domain Services inte har autentiseringsuppgifter för sådana användarkonton kan kan inte dessa användare logga in till den hanterade domänen.
* **Synkroniserade konton:** Om de berörda användarkontona synkroniseras från en lokal katalog, kontrollerar du att:

  * Du har distribuerat eller uppdaterats till den [senaste rekommenderade versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
  * Du har konfigurerat Azure AD Connect för [utför en fullständig synkronisering](active-directory-ds-getting-started-password-sync.md).
  * Beroende på storleken på din katalog kan det ta en stund för användarkonton och autentiseringshasher ska vara tillgängliga i Azure AD Domain Services. Se till att du vänta tillräckligt länge innan du försöker utföra autentisering.
  * Om problemet kvarstår efter verifiering av föregående steg, försök att starta om tjänsten Microsoft Azure AD Sync. Starta en kommandotolk och kör följande kommandon från synkroniseringsdatorn:

    1. net stop ”Microsoft Azure AD Sync”
    2. net start ”Microsoft Azure AD Sync”
* **Endast molnbaserad konton**: Om det berörda användarkontot är ett molnbaserad användarkonto, kontrollerar du att användaren har ändrat sitt lösenord när du har aktiverat Azure AD Domain Services. Det här steget gör att de autentiseringshashvärden som krävs för Azure AD Domain Services genereras.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Det finns en eller flera aviseringar på din hanterade domän

Se hur du löser aviseringar på din hanterade domän genom att besöka den [felsöka aviseringar](active-directory-ds-troubleshoot-alerts.md) artikeln.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Användare har tagits bort från Azure AD-klienten tas inte bort från en hanterad domän
Azure AD skyddar dig mot oavsiktlig borttagning av användarobjekt. När du tar bort ett användarkonto från Azure AD-klienten flyttas motsvarande användarobjekt till papperskorgen. När borttagningsåtgärden synkroniseras till din hanterade domän, orsakar det motsvarande användarkontot markeras som inaktiverat. Den här funktionen kan du återställa eller ångra borttagning av användarkontot senare.

Användarkontot förblir inaktiverad i den hanterade domänen, även om du återskapar ett användarkonto med samma UPN i Azure AD-katalogen. Om du vill ta bort användarkontot från en hanterad domän som du behöver tvång ta bort den från Azure AD-klienten.

Om du vill ta bort användarkontot helt från en hanterad domän, ta bort användaren permanent från Azure AD-klienten. Använd den `Remove-MsolUser` PowerShell-cmdlet med den `-RemoveFromRecycleBin` enligt beskrivningen i det här [MSDN-artikeln](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services-produktteamet att [dela feedback eller support](active-directory-ds-contact-us.md).
