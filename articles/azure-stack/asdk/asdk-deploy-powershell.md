---
title: Distribuera Azure Stack – PowerShell | Microsoft Docs
description: I den här artikeln får installera du ASDK från kommandoraden med hjälp av PowerShell.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 09/10/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 09/10/2018
ms.openlocfilehash: 2513f397457c4866229605487149aa1fe03a2c68
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247739"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>Distribuera ASDK från kommandoraden
ASDK är en miljö för testning och utveckling som du kan distribuera för att utvärdera och demonstrera Azure Stack-funktioner och tjänster. Om du vill hämta den måste drift, att förbereda miljön maskinvara och köra några skript (det kan ta flera timmar). Efter det kan du logga in på portalerna administratörs- och att börja använda Azure Stack.

## <a name="prerequisites"></a>Förutsättningar 
Förbered värddatorn development kit. Planera din maskinvara, programvara och nätverk. Den dator som är värd för development kit (development kit host) måste uppfylla maskinvara, programvara och nätverkskrav. Du måste också välja mellan att använda Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS). Var noga med att följa dessa krav innan du påbörjar distributionen så att installationen körs smidigt. 

Innan du distribuerar ASDK måste du kontrollera att planerade development kit värddatorns maskinvara, operativsystem, konto och nätverkskonfigurationer uppfyller minimikraven för att installera ASDK.

**[Granska ASDK distributionen saker att tänka på](asdk-deploy-considerations.md)**

> [!TIP]
> Du kan använda den [distributionskrav för Azure Stack Kontrollera verktyget](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) när du har installerat operativsystemet för att bekräfta att maskinvaran uppfyller alla krav.

## <a name="download-and-extract-the-deployment-package"></a>Ladda ned och extrahera distributionspaketet
När du har säkerställt att värddatorn development kit uppfyller de grundläggande kraven för att installera ASDK, är nästa steg att ladda ned och extrahera ASDK distributionspaketet. Distributionspaketet innehåller filen Cloudbuilder.vhdx, som är en virtuell hårddisk som innehåller en startbar operativsystemet och Azure Stack-installationsfilerna.

Du kan hämta distributionspaketet till development kit värden eller till en annan dator. Extraherade distributionsfiler ta 60 GB ledigt diskutrymme, så att använda en annan dator kan minska maskinvarukraven för development kit värden.

**[Ladda ned och extrahera Azure Stack Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Förbereda värddatorn development kit
Innan du kan installera ASDK på värddatorn, måste du förbereda miljön och systemet konfigurerade för start från virtuell Hårddisk. Efter det här steget startas development kit värden med Cloudbuilder.vhdx (en virtuell hårddisk som innehåller en startbar operativsystemet och Azure Stack-installationsfiler).

Använd PowerShell för att konfigurera värddatorn ASDK CloudBuilder.vhdx för start. Dessa kommandon konfigurerar ASDK värddatorn för start från den hämtade och extraherade Azure Stack virtuella utrymme på (CloudBuilder.vhdx). Starta om värddatorn ASDK när du har slutfört de här stegen.

Konfigurera ASDK värddatorn för start från CloudBuilder.vhdx:

  1. Starta en kommandotolk som administratör.
  2. Kör `bcdedit /copy {current} /d "Azure Stack"`
  3. Kopiera (CTRL + C) CLSID-värdet returneras, inklusive de nödvändiga {}”s. Det här värdet kallas {CLSID} och behöver för att klistras in i (CTRL + V eller högerklicka) i de resterande stegen.
  4. Kör `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
  5. Kör `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. Kör `bcdedit /set {CLSID} detecthal on` 
  7. Kör `bcdedit /default {CLSID}`
  8. Kontrollera inställningar för klientstart med `bcdedit`. 
  9. Se till att CloudBuilder.vhdx filen har flyttats till roten C:\ enheten (C:\CloudBuilder.vhdx) och starta om värddatorn development kit. När värddatorn ASDK startas om, bör den startas från CloudBuilder.vhdx virtuella hårddisken till börjar ASDK distributionen. 

> [!IMPORTANT]
> Se till att du har direkt eller KVM-åtkomst till värddatorn development kit innan du startar om den. När den virtuella datorn startas första gången, uppmanas du att slutföra installationsprogrammet för Windows Server. Ange samma administratörsautentiseringsuppgifter som du använde för att logga in på värddatorn för development kit. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Förbereda development kit värden med hjälp av PowerShell 
När du har i development kit startar värddatorn i CloudBuilder.vhdx bilden och logga in med samma autentiseringsuppgifter för lokal administratör du använde för att logga in på värddatorn för development kit (och som du angav som en del av Slutför Windows Server Konfigurera när värddatorn startats från en virtuell Hårddisk). 

> [!NOTE]
> Du kan också konfigurera [Azure Stack-telemetriinställningar](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *innan* installerar ASDK.

Öppna en upphöjd PowerShell-konsol och köra kommandona i det här avsnittet för att distribuera ASDK på development kit värden.

> [!IMPORTANT] 
> ASDK installation har stöd för exakt ett nätverkskort (NIC) för nätverk. Om du har flera nätverkskort kan du kontrollera att endast ett är aktiverat (och alla andra har inaktiverats) innan du kör skriptet för distribution.

Du kan distribuera Azure Stack med Azure AD- eller Windows Server AD FS som identitetsleverantör. Fungerar på samma sätt med både Azure Stack, resursprovidrar och andra program.

> [!TIP]
> Om du inte anger några parametrar för installation (se InstallAzureStackPOC.ps1 valfria parametrar och exemplen nedan), uppmanas du de obligatoriska parametrarna.

### <a name="deploy-azure-stack-using-azure-ad"></a>Distribuera Azure Stack med hjälp av Azure AD 
Distribuera Azure Stack **med hjälp av Azure AD som identitetsleverantör**, du måste ha Internetanslutning antingen direkt eller via en transparent proxy. 

Kör följande PowerShell-kommandon för att distribuera i development kit med hjälp av Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Ett par minuter till ASDK installationen uppmanas du att Azure AD-autentiseringsuppgifter. Du måste ange autentiseringsuppgifterna för global administratör för Azure AD-klienten. 

Efter distributionen krävs inte behörighet för Azure Active Directory global administratör. Vissa åtgärder kan dock kräva autentiseringsuppgifter för global administratör. Till exempel ett resource provider installer skript eller en ny funktion som kräver en behörighet som ska beviljas. Du kan tillfälligt återställa kontots behörigheter som global administratör eller använda ett separat globalt administratörskonto som äger den *standard providerprenumeration*.

### <a name="deploy-azure-stack-using-ad-fs"></a>Distribuera Azure Stack med hjälp av AD FS 
Distribuera i development kit **med hjälp av AD FS som identitetsleverantör**, kör följande PowerShell-kommandon (du behöver bara lägga till parametern - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

I AD FS-distributioner används standard-stämpel katalogtjänst som identitetsleverantör. Logga in med standardkontot är azurestackadmin@azurestack.local, och lösenordet anges till vad du har angett som en del av installationen PowerShell-kommandon.

Distributionsprocessen kan ta några timmar, under tiden systemet startar om automatiskt en gång. När distributionen är klar visas PowerShell-konsolen: **SLUTFÖR: Åtgärden ”distribution”**. Om distributionen misslyckas, kan du köra skriptet igen med kör parametern-. Du kan också [omdistribuera ASDK](asdk-redeploy.md) från grunden.

> [!IMPORTANT]
> Om du vill övervaka förloppet för distributionen när ASDK värden om måste du logga in som AzureStack\AzureStackAdmin. Om du loggar in som lokal administratör när värddatorn har startats om (och anslutna till domänen azurestack.local), visas inte förloppet för distributionen. Inte köra distributionen, i stället logga in som azurestack att verifiera att den körs.


#### <a name="azure-ad-deployment-script-examples"></a>Exempelskript för Azure AD-distribution
Du kan skapa ett skript för hela Azure AD-distribution. Här följer några kommenterade exempel som innehåller vissa valfria parametrar.

Om din Azure AD-identitet är endast associerat med **en** Azure AD-katalog:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Om din Azure AD-identitet som är associerad med **större än ett** Azure AD-katalog:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Om din miljö inte har DHCP-aktiverat, måste du inkludera de här parametrarna till något av alternativen ovan (exempel på användning tillhandahålls): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 valfria parametrar
|Parameter|Krävs/valfritt|Beskrivning|
|-----|-----|-----|
|AdminPassword|Krävs|Anger det lokala administratörskontot och alla andra användarkonton för alla virtuella datorer som skapas som en del av development kit distribution. Det här lösenordet måste matcha det aktuella lösenordet för lokal administratör på värden.|
|InfraAzureDirectoryTenantName|Krävs|Anger klientkatalog. Använd den här parametern om du vill ange en viss katalog där AAD-kontot har behörighet att hantera flera kataloger. Fullständig namnet på en AAD-Directory-klient i formatet. onmicrosoft.com eller en Azure AD verifierat domännamn.|
|TimeServer|Krävs|Använd den här parametern om du vill ange en viss tidpunkt-server. Den här parametern måste anges som en giltig tid serverns IP-adress. Servernamn stöds inte.|
|InfraAzureDirectoryTenantAdminCredential|Valfri|Ställer in Azure Active Directory-användarnamn och lösenord. Dessa autentiseringsuppgifter för Azure måste vara ett Org ID.|
|InfraAzureEnvironment|Valfri|Välj Azure-miljön som du vill registrera den här Azure Stack-distributionen. Till exempel offentliga Azure, Azure - Kina, Azure - amerikanska myndigheter.|
|DNSForwarder|Valfri|En DNS-server skapas som en del av Azure Stack-distribution. Ange din befintliga infrastruktur för DNS-server så att datorer i lösningen att matcha namn utanför stämpeln. DNS-server i stämpel vidarebefordras okänt namn på den här servern.|
|NatIPv4Address|Krävs för DHCP NAT-support|Anger en statisk IP-adress för MAS-BGPNAT01. Använd enbart den här parametern om DHCP inte kan tilldela en giltig IP-adress för att få åtkomst till Internet.|
|NatIPv4Subnet|Krävs för DHCP NAT-support|IP-undernät prefix som används för DHCP över NAT-stöd. Använd enbart den här parametern om DHCP inte kan tilldela en giltig IP-adress för att få åtkomst till Internet.|
|PublicVlanId|Valfri|Anger VLAN-ID. Använd bara den här parametern om värden och MAS-BGPNAT01 måste konfigurera VLAN-ID för att komma åt den fysiska nätverk (och Internet). Till exempel.\InstallAzureStackPOC.ps1-Verbose - PublicVLan 305|
|Kör om|Valfri|Använd den här flaggan för att köra distributionen. Alla tidigare indata används. Återinföra data som tidigare tillhandahölls stöds inte eftersom flera unika värden genereras och används för distribution.|


## <a name="perform-post-deployment-configurations"></a>Utför efter distributionen konfigurationer
När du har installerat ASDK, finns det några rekommenderade efter installationen kontroller och konfigurationsändringar som ska göras. Du kan verifiera installationen har installerats med cmdleten test-AzureStack och installera verktyg för Azure Stack PowerShell och GitHub. 

Du bör också återställa förfalloprincipen för lösenord för att se till att lösenordet för development kit värden inte går ut innan din utvärderingsversion har upphört att gälla.

> [!NOTE]
> Du kan också konfigurera [Azure Stack-telemetriinställningar](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *när* installerar ASDK.

**[Publicera ASDK distributionsuppgifter](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrera med Azure
Du måste registrera Azure Stack med Azure så att du kan [ladda ned Azure marketplace-objekt](asdk-marketplace-item.md) till Azure Stack.

**[Registrera Azure Stack med Azure](asdk-register.md)**

## <a name="next-steps"></a>Nästa steg
Grattis! När du har slutfört de här stegen har du en utvecklingsmiljö kit med både [administratör](https://adminportal.local.azurestack.external) och [användaren](https://portal.local.azurestack.external) portaler. 

[Publicera konfigurationsuppgifter för ASDK installation](asdk-post-deploy.md)

