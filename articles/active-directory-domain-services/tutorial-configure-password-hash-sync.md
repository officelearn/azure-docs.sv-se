---
title: Aktivera synkronisering av lösenord hash för Azure AD Domain Services | Microsoft-dokument
description: I den här självstudien kan du läsa om hur du aktiverar synkronisering av lösenord hash-synkronisering med Azure AD Connect till en hanterad Azure Active Directory Domain Services-domän.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 93e5ee9b46fb3387b70dd5092f72efcaa8a2bc19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239170"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Självstudiekurs: Aktivera lösenordssynkronisering i Azure Active Directory Domain Services för hybridmiljöer

För hybridmiljöer kan en Azure Active Directory-klientorganisation (Azure AD) konfigureras för synkronisering med en lokal AD DS-miljö (Active Directory Domain Services) med Azure AD Connect. Som standard synkroniserar Azure AD Connect inte äldre NT LAN Manager (NTLM) och Kerberos-lösenordshöns som behövs för Azure Active Directory Domain Services (Azure AD DS).

Om du vill använda Azure AD DS med konton synkroniserade från en lokal AD DS-miljö måste du konfigurera Azure AD Connect för att synkronisera de lösenordshöningar som krävs för NTLM- och Kerberos-autentisering. När Azure AD Connect har konfigurerats synkroniseras även den äldre lösenordsharen till Azure AD.

Du behöver inte utföra dessa steg om du använder molnkonton utan lokal AD DS-miljö.

I den här självstudien lär du dig:

> [!div class="checklist"]
> * Varför äldre NTLM- och Kerberos-lösenordsh hashar behövs
> * Konfigurera äldre synkronisering av problemh-synkronisering av lösenord för Azure AD Connect

Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration som synkroniseras med en lokal katalog med Azure AD Connect.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
    * Om det behövs [aktiverar du Azure AD Connect för synkronisering av lösenordsh hash][enable-azure-ad-connect].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs [kan du skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Synkronisering av lösenordsh hash med Azure AD Connect

Azure AD Connect används för att synkronisera objekt som användarkonton och grupper från en lokal AD DS-miljö till en Azure AD-klientorganisation. Som en del av processen gör synkronisering av lösenord hash konton att använda samma lösenord i AD DS-miljön för prem och Azure AD.

För att autentisera användare på den hanterade domänen behöver Azure AD DS lösenordshashar i ett format som är lämpligt för NTLM- och Kerberos-autentisering. Azure AD lagrar inte lösenordsh hashar i det format som krävs för NTLM- eller Kerberos-autentisering förrän du aktiverar Azure AD DS för din klient. Av säkerhetsskäl lagrar Azure AD inte heller några lösenordsuppgifter i klartextform. Därför kan Azure AD inte automatiskt generera dessa NTLM- eller Kerberos-lösenordshashar baserat på användarnas befintliga autentiseringsuppgifter.

Azure AD Connect kan konfigureras för att synkronisera de NTLM- eller Kerberos-lösenordshiseringar som krävs för Azure AD DS. Kontrollera att du har slutfört stegen för att [aktivera Azure AD Connect för synkronisering av lösenord hash][enable-azure-ad-connect]. Om du hade en befintlig instans av Azure AD Connect [kan du hämta och uppdatera till den senaste versionen][azure-ad-connect-download] för att se till att du kan synkronisera det äldre lösenordet för NTLM och Kerberos. Den här funktionen är inte tillgänglig i tidiga versioner av Azure AD Connect eller med det äldre DirSync-verktyget. Azure AD Connect version *1.1.614.0* eller senare krävs.

> [!IMPORTANT]
> Azure AD Connect bör endast installeras och konfigureras för synkronisering med lokala AD DS-miljöer. Det stöds inte för att installera Azure AD Connect i en Azure AD DS-hanterad domän för att synkronisera objekt tillbaka till Azure AD.

## <a name="enable-synchronization-of-password-hashes"></a>Aktivera synkronisering av lösenordshashar

Med Azure AD Connect installerat och konfigurerat för synkronisering med Azure AD konfigurerar du nu den äldre lösenordshenschsynkroniseringen för NTLM och Kerberos. Ett PowerShell-skript används för att konfigurera de nödvändiga inställningarna och sedan starta en fullständig lösenordssynkronisering till Azure AD. När den Azure AD Connect-lösenordshã¤nda synkroniseringsprocessen är klar kan användare logga in på program via Azure AD DS som använder äldre NTLM- eller Kerberos-lösenordshar.

1. Öppna **Azure AD Connect > Synkroniseringstjänst**på datorn med Azure AD Connect installerat .
1. Välj fliken **Kontakter.** Anslutningsinformationen som används för att upprätta synkroniseringen mellan den lokala AD DS-miljön och Azure AD visas.

    **Typen** anger antingen *Windows Azure Active Directory (Microsoft)* för Azure AD-anslutningen eller *Active Directory Domain Services* för den lokala AD DS-anslutningen. Anteckna kopplingsnamnen som ska användas i PowerShell-skriptet i nästa steg.

    ![Lista kopplingsnamnen i Synkroniseringstjänsthanteraren](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    I det här exemplet används följande kopplingar:

    * Azure AD-kopplingen heter *aaddscontoso.onmicrosoft.com - AAD*
    * Den lokala AD DS-kopplingen får namnet *onprem.contoso.com*

1. Kopiera och klistra in följande PowerShell-skript på datorn med Azure AD Connect installerat. Skriptet utlöser en fullständig lösenordssynkronisering som innehåller äldre lösenordsharningar. Uppdatera `$azureadConnector` variablerna och `$adConnector` variablerna med kopplingsnamnen från föregående steg.

    Kör det här skriptet i varje AD-skog för att synkronisera lokala konto NTLM och Kerberos lösenord hashar till Azure AD.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    Beroende på katalogens storlek när det gäller antal konton och grupper kan synkronisering av det äldre lösenordet hashar till Azure AD ta lite tid. Lösenorden synkroniseras sedan till Azure AD DS-hanterad domän när de har synkroniserats med Azure AD.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig:

> [!div class="checklist"]
> * Varför äldre NTLM- och Kerberos-lösenordsh hashar behövs
> * Konfigurera äldre synkronisering av problemh-synkronisering av lösenord för Azure AD Connect

> [!div class="nextstepaction"]
> [Lär dig hur synkronisering fungerar i en hanterad domän för Azure AD Domain Services](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
