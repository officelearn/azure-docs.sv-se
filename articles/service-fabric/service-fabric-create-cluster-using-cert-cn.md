---
title: Skapa ett kluster med gemensamt namn på certifikat
description: Lär dig hur du skapar ett Service Fabric-kluster med ett vanligt certifikatnamn från en mall.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 4a4448c88fa9493979f075f6b9c669927dd1d39e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614561"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Distribuera ett Service Fabric-kluster som använder certifikatets gemensamma namn i stället för tumavtryck
Det finns inga två certifikat som har samma tumavtryck, vilket gör det svårt att rulla över klustercertifikat eller hantera. Flera certifikat kan dock ha samma gemensamma namn eller ämne.  Ett kluster med vanliga certifikatnamn gör certifikathanteringen mycket enklare. I den här artikeln beskrivs hur du distribuerar ett Service Fabric-kluster för att använda certifikatets gemensamma namn i stället för certifikatets tumavtryck.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Skaffa ett certifikat
Hämta först ett certifikat från en [certifikatutfärdarcertifikatutfärdning](https://wikipedia.org/wiki/Certificate_authority).  Certifikatets gemensamma namn ska vara för den anpassade domän du äger och köpas från en domänregistratorer. Till exempel "azureservicefabricbestpractices.com"; De som inte är Microsoft-anställda kan inte etablera certifikat för MS-domäner, så du kan inte använda DNS-namnen på din LB eller Traffic Manager som vanliga namn för ditt certifikat, och du måste etablera en [Azure DNS-zon](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) om din anpassade domän ska kunna lösas i Azure. Du vill också deklarera din anpassade domän som du äger som klustrets "managementEndpoint" om du vill att portalen ska återspegla det anpassade domänaliaset för klustret.

I testsyfte kan du få ett certifikatutfärdare signerat certifikat från en kostnadsfri eller öppen certifikatutfärdare.

> [!NOTE]
> Självsignerade certifikat, inklusive de som genereras vid distribution av ett Service Fabric-kluster i Azure-portalen, stöds inte. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Ladda upp certifikatet till ett nyckelvalv
I Azure distribueras ett Service Fabric-kluster på en skalningsuppsättning för virtuella datorer.  Ladda upp certifikatet till ett nyckelvalv.  När klustret distribueras installeras certifikatet på den virtuella datorskalauppsättning som klustret körs på.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Hämta och uppdatera en exempelmall
Den här artikeln använder [5-noden säker kluster exempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) mall och parametrar mall. Hämta *azuredeploy.json-* och *azuredeploy.parameters.json-filerna* till datorn.

### <a name="update-parameters-file"></a>Uppdatera parameterfil
Öppna först filen *azuredeploy.parameters.json* i en textredigerare och lägg till följande parametervärde:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Ange sedan *parametervärdena certificateCommonName*, *sourceVaultValue*och *certificateUrlValue* till de värden som returneras av föregående skript:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Uppdatera mallfilen
Öppna sedan *filen azuredeploy.json* i en textredigerare och gör tre uppdateringar för att stödja certifikatets gemensamma namn.

1. Lägg till en *parameter för certificateCommonName* i **parametern parametrar:**
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Också överväga att ta bort *certifikatetDumbprint*, det kanske inte längre behövs.

2. Ange värdet för *sfrpApiVersion-variabeln* till "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. I **microsoft.Compute/virtualMachineScaleSets-resursen** uppdaterar du tillägget för virtuella datorer så att det använder det vanliga namnet i certifikatinställningarna i stället för tumavtrycket.  I **virtualMachineProfile-tilläggProfile-tilläggsegenskapersinställningar**->**extensionProfile**->**extensions**->**properties**->**settings**->**certifikat**lägger du till 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    och `"thumbprint": "[parameters('certificateThumbprint')]",`ta bort .

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. I **Microsoft.ServiceFabric/clusters-resursen** uppdaterar du API-versionen till "2018-02-01".  Lägg också till en **certifikatInställningar** med egenskapen **commonNames** och ta bort **certifikatinställningen** (med tumavtrycksegenskapen) som i följande exempel:
   ```json
   {
       "apiVersion": "2018-02-01",
       "type": "Microsoft.ServiceFabric/clusters",
       "name": "[parameters('clusterName')]",
       "location": "[parameters('clusterLocation')]",
       "dependsOn": [
       "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
       ],
       "properties": {
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > Fältet certifikatIssuerThumbprint gör det möjligt att ange förväntade utfärdare av certifikat med ett visst gemensamt namn. Det här fältet accepterar en kommaavgränsad uppräkning av SHA1-tumavtryck. Observera att detta är en förstärkning av certifikatvalideringen - om utfärdaren inte anges eller är tom accepteras certifikatet för autentisering om dess kedja kan byggas och hamnar i en rot som är betrodd av valideraren. Om utfärdaren anges accepteras certifikatet om tumavtrycket för dess direkta utfärdare matchar något av de värden som anges i det här fältet - oavsett om roten är betrodd eller inte. Observera att en PKI kan använda olika certifikatutfärdare för att utfärda certifikat för samma ämne, och därför är det viktigt att ange alla förväntade emittenttumavtryck för ett visst ämne.
   >
   > Att ange utfärdaren anses vara en bästa praxis. medan utelämna det kommer att fortsätta att fungera - för certifikat kedja upp till en betrodd rot - detta beteende har begränsningar och kan fasas ut inom en snar framtid. Observera också att kluster som distribueras i Azure och skyddas med X509-certifikat som utfärdats av en privat PKI och deklarerats av ämne kanske inte kan valideras av Azure Service Fabric-tjänsten (för kluster-till-tjänst-kommunikation), om PKI:s certifikatprincip inte kan upptäckas, är tillgängligt och tillgängligt. 

## <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Distribuera om den uppdaterade mallen när du har gjort ändringarna.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [klustersäkerhet](service-fabric-cluster-security.md).
* Lär dig hur du [övertäcker ett klustercertifikat](service-fabric-cluster-rollover-cert-cn.md)
* [Uppdatera och hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)
* Förenkla certifikathantering genom [att ändra kluster från certifikattumavtryck till vanligt namn](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
