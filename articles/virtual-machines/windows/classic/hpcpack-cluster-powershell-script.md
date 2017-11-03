---
title: "PowerShell-skript för att distribuera Windows HPC-kluster | Microsoft Docs"
description: "Köra ett PowerShell-skript för att distribuera ett Windows HPC Pack 2012 R2-kluster i Azure-datorer"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 286b2be8-2533-40df-b02a-26156b1f1133
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 85b125ab19671b61d2541af6378c95feb88bf952
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Skapa Windows kluster för högpresterande datorbearbetning (HPC) med HPC Pack IaaS-distributionsskriptet
Kör PowerShell-skript för att distribuera en komplett HPC Pack 2012 R2-kluster för Windows-arbetsbelastningar på virtuella Azure-datorer för distributionen av HPC Pack IaaS. Klustret består av en Active Directory-anslutna huvudnod som kör Windows Server och Microsoft HPC Pack och ytterligare Windows beräkningsresurser som du anger. Om du vill distribuera ett HPC Pack kluster i Azure för Linux-arbetsbelastningar, se [skapar ett Linux-HPC-kluster med HPC Pack IaaS-distributionsskriptet](../../linux/classic/hpcpack-cluster-powershell-script.md). Du kan också använda en Azure Resource Manager-mall för att distribuera ett kluster med HPC Pack. Exempel finns i [skapa ett HPC-kluster](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) och [skapa ett HPC-kluster med en nod avbildning för anpassade beräkningen](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/).

> [!IMPORTANT] 
> PowerShell-skriptet som beskrivs i den här artikeln skapar ett Microsoft HPC Pack 2012 R2-kluster i Azure med hjälp av den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> Dessutom stöder inte i skriptet som beskrivs i den här artikeln HPC Pack 2016.

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Exempel konfigurationsfiler
Ersätt värdena för ditt prenumerations-Id eller namn och namnen på kontot och tjänsten i följande exempel.

### <a name="example-1"></a>Exempel 1
Följande konfigurationsfil distribuerar ett HPC Pack-kluster som har en huvudnod med lokala databaser och fem compute-noder som kör operativsystemet Windows Server 2012 R2. Alla molntjänster skapas direkt i USA, västra plats. Huvudnoden fungerar som domänkontrollant för domänskogen.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Exempel 2
Följande konfigurationsfil distribuerar ett HPC Pack kluster i en befintlig domänskog. Klustret har 1 huvudnod med lokala databaser och 12 compute-noder med filnamnstillägget BGInfo VM tillämpas.
Automatisk installation av Windows-uppdateringar är inaktiverad för alla virtuella datorer i domänskogen. Alla molntjänster skapas direkt i Östasien plats. Compute-noderna skapas i tre molntjänster och tre storage-konton: *MyHPCCN 0001* till *MyHPCCN 0005* i *MyHPCCNService01* och  *mycnstorage01*; *MyHPCCN 0006* till *MyHPCCN0010* i *MyHPCCNService02* och *mycnstorage02*; och  *MyHPCCN-0011* till *MyHPCCN 0012* i *MyHPCCNService03* och *mycnstorage03*). Compute-noder har skapats från en befintlig privat avbildning som hämtats från en beräkningsnod. Automatisk växa eller krympa-tjänsten är aktiverad med standard växa eller krympa intervall.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Exempel 3
Följande konfigurationsfil distribuerar ett HPC Pack kluster i en befintlig domänskog. Klustret innehåller en huvudnod, en databasserver med en disk på 500 GB data, två broker noder som kör operativsystemet Windows Server 2012 R2 och fem compute-noder som kör operativsystemet Windows Server 2012 R2. Molntjänsten MyHPCCNService skapas i tillhörighetsgruppen *MyIBAffinityGroup*, och de andra molntjänsterna har skapats i tillhörighetsgruppen *MyAffinityGroup*. HPC-jobbet Scheduler REST-API och HPC-webbportalens är aktiverade på huvudnoden.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Exempel 4
Följande konfigurationsfil distribuerar ett HPC Pack kluster i en befintlig domänskog. Klustret har två huvudnod med lokala databaser, två Azure noden mallar skapas och tre storlek medel Azure noder har skapats för Azure nodmallen *AzureTemplate1*. En skriptfil som körs på huvudnoden när huvudnoden har konfigurerats.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Felsökning
* **”Virtuella nätverk finns inte” fel** -om du kör skriptet för att distribuera flera kluster i Azure samtidigt under en prenumeration, en eller flera distributioner kan misslyckas med fel ”VNet *VNet\_namn* finns inte ”.
  Om det här felet inträffar, kör du skriptet igen för misslyckad distribution.
* **Problem som har åtkomst till Internet från virtuella Azure-nätverket** – om du skapar ett kluster med en ny domänkontrollant med hjälp av skriptet för distribution eller manuellt upphöja huvudnod VM till domänkontrollant kan det uppstå problem med att ansluta den Virtuella datorer till Internet. Det här problemet kan inträffa om en vidarebefordrare DNS-server konfigureras automatiskt på domänkontrollanten och DNS-servern vidarebefordraren löses inte korrekt.
  
    Att undvika det här problemet, logga in på domänkontrollanten och antingen ta bort inställningen vidarebefordrare konfiguration eller konfigurera en giltig vidarebefordrare DNS-server. Konfigurera den här inställningen i Serverhanteraren klickar du på **verktyg** >
    **DNS** att öppna DNS-hanteraren och dubbelklicka sedan på **vidarebefordrare**.
* **Fel vid åtkomst av RDMA-nätverk från beräkningsintensiva VMs** – om du lägger till Windows Server-beräkning eller Meddelandenod virtuella datorer med en RDMA-kompatibelt storlek exempelvis A8 eller A9 kan det uppstå problem med att ansluta dessa virtuella datorer till nätverkets RDMA program. En beror problemet uppstår på om HpcVmDrivers tillägget inte har installerats korrekt när de virtuella datorerna har lagts till i klustret. Tillägget kan till exempel ha fastnat i tillståndet installation.
  
    Undvik problemet genom att kontrollera tillståndet för tillägget i de virtuella datorerna först. Om tillägget inte har installerats korrekt, tar du bort noder från HPC-kluster och Lägg sedan till noderna. Exempelvis kan du lägga till Beräkningsnoden virtuella datorer genom att köra skriptet Lägg till HpcIaaSNode.ps1 på huvudnoden.

## <a name="next-steps"></a>Nästa steg
* Försök att köra en test-arbetsbelastning i klustret. Ett exempel finns i HPC Pack [Kom igång med](https://technet.microsoft.com/library/jj884144).
* Ett skript för kluster-distributionen och köra ett HPC-arbetsbelastning, finns [komma igång med ett HPC Pack kluster i Azure för att köra arbetsbelastningar för Excel och SOA-](../../virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Försök HPC Pack verktyg för att starta, stoppa, lägga till och ta bort compute-noder från ett kluster som du skapar. Se [hantera compute-noder i ett HPC Pack kluster i Azure](hpcpack-cluster-node-manage.md).
* Om du vill konfigurera att skicka jobb till klustret från en lokal dator, se [skicka HPC-jobb från en lokal dator till ett HPC Pack kluster i Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

