---
title: Ansluta till Azure-stacken med CLI | Microsoft Docs
description: "Lär dig hur du använder plattformsoberoende kommandoradsgränssnittet (CLI) för att hantera och distribuera resurser i Azure-stacken"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: sngun
ms.openlocfilehash: 1cfbe988d881075d1a7bfc7513fbe5f44a531abd
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>Installera och konfigurera CLI för användning med Azure-stacken

I den här artikeln hjälper vi dig under processen för att använda Azure-kommandoradsgränssnittet (CLI) för att hantera Azure-stacken Development Kit resurser från Linux och Mac-klientplattformar. 

## <a name="install-cli"></a>Installera CLI

Sedan loggar in på utvecklingsdatorn och installera CLI. Azure-stacken kräver version 2.0 av Azure CLI. Du kan installera som med hjälp av stegen som beskrivs i den [installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) artikel. Öppna en terminal eller Kommandotolken för att verifiera om installationen har lyckats, och kör följande kommando:

```azurecli
az --version
```

Du bör se versionen av Azure CLI och andra beroende bibliotek som är installerade på datorn.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Lita på Azure-stacken Certifikatutfärdarens rotcertifikat

Hämta Azure Stack Certifikatutfärdarens rotcertifikat från Azure Stack-operatorn och litar på den. Om du vill lita på Azure-stacken Certifikatutfärdarens rotcertifikat, lägger du till dem i det befintliga certifikatet Python. Om du kör CLI från en Linux-dator som har skapats i Azure Stack-miljön, kör du kommandot bash:

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

Om du kör CLI från en dator utanför Azure säckformat-miljön, måste du först ställa in [VPN-anslutning till Azure-stacken](azure-stack-connect-azure-stack.md). Nu kopiera PEM-certifikatet som du exporterade tidigare på utvecklingsdatorn och kör följande kommandon, beroende på din arbetsstation development OS.

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path root.pem -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Hämta alias virtuell datorslutpunkt

Innan användare kan skapa virtuella datorer med hjälp av CLI, måste de kontakta Azure Stack-operatorn och hämta virtuella datorn alias slutpunkten URI. Till exempel Azure använder du följande URI: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. Molnadministratören bör ställa in en liknande slutpunkt för Azure-stacken med bilder som är tillgängliga i stacken för Azure marketplace. Användare behöver skicka slutpunkten URI till den `endpoint-vm-image-alias-doc` parametern till den `az cloud register` som visas i nästa avsnitt. 
   

## <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

Använd följande steg för att ansluta till Azure Stack:

1. Registrera Azure Stack-miljö genom att köra den `az cloud register` kommando.
   
   a. Att registrera den *moln administrativa* miljö, Använd:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. Att registrera den *användaren* miljö, Använd:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

2. Ange den aktiva miljön med hjälp av följande kommandon.

   a. För den *moln administrativa* miljö, Använd:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. För den *användaren* miljö, Använd:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

3. Uppdatera din miljö konfiguration om du vill använda Azure Stack API-version profilen. Uppdatera konfigurationen genom att köra följande kommando:

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. Logga in på Azure Stack-miljö med hjälp av den `az login` kommando. Du kan logga in på Azure Stack-miljö som en användare eller som en [tjänstens huvudnamn](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects). 

   * Logga in som en *användare*: du kan antingen ange användarnamn och lösenord direkt i den `az login` kommando eller autentisera med hjälp av en webbläsare. Du behöver göra det senare om ditt konto har multifaktorautentisering aktiveras.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Om ditt användarkonto har multifaktorautentisering aktiverad, kan du använda den `az login command` utan att ange den `-u` parameter. Kör kommandot ger dig en URL och en kod som du måste använda för att autentisera.
   
   * Logga in som en *tjänstens huvudnamn*: innan du loggar in, [skapa ett huvudnamn för tjänsten via Azure portal](azure-stack-create-service-principals.md) eller CLI och tilldela den en roll. Nu kan logga in med hjälp av följande kommando:

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>Testa anslutningen

Nu när vi har allt installationsprogrammet, ska vi använda CLI för att skapa resurser i Azure-stacken. Du kan till exempel skapa en resursgrupp för ett program och lägga till en virtuell dator. Använd följande kommando för att skapa en resursgrupp med namnet ”MyResourceGroup”:

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Om resursgruppen har skapats, utdata följande egenskaper för den nyligen skapade resursen i det föregående kommandot:

![Resursgruppen skapa utdata](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Kända problem
Det finns några kända problem som du måste vara medveten om när du använder CLI i Azure Stack:

* CLI interaktivt läge engångsfaktorautentisering den `az interactive` kommandot stöds inte ännu i Azure-stacken.
* Om du vill hämta listan över tillgängliga i Azure-stacken avbildningar av virtuella datorer använder den `az vm images list --all` kommandot i stället för den `az vm image list` kommando. Ange den `--all` alternativet ser till att svaret returnerar bara de avbildningar som är tillgängliga i Azure Stack-miljö. 
* Virtuella avbildningen alias som är tillgängliga i Azure kan inte tillämpas på Azure-stacken. När du använder avbildningar av virtuella datorer, måste du använda parametern hela URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) i stället för det bild aliaset. Den här URN måste matcha avbildningen specifikationer som härletts från den `az vm images list` kommando.
* Som standard använder CLI 2.0 ”Standard_DS1_v2” som standardstorlek för avbildning av virtuell dator. Men den här storleken inte är tillgängligt i Azure-stacken, så du måste ange den `--size` explicit när du skapar en virtuell dator. Du kan hämta listan över storlekar för virtuella datorer som är tillgängliga i Azure-stacken genom att använda den `az vm list-sizes --location <locationName>` kommando.


## <a name="next-steps"></a>Nästa steg

[Distribuera mallar med Azure CLI](azure-stack-deploy-template-command-line.md)

[Hantera användarbehörigheter](azure-stack-manage-permissions.md)

