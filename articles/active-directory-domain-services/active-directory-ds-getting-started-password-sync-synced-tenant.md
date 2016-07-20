<properties
    pageTitle="Azure AD Domain Services: Aktivera lösenordssynkronisering | Microsoft Azure"
    description="Komma igång med Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/25/2016"
    ms.author="maheshu"/>

# Azure AD Domain Services *(förhandsversion)* – Aktivera lösenordssynkronisering till Azure AD DS

## Uppgift 5: Aktivera lösenordssynkronisering till AAD Domain Services för en synkroniserad Azure AD-klient
När du har aktiverat Azure AD Domain Services för din Azure AD-katalog är nästa uppgift att aktivera synkronisering av lösenord till Azure AD Domain Services. Detta gör det möjligt för användare att logga in i domänen med deras företagsuppgifter.

Stegen skiljer sig beroende på om din organisation bara har en molnbaserad Azure AD-katalog eller om du synkroniserar med din lokala katalog med hjälp av Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Endast molnbaserad Azure AD-katalog](active-directory-ds-getting-started-password-sync.md)
- [Synkroniserad Azure AD-katalog](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Synkroniserade klienter – Aktivera synkroniseringen av NTLM- och Kerberos-autentiseringshasher till Azure AD
Om Azure AD-klienten för din organisation är inställd att synkronisera med din lokala katalog med hjälp av Azure AD Connect måste du konfigurera Azure AD Connect att synkronisera autentiseringshasher som krävs för NTLM- och Kerberos-autentisering. Dessa värden synkroniseras inte med Azure AD som standard. Genom att följa stegen nedan kan du aktivera synkroniseringen av hashvärdena till Azure AD-klienten.

#### Installera eller uppdatera Azure AD Connect

Du måste installera den senaste rekommenderade versionen av Azure AD Connect på en domänansluten dator. Om du har en befintlig instans av Azure AD Connect-installationsprogrammet måste du uppdatera den för att använda Azure AD Connect GA-versionen. Kontrollera att du använder den senaste versionen av Azure AD Connect för att undvika kända problem/buggar som kanske redan har åtgärdats.

**[Ladda ned Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Rekommenderad version: **1.1.130.0** – publicerades 12 april 2016.

  > [AZURE.WARNING] Du MÅSTE installera den senaste rekommenderade versionen av Azure AD Connect för att äldre lösenordsinformation (som krävs för NTLM- och Kerberos-autentisering) ska kunna synkroniseras till din Azure AD-klient. Den här funktionen är inte tillgänglig i tidigare versioner av Azure AD Connect eller med det äldre DirSync-verktyget.

Installationsinstruktioner för Azure AD Connect finns i följande artikel – [Komma igång med Azure AD Connect](../active-directory/active-directory-aadconnect.md)


#### Framtvinga fullständig lösenordssynkronisering till Azure AD

Kör följande PowerShell-skript i varje AD-skog för att framtvinga fullständig lösenordssynkronisering och aktivera alla lokala användares lösenordshasher (inklusive autentiseringshasherna som krävs för NTLM/Kerberos-autentisering).

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

Beroende på storleken på din katalog (antal användare, grupper osv.) kan synkroniseringen av autentiseringsuppgifterna till Azure AD ta relativt lång tid. Lösenorden kan användas på den Azure AD DS-hanterade domänen strax efter att hashvärdena för autentiseringsuppgifterna har synkroniserats till Azure AD.


<br>

## Relaterat innehåll

- [Aktivera lösenordssynkronisering till AAD Domain Services för en molnbaserad Azure AD-katalog](active-directory-ds-getting-started-password-sync.md)

- [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)

- [Ansluta en virtuell Windows-dator till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)

- [Ansluta en virtuell Linux-dator med Red Hat Enterprise till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-rhel-linux-vm.md)



<!--HONumber=Jun16_HO2-->


