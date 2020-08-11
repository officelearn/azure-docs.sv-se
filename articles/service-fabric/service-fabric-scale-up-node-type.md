---
title: Skala upp en typ av Azure-Service Fabric Node
description: Lär dig hur du skalar ett Service Fabric kluster genom att lägga till en skalnings uppsättning för virtuella datorer.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: a8a8a432d4a11427f2c4f545a0907540af3112bc
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056490"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-virtual-machine-scale-set"></a>Skala upp en Service Fabric-kluster primär nodtyp genom att lägga till en skalnings uppsättning för virtuella datorer
Den här artikeln beskriver hur du skalar upp en Service Fabric-klusters primära nodtyp genom att öka resurserna för virtuella datorer. Ett Service Fabric kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. En dator eller en virtuell dator som ingår i ett kluster kallas för en nod. Skalnings uppsättningar för virtuella datorer är en Azure Compute-resurs som du använder för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster har [kon figurer ATS som en separat skalnings uppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. När du har skapat ett Service Fabric-kluster kan du skala en typ av klusternod lodrätt (ändra resurserna för noderna) eller uppgradera operativ systemet för de virtuella datorernas nodtyper.  Du kan skala klustret när som helst, även när arbets belastningar körs på klustret.  När klustret skalas, skalas programmen automatiskt.

> [!WARNING]
> Försök inte att skala upp en primär nodtyp om klustrets status är ohälsosam, eftersom detta bara kommer att göra klustret ytterligare.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Process för att uppgradera storlek och operativ system för de virtuella datorernas primära nodtyp
Här är processen för att uppdatera VM-storlek och operativ system för de virtuella datorernas primära nodtypen.  Efter uppgraderingen är den primära nodtypen för virtuella datorer storlek standard D4_V2 och kör Windows Server 2016 Data Center med behållare.

> [!WARNING]
> Innan du försöker utföra den här proceduren i ett produktions kluster rekommenderar vi att du studerar exempelfilerna och kontrollerar processen mot ett test kluster. Klustret är inte heller tillgängligt under en tid. Du kan inte göra ändringar i flera VMSS som deklareras som samma NodeType parallellt. Du måste utföra åtskilda distributions åtgärder för att tillämpa ändringar på varje NodeType-VMSS individuellt.

1. Distribuera det första klustret med två nodtyper och två skalnings uppsättningar (en skalnings uppsättning per nodtyp) med hjälp av dessa exempel [mal len](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) och [parametrar](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) filer.  Båda skalnings uppsättningarna är storlek standard D2_V2 och kör Windows Server 2012 R2 Data Center.  Vänta tills klustret har slutfört bas linje uppgraderingen.   
2. Valfritt – distribuera ett tillstånds känsligt exempel till klustret.
3. När du har bestämt dig för att uppgradera den primära nodtypen för virtuella datorer lägger du till en ny skalnings uppsättning till den primära nodtypen med hjälp av dessa exempel- [mall](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) och [parametrar](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) filer så att den primära nodtypen nu har två skalnings uppsättningar.  System tjänster och användar program kan migreras mellan virtuella datorer i de två olika skalnings uppsättningarna.  De nya virtuella datorerna med skalnings uppsättningen är storlek standard D4_V2 och kör Windows Server 2016 Data Center med behållare.  En ny belastningsutjämnare och en offentlig IP-adress läggs också till med den nya skalnings uppsättningen.  
    Sök efter den nya skalnings uppsättningen i mallen genom att söka efter resursen "Microsoft. Compute/virtualMachineScaleSets" som heter med parametern *vmNodeType2Name* .  Den nya skalnings uppsättningen läggs till i den primära nodtypen med hjälp av egenskaperna->virtualMachineProfile->extensionProfile->tillägg->egenskaper->inställningar->nodeTypeRef inställning.
4. Kontrol lera kluster hälsan och kontrol lera att alla noder är felfria.
5. Inaktivera noderna i den gamla skalnings uppsättningen för den primära nodtypen med avsikt att ta bort noden. Du kan inaktivera alla samtidigt och åtgärderna placeras i kö. Vänta tills alla noder har inaktiverats, vilket kan ta lite tid.  Eftersom de äldre noderna i nodtypen är inaktiverade migreras system tjänsterna och dirigeringsrouters till de virtuella datorerna för den nya skalnings uppsättningen i den primära nodtypen.
6. Ta bort den äldre skalnings uppsättningen från den primära nodtypen. (När noderna har inaktiverats som i steg 5, i bladet för skalnings uppsättning för virtuell dator i Azure Portal, frigör du noderna från den gamla nodtypen en i taget.)
7. Ta bort belastningsutjämnaren som är associerad med den gamla skalnings uppsättningen. Klustret är inte tillgängligt medan den nya offentliga IP-adressen och belastningsutjämnaren har kon figurer ATS för den nya skalnings uppsättningen.  
8. Lagra DNS-inställningar för den offentliga IP-adress som är kopplad till den gamla skalnings uppsättningen för den primära nodtypen i en variabel och ta bort den offentliga IP-adressen.
9. Ersätt DNS-inställningarna för den offentliga IP-adress som är associerad med den nya primära nodens typ skalnings uppsättning med DNS-inställningarna för den borttagna offentliga IP-adressen.  Klustret kan nu uppnås igen.
10. Ta bort nodens tillstånd för noderna från klustret.  Om hållbarhets nivån för den gamla skalnings uppsättningen är silver eller guld utförs det här steget automatiskt av systemet.
11. Om du har distribuerat det tillstånds känsliga programmet i föregående steg kontrollerar du att programmet fungerar.

## <a name="set-up-the-test-cluster"></a>Konfigurera test klustret

Börja med att hämta de två uppsättningar av filer som vi behöver för den här själv studie kursen, före- [mallen]() och- [parametrarna]() och efter- [mallen]() och- [parametrarna]().

Logga sedan in på ditt Azure-konto.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

Den här självstudien vägleder dig genom scenariot med att skapa ett självsignerat certifikat. Om du vill använda ett befintligt certifikat från Azure Key Vault hoppar du över steget nedan och speglar i stället stegen i [använda ett befintligt certifikat för att distribuera klustret](./upgrade-managed-disks.md#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Generera ett självsignerat certifikat och distribuera klustret

Först tilldelar du de variabler du behöver för Service Fabric kluster distribution. Justera värdena för `resourceGroupName` , `certSubjectName` , `parameterFilePath` och `templateFilePath` för ditt eget konto och din miljö:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> Kontrol lera att `certOutputFolder` platsen finns på den lokala datorn innan du kör kommandot för att distribuera ett nytt Service Fabric-kluster.

Öppna sedan *Deploy-2NodeTypes-2ScaleSets.parameters.jspå* filen och justera värdena för `clusterName` och `dnsName` för att motsvara de dynamiska värden som du angav i PowerShell och spara ändringarna.

Distribuera sedan Service Fabric test kluster:

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

När distributionen är klar letar du upp *. pfx* -filen ( `$certPfx` ) på den lokala datorn och importerar den till certifikat arkivet:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

Åtgärden returnerar certifikatets tumavtryck, som du kommer att använda för att ansluta till det nya klustret och kontrol lera dess hälso status.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Anslut till det nya klustret och kontrol lera hälso status

Anslut till klustret och se till att alla dess noder är felfria (ersätter `clusterName` `thumb` variablerna och för klustret):

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

Vi är redo att påbörja uppgraderings proceduren.

## <a name="upgrade-the-primary-node-type-vms"></a>Uppgradera den primära nodtypen av typen VM

När du har bestämt dig för att uppgradera den primära nodtypen för virtuella datorer lägger du till en ny skalnings uppsättning till den primära nodtypen, så att den primära nodtypen nu har två skalnings uppsättningar. En [mall](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) och [parametrar](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) filer har angetts för att Visa nödvändiga ändringar. Den nya skalnings uppsättningens virtuella datorer är storleks standard D4_V2 och kör Windows Server 2016 Data Center med behållare. En ny belastningsutjämnare och en offentlig IP-adress läggs också till med den nya skalnings uppsättningen. 

Sök efter den nya skalnings uppsättningen i mallen genom att söka efter resursen "Microsoft. Compute/virtualMachineScaleSets" som heter med parametern vmNodeType2Name. Den nya skalnings uppsättningen läggs till i den primära nodtypen med hjälp av egenskaperna->virtualMachineProfile->extensionProfile->tillägg->egenskaper->inställningar->nodeTypeRef inställning.

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen

Justera `parameterFilePath` och `templateFilePath` kör sedan följande kommando:

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

När distributionen är klar kontrollerar du kluster tillståndet igen och ser till att alla noder (i original och på den nya skalnings uppsättningen) är felfria.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>Migrera noder till den nya skalnings uppsättningen

Nu är det dags att börja inaktivera noderna i den ursprungliga skalnings uppsättningen. Eftersom dessa noder blir inaktiverade migreras system tjänsterna och dirigeringsrouters till de virtuella datorerna i den nya skalnings uppsättningen eftersom den också är markerad som den primära nodtypen.

I det här steget ändrar du tjänst placerings begränsningen till att inkludera den nya skalnings uppsättningen för virtuella datorer/nodtypen och minskar sedan antalet gamla instanser av virtuell dators skalnings uppsättning till noll, en nod i taget (för att säkerställa att noden inte påverkar kluster tillförlitligheten).

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Använd Service Fabric Explorer för att övervaka migreringen av startnoder till den nya skalnings uppsättningen och förloppet för noder i den ursprungliga skalnings uppsättningen från att *inaktivera* till *inaktive rad* status.

> [!NOTE]
> Det kan ta lite tid att slutföra den inaktiverade åtgärden för alla noder i den ursprungliga skalnings uppsättningen. För att garantera data konsekvens kan endast en Seed-nod ändras i taget. För varje Seed-nods ändring krävs en kluster uppdatering. att ersätta en Seed-nod kräver därför två kluster uppgraderingar (en varje för nod tillägg och borttagning). Om du uppgraderar de fem startnoderna i det här exempel scenariot uppstår tio kluster uppgraderingar.

## <a name="remove-the-original-scale-set"></a>Ta bort den ursprungliga skalnings uppsättningen

Ta bort skalnings uppsättningen när inaktive rings åtgärden har slutförts.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

I Service Fabric Explorer kommer de borttagna noderna (och därmed *kluster hälso tillståndet*) nu att visas i *fel* tillstånd.

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>Ta bort den gamla belastningsutjämnaren och uppdatera DNS-inställningar

Nu kan vi ta bort de resurser som är relaterade till den gamla primära nodtypen, från belastningsutjämnaren och den gamla offentliga IP-adressen. 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Sedan uppdaterar vi DNS-inställningarna för den nya offentliga IP-adressen för att spegla inställningarna från den gamla primära IP-adressen för den primära noden.

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

Läs mer i kluster hälsa

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

Slutligen tar du bort nodens tillstånd för var och en av de relaterade noderna. Om den gamla skalnings uppsättningens hållbarhets nivå var silver eller guld sker detta automatiskt.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

Klustrets primära Node-typ har nu uppgraderats. Kontrol lera att alla distribuerade program fungerar korrekt och att kluster hälsan är OK.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [lägger till en nodtyp i ett kluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster program mässigt](service-fabric-cluster-programmatic-scaling.md) med hjälp av Fluent Azure Compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).
