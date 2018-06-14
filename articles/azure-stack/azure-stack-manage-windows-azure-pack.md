---
title: Hantera Windows Azure Pack virtuella datorer från Azure-stacken | Microsoft Docs
description: Lär dig hur du hanterar Windows Azure Pack (WAP) virtuella datorer från användarportalen i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a7e4896c84938b392a86f4d9609c4932324c785d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
ms.locfileid: "29735832"
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Hantera Windows Azure Pack virtuella datorer från Azure-stacken

*Gäller för: Azure stacken Development Kit*

Du kan aktivera åtkomst från Azure-stacken användarportalen till virtuella datorer som körs på Windows Azure Pack-klient i Azure-stacken Development Kit. Användarna kan använda Azure Stack-portal för att hantera sina befintliga IaaS virtuella datorer och virtuella nätverk. Dessa resurser görs tillgängliga på Windows Azure-paket via underliggande komponenter för Service Provider Foundation (SPF) och Virtual Machine Manager (VMM). Mer specifikt kan användare:

* Bläddrar bland resurser
* Granska konfigurationsvärden
* Stoppa eller starta en virtuell dator
* Ansluta till en virtuell dator via Remote Desktop Protocol (RDP)
* Skapa och hantera kontrollpunkter
* Ta bort virtuella datorer och virtuella nätverk

Den här funktionen tillhandahålls av Windows Azure Pack-anslutningen för Azure-Stack (förhandsversion). Den här artikeln visar hur du konfigurerar anslutningen till en nod Azure Stack-miljö.

Den här förhandsversionen av anslutningen kan vara medveten om följande:
* Använda Windows Azure Pack Connector bara i testmiljöer (för både Azure-stacken och Windows Azure-paket) och inte i Produktionsdistribution.
* Du måste ha Windows Azure Pack samlade uppdateringen (UR) 9.1 eller senare och System Center SPF och VMM UR 9 eller senare.
* VMM och SPF-komponenter kan vara System Center 2012 R2 eller System Center 2016.
* Du måste utföra konfigurationssteg både Azure-stacken och på Windows Azure-paket.
* Anvisningarna gäller icke - Cloud Platform System CPS miljöer.
* Kända problem finns [felsökning av Microsoft Azure-stacken](azure-stack-troubleshooting.md).


## <a name="architecture"></a>Arkitektur
Följande diagram visar huvudkomponenterna i Windows Azure Pack Connector.

![Windows Azure Pack Connector-komponenter](media/azure-stack-manage-wap/image1.png)

Observera följande information:
* Användarportalen Azure stacken använder resursinformation från båda molnen (Stack för Azure och Windows Azure-paket).
* Användaren måste ha ett konto som är giltig i båda miljöerna.
* Användarportalen Azure stacken måste ha nätverksåtkomst till de komponenter som körs på Windows Azure-paket.
* I den **WAP** avsnitt i diagrammet kan du se nya Windows Azure Pack Connector-moduler (WAP-tillägget och Connector) och befintliga Windows Azure Pack klient API med SPF och VMM-komponenter.


## <a name="identity-management"></a>Identitetshantering
Windows Azure Pack klient API måste lita på Azure-stacken säkerhetstokentjänst (STS).

När användaren utför en åtgärd på stacken Azure-portalen som riktar in sig Windows Azure Pack resurser, använder portalen Windows Azure Pack klient-API. Därför måste autentiseringstoken användaren komma från en betrodd STS. Se i följande diagram:

![Diagram över Windows Azure Pack Connector-autentisering](media/azure-stack-manage-wap/image2.png)

Kit utvecklingsmiljö ha Windows Azure-paket och Azure-stacken oberoende identitetsleverantörer. Användare som har åtkomst till båda miljöerna från stacken Azure-portalen måste ha samma användarnamn huvudnamn (UPN) i båda identitetsleverantörer. Till exempel kontot  *azurestackadmin@azurestack.local*  måste även finnas i STS för Windows Azure-paketet. Om AD FS inte har ställts in för att stödja utgående förtroenderelationer, kommer du upprätta förtroende från Windows Azure Pack-komponenter (klient-API) till Azure Stack-instans av AD FS.

## <a name="prerequisites"></a>Förutsättningar

### <a name="download-the-windows-azure-pack-connector"></a>Hämta Windows Azure Pack Connector
På den [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc)ladda ned .exe-fil och extraherar du det till den lokala datorn. Senare kan kopiera du innehållet till en dator som har åtkomst till din Windows Azure Pack-miljö.

### <a name="deployment-option-requirement"></a>Alternativet distributionskrav
Du kan distribuera Azure-stacken genom att använda alternativet AD FS eller i Azure Active Directory om du vill integrera med Windows Azure-paket.

### <a name="connectivity-requirements"></a>Anslutningskrav
1. Kontrollera att du kan komma åt Windows Azure Pack klientportalen via webbläsaren från den dator som du åtkomst till Azure-stacken användarportalen.
2. AzS WASP01 virtuell dator på Azure-stacken måste kunna ansluta till Windows Azure Pack-klientdator portal. Använd Ping.exe för att kontrollera nätverksanslutningen. 
3.  Du måste ha giltiga certifikat för de nya kopplingen-tjänsterna. Dessa SSL-certifikat måste utfärdas av en betrodd certifikatutfärdare (CA). Du kan inte använda självsignerade certifikat. SSL-certifikat måste vara betrott av Azure-stacken (särskilt AzS WASP01 VM) och andra datorer som klienten kan använda för att få åtkomst till Azure-stacken användarportalen.
 
    >[!NOTE]
    Eftersom AzS WASP01 körs Windows Server med installationsalternativet Server Core, kan du använda ett kommandoradsverktyg, till exempel Certutil.ext för att importera certifikatet. Du kan till exempel kopiera .cer-fil till c:\temp på AzS WASP01 och kör sedan kommandot **certutil - addstore ”CA” ”c:\temp\certname.cer”**.

4.  Windows Azure Pack-miljön måste tillåta fjärrskrivbord trafik till de virtuella klientdatorerna för att upprätta RDP-anslutning till Windows Azure Pack virtuella datorer via Azure Stack-portalen.
5.  För anslutning mellan Azure Stack virtuella datorer och Windows Azure Pack virtuella datorer, måste externa IP-adresser vara dirigerbara mellan två miljöer. Den här anslutningen kan också omfatta associera en DNS-server för att matcha namn på virtuella datorer mellan miljöer.

### <a name="iis-requirements"></a>IIS-krav
Den dator som är värd för Windows Azure Pack klientportalen (vilket kan vara en fysisk värd, en virtuell dator eller flera virtuella datorer) måste ha filnamnstillägget URL-omskrivning IIS installerat. Om den inte redan är installerad, kan du hämta det från [här](https://www.iis.net/downloads/microsoft/url-rewrite). Om klientportalen värd för flera virtuella datorer måste du installera tillägget på varje virtuell dator.

## <a name="configure-azure-stack"></a>Konfigurera Azure Stack
Innan du konfigurerar Windows Azure Pack-anslutningen måste du aktivera läget för flera moln i Azure-stacken användarportalen. Det här läget kan du välja från vilka molnappar som för åtkomst av resurser.

Om du vill aktivera läget för flera molntjänster, måste du köra skriptet Lägg till AzurePackConnector.ps1 efter Azure Stack-distributionen. I följande tabell beskrivs skriptparametrarna.


|  Parameter | Beskrivning | Exempel |   
| -------- | ------------- | ------- |  
| AzurePackClouds | URI: er till Windows Azure Pack-kopplingar. Dessa URI: er bör motsvara portaler för Windows Azure Pack-klient. | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (Som standard är värdet för porten 40005.) |  
| AzureStackCloudName | Etikett för att representera det Azure-stacken molnet.| "AzureStack" |
| DisableMultiCloud | En växel för att inaktivera flera molnet läge.| Gäller inte |
| | |

Du kan köra skriptet Lägg till AzurePackConnector.ps1 direkt efter distributionen, eller senare. Kör skriptet direkt efter distributionen med samma Windows PowerShell-session där Azure Stack-distributionen är klar. Annars kan du öppna en ny Windows PowerShell-session som administratör (inloggad som kontot azurestackadmin).

1. Kör skriptet Lägg till AzurePackConnector.ps1 med hjälp av följande kommandon (med värden som är specifika för din miljö). Observera att skriptet Lägg till AzurePackConnector kan du lägga till mer än en Windows Azure Pack Connector slutpunkt.
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> Det finns ett problem i den aktuella versionen där när skriptet Lägg till AzurePackConnector slutar förblir den i en slinga avsökning för en viss tid (flera minuter) tills den avslutas. När du ser meddelandet **UTFÖRLIG: steg, konfigurera Azure Pack Connector-status: ”lyckades”**, kan du stoppa skriptet eller vänta tills det slutar ensamt. Det gör skillnad eftersom konfigurationen har redan slutförts.

2. Anteckna utdatafilerna som produceras av det här skriptet, ett för varje miljö för Windows Azure-paket som du angav. Filerna finns på: \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput. Dessa filer innehåller den information som krävs för att konfigurera Windows Azure Pack-miljöer mål. Du kan skicka den här filen som en parameter till ett skript senare i dessa instruktioner. Den här filen innehåller följande information:

    * **AzurePackConnectorEndpoint**: innehåller slutpunkten till Windows Azure Pack kopplingstjänsten.
    * **AuthenticationIdentityProviderPartner**: innehåller följande värde-par:
        * Autentiseringstoken signeringscertifikat som Windows Azure Pack klient API behöver litar på för att acceptera anrop från stacken Azure-portaltillägg.

        * ”Sfären” som är associerade med signeringscertifikatet. For example: https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/.

3.  Bläddra till den mapp som innehåller utdatafilerna (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput), och kopierar filerna till den lokala datorn. Filerna kommer att se ut ungefär så här: AzurePack-06-27-15-50.txt.

4.  Testa konfigurationen.

    a. Öppna webbläsaren och logga in på användarportalen Azure Stack (https://portal.local.azurestack.external/).
    
    b. När du loggar in som en klient och portalen belastningarna visas fel om inte att kunna hämta prenumerationer eller tillägg från molnet Azure-paket. Klicka på **OK** att stänga dessa meddelanden. (Dessa felmeddelanden försvinner när du konfigurerar Windows Azure-paket.)

    c. Observera den **moln** listrutan i det övre vänstra hörnet av portalen.

    ![Molnet Väljaren i användargränssnittet för Azure-stacken](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>Konfigurera Windows Azure-paket
För den här anslutningen förhandsversionen endast kräver manuell konfiguration av Windows Azure-paket.

>[!IMPORTANT]
Använda Windows Azure Pack Connector för den här förhandsversionen endast i testmiljöer och inte i Produktionsdistribution.

1.  Installera Connector MSI-filerna på Windows Azure Pack klient portal virtuell dator och installera certifikat. (Om du har flera portal virtuella datorer, måste du slutföra det här steget på varje virtuell dator.)

    a. I Utforskaren, kopiera den **WAPConnector** mappen (vad du har hämtat tidigare) till en mapp med namnet **c:\temp** i klient portal virtual machine.

    b. Öppna en konsol eller RDP-anslutning till klient portal virtuella datorn.

    c. Ändra kataloger till **c:\temp\wapconnector\setup\scripts**, och kör den **installera Connector.ps1** skript för att installera tre MSI-filerna. Inga parametrar krävs.

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. Ändra kataloger till **c:\inetpub** och kontrollera att de tre nya platserna är installerade:

       * MgmtSvc-Connector

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    e. Från samma **c:\temp\wapconnector\setup\scripts** mapp, kör den **konfigurera Certificates.ps1** skript för att installera certifikat. Som standard använder samma certifikat som är tillgänglig för innehavaren portalwebbplatsen i Windows Azure-paket. Kontrollera att detta är ett giltigt certifikat (betrodd av den virtuella datorn i Azure Stack AzS-WASP01 och alla klientdatorer som ansluter till stacken för Azure-portalen). Annars fungerar kommunikation inte. (Du kan också du uttryckligen överföra ett tumavtryck för certifikat som en parameter med hjälp av parametern - tumavtryck.)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. Slutför konfigurationen av dessa tre tjänster genom att köra den **konfigurera WapConnector.ps1** skript för att uppdatera parametrar för Web.config-filen.

    |  Parameter | Beskrivning | Exempel |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | Innehavarportalen Windows Azure Pack FQDN. | tenant.contoso.com | 
    | TenantAPIFQDN | Windows Azure Pack klient API FQDN. | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | Användarportalen Azure Stack FQDN. | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. Om du har flera portal virtuella datorer kan du upprepa steg 1 för var och en av dessa virtuella datorer.

2. Installera den nya innehavaren API MSI på varje virtuell dator för Windows Azure Pack klient-API.

    a. Om en belastningsutjämnare används, kanske du vill markera som offline den virtuella datorn.

    b. I Utforskaren, kopiera den **WAPConnector** mappen till en mapp med namnet **c:\temp** på varje klient-API-dator.

    c. Kopiera filen AzurePackConnectorOutput.txt som du sparade tidigare, till **c:\temp\WAPConnector**.

    d. Öppna en konsol eller RDP-anslutning till klient API VM som du kopierade filen till.
    
    e. Ändra kataloger till **c:\temp\wapconnector\setup\scripts**, och kör **uppdatering TenantAPI.ps1**. Den här nya versionen av API: et för WAP-klient innehåller en ändring för att aktivera en förtroenderelation inte bara med aktuella STS, utan också med instans av AD FS i Azure-stacken.

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  Upprepa steg 2 på någon annan virtuell dator kör klient-API.
3. Från **endast en** för klient-API virtuella datorer, kör skriptet konfigurera TrustAzureStack.ps1 för att lägga till en betrodd relation mellan klient-API och AD FS-instans på Azure-stacken. Du måste använda ett konto med systemadministratörs åtkomst till databasen Microsoft.MgmtSvc.Store. Det här skriptet har följande parametrar:

    | Parameter | Beskrivning | Exempel |
    | --------- | ------------| ------- |
   | SqlServer | Namnet på den SQL Server som innehåller Microsoft.MgmtSvc.Store-databasen. Den här parametern krävs. | SQLServer | 
   | DataFile | Utdatafilen som genererades under konfigurationen av Azure-stacken flera molnet läge tidigare. Den här parametern krävs. | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | Anger att skriptet ska fråga du interaktivt för SQL-autentisering autentiseringsuppgifter ska användas vid anslutning till SQL Server-instansen. De angivna autentiseringsuppgifterna måste ha behörighet att avinstallera databaser, scheman, och ta bort användarinloggningar. Om ingen anges förutsätter skriptet att aktuellt användarsammanhang har åtkomst till. | Inget värde krävs. |
   |  |  |

    Om du inte vet att använda SQL Server kan du identifiera den. Ansluta till klient-API-dator, Använd Unprotect MgmtSvc-kommando för att ta bort skyddet från innehavaren API Web.config-filen och leta efter servernamnet i anslutningssträngen. Kom ihåg att köra skydda MgmtSvc igen för att skydda filen Web.config för klient-API.

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>Exempel
I följande exempel visas en fullständig Windows Azure Pack Connector-distribution på en enda nod Azure Stack-konfiguration och två Windows Azure Pack Express-installationer. (Varje Express-installationen finns på en enstaka dator, till exempel namn *wapcomputer1* och*wapcomputer2*.)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
Hämta .exe-fil från den [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), extrahera det och kopiera mappen WAPConnector till en **c:\temp** mapp på Windows Azure Pack-datorn. Kopiera filer som har genererats som utdata i föregående skript (som finns i \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) till den **c:\temp\WAPConnector** mapp. (Filerna ska ser ut ungefär så här: AzurePack-06-27-15-50.txt.) Kör följande skript, en gång per instans av Windows Azure-paket:

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>Felsökningstips
1.  Se till att det finns en nätverksanslutning mellan Azure-stacken och Windows Azure-paket. Den här anslutningen ska vara mellan alla klient-datorer som har åtkomst till stacken för Azure-portalen och Windows Azure Pack klient portal virtuella datorn där nya Connector-tjänsterna körs.
2.  Se till att alla angivna FQDN-namn är korrekta.
3.  Kontrollera att SSL-certifikat som används i de nya kopplingstjänsterna är betrodda av Azure-stacken (särskilt AzS WASP01 VM) och vilken dator som klienten kan använda för att komma åt Azure Stack användarportalen.
4. Kända problem finns [felsökning av Microsoft Azure-stacken](azure-stack-troubleshooting.md).


## <a name="next-steps"></a>Nästa steg
[Med hjälp av administratörs- och portaler i Azure-stacken](azure-stack-manage-portals.md)
