---
title: Uppgradera klusternoder för att använda Azure-hanterade diskar
description: Så här uppgraderar du ett befintligt Service Fabric-kluster för att använda Azure-hanterade diskar med liten eller ingen stilleståndstid för klustret.
ms.topic: how-to
ms.date: 3/01/2020
ms.openlocfilehash: 2bda5572eda5579bb31c3613b220885f27220d99
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758058"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Uppgradera klusternoder för att använda Azure-hanterade diskar

[Azure-hanterade diskar](../virtual-machines/windows/managed-disks-overview.md) är det rekommenderade disklagringserbjudandet för användning med virtuella Azure-datorer för beständig lagring av data. Du kan förbättra återhämtningen för dina arbetsbelastningar för service fabric genom att uppgradera skaluppsättningarna för den virtuella datorn som ligger till grund för nodtyperna för att använda hanterade diskar. Så här uppgraderar du ett befintligt Service Fabric-kluster för att använda Azure-hanterade diskar med liten eller ingen stilleståndstid för klustret.

Den allmänna strategin för att uppgradera en klusternod för service fabric för att använda hanterade diskar är att:

1. Distribuera en annars dubblettuppsättning för virtuell datorskala av den nodtypen, men med [det managedDisk-objektet](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) som lagts till i `osDisk` avsnittet i distributionsmallen för den virtuella datorns skaluppsättning. Den nya skaluppsättningen ska binda till samma belastningsutjämnare/IP som originalet, så att dina kunder inte drabbas av ett avbrott i tjänsten under migreringen.

2. När både de ursprungliga och uppgraderade skaluppsättningarna körs sida vid sida inaktiverar du de ursprungliga nodinstanserna en i taget så att systemtjänsterna (eller replikerna av tillståndskänsliga tjänster) migrerar till den nya skalningsuppsättningen.

3. Kontrollera att klustret och nya noder är felfria och ta sedan bort den ursprungliga skalningsuppsättningen och nodtillståndet för de borttagna noderna.

Den här artikeln kommer att gå igenom stegen för att uppgradera den primära nodtypen för ett exempelkluster för att använda hanterade diskar, samtidigt som du undviker eventuella avbrott i klustret (se anmärkning nedan). Det ursprungliga tillståndet för exempeltestklustret består av en nodtyp av [Silver hållbarhet](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster), backas upp av en enda skalningsuppsättning med fem noder.

> [!CAUTION]
> Ett avbrott med den här proceduren uppstår endast om du är beroende av klustret DNS (till exempel när du öppnar [Service Fabric Explorer).](service-fabric-visualizing-your-cluster.md) Arkitektonisk [bästa praxis för frontend-tjänster](https://docs.microsoft.com/azure/architecture/microservices/design/gateway) är att ha någon form av [belastningsutjämnare](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) framför nodtyperna för att göra nodbyte möjligt utan avbrott.

Här är [mallarna och cmdlets](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage) för Azure Resource Manager som vi använder för att slutföra uppgraderingsscenariot. Malländringarna förklaras i [Distribuera en uppgraderad skalningsuppsättning för den primära nodtypen](#deploy-an-upgraded-scale-set-for-the-primary-node-type) nedan.

## <a name="set-up-the-test-cluster"></a>Konfigurera testklustret

Nu ställer vi in det första testklustret för service fabric. Hämta [download](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage) först exempelmallarna för Azure Resource Manager som vi använder för att slutföra det här scenariot.

Logga sedan in på ditt Azure-konto.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Följande kommandon hjälper dig att skapa ett nytt självsignerat certifikat och distribuera testklustret. Om du redan har ett certifikat som du vill använda hoppar du till [Använda ett befintligt certifikat för att distribuera klustret](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Generera ett självsignerat certifikat och distribuera klustret

Tilldela först de variabler som du behöver för klusterdistribution av Service Fabric. Justera värdena `resourceGroupName` `certSubjectName`för `parameterFilePath`, `templateFilePath` , och för ditt specifika konto och din miljö:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> Kontrollera att `certOutputFolder` platsen finns på den lokala datorn innan du kör kommandot för att distribuera ett nytt Service Fabric-kluster.

Öppna sedan filen [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) och `clusterName` justera `dnsName` värdena för och för att motsvara de dynamiska värden som du anger i PowerShell och spara ändringarna.

Distribuera sedan testklustret för Service Fabric:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

När distributionen är klar letar du`$certPfx`reda på *PFX-filen* ( ) på den lokala datorn och importerar den till certifikatarkivet:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

Åtgärden returnerar certifikatets tumavtryck, som du använder för att [ansluta till det nya klustret](#connect-to-the-new-cluster-and-check-health-status) och kontrollera dess hälsostatus. (Hoppa över följande avsnitt, som är en alternativ metod för klusterdistribution.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Använda ett befintligt certifikat för att distribuera klustret

Du kan också använda ett befintligt Azure Key Vault-certifikat för att distribuera testklustret. För att göra detta måste du [hämta referenser till key vault](#obtain-your-key-vault-references) och certifikat tumavtryck.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Öppna filen [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) och ändra `clusterName` `dnsName` värdena för och till något unikt.

Ange slutligen ett resursgruppnamn för klustret och ange `templateFilePath` och platser för `parameterFilePath` dina *Initial-1NodeType-UnmanagedDisks-filer:*

> [!NOTE]
> Den angivna resursgruppen måste redan finnas och finnas i samma region som key vault.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Kör slutligen följande kommando för att distribuera det första testklustret:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Ansluta till det nya klustret och kontrollera hälsotillståndet

Anslut till klustret och se till att alla `clusterName` fem `thumb` noderna är felfria (ersätter och variabler för klustret):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Med det är vi redo att påbörja uppgraderingsproceduren.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Distribuera en uppgraderad skalningsuppsättning för den primära nodtypen

För att uppgradera, eller *lodrätt skala*, en nodtyp, måste vi distribuera en kopia av nodtypens skaluppsättning för virtuella datorer, `subnet`som `loadBalancerBackendAddressPools`annars är identisk med den ursprungliga skaluppsättningen (inklusive referens till samma `nodeTypeRef`, och ) förutom att den innehåller önskad uppgradering/ändringar och en egen separat undernäts- och inkommande NAT-adresspool. Eftersom vi uppgraderar en primär nodtyp markeras den nya`isPrimary: true`skaluppsättningen som primär ( ), precis som den ursprungliga skalningsuppsättningen. (För uppgraderingar av icke-primär nodtyp utelämnar du bara detta.)

För enkelhetens skull har de nödvändiga ändringarna redan gjorts för dig i [mallen](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) *Upgrade-1NodeType-2ScaleSets-ManagedDisks* och [parameterfiler.](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)

I följande avsnitt beskrivs malländringarna i detalj. Om du vill kan du hoppa över förklaringen och fortsätta till [nästa steg i uppgraderingsproceduren](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Uppdatera klustermallen med den uppgraderade skalningsuppsättningen

Här är avsnittsändringarna i den ursprungliga klusterdistributionsmallen för att lägga till en uppgraderad skalningsuppsättning för den primära nodtypen.

#### <a name="parameters"></a>Parametrar

Lägg till parametrar för förekomstnamn, antal och storlek på den nya skaluppsättningen. Observera `vmNodeType1Name` att det är unikt för den nya skalningsuppsättningen, medan värdena antal och storlek är identiska med den ursprungliga skaluppsättningen.

**Mallfil**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Parameterfil**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Variabler

Lägg till `variables` en post för den inkommande NAT-adresspoolen i den nya skalningsuppsättningen i avsnittet distributionsmall.

**Mallfil**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Resurser

Lägg till den nya skalningsuppsättningen för den virtuella datorn i avsnittet *resurser* för distributionsmall och tänk på följande:

* Den nya skaluppsättningen refererar till samma nodtyp som originalet:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Den nya skaluppsättningen refererar till samma belastningsutjämnadsbackendadress och undernät (men använder en annan belastningsutjämnad NAT-pool):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Precis som den ursprungliga skaluppsättningen markeras den nya skaluppsättningen som den primära nodtypen. (När du uppgraderar icke-primära nodtyper utelämnar du den här ändringen.)

    ```json
    "isPrimary": true,
    ```

* Till skillnad från den ursprungliga skalningsuppsättningen uppgraderas den nya skalningsuppsättningen för att använda hanterade diskar.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

När du har implementerat alla ändringar i mall- och parameterfilerna går du vidare till nästa avsnitt för att hämta dina Key Vault-referenser och distribuera uppdateringarna till klustret.

### <a name="obtain-your-key-vault-references"></a>Skaffa dina Key Vault-referenser

To deploy the updated configuration, you'll first to obtain several references to your cluster certificate stored in your Key Vault. Det enklaste sättet att hitta dessa värden är via Azure-portalen. Du behöver:

* **URL:en för nyckelvalv för klustercertifikatet.** Välj **Certifikat** > *som din önskade hemlig* > **identifierare**för certifikat i Key Vault i Azure-portalen:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Tumavtrycket för klustercertifikatet.** (Du har förmodligen redan detta om du [är ansluten till det första klustret](#connect-to-the-new-cluster-and-check-health-status) för att kontrollera dess hälsostatus.) Från samma certifikatblad (Certifikat*ditt önskade certifikat)* i Azure-portalen**kopierar** > du **X.509 SHA-1 Thumbprint (i hex):**

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Resurs-ID för key vault.** Välj **Egenskaper** > **resurs-ID**från Key Vault i Azure-portalen:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen

Justera `parameterFilePath` och `templateFilePath` vid behov och kör sedan följande kommando:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

När distributionen är klar kontrollerar du klustrets hälsotillstånd igen och ser till att alla tio noder (fem på originalet och fem i den nya skalningsuppsättningen) är felfria.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Migrera seed-noder till den nya skalningsuppsättningen

Vi är nu redo att börja inaktivera noderna i den ursprungliga skalningsuppsättningen. När dessa noder inaktiveras migreras systemtjänsterna och frönoderna till de virtuella datorerna i den nya skaluppsättningen eftersom den också är markerad som den primära nodtypen.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Använd Service Fabric Explorer för att övervaka migreringen av seed-noder till den nya skaluppsättningen och förloppet för noder i den ursprungliga skalningsuppsättningen från *Inaktivera* *till inaktiverad* status.

![Service Fabric Explorer som visar status för inaktiverade noder](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Det kan ta lite tid att slutföra inaktiveringen över alla noder i den ursprungliga skalningsuppsättningen. För att garantera datakonsekvens kan bara en seed-nod ändras åt gången. Varje ändring av seed-nod kräver en klusteruppdatering. För att ersätta en frönod krävs alltså två klusteruppgraderingar (en vardera för nodtillägg och borttagning). Om du uppgraderar de fem frönoderna i det här exempelscenariot resulterar det i tio klusteruppgraderingar.

## <a name="remove-the-original-scale-set"></a>Ta bort den ursprungliga skaluppsättningen

När inaktiveringen är klar tar du bort skalningsuppsättningen.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

I Service Fabric Explorer visas de borttagna noderna (och därmed *klusterhälsotillståndet)* nu i *feltillstånd.*

![Service Fabric Explorer visar inaktiverade noder i feltillstånd](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Ta bort de föråldrade noderna från service fabric-klustret för att återställa klusterhälsotillståndet till *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer med nednoder i feltillstånd borttaget](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Nästa steg

I den här genomgången lärde du dig hur du uppgraderar skaluppsättningarna för den virtuella datorn i ett Service Fabric-kluster för att använda hanterade diskar samtidigt som du undviker avbrott i tjänsten under processen. För mer information om relaterade ämnen kolla in följande resurser.

Lär dig att:

* [Skala upp en primär nodtyp för Service Fabric-klustret](service-fabric-scale-up-node-type.md)

* [Konvertera en skalningsuppsättningsmall för att använda hanterade diskar](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Ta bort en nodtyp för Service Fabric](service-fabric-how-to-remove-node-type.md)

Se även:

* [Exempel: Uppgradera klusternoder för att använda Azure-hanterade diskar](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage)

* [Hänsyn till lodrät skalning](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)