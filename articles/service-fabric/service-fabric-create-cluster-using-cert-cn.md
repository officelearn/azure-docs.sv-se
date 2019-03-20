---
title: Skapa ett Azure Service Fabric-kluster med certifikatets unika namn | Microsoft Docs
description: Lär dig hur du skapar ett Service Fabric-kluster med certifikatets unika namn från en mall.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: f6f4858740288facb1e206eed3a8cd4ee1854daa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58111454"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Distribuera ett Service Fabric-kluster som använder certifikatets unika namn i stället för tumavtryck
Inga två certifikat kan ha samma tumavtryck, vilket gör förnya certifikatet för klustret eller management svårt. Flera certifikat kan dock ha samma namn eller ämne.  Ett kluster med vanliga namn för certifikatet gör det mycket enklare att certifikathantering. Den här artikeln beskriver hur du distribuerar Service Fabric-kluster för certifikatets unika namn istället för certifikatets tumavtryck.
 
## <a name="get-a-certificate"></a>Få ett certifikat
Först hämtar ett certifikat från en [certifikatutfärdare (CA)](https://wikipedia.org/wiki/Certificate_authority).  Namnet på certifikatet ska vara för den anpassade domänen du egna och köpta från en domänregistrator. Till exempel ”azureservicefabricbestpractices.com”; de personer som inte är Microsoft-anställda kan inte etablera certifikat för MS domäner, så att du inte kan använda din LB eller Traffic Manager DNS-namn som vanliga namn för ditt certifikat och du måste etablera en [Azure DNS-zon](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) om dina anpassade domän som ska matchas i Azure. Du bör även att deklarera den anpassade domänen som du äger som din klustrets ”managementEndpoint” om du vill att portalen att visa den anpassade domänen alias för klustret.

Du kan få en CA-signerat certifikat från en kostnadsfri eller öppna certifikatutfärdare i testsyfte.

> [!NOTE]
> Självsignerat certifikat, inklusive de som skapas när du distribuerar Service Fabric-kluster i Azure portal, stöds inte. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Överför certifikatet till ett nyckelvalv
Service Fabric-kluster har distribuerats på en VM-skalningsuppsättning i Azure.  Överför certifikatet till ett nyckelvalv.  När klustret distribueras installerar certifikatet på virtuella datorns skalningsuppsättning som klustret körs på.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Ladda ned och uppdatera en exempelmall
Den här artikeln används den [säkra kluster med 5 noder exempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) mall och mallparametrar. Ladda ned den *azuredeploy.json* och *azuredeploy.parameters.json* filer till din dator.

### <a name="update-parameters-file"></a>Uppdatera parameterfilen
Först, öppnar den *azuredeploy.parameters.json* filen i en textredigerare och Lägg till följande parametervärde:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Nu ska vi konfigurera den *certificateCommonName*, *sourceVaultValue*, och *certificateUrlValue* parametervärden till dem som returneras av det här skriptet:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Uppdatera mallfilen
Därefter öppnar den *azuredeploy.json* filen i en textredigerare och gör tre uppdateringar för certifikatets unika namn.

1. I den **parametrar** lägger du till en *certificateCommonName* parameter:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Överväg även att ta bort den *certificateThumbprint*, den kan inte längre behövs.

2. Ange värdet för den *sfrpApiVersion* variabeln ”2018-02-01”:
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. I den **Microsoft.Compute/virtualMachineScaleSets** resurs, uppdatera tillägg för virtuell dator om du vill använda det gemensamma namnet i certifikatinställningarna i stället för tumavtrycket för.  I **virtualMachineProfile**->**extensionProfile**->**tillägg**->**egenskaper** -> **inställningar**->**certifikat**, lägga till 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    och ta bort `"thumbprint": "[parameters('certificateThumbprint')]",`.

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

4. I den **Microsoft.ServiceFabric/clusters** resurs, Uppdateringsversion API: et till ”2018-02-01”.  Också lägga till en **certificateCommonNames** inställning med en **commonNames** egenskap och ta bort den **certifikat** inställning (med tumavtrycksegenskapen) enligt följande Exempel:
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
   > Fältet ”certificateIssuerThumbprint” kan du ange de förväntade utfärdarna av certifikat med ett visst ämne nätverksnamn. Det här fältet accepterar en kommaavgränsad uppräkning av SHA1-tumavtryck. Observera att detta är en förstärkning av certifikatsverifiering - i fall när utfärdaren inte har angetts eller tom certifikatet kommer accepteras för autentisering om kedjan kan byggas och ends upp i en rotcertifikatutfärdare som är betrodd av verifieraren. Om utfärdaren har angetts, kommer att accepteras certifikatet om tumavtrycket för dess direkta utfärdaren matchar något av värdena som anges i det här fältet – oavsett om rot är betrodd eller inte. Observera att en PKI kan använda olika certifikatutfärdare för att utfärda certifikat för samma ämne och det är därför viktigt att ange alla förväntade certifikatutfärdarens tumavtryck för ett visst ämne.
   >
   > Att ange utfärdaren anses vara bästa praxis. och utelämna det fortsätter att fungera – för certifikat som länkning till ett betrott rotcertifikat – det här beteendet har begränsningar och kan fasas ut inom en snar framtid. Observera också att kluster distribueras i Azure och skyddas med X509 certifikat som utfärdats av en privat PKI och deklarerats av ämne kanske inte kan verifieras av Azure Service Fabric-tjänsten (för kluster-till-tjänst-kommunikation) om PKI: ns certifikatprincip är inte synliga, tillgänglig och kan nås. 

## <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Distribuera om den uppdaterade mallen när du har gjort ändringarna.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [kluster security](service-fabric-cluster-security.md).
* Lär dig hur du [förnya ett klustercertifikat](service-fabric-cluster-rollover-cert-cn.md)
* [Uppdatera och hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)
* Förenkla certifikathantering av [ändra kluster från tumavtrycket för certifikatet till unika namn](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
