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
ms.date: 03/17/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 4969b43831a3813a4e76c6447c252a9c458f371a
ms.lasthandoff: 03/17/2017


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Aktivera lösenordssynkronisering till Azure AD Domain Services
I de föregående uppgifterna aktiverade du Azure AD Domain Services för din Azure AD-klient. Nästa uppgift är att aktivera synkronisering av lösenord till Azure AD Domain Services. När synkroniseringen av autentiseringsuppgifter har konfigurerats kan användarna logga in till den hanterade domänen med sina företagsuppgifter.

Stegen skiljer sig beroende på om din organisation har en helt molnbaserad Azure AD-klient eller om du synkroniserar med din lokala katalog med hjälp av Azure AD Connect.

<br>

> [!div class="op_single_selector"]
> * [Endast molnbaserad Azure AD-klient](active-directory-ds-getting-started-password-sync.md)
> * [Synkroniserad Azure AD-klient](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Uppgift 5: Aktivera lösenordssynkronisering till AAD Domain Services för en synkroniserad Azure AD-klient
En synkroniserad Azure AD-klient konfigureras att synkronisera med din organisations lokal katalog med hjälp av Azure AD Connect. Azure AD Connect synkroniserar inte som standard NTLM- och Kerberos-autentiseringshasher till Azure AD. Om du vill använda Azure AD Domain Services måste du konfigurera Azure AD Connect att synkronisera autentiseringshasher som krävs för NTLM- och Kerberos-autentisering. 

> [!WARNING]
> Du måste aktivera lösenordssynkronisering till AAD Domain Services varje gång du aktiverar Azure AD Domain Services. Du kanske tidigare har aktiverat Azure AD Domain Services för din Azure AD-katalog och stängt av det. Du måste ändå aktivera lösenordssynkronisering nästa gång du aktiverar Azure AD Domain Services för katalogen.
>
>

Följande steg aktiverar synkronisering av nödvändiga autentiseringshasher till din Azure AD-klient.

### <a name="install-or-update-azure-ad-connect"></a>Installera eller uppdatera Azure AD Connect
Installera den senaste rekommenderade versionen av Azure AD Connect på en domänansluten dator. Om du har en befintlig instans av installationsprogrammet för Azure AD Connect måste du uppdatera den så att den använder den senaste versionen av Azure AD Connect. Kontrollera att du använder den senaste versionen av Azure AD Connect för att undvika kända problem/buggar som kanske redan har åtgärdats.

**[Ladda ned Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Rekommenderad version: **1.1.281.0** – publicerad den 7 september 2016.

> [!WARNING]
> Du MÅSTE installera den senaste rekommenderade versionen av Azure AD Connect för att den äldre lösenordsinformationen (som krävs för NTLM- och Kerberos-autentisering) ska kunna synkroniseras till din Azure AD-klient. Den här funktionen är inte tillgänglig i tidigare versioner av Azure AD Connect eller med det äldre DirSync-verktyget.
>
>

Installationsinstruktioner för Azure AD Connect finns i följande artikel – [Komma igång med Azure AD Connect](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Aktivera synkroniseringen av NTLM- och Kerberos-autentiseringshasher till Azure AD
Kör följande PowerShell-skript i varje AD-skog för att framtvinga fullständig lösenordssynkronisering och aktivera alla lokala användares autentiseringshasher för synkronisering till din AD-klient. Det här skriptet gör att autentiseringshasherna som krävs för NTLM- och Kerberos-autentisering kan synkroniseras till din Azure AD-klient.

```
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

