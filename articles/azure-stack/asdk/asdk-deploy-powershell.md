---
title: Distribuera Azure Stack - PowerShell | Microsoft Docs
description: "I kursen får installera du ASDK från kommandoraden."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 48ccccaba6b7f5780f1d42dfbe5d9747c5e30292
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-deploy-the-asdk-from-the-command-line"></a>Självstudier: distribuera ASDK från kommandoraden
I kursen får distribuera du Azure Stack Development Kit (ASDK) från kommandoraden i en produktionsmiljö. 

ASDK är en miljö för testning och utveckling som du kan distribuera om du vill utvärdera och visa Azure Stack-funktioner och tjänster. För att få måste igång, att förbereda miljön maskinvaran och köra några skript (det kan ta flera timmar). Därefter kan du logga in på portalerna administratörs- och att börja använda Azure-stacken.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Ladda ned och extrahera distributionspaketet
> * Förbereda värddatorn development kit 
> * Utför konfigurationer efter distributionen
> * Registrera med Azure

## <a name="prerequisites"></a>Förutsättningar 
Förbereda värddatorn development kit. Planera din maskinvara, programvara och nätverk. Den dator som är värd för development kit (development kit host) måste uppfylla nätverkskrav för maskinvara, programvara och. Du måste också välja mellan att använda Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS). Se till att uppfylla dessa krav innan du påbörjar distributionen så att installationen körs utan problem. 

Innan du distribuerar ASDK Kontrollera planerad development kit värddatorns maskinvara, operativsystem, konto och nätverkskonfigurationer uppfyller de lägsta kraven för att installera ASDK.

**[Granska överväganden vid planering av ASDK distributionen](asdk-deploy-considerations.md)**

> [!TIP]
> Du kan använda den [Azure Stack distributionskrav Kontrollera verktyget](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) när du har installerat operativsystemet för att bekräfta att maskinvaran uppfyller alla krav.

## <a name="download-and-extract-the-deployment-package"></a>Ladda ned och extrahera distributionspaketet
När du har kontrollerat att värddatorn development kit uppfyller de grundläggande kraven för att installera ASDK är nästa steg att ladda ned och extrahera ASDK distributionspaketet. Distributionspaketet innehåller filen Cloudbuilder.vhdx, som är en virtuell hårddisk som innehåller ett startbart operativsystem och Azure Stack-installationsfilerna.

Du kan hämta distributionspaketet för development kit värden eller en annan dator. De extraherade filerna tar upp 60 GB ledigt diskutrymme, så använder en annan dator kan minska maskinvarukraven för development kit värden.

**[Ladda ned och extrahera Azure Stack Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Förbereda värddatorn development kit
Innan du kan installera ASDK på värddatorn, måste du förbereda miljön och systemets konfigurerade för start från VHD. Efter det här steget startar development kit värden till Cloudbuilder.vhdx (en virtuell hårddisk som innehåller ett startbart operativsystem och installationsfilerna för Azure-stacken).

Använd PowerShell för att konfigurera värddatorn ASDK för start från CloudBuilder.vhdx. Dessa kommandon konfigurerar datorn ASDK värden för start från hämtade och extraherade Azure Stack virtuella inaktivitetsgränsen (CloudBuilder.vhdx). Starta om värddatorn ASDK när du har slutfört de här stegen.

Konfigurera värddatorn ASDK för start från CloudBuilder.vhdx:

  1. Starta en kommandotolk som administratör.
  2. Kör `bcdedit /copy {current} /d "Azure Stack"`
  3. Kopiera (CTRL + C) CLSID-värdet returnerade, inklusive krävs {} ”s. Det här värdet kallas {CLSID} och behöver att kunna klistras in i (CTRL + V eller högerklicka) i stegen.
  4. Kör `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
  5. Kör `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. Kör `bcdedit /set {CLSID} detecthal on` 
  7. Kör `bcdedit /default {CLSID}`
  8. Om du vill verifiera startinställningarna, kör `bcdedit`. 
  9. Se till att CloudBuilder.vhdx filen har flyttats till roten på enhet C:\ (C:\CloudBuilder.vhdx) och starta om värddatorn development kit. När värddatorn ASDK startas om, bör den startas från CloudBuilder.vhdx virtuella hårddisken ska börja ASDK distribution. 

> [!IMPORTANT]
> Kontrollera att du har direkt fysiska eller KVM-åtkomst till värddatorn development kit innan du startar om den. När den virtuella datorn först startas, uppmanas du att slutföra installationen av Windows Server. Ange samma administratörsautentiseringsuppgifter som du använde för att logga in på värddatorn development kit. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Förbereda development kit värden med hjälp av PowerShell 
Efter development kit startar värddatorn i CloudBuilder.vhdx bilden och logga in med samma autentiseringsuppgifter för lokal administratör du använde för att logga in på värddatorn development kit (och som du angav som en del av Slutför Windows Server Konfigurera när värddatorn startas från VHD). 

> [!NOTE]
> Du kan också konfigurera [Azure Stack telemetriinställningar](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *innan* installerar ASDK.

Öppna en upphöjd PowerShell-konsolen och kör kommandona i det här avsnittet för att distribuera ASDK på development kit värden.

> [!IMPORTANT] 
> Installation av ASDK stöder exakt en nätverksgränssnittskortet (NIC) för nätverk. Om du har flera nätverkskort kan du kontrollera att endast en är aktiverat (och alla andra har inaktiverats) innan du kör skriptet för distribution.

Du kan distribuera Azure-stacken med Azure AD- eller Windows Server AD FS som identitetsleverantören. Fungerar på samma sätt med både Azure-stacken, resursproviders och andra program.

> [!TIP]
> Om du inte anger någon installationsparametrar (se InstallAzureStackPOC.ps1 valfria parametrar och exemplen nedan) måste ombeds du de obligatoriska parametrarna.

### <a name="deploy-azure-stack-using-azure-ad"></a>Distribuera Azure-stacken använder Azure AD 
Att distribuera Azure-stacken **använder Azure AD som identitetsleverantören**, måste du ha Internetanslutning direkt eller via en transparent proxy. 

Kör följande PowerShell-kommandon för att distribuera i development kit med Azure AD:

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

Distributionen kan ta några timmar, under vilken tid systemet startar om automatiskt en gång. När distributionen lyckas PowerShell-konsolen visar: **Slutför: åtgärden 'Distribution'**. Om distributionen av misslyckas, kan du köra skriptet igen med - kör-parametern. Du kan [omdistribuera ASDK](asdk-redeploy.md) från början.

> [!IMPORTANT]
> Om du vill övervaka förloppet för distributionen efter ASDK värden startas om måste du logga in som AzureStack\AzureStackAdmin. Om du loggar in som lokal administratör när värddatorn har startats om (och ansluten till domänen azurestack.local), visas inte förloppet för distributionen. Inte köra distribution, i stället logga in som azurestack att verifiera att den körs.


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

Om miljön inte har DHCP aktiverat, måste du inkludera följande ytterligare parametrar till något av alternativen ovan (exempel på användning tillhandahålls): 

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


## <a name="perform-post-deployment-configurations"></a>Utför konfigurationer efter distributionen
När du har installerat ASDK finns några rekommenderade efter installationen kontroller och konfigurationsändringar som ska göras. Du kan verifiera installationen har installerats kan använda cmdleten test-AzureStack och installera Azure-stacken PowerShell- och GitHub. 

Du bör också återställa förfalloprincipen för lösenord för att se till att lösenordet för development kit värden inte upphör att gälla innan din utvärderingsversion har upphört att gälla.

> [!NOTE]
> Du kan också konfigurera [Azure Stack telemetriinställningar](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *när* installerar ASDK.

**[Bokför ASDK distributionsuppgifter](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrera med Azure
Du måste registrera Azure stacken med Azure så att du kan [hämta Azure marketplace-objekt](asdk-marketplace-item.md) till Azure-stacken.

**[Registrera Azure stacken med Azure](asdk-register.md)**

## <a name="next-steps"></a>Nästa steg
Grattis! När du slutfört de här stegen har du en development kit miljö med både [administratör](https://adminportal.local.azurestack.external) och [användaren](https://portal.local.azurestack.external) portaler. 

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ladda ned och extrahera distributionspaketet
> * Förbereda värddatorn development kit 
> * Utför konfigurationer efter distributionen
> * Registrera med Azure

Gå vidare till nästa kurs att lära dig hur du lägger till en Azure-stacken marketplace-objektet.

> [!div class="nextstepaction"]
> [Lägg till ett Azure-stacken marketplace-objekt](asdk-marketplace-item.md)

