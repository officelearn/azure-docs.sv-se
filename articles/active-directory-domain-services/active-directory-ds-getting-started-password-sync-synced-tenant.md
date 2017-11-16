---
title: "Azure AD Domain Services: Aktivera lösenordssynkronisering | Microsoft Docs"
description: "Komma igång med Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: b658bc91723c8384b91eb62e8506c82aa5112509
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Aktivera lösenordssynkronisering med Azure Active Directory Domain Services
I föregående uppgifter aktiverade du Azure Active Directory Domain Services för din Azure Active Directory-klient (Azure AD). Nästa uppgift är att aktivera synkronisering av autentiseringshasher som krävs för NT LAN Manager (NTLM)- och Kerberos-autentisering till Azure AD Domain Services. När du har konfigurerat synkroniseringen av autentiseringsuppgifter kan användarna logga in till den hanterade domänen med sina företagsuppgifter.

Stegen är olika för endast molnbaserade användarkonton och användarkonton som synkroniseras från din lokala katalog med Azure AD Connect.

<br>
| **Typ av användarkonto** | **Steg att utföra** |
| --- | --- |
| **Användarkonton som synkroniserats från en lokal katalog** |**&#x2713;** [Följ anvisningarna i den här artikeln](active-directory-ds-getting-started-password-sync-synced-tenant.md#task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad) | 
| **Molnanvändarkonton som har skapats i Azure AD** |**&#x2713;** [Synkronisera lösenord för endast molnbaserade användarkonton till din hanterade domän](active-directory-ds-getting-started-password-sync.md) |
<br>

> [!TIP]
> **Du kan behöva utföra båda steguppsättningarna.**
> Om Azure AD-klienten har en kombination av endast molnbaserade användare och användare från din lokala AD så måste du utföra båda dessa steguppsättningar.
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>Uppgift 5: aktivera lösenordssynkronisering till din hanterade domän för användarkonton som synkroniserats med din lokala AD
En synkroniserad Azure AD-klient konfigureras att synkronisera med din organisations lokal katalog med hjälp av Azure AD Connect. Som standard synkroniserar Azure AD Connect inte NTLM- och Kerberos-autentiseringshasher till Azure AD. Om du vill använda Azure AD Domain Services måste du konfigurera Azure AD Connect att synkronisera autentiseringshasher som krävs för NTLM- och Kerberos-autentisering. Följande steg aktiverar synkronisering av nödvändiga autentiseringshasher från din lokala katalog till din Azure AD-klient.

> [!NOTE]
> **Om din organisation har användarkonton som synkroniseras från din lokala katalog så måste du aktivera synkronisering av NTLM och Kerberos-hashvärden för att kunna använda den hanterade domänen.** Ett synkroniserat användarkonto är ett konto som skapades i din lokala katalog och som synkroniseras till Azure AD-klienten med Azure AD Connect.
>
>

### <a name="install-or-update-azure-ad-connect"></a>Installera eller uppdatera Azure AD Connect
Installera den senaste rekommenderade versionen av Azure AD Connect på en domänansluten dator. Om du har en befintlig instans av installationsprogrammet för Azure AD Connect måste du uppdatera den så att den använder den senaste versionen av Azure AD Connect. Använd alltid den senaste versionen av Azure AD Connect för att undvika kända problem/buggar som kanske redan har åtgärdats.

**[Ladda ned Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Rekommenderad version: **1.1.614.0** – publicerad den 5 september 2017.

> [!WARNING]
> Du MÅSTE installera den senaste rekommenderade versionen av Azure AD Connect för att den äldre lösenordsinformationen (som krävs för NTLM- och Kerberos-autentisering) ska kunna synkroniseras till din Azure AD-klient. Den här funktionen är inte tillgänglig i tidigare versioner av Azure AD Connect eller med det äldre DirSync-verktyget.
>
>

Installationsinstruktioner för Azure AD Connect finns i följande artikel – [Komma igång med Azure AD Connect](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Aktivera synkroniseringen av NTLM- och Kerberos-autentiseringshasher till Azure AD
Kör följande PowerShell-skript på varje AD-skog. Skriptet aktiverar alla lokala användares NTLM- och Kerberos-lösenordshashvärden som ska synkroniseras med din Azure AD-klientorganisation. Skriptet initierar också en fullständig synkronisering i Azure AD Connect.

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Beroende på storleken på din katalog (antal användare, grupper osv.) kan synkroniseringen av autentiseringshasherna till Azure AD ta ett tag. Lösenorden kan användas på den Azure AD DS-hanterade domänen strax efter att hashvärdena för autentiseringsuppgifterna har synkroniserats till Azure AD.

<br>

## <a name="related-content"></a>Relaterat innehåll
* [Aktivera lösenordssynkronisering till AAD Domain Services för en molnbaserad Azure AD-katalog](active-directory-ds-getting-started-password-sync.md)
* [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
* [Ansluta en virtuell Windows-dator till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Ansluta en virtuell Linux-dator med Red Hat Enterprise till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
