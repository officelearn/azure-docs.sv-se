---
title: Distribuera Azure Stack Development Kit | Microsoft Docs
description: "Lär dig hur du förbereder Azure Stack Development Kit och köra PowerShell-skript för att distribuera den."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 6a5912117a475c7af028f01ea47a7042677992ca
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="deploy-the-azure-stack-development-kit"></a>Distribuera Azure Stack Development Kit

*Gäller för: Azure stacken Development Kit*

Att distribuera den [Azure Stack Development Kit](azure-stack-poc.md), måste du utföra följande steg:

1. [Hämta distributionspaketet](https://azure.microsoft.com/overview/azure-stack/try/?v=try) att hämta Cloudbuilder.vhdx.
2. Förbered cloudbuilder.vhdx att konfigurera datorn (development kit värden) som du vill installera development kit. Efter det här steget startas development kit värden med Cloudbuilder.vhdx.
3. Distribuera development kit på development kit värden.

> [!NOTE]
> För bästa resultat, även om du vill använda en frånkopplad miljö i Azure-stacken, är det bäst att distribuera medan du är ansluten till internet. På så sätt kan kan Windows Server 2016-utvärderingsversion som ingår i development kit installationen aktiveras vid tidpunkten för distribution.

## <a name="download-and-extract-the-development-kit"></a>Ladda ned och extrahera development kit
1. Kontrollera att datorn uppfyller följande krav innan du startar hämtningen:

  - Datorn måste ha minst 60 GB ledigt utrymme på fyra separata, identiska logiska hårddiskar dessutom att operativsystemets disk.
  - [.NET framework 4.6 (eller en senare version)](https://aka.ms/r6mkiy) måste vara installerad.

2. [Gå till sidan komma igång](https://azure.microsoft.com/overview/azure-stack/try/?v=try) där du kan hämta Azure Stack Development Kit, ange din information och klickar sedan på **skicka**.
3. Hämta och kör den [distribution av förutsättningar för Azure-stacken Development Kit](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) kravkontrollen skript. Fristående skriptet går igenom vilka förutsättningar kontroller som utförs av inställningarna för Azure-stacken Development Kit. Det ger ett sätt att bekräfta att du uppfyller kraven på maskinvara och programvara innan du hämtar större paketet för Azure-stacken Development Kit.
4. Under **ladda ned programvaran**, klickar du på **Azure Stack Development Kit**.

  > [!NOTE]
  > ASDK hämtningen (AzureStackDevelopmentKit.exe) är approximiately 10GB ensamt. Om du väljer att också ladda ned Windows Server 2016 utvärdering version ISO-filen ökar storleken till cirka 17GB. Du kan använda som ISO-filen för att skapa och lägga till en avbildning av virtuell dator för Windows Server 2016 Azure Stack Marketplace när ASDK installationen är klar. Observera att den här avbildningen för utvärdering av Windows Server 2016 kan endast användas med ASDK och regleras ASDK licensvillkoren.

5. När nedladdningen är klar klickar du på **kör** att starta ASDK Self-Extractor (AzureStackDevelopmentKit.exe).
6. Granska och godkänn licensavtalet visas från den **licensavtalet** i Self-Extractor guiden och klicka sedan på **nästa**.
7. Granska sekretessinformationen, som visas på den **viktigt meddelande** i Self-Extractor guiden och klicka sedan på **nästa**.
8. Välj platsen för installationsfilerna för Azure-stacken extraheras till på den **Välj målplats** i Self-Extractor guiden och klicka sedan på **nästa**. Standardplatsen är *aktuella mappen*\Azure Stack Development Kit. 
9. Granska sammanfattningen målplatsen den **redo att extrahera** i Self-Extractor guiden, och klicka sedan på **extrahera** att extrahera CloudBuilder.vhdx (cirka 25 GB) och ThirdPartyLicenses.rtf filer. Den här processen kommer ta lite tid att slutföra.
10. Kopiera eller flytta filen CloudBuilder.vhdx till roten på enhet C:\ (C:\CloudBuilder.vhdx) på värddatorn ASDK.

> [!NOTE]
> När du har extraherat filerna kan du ta bort den. EXE och. BIN-filer för återställning av utrymme på hårddisken. Eller, du kan säkerhetskopiera dessa filer så att du inte behöver hämta filerna igen om du behöver distribuera om ASDK.

## <a name="deploy-the-asdk-using-a-guided-experience"></a>Distribuera ASDK med en interaktiv upplevelse
ASDK kan distribueras med ett grafiskt användargränssnitt (GUI) som angavs genom att hämta och köra asdk installer.ps1 PowerShell-skript.

> [!NOTE]
> Användargränssnittet installationsprogram för Azure-stacken Development Kit är en öppen ursprung skript baserat på WCF- och PowerShell.

### <a name="prepare-the-development-kit-host-using-a-guided-user-experience"></a>Förbereda development kit värden med en interaktiv användarupplevelse
Innan du kan installera ASDK på värddatorn, måste du förbereda miljön ASDK.
1. Logga in som lokal administratör på datorn ASDK värden.
2. Kontrollera att filen CloudBuilder.vhdx har flyttats till roten på enhet C:\ (C:\CloudBuilder.vhdx).
3. Kör följande skript för att hämta installationsfilen för development kit (asdk installer.ps1) från den [Azure Stack GitHub verktyg databasen](https://github.com/Azure/AzureStack-Tools) till den **C:\AzureStack_Installer** mapp på din värddatorn för Development kit:

  ```powershell
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. Starta från en upphöjd PowerShell-konsol i **C:\AzureStack_Installer\asdk-installer.ps1** skript och klicka sedan på **förbereda miljön**.
5. På den **Välj Cloudbuilder vhdx** sidan i installationsprogrammet, bläddra till och välj den **cloudbuilder.vhdx** -fil som du hämtade och installerade i föregående steg. På den här sidan kan du också, om du vill kan du aktivera den **lägga till drivrutiner** kryssruta om du behöver lägga till ytterligare drivrutiner till värddatorn development kit.  
6. På den **valfria inställningar** anger det lokala administratörskontot för värddatorn development kit. 

  > [!IMPORTANT]
  > Om du inte anger dessa autentiseringsuppgifter måste du ha direkt eller KVM-åtkomst till värden efter omstart av datorn som en del av hur du konfigurerar i development kit.

7. Du kan även ange de här valfria inställningarna på den **valfria inställningar** sidan:
    - **ComputerName**: det här alternativet anger namn för development kit värden. Namnet måste uppfylla krav för FQDN och måste vara högst 15 tecken eller färre. Standardvärdet är ett slumpmässigt datornamn som genererats av Windows.
    - **Tidszon**: Anger tidszonen för development kit värden. Standardvärdet är (UTC-8:00) Stillahavstid (USA och Kanada).
    - **Statisk IP-konfiguration**: Anger distributionen för att använda en statisk IP-adress. När installationsprogrammet startar om i cloudbuilder.vhx, annars konfigureras nätverksgränssnitten med DHCP.
11. Klicka på **Nästa**.
12. Om du väljer en statisk IP-konfiguration i föregående steg, måste du nu:
    - Välj ett nätverkskort. Kontrollera att du kan ansluta till nätverkskortet innan du klickar på **nästa**.
    - Se till att den **IP-adress**, **Gateway**, och **DNS** värden är korrekta och klicka sedan på **nästa**.
13. Klicka på **nästa** att starta förberedelseprocessen.
14. När förberedelsen anger **slutförd**, klickar du på **nästa**.
15. Klicka på **starta om nu** starta i cloudbuilder.vhdx och fortsätta distributionsprocessen.


### <a name="deploy-the-development-kit-using-a-guided-experience"></a>Distribuera i development kit med en interaktiv upplevelse
När du har förberett ASDK värddatorn, kan ASDK distribueras till CloudBuilder.vhdx avbildningen med hjälp av följande steg. 
1. När värddatorn startar i CloudBuilder.vhdx avbildningen, logga in med administratörsbehörighet som anges i föregående steg. 
2. Öppna en upphöjd PowerShell-konsolen och kör den **\AzureStack_Installer\asdk-installer.ps1** skript (som kan vara på en annan enhet i CloudBuilder.vhdx bilden). Klicka på **Installera**.
3. I den **typen** listrutan, Välj **Azure-molnet** eller **AD FS**.
    - **Azure-molnet**: konfigurerar Azure Active Directory (AD Azure) som identitetsleverantören. Om du vill använda det här alternativet om du behöver en Internetanslutning, det fullständiga namnet på en Azure AD directory-klient i form av *domainname*. onmicrosoft.com eller en Azure AD verifiera domänen namn och en global administratörsautentiseringsuppgifter för den angiven katalog. 
    - **AD FS**: standard stämpeln katalogtjänsten kommer att användas som identitetsleverantören. Logga in med standardkontot är azurestackadmin@azurestack.local, och lösenordet är det du angav som en del av installationen.
4. Under **lokala administratörslösenordet**i den **lösenord** rutan skriver du det lokala administratörslösenordet (som måste matcha det aktuella konfigurerade lokala administratörslösenordet) och klicka sedan på **Nästa**.
5. Välj ett nätverkskort som ska användas för development kit och klicka sedan på **nästa**.
6. Välj DHCP eller statiska nätverkskonfigurationen för den virtuella datorn BGPNAT01.
    - **DHCP** (standard): den virtuella datorn hämtar den IP-konfigurationen från DHCP-servern.
    - **Statisk**: Använd bara det här alternativet om DHCP inte kan tilldela en giltig IP-adress för Azure stackutrymme för att få åtkomst till Internet. En statisk IP-adress måste anges med längden nätmask i CIDR-format (till exempel 10.0.0.5/24).
7. Du kan också ange följande värden:
    - **VLAN-ID**: Anger VLAN-ID. Använd bara det här alternativet om värden och AzS BGPNAT01 måste konfigurera VLAN-ID för att komma åt den fysiska nätverk (och internet). 
    - **DNS-vidarebefordrare**: en DNS-server har skapats som en del av distributionen av Azure-stacken. Ange din befintliga infrastruktur för DNS-server så att datorer i lösningen för att matcha namn utanför stämpeln. DNS-servern i stämpel vidarebefordras okänt namn på den här servern.
    - **Tidsserver**: här obligatoriska fältet anger tid-server som ska användas av development kit. Den här parametern måste anges som en giltig tid serverns IP-adress. Servernamn stöds inte.
  
  > [!TIP]
  > Du hittar en IP-adress [pool.ntp.org](http:\\pool.ntp.org) eller pinga time.windows.com. 
  
8. Klicka på **Nästa**. 
9. På den **Kontrollera egenskaper för nätverksgränssnitt kort** sidan visas en förloppsindikator. 
    - Om det står **kan inte hämtas en uppdatering**, följ instruktionerna på sidan.
    - När det står **slutförd**, klickar du på **nästa**.
10. På **sammanfattning** klickar du på **distribuera**. Här kan du också kopiera installationsprogrammet för PowerShell-kommandon som används för att installera development kit.
11. Om du använder en Azure AD-distribution, uppmanas du att ange dina autentiseringsuppgifter för Azure AD som global administratör konto några minuter efter installationen startar.
12. Distributionen kan ta några timmar, då systemet startar om automatiskt en gång. När distributionen lyckas PowerShell-konsolen visar: **Slutför: åtgärden 'Distribution'**. Om distributionen av misslyckas kan du [omdistribuera](azure-stack-redeploy.md) från grunden eller Använd följande PowerShell-kommandon, från samma upphöjd PowerShell-fönstret, starta om distributionen från det senaste lycka steget:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

   > [!IMPORTANT]
   > Om du vill övervaka förloppet för distributionen, logga in som azurestack\AzureStackAdmin när development kit värden startas om under installationen. Om du loggar in som lokal administratör när datorn är ansluten till domänen, visas inte förloppet för distributionen. Inte köra distribution, i stället logga in som azurestack\AzureStackAdmin att verifiera att den körs.
   

## <a name="deploy-the-asdk-using-powershell"></a>Distribuera ASDK med hjälp av PowerShell
De här stegen gått du igenom distribuera ASDK med en interaktiv användarupplevelse. Du kan också använda PowerShell för att distribuera ASDK på development kit värden genom att följa stegen i det här avsnittet.

### <a name="configure-the-asdk-host-computer-to-boot-from-cloudbuildervhdx"></a>Konfigurera värddatorn ASDK för start från CloudBuilder.vhdx
Dessa kommandon konfigurerar ASDK värddatorn för start från hämtade och extraherade Azure Stack virtuella inaktivitetsgränsen (CloudBuilder.vhdx). Starta om värddatorn ASDK när du har slutfört de här stegen.

1. Starta en kommandotolk som administratör.
2. Kör `bcdedit /copy {current} /d "Azure Stack"`
3. Kopiera (CTRL + C) CLSID-värdet returnerade, inklusive krävs {} ”s. Det här värdet kallas {CLSID} och behöver att kunna klistras in i (CTRL + V eller högerklicka) i stegen.
4. Kör `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
5. Kör `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
6. Kör `bcdedit /set {CLSID} detecthal on` 
7. Kör `bcdedit /default {CLSID}`
8. Om du vill verifiera startinställningarna, kör `bcdedit`. 
9. Se till att CloudBuilder.vhdx filen har flyttats till roten på enhet C:\ (C:\CloudBuilder.vhdx) och starta om värddatorn development kit. När ASDK värden har startats om den ska nu som standard startar från den virtuella datorn CloudBuilder.vhdx. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Förbereda development kit värden med hjälp av PowerShell 
När värddatorn development kit startar i CloudBuilder.vhdx avbildningen, du öppna en upphöjd PowerShell-konsolen och kör kommandona i det här avsnittet för att distribuera ASDK på development kit värden.

> [!IMPORTANT] 
> Installation av ASDK stöder exakt en nätverksgränssnittskortet (NIC) för nätverk. Om du har flera nätverkskort kan du kontrollera att endast en är aktiverat (och alla andra har inaktiverats) innan du kör skriptet för distribution.

Du kan distribuera Azure-stacken med Azure AD eller AD FS som identitetsleverantören. Fungerar på samma sätt med både Azure-stacken, resursproviders och andra program. Läs mer om vad som stöds med AD FS i Azure-stacken i den [viktiga funktioner begrepp](.\azure-stack-key-features.md) artikel.

> [!TIP]
> Om du inte anger någon installationsparametrar (se InstallAzureStackPOC.ps1 valfria parametrar och exemplen nedan), uppmanas du ange de obligatoriska parametrarna.

### <a name="deploy-azure-stack-using-azure-ad"></a>Distribuera Azure-stacken använder Azure AD 
Att distribuera Azure-stacken **använder Azure AD som identitetsleverantören**, måste du ha Internetanslutning direkt eller via en transparent proxy. Kör följande PowerShell-kommandon för att distribuera i development kit med Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

  Några minuter till ASDK installationen uppmanas du att autentiseringsuppgifter för Azure AD. Du måste ange autentiseringsuppgifter som global administratör för Azure AD-klienten. 

### <a name="deploy-azure-stack-using-ad-fs"></a>Distribuera Azure-stacken använder AD FS 
Att distribuera development kit **med AD FS som identitetsleverantören**, kör följande PowerShell-kommandon (du behöver bara lägga till parametern - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

I AD FS-distributioner används standard stämpeln katalogtjänsten som identitetsleverantören. Logga in med standardkontot är azurestackadmin@azurestack.local, och kommer att ange lösenordet du angav som en del av installationen PowerShell-kommandon.

Distributionen kan ta några timmar, under vilken tid systemet startar om automatiskt en gång. När distributionen lyckas PowerShell-konsolen visar: **Slutför: åtgärden 'Distribution'**. Om distributionen av misslyckas, kan du köra skriptet igen med - kör-parametern. Du kan [omdistribuera ASDK](.\azure-stack-redeploy.md) från början.
> [!IMPORTANT]
> Om du vill övervaka förloppet för distributionen efter ASDK värden startas om måste du logga in som AzureStack\AzureStackAdmin. Om du loggar in som lokal administratör när värddatorn har startats om (och ansluten till domänen azurestack.local), visas inte förloppet för distributionen. Inte köra distribution, i stället logga in som azurestack att verifiera att den körs.
>

#### <a name="azure-ad-deployment-script-examples"></a>Azure AD-distribution exempel på skript
Du kan skapa ett skript för hela Azure AD-distribution. Här följer några kommenterad exempel som innehåller vissa valfria parametrar.

Om din Azure AD-identitet är endast associerat med **en** Azure AD-katalog:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Om din Azure AD-identitet är associerat med **större än ett** Azure AD-katalog:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Om din miljö **inte** har DHCP aktiverat och sedan måste du inkludera följande ytterligare parametrar till något av alternativen ovan (exempel på användning tillhandahålls): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 valfria parametrar finns:
|Parameter|Nödvändiga/valfria|Beskrivning|
|-----|-----|-----|
|AdminPassword|Krävs|Anger det lokala administratörskontot och alla andra användarkonton för alla virtuella datorer som skapats som en del av development kit distribution. Lösenordet måste överensstämma med det aktuella lösenordet för lokal administratör på värden.|
|InfraAzureDirectoryTenantName|Krävs|Anger klientkatalogen. Använd den här parametern om du vill ange en specifik katalog där AAD-konto har behörighet att hantera flera kataloger. Fullständigt namn på en AAD Directory-klient i formatet. onmicrosoft.com eller en Azure AD verifierat domännamn.|
|TimeServer|Krävs|Använd den här parametern om du vill ange en viss tidpunkt-server. Den här parametern måste anges som en giltig tid serverns IP-adress. Servernamn stöds inte.|
|InfraAzureDirectoryTenantAdminCredential|Valfri|Anger det Azure Active Directory-användarnamn och lösenord. Dessa autentiseringsuppgifter för Azure måste vara ett Org ID.|
|InfraAzureEnvironment|Valfri|Välj den Azure-miljön som du vill registrera den här Azure Stack-distributionen. Till exempel offentlig Azure, Azure - Kina, Azure - som tillhör amerikanska myndigheter.|
|DNSForwarder|Valfri|En DNS-server har skapats som en del av distributionen av Azure-stacken. Ange din befintliga infrastruktur för DNS-server så att datorer i lösningen för att matcha namn utanför stämpeln. DNS-servern i stämpel vidarebefordras okänt namn på den här servern.|
|NatIPv4Address|Krävs för DHCP NAT-stöd|Anger en statisk IP-adress för MAS BGPNAT01. Använd enbart den här parametern om DHCP inte kan tilldela en giltig IP-adress för att få åtkomst till Internet.|
|NatIPv4Subnet|Krävs för DHCP NAT-stöd|IP-undernät prefix som används för DHCP över NAT-stöd. Använd enbart den här parametern om DHCP inte kan tilldela en giltig IP-adress för att få åtkomst till Internet.|
|PublicVlanId|Valfri|Anger VLAN-ID. Använd bara den här parametern om värden och MAS BGPNAT01 måste konfigurera VLAN-ID för att komma åt den fysiska nätverk (och Internet). For example, .\InstallAzureStackPOC.ps1 -Verbose -PublicVLan 305|
|Kör om|Valfri|Använd den här flaggan igen för distribution. Alla tidigare indata används. Återinföra informationen tidigare stöds inte eftersom flera unika värden genereras och används för distribution.|

## <a name="activate-the-administrator-and-tenant-portals"></a>Aktivera portaler administratör och klient
Du måste aktivera både Azure Stack-administratör och klienten portaler när distributioner som använder Azure AD. Den här aktiveringen samtycker till att ge Azure-Stack-portalen och Azure Resource Manager rätt behörigheter (som visas på sidan medgivande) för alla användare av katalogen.

- För administratörsportalen, navigera till https://adminportal.local.azurestack.external/guest/signup, Läs informationen och klicka sedan på **acceptera**. När du accepterar bör du lägga till tjänstadministratörer som inte är också directory innehavaradministratörer.

- Navigera till https://portal.local.azurestack.external/guest/signup för klientportalen, Läs informationen och klicka sedan på **acceptera**. När du accepterar, kan användare i katalogen logga in på klientportalen. 

> [!NOTE] 
> Om portalerna inte har aktiverat directory administratören logga in och använda portalerna. Om någon annan användare loggar in, visas ett fel som talar om att administratören inte har behörighet till andra användare. När administratören inte internt hör till den katalog som Azure-stacken är registrerad på måste Azure-stacken katalogen läggas till Aktiverings-URL. Till exempel om Azure-stacken är registrerad på fabrikam.onmicrosoft.com och Administratörsanvändare är admin@contoso.com, navigera till https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com att aktivera portalen. 

## <a name="reset-the-password-expiration-policy"></a>Återställ förfalloprincipen för lösenord 
Följ dessa steg om du vill kontrollera att lösenordet för development kit värden inte upphör att gälla innan din utvärdering avslutas, när du har distribuerat ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Ändra förfalloprincipen för lösenord från Powershell:
Kör kommandot från en upphöjd Powershell-konsolen:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Ändra förfalloprincipen för lösenord manuellt:
1. Öppna på development kit värden **Grupprinciphantering** och gå till **Grupprinciphantering** – **skog: azurestack.local** – **domäner** – **azurestack.local**.
2. Högerklicka på **Standarddomänprincip** och på **redigera**.
3. I den Redigeraren för Grupprinciphantering, navigera till **Datorkonfiguration** – **principer** – **Windowsinställningar** – **säkerhetsinställningar**– **Konto principer** – **lösenordsprincip**.
4. I den högra rutan, dubbelklickar du på **högsta ålder för lösenord**.
5. I den **högsta ålder för lösenord egenskaper** i dialogrutan den **lösenord upphör att gälla om** värde till 180 och klicka sedan på **OK**.


## <a name="next-steps"></a>Nästa steg

[Anslut till Azure Stack](azure-stack-connect-azure-stack.md)

[Konfigurera PowerShell för Azure Stack-miljöer](azure-stack-powershell-configure-quickstart.md)

[Registrera Azure stacken med din Azure-prenumeration](azure-stack-register.md)



