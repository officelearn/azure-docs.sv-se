---
title: Anslut till Azure Stack med CLI | Microsoft Docs
description: Lär dig hur du använder plattformsoberoende kommandoradsgränssnittet (CLI) för att hantera och distribuera resurser i Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 21167366ff3af2bb360c33eaae9d591020bf11a5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487591"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Använd API-versionsprofiler med Azure CLI i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan följa stegen i den här artikeln för att ställa in Azure kommandoradsgränssnitt (CLI) att hantera resurser i Azure Stack Development Kit (ASDK) från Linux, Mac och Windows-klientplattformar.

## <a name="prepare-for-azure-cli"></a>Förbereda för Azure CLI

Du måste Certifikatutfärdarens rotcertifikat för Azure Stack du använder Azure CLI på din utvecklingsdator. Du kan använda certifikat för att hantera resurser via CLI.

 - **Azure Stack Certifikatutfärdarens rotcertifikat** krävs om du använder CLI från en dator utanför ASDK.  

 - **Virtuella datorns alias slutpunkt** innehåller ett alias, t.ex. ”UbuntuLTS” eller ”Win2012Datacenter” som refererar till en avbildningens utgivare, erbjudande, SKU och version som en parameter när du distribuerar virtuella datorer.  

I följande avsnitt beskrivs hur du hämtar dessa värden.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Exportera rotcertifikatet för Azure Stack

Om du använder ett integrerat system, behöver du inte exportera Certifikatutfärdarens rotcertifikat. Du måste du exportera Certifikatutfärdarens rotcertifikat på en ASDK.

Så här exporterar ASDK rotcertifikat i PEM-format:

1. [Skapa en Windows VM på Azure Stack](azure-stack-quick-windows-portal.md).

2. Logga in på datorn, öppna en upphöjd PowerShell-kommandotolk och kör sedan följande skript:

    ```powershell  
      $label = "AzureStackSelfSignedRootCert"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

3. Kopiera certifikatet till den lokala datorn.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Konfigurera virtuella datorns alias-slutpunkt

Du kan ställa in en offentligt tillgänglig slutpunkt som är värd för en virtuell dator alias-fil. Alias VHD-filen är en JSON-fil som innehåller ett eget namn för en bild. Du använder namnet när du distribuerar en virtuell dator som en parameter för Azure CLI.

1. Om du publicerar en anpassad avbildning anteckna utgivare, erbjudande, SKU och version informationen som du angav under publiceringen. Om det är en avbildning från marketplace kan du visa informationen med hjälp av den ```Get-AzureVMImage``` cmdlet.  

2. Ladda ned den [exempelfilen](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) från GitHub.

3. Skapa ett lagringskonto i Azure Stack. När det är klart kan du skapa en blobbehållare. Skapa princip för åtkomst till ”offentliga”.  

4. Ladda upp JSON-filen till den nya behållaren. När det är klart kan du visa blobens URL. Välj blob-namnet och sedan välja URL: en från blobegenskaper.

### <a name="install-or-upgrade-cli"></a>Installera eller uppgradera CLI

Logga in på utvecklingsdatorn och installera CLI. Azure Stack kräver version 2.0 eller senare av Azure CLI. Den senaste versionen av API-profiler kräver en aktuell version av CLI.  Du kan installera CLI med hjälp av stegen som beskrivs i den [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) artikeln. Kontrollera om installationen lyckades, öppna en terminal eller kommandotolk och kör följande kommando:

```shell
az --version
```

Du bör se versionen av Azure CLI och andra beroende bibliotek som är installerade på datorn.

### <a name="install-python-on-windows"></a>Installera Python på Windows

1. Installera [Python 3 på datorn](https://www.python.org/downloads/).

2. Uppgradera PIP. PIP är en pakethanterare för Python. Öppna en kommandotolk eller en förhöjd PowerShell och Skriv följande kommando:

    ```powershell  
    python -m pip install --upgrade pip
    ```

3. Installera den **certifi** modulen. [Certifi](https://pypi.org/project/certifi/) en modul och en samling av rotcertifikat för att verifiera trovärdigheten för SSL-certifikat vid verifiering av identiteten för TLS-värdar. Öppna en kommandotolk eller en förhöjd PowerShell och Skriv följande kommando:

    ```powershell
    pip install certifi
    ```

### <a name="install-python-on-linux"></a>Installera Python på Linux

1. Ubuntu 16.04 avbildningen har Python 2.7 och Python 3.5 installeras som standard. Du kan kontrollera din version av Python 3 genom att köra följande kommando:

    ```bash  
    python3 --version
    ```

2. Uppgradera PIP. PIP är en pakethanterare för Python. Öppna en kommandotolk eller en förhöjd PowerShell och Skriv följande kommando:

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Installera den **certifi** modulen. [Certifi](https://pypi.org/project/certifi/) är en samling rotcertifikat för att verifiera trovärdigheten för SSL-certifikat vid verifiering av identiteten för TLS-värdar. Öppna en kommandotolk eller en förhöjd PowerShell och Skriv följande kommando:

    ```bash
    pip3 install certifi
    ```

### <a name="install-python-on-macos"></a>Installera Python på macOS

1. Installera [Python 3 på datorn](https://www.python.org/downloads/). För Python 3.7 versioner innehåller Python.org två binära installationsalternativen för hämtning. Standard-variant är 64-bitars och fungerar på macOS 10.9 (Mavericks) och senare operativsystem. Kontrollera python-versionen genom att öppna terminalen och skriva följande kommando:

    ```bash  
    python3 --version
    ```

2. Uppgradera PIP. PIP är en pakethanterare för Python. Öppna en kommandotolk eller en förhöjd PowerShell och Skriv följande kommando:

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. Installera den **certifi** modulen. [Certifi](https://pypi.org/project/certifi/) en modul och en samling av rotcertifikat för att verifiera trovärdigheten för SSL-certifikat vid verifiering av identiteten för TLS-värdar. Öppna en kommandotolk eller en förhöjd PowerShell och Skriv följande kommando:

    ```bash
    pip3 install certifi
    ```

## <a name="windows-azure-ad"></a>Windows (Azure AD)

Det här avsnittet vägleder dig genom hur du konfigurerar CLI om du använder Azure AD som identity management-tjänsten och använder CLI på en Windows-dator.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Lita på Azure Stack Certifikatutfärdarens rotcertifikat

Om du använder ASDK behöver ska lita på Certifikatutfärdarens rotcertifikat på din fjärrdatorn. Du behöver inte göra detta med integrerade system.

Om du vill lita på rotcertifikatet för Azure Stack-CA, lägger du till dem i det befintliga certifikatet för Python.

1. Hitta certifikatsplats på din dator. Platsen kan variera beroende på var du har installerat Python. Öppna en kommandotolk eller en förhöjd PowerShell och Skriv följande kommando:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Anteckna certifikatsplatsen. Till exempel `~/lib/python3.5/site-packages/certifi/cacert.pem`. Viss sökvägen beror på ditt operativsystem och vilken version av Python som du har installerat.

2. Lita på Azure Stack Certifikatutfärdarens rotcertifikat genom att den läggs till det befintliga certifikatet för Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

1. Registrera Azure Stack-miljön genom att köra den `az cloud register` kommando.

    I vissa situationer kan dirigeras direkt utgående internet-anslutning via en proxy eller brandvägg som framtvingar SSL internetkommunikationer. I dessa fall kan den `az cloud register` kommando kan misslyckas med ett fel, till exempel ”det går inte att hämta slutpunkter från molnet”. Undvik det här felet, kan du ange följande miljövariabler:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Registrera din miljö. Använd följande parametrar när du kör `az cloud register`.

    | Värde | Exempel | Beskrivning |
    | --- | --- | --- |
    | Miljönamn | AzureStackUser | Använd `AzureStackUser` för användarmiljön. Om du är operatör kan du ange `AzureStackAdmin`. |
    | Resource Manager-slutpunkten | https://management.local.azurestack.external | Den **ResourceManagerUrl** i Azure Stack Development Kit (ASDK) är: `https://management.local.azurestack.external/` Den **ResourceManagerUrl** integrerade system är: `https://management.<region>.<fqdn>/` Att hämta de metadata som krävs: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Om du har en fråga om integrerade system-slutpunkten kan du kontakta din molnoperator. |
    | Slutpunkt för lagring | local.azurestack.external | `local.azurestack.external` avser ASDK. För ett integrerat system, kommer du att använda en slutpunkt för ditt system.  |
    | Keyvalut suffix | .vault.local.azurestack.external | `.vault.local.azurestack.external` avser ASDK. För ett integrerat system, kommer du att använda en slutpunkt för ditt system.  |
    | VM-avbildning alias doc-slutpunkt- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI för det dokument som innehåller VM-avbildning alias. Mer information finns i [### Konfigurera virtuella datorns alias slutpunkt](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Konfigurera active miljön med hjälp av följande kommandon.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Uppdatera din miljökonfiguration om du vill använda Azure Stack-profil för versionen av specifika API: et. Uppdatera konfigurationen genom att köra följande kommando:

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Om du kör en version av Azure Stack innan 1808 versionen, måste du använda profilen för API-version **2017-03-09-profile** i stället för API-version profilen **2018-03-01-hybrid**. Du måste använda den senaste versionen av Azure CLI.
 
1. Logga in på Azure Stack-miljön med hjälp av den `az login` kommando. Du kan logga in på Azure Stack-miljön som en användare eller som en [tjänstens huvudnamn](../../active-directory/develop/app-objects-and-service-principals.md). 

   - Logga in som en *användaren*: 

     Du kan antingen ange användarnamnet och lösenordet direkt inom den `az login` kommandot eller autentisera med hjälp av en webbläsare. Om ditt konto har aktiverat multifaktorautentisering måste du göra det senare:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Om ditt konto har aktiverat multifaktorautentisering, kan du använda den `az login` kommandot utan att ange den `-u` parametern. Kör det här kommandot ger dig en URL och en kod som du måste använda för att autentisera.

   - Logga in som en *tjänstens huvudnamn*: 
    
     Innan du loggar in, [skapa ett huvudnamn för tjänsten via Azure portal](azure-stack-create-service-principals.md) eller CLI och tilldela den till en roll. Nu kan logga in med hjälp av följande kommando:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testa anslutningen

Konfigurera med allt, Använd CLI för att skapa resurser i Azure Stack. Du kan till exempel skapa en resursgrupp för ett program och lägga till en virtuell dator. Använd följande kommando för att skapa en resursgrupp med namnet ”MyResourceGroup”:

```azurecli
az group create -n MyResourceGroup -l local
```

Om resursgruppen har skapats, visar följande egenskaper för den nyligen skapade resursen i det föregående kommandot:

![Resursgrupp skapa utdata](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

Det här avsnittet vägleder dig genom hur du konfigurerar CLI om du använder Active Directory Federation Services (AD FS) som identity management-tjänsten och använder CLI på en Windows-dator.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Lita på Azure Stack Certifikatutfärdarens rotcertifikat

Om du använder ASDK behöver ska lita på Certifikatutfärdarens rotcertifikat på din fjärrdatorn. Du behöver inte göra detta med integrerade system.

1. Hitta certifikatsplats på din dator. Platsen kan variera beroende på var du har installerat Python. Öppna en kommandotolk eller en förhöjd PowerShell och Skriv följande kommando:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Anteckna certifikatsplatsen. Till exempel `~/lib/python3.5/site-packages/certifi/cacert.pem`. Viss sökvägen beror på ditt operativsystem och vilken version av Python som du har installerat.

2. Lita på Azure Stack Certifikatutfärdarens rotcertifikat genom att den läggs till det befintliga certifikatet för Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

1. Registrera Azure Stack-miljön genom att köra den `az cloud register` kommando.

    I vissa situationer kan dirigeras direkt utgående internet-anslutning via en proxy eller brandvägg som framtvingar SSL internetkommunikationer. I dessa fall kan den `az cloud register` kommando kan misslyckas med ett fel, till exempel ”det går inte att hämta slutpunkter från molnet”. Undvik det här felet, kan du ange följande miljövariabler:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Registrera din miljö. Använd följande parametrar när du kör `az cloud register`.

    | Värde | Exempel | Beskrivning |
    | --- | --- | --- |
    | Miljönamn | AzureStackUser | Använd `AzureStackUser` för användarmiljön. Om du är operatör kan du ange `AzureStackAdmin`. |
    | Resource Manager-slutpunkten | https://management.local.azurestack.external | Den **ResourceManagerUrl** i Azure Stack Development Kit (ASDK) är: `https://management.local.azurestack.external/` Den **ResourceManagerUrl** integrerade system är: `https://management.<region>.<fqdn>/` Att hämta de metadata som krävs: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Om du har en fråga om integrerade system-slutpunkten kan du kontakta din molnoperator. |
    | Slutpunkt för lagring | local.azurestack.external | `local.azurestack.external` avser ASDK. För ett integrerat system, kommer du att använda en slutpunkt för ditt system.  |
    | Keyvalut suffix | .vault.local.azurestack.external | `.vault.local.azurestack.external` avser ASDK. För ett integrerat system, kommer du att använda en slutpunkt för ditt system.  |
    | VM-avbildning alias doc-slutpunkt- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI för det dokument som innehåller VM-avbildning alias. Mer information finns i [### Konfigurera virtuella datorns alias slutpunkt](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Konfigurera active miljön med hjälp av följande kommandon.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Uppdatera din miljökonfiguration om du vill använda Azure Stack-profil för versionen av specifika API: et. Uppdatera konfigurationen genom att köra följande kommando:

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Om du kör en version av Azure Stack innan 1808 versionen, måste du använda profilen för API-version **2017-03-09-profile** i stället för API-version profilen **2018-03-01-hybrid**. Du måste använda den senaste versionen av Azure CLI.

1. Logga in på Azure Stack-miljön med hjälp av den `az login` kommando. Du kan logga in på Azure Stack-miljön som en användare eller som en [tjänstens huvudnamn](../../active-directory/develop/app-objects-and-service-principals.md). 

   - Logga in som en *användaren*:

     Du kan antingen ange användarnamnet och lösenordet direkt inom den `az login` kommandot eller autentisera med hjälp av en webbläsare. Om ditt konto har aktiverat multifaktorautentisering måste du göra det senare:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/" --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2018-03-01-hybrid"
     ```

     > [!NOTE]
     > Om ditt konto har aktiverat multifaktorautentisering, kan du använda den `az login` kommandot utan att ange den `-u` parametern. Kör det här kommandot ger dig en URL och en kod som du måste använda för att autentisera.

   - Logga in som en *tjänstens huvudnamn*: 
    
     Förbered PEM-filen som ska användas för huvudsaklig inloggning på tjänsten.

     Exportera tjänstobjektscertifikatet på klientdatorn där huvudkontot som har skapats, som en pfx med den privata nyckeln som finns på `cert:\CurrentUser\My`; cert namn har samma namn som huvudnamnet.

     Konvertera pfx till pem (Använd verktyget OpenSSL).

     Logga in på CLI:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>Testa anslutningen

Konfigurera med allt, Använd CLI för att skapa resurser i Azure Stack. Du kan till exempel skapa en resursgrupp för ett program och lägga till en virtuell dator. Använd följande kommando för att skapa en resursgrupp med namnet ”MyResourceGroup”:

```azurecli
az group create -n MyResourceGroup -l local
```

Om resursgruppen har skapats, visar följande egenskaper för den nyligen skapade resursen i det föregående kommandot:

![Resursgrupp skapa utdata](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Det här avsnittet vägleder dig genom hur du konfigurerar CLI om du använder Azure AD som identity management-tjänsten och använder CLI på en Linux-dator.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Lita på Azure Stack Certifikatutfärdarens rotcertifikat

Om du använder ASDK behöver ska lita på Certifikatutfärdarens rotcertifikat på din fjärrdatorn. Du behöver inte göra detta med integrerade system.

Lita på Azure Stack Certifikatutfärdarens rotcertifikat genom att den läggs till det befintliga certifikatet för Python.

1. Hitta certifikatsplats på din dator. Platsen kan variera beroende på var du har installerat Python. Du måste ha pip och certifi [-modulen installerad](#install-python-on-linux). Du kan använda följande Python-kommandot från bash-Kommandotolken:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Anteckna certifikatsplatsen; till exempel `~/lib/python3.5/site-packages/certifi/cacert.pem`. Din specifika sökväg beror på operativsystemet och versionen av Python som du har installerat.

2. Kör följande bash-kommando med sökvägen till ditt certifikat.

   - För en fjärransluten Linux-dator:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - För en Linux-dator i Azure Stack-miljön:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

Använd följande steg för att ansluta till Azure Stack:

1. Registrera Azure Stack-miljön genom att köra den `az cloud register` kommando. I vissa situationer kan dirigeras direkt utgående internet-anslutning via en proxy eller brandvägg som framtvingar SSL internetkommunikationer. I dessa fall kan den `az cloud register` kommando kan misslyckas med ett fel, till exempel ”det går inte att hämta slutpunkter från molnet”. Undvik det här felet, kan du ange följande miljövariabler:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Registrera din miljö. Använd följande parametrar när du kör `az cloud register`.

    | Värde | Exempel | Beskrivning |
    | --- | --- | --- |
    | Miljönamn | AzureStackUser | Använd `AzureStackUser` för användarmiljön. Om du är operatör kan du ange `AzureStackAdmin`. |
    | Resource Manager-slutpunkten | https://management.local.azurestack.external | Den **ResourceManagerUrl** i Azure Stack Development Kit (ASDK) är: `https://management.local.azurestack.external/` Den **ResourceManagerUrl** integrerade system är: `https://management.<region>.<fqdn>/` Att hämta de metadata som krävs: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Om du har en fråga om integrerade system-slutpunkten kan du kontakta din molnoperator. |
    | Slutpunkt för lagring | local.azurestack.external | `local.azurestack.external` avser ASDK. För ett integrerat system, kommer du att använda en slutpunkt för ditt system.  |
    | Keyvalut suffix | .vault.local.azurestack.external | `.vault.local.azurestack.external` avser ASDK. För ett integrerat system, kommer du att använda en slutpunkt för ditt system.  |
    | VM-avbildning alias doc-slutpunkt- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI för det dokument som innehåller VM-avbildning alias. Mer information finns i [### Konfigurera virtuella datorns alias slutpunkt](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Ange aktiv miljö. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Uppdatera din miljökonfiguration om du vill använda Azure Stack-profil för versionen av specifika API: et. Uppdatera konfigurationen genom att köra följande kommando:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Om du kör en version av Azure Stack innan 1808 versionen, måste du använda profilen för API-version **2017-03-09-profile** i stället för API-version profilen **2018-03-01-hybrid**. Du måste använda den senaste versionen av Azure CLI.

5. Logga in på Azure Stack-miljön med hjälp av den `az login` kommando. Du kan logga in på Azure Stack-miljön som en användare eller som en [tjänstens huvudnamn](../../active-directory/develop/app-objects-and-service-principals.md). 

   * Logga in som en *användaren*:

     Du kan antingen ange användarnamnet och lösenordet direkt inom den `az login` kommandot eller autentisera med hjälp av en webbläsare. Om ditt konto har aktiverat multifaktorautentisering måste du göra det senare:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Om ditt konto har aktiverat multifaktorautentisering, kan du använda den `az login` kommandot utan att ange den `-u` parametern. Kör det här kommandot ger dig en URL och en kod som du måste använda för att autentisera.
   
   * Logga in som en *tjänstens huvudnamn*
    
     Innan du loggar in, [skapa ett huvudnamn för tjänsten via Azure portal](azure-stack-create-service-principals.md) eller CLI och tilldela den till en roll. Nu kan logga in med hjälp av följande kommando:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testa anslutningen

Konfigurera med allt, Använd CLI för att skapa resurser i Azure Stack. Du kan till exempel skapa en resursgrupp för ett program och lägga till en virtuell dator. Använd följande kommando för att skapa en resursgrupp med namnet ”MyResourceGroup”:

```azurecli
    az group create -n MyResourceGroup -l local
```

Om resursgruppen har skapats, visar följande egenskaper för den nyligen skapade resursen i det föregående kommandot:

![Resursgrupp skapa utdata](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

Det här avsnittet vägleder dig genom hur du konfigurerar CLI om du använder Active Directory Federation Services (AD FS) som management-tjänsten och använder CLI på en Linux-dator.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Lita på Azure Stack Certifikatutfärdarens rotcertifikat

Om du använder ASDK behöver ska lita på Certifikatutfärdarens rotcertifikat på din fjärrdatorn. Du behöver inte göra detta med integrerade system.

Lita på Azure Stack Certifikatutfärdarens rotcertifikat genom att den läggs till det befintliga certifikatet för Python.

1. Hitta certifikatsplats på din dator. Platsen kan variera beroende på var du har installerat Python. Du måste ha pip och certifi [-modulen installerad](#install-python-on-linux). Du kan använda följande Python-kommandot från bash-Kommandotolken:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Anteckna certifikatsplatsen; till exempel `~/lib/python3.5/site-packages/certifi/cacert.pem`. Din specifika sökväg beror på operativsystemet och versionen av Python som du har installerat.

2. Kör följande bash-kommando med sökvägen till ditt certifikat.

   - För en fjärransluten Linux-dator:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - För en Linux-dator i Azure Stack-miljön:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

Använd följande steg för att ansluta till Azure Stack:

1. Registrera Azure Stack-miljön genom att köra den `az cloud register` kommando. I vissa situationer kan dirigeras direkt utgående internet-anslutning via en proxy eller brandvägg som framtvingar SSL internetkommunikationer. I dessa fall kan den `az cloud register` kommando kan misslyckas med ett fel, till exempel ”det går inte att hämta slutpunkter från molnet”. Undvik det här felet, kan du ange följande miljövariabler:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Registrera din miljö. Använd följande parametrar när du kör `az cloud register`.

    | Värde | Exempel | Beskrivning |
    | --- | --- | --- |
    | Miljönamn | AzureStackUser | Använd `AzureStackUser` för användarmiljön. Om du är operatör kan du ange `AzureStackAdmin`. |
    | Resource Manager-slutpunkten | https://management.local.azurestack.external | Den **ResourceManagerUrl** i Azure Stack Development Kit (ASDK) är: `https://management.local.azurestack.external/` Den **ResourceManagerUrl** integrerade system är: `https://management.<region>.<fqdn>/` Att hämta de metadata som krävs: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Om du har en fråga om integrerade system-slutpunkten kan du kontakta din molnoperator. |
    | Slutpunkt för lagring | local.azurestack.external | `local.azurestack.external` avser ASDK. För ett integrerat system, kommer du att använda en slutpunkt för ditt system.  |
    | Keyvalut suffix | .vault.local.azurestack.external | `.vault.local.azurestack.external` avser ASDK. För ett integrerat system, kommer du att använda en slutpunkt för ditt system.  |
    | VM-avbildning alias doc-slutpunkt- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | URI för det dokument som innehåller VM-avbildning alias. Mer information finns i [### Konfigurera virtuella datorns alias slutpunkt](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Ange aktiv miljö. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Uppdatera din miljökonfiguration om du vill använda Azure Stack-profil för versionen av specifika API: et. Uppdatera konfigurationen genom att köra följande kommando:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Om du kör en version av Azure Stack innan 1808 versionen, måste du använda profilen för API-version **2017-03-09-profile** i stället för API-version profilen **2018-03-01-hybrid**. Du måste använda den senaste versionen av Azure CLI.

5. Logga in på Azure Stack-miljön med hjälp av den `az login` kommando. Du kan logga in på Azure Stack-miljön som en användare eller som en [tjänstens huvudnamn](../../active-directory/develop/app-objects-and-service-principals.md). 

6. Logga in: 

   *  Som en **användaren** via en webbläsare med en kod för enheten:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >Kör kommandot ger dig en URL och en kod som du måste använda för att autentisera.

   * Som ett huvudnamn för tjänsten:
        
     Förbered PEM-filen som ska användas för huvudsaklig inloggning på tjänsten.

      * Exportera tjänstobjektscertifikatet på klientdatorn där huvudkontot som har skapats, som en pfx med den privata nyckeln som finns på `cert:\CurrentUser\My`; cert namn har samma namn som huvudnamnet.
  
      * Konvertera pfx till pem (Använd verktyget OpenSSL).

     Logga in på CLI:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>Testa anslutningen

Konfigurera med allt, Använd CLI för att skapa resurser i Azure Stack. Du kan till exempel skapa en resursgrupp för ett program och lägga till en virtuell dator. Använd följande kommando för att skapa en resursgrupp med namnet ”MyResourceGroup”:

```azurecli
  az group create -n MyResourceGroup -l local
```

Om resursgruppen har skapats, visar följande egenskaper för den nyligen skapade resursen i det föregående kommandot:

![Resursgrupp skapa utdata](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Kända problem

Det finns kända problem när du använder CLI i Azure Stack:

 - Interaktivt läge CLI; till exempel den `az interactive` kommandot, stöds inte ännu i Azure Stack.
 - Hämta listan över avbildningar av virtuella datorer som är tillgängliga i Azure Stack med den `az vm image list --all` kommandot i stället för den `az vm image list` kommando. Anger den `--all` alternativet ser du till att svaret returnerar endast de avbildningar som är tillgängliga i Azure Stack-miljön.
 - VM-avbildning alias som är tillgängliga i Azure kan inte tillämpas på Azure Stack. När du använder avbildningar av virtuella datorer, måste du använda parametern hela URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) i stället för alias för avbildningen. Den här URN måste matcha bild-specifikationer som härletts från den `az vm images list` kommando.

## <a name="next-steps"></a>Nästa steg

- [Distribuera mallar med Azure CLI](azure-stack-deploy-template-command-line.md)
- [Aktivera Azure CLI för Azure Stack-användare (operatorn)](../azure-stack-cli-admin.md)
- [Hantera användarbehörigheter](azure-stack-manage-permissions.md) 
