---
title: Anslut till Azure Stack med CLI | Microsoft Docs
description: Lär dig hur du använder plattformsoberoende kommandoradsgränssnittet (CLI) för att hantera och distribuera resurser i Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 40973fbdd1965eb84776fc9365718c65fa0149a7
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416997"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Använd API-versionsprofiler med Azure CLI i Azure Stack

Du kan följa stegen i den här artikeln för att ställa in Azure kommandoradsgränssnitt (CLI) att hantera Azure Stack Development Kit-resurser från Linux, Mac och Windows-klientplattformar.

## <a name="install-cli"></a>Installera CLI

Logga in på utvecklingsdatorn och installera CLI. Azure Stack kräver version 2.0 eller senare av Azure CLI. Du kan installera den här versionen med hjälp av stegen som beskrivs i den [installera Azure CLI](/cli/azure/install-azure-cli) artikeln. Kontrollera om installationen lyckades, öppna en terminal eller kommandotolk och kör följande kommando:

```azurecli
az --version
```

Du bör se versionen av Azure CLI och andra beroende bibliotek som är installerade på datorn.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Lita på Azure Stack Certifikatutfärdarens rotcertifikat

1. Hämta Azure Stack Certifikatutfärdarens rotcertifikat från [Azure Stack-operatör](../azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) och lita på den. Om du vill lita på rotcertifikatet för Azure Stack-CA, lägger du till dem i det befintliga certifikatet för Python.

1. Hitta certifikatsplats på din dator. Platsen kan variera beroende på var du har installerat Python. Du måste ha [pip](https://pip.pypa.io) och [certifi](https://pypi.org/project/certifi/) -modulen installerad. Du kan använda följande Python-kommandot från bash-Kommandotolken:

    ```bash  
    python -c "import certifi; print(certifi.where())"
    ```

    Anteckna certifikatsplatsen; till exempel `~/lib/python3.5/site-packages/certifi/cacert.pem`. Din specifika sökväg beror på operativsystemet och versionen av Python som du har installerat.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>Ange sökvägen för en utvecklingsdator i molnet

Om du kör CLI från en Linux-dator som har skapats i Azure Stack-miljön, kör följande kommando för bash med sökvägen till ditt certifikat.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>Ange sökväg för en utvecklingsdator utanför molnet

Om du kör CLI från en dator utanför Azure Stack-miljön:  

1. Konfigurera [VPN-anslutning till Azure Stack](azure-stack-connect-azure-stack.md).
1. Kopiera det PEM-certifikat som du fick från Azure Stack-operatör och Anteckna platsen för filen (PATH_TO_PEM_FILE).
1. Kör kommandon i avsnitten nedan, beroende på operativsystemet på utvecklingsdatorn.

#### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="windows"></a>Windows

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

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Hämta virtuella datorns alias-slutpunkt

Innan du kan skapa virtuella datorer med hjälp av CLI, måste du kontakta Azure Stack-operatör och hämta virtuella datorns alias slutpunkt URI. Till exempel Azure använder du följande URI: `https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json`. Molnadministratören bör ställa in en liknande slutpunkt för Azure Stack med bilder som är tillgängliga i Azure Stack marketplace. Måste du ange slutpunkten URI för den `endpoint-vm-image-alias-doc` parametern till den `az cloud register` kommandot som du ser i nästa avsnitt. 
  
## <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

Använd följande steg för att ansluta till Azure Stack:

1. Registrera Azure Stack-miljön genom att köra den `az cloud register` kommando. I vissa situationer kan dirigeras direkt utgående internet-anslutning via en proxy eller brandvägg som framtvingar SSL internetkommunikationer. I dessa fall kan den `az cloud register` kommando kan misslyckas med ett fel, till exempel ”det går inte att hämta slutpunkter från molnet”. Undvik det här felet, kan du ange följande miljövariabler:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```
   
    a. Att registrera den *molnet administrativa* miljö, Använd:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    b. Att registrera den *användaren* miljö, Använd:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    c. Att registrera den *användaren* i en miljö med flera Klientorganisationer, använder:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --endpoint-active-directory-resource-id=<URI of the ActiveDirectoryServiceEndpointResourceID> \
        --profile 2018-03-01-hybrid
      ```
    d. För att registrera användaren i en AD FS-miljön, använder du:

      ```azurecli
      az cloud register \
        -n AzureStack  \
        --endpoint-resource-manager "https://management.local.azurestack.external" \
        --suffix-storage-endpoint "local.azurestack.external" \
        --suffix-keyvault-dns ".vault.local.azurestack.external"\
        --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" \
        --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/"\
        --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/"\
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --profile "2018-03-01-hybrid"
      ```
1. Konfigurera active miljön med hjälp av följande kommandon.
   
    a. För den *molnet administrativa* miljö, Använd:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

    b. För den *användaren* miljö, Använd:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

1. Uppdatera din miljökonfiguration om du vill använda Azure Stack-profil för versionen av specifika API: et. Uppdatera konfigurationen genom att köra följande kommando:

    ```azurecli
    az cloud update \
      --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Om du kör en version av Azure Stack innan 1808 versionen, måste du använda profilen för API-version **2017-03-09-profile** i stället för API-version profilen **2018-03-01-hybrid**.

1. Logga in på Azure Stack-miljön med hjälp av den `az login` kommando. Du kan logga in på Azure Stack-miljön som en användare eller som en [tjänstens huvudnamn](../../active-directory/develop/app-objects-and-service-principals.md). 

    * Azure AD-miljöer
      * Logga in som en *användaren*: Du kan antingen ange användarnamnet och lösenordet direkt inom den `az login` kommandot eller autentisera med hjälp av en webbläsare. Om ditt konto har aktiverat multifaktorautentisering måste du göra det senare:

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Om ditt konto har aktiverat multifaktorautentisering, kan du använda den `az login` kommandot utan att ange den `-u` parametern. Kör det här kommandot ger dig en URL och en kod som du måste använda för att autentisera.
   
      * Logga in som en *tjänstens huvudnamn*: Innan du loggar in, [skapa ett huvudnamn för tjänsten via Azure portal](azure-stack-create-service-principals.md) eller CLI och tilldela den till en roll. Nu kan logga in med hjälp av följande kommando:

      ```azurecli  
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```
    * AD FS-miljöer

        * Logga in som en användare som använder en webbläsare med en kod för enheten:  
           ```azurecli  
           az login --use-device-code
           ```

           > [!NOTE]  
           >Kör kommandot ger dig en URL och en kod som du måste använda för att autentisera.

        * Logga in som ett huvudnamn för tjänsten:
        
          1. Förbered PEM-filen som ska användas för huvudsaklig inloggning på tjänsten.

            * Exportera tjänstobjektscertifikatet på klientdatorn där huvudkontot som har skapats, som en pfx med den privata nyckeln som finns på `cert:\CurrentUser\My`; cert namn har samma namn som huvudnamnet.
        
            * Konvertera pfx till pem (Använd verktyget OpenSSL).

          2.  Logga in på CLI:
            ```azurecli  
            az login --service-principal \
              -u <Client ID from the Service Principal details> \
              -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
              --tenant <Tenant ID> \
              --debug 
            ```

## <a name="test-the-connectivity"></a>Testa anslutningen

Konfigurera med allt, Använd CLI för att skapa resurser i Azure Stack. Du kan till exempel skapa en resursgrupp för ett program och lägga till en virtuell dator. Använd följande kommando för att skapa en resursgrupp med namnet ”MyResourceGroup”:

```azurecli
az group create \
  -n MyResourceGroup -l local
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
