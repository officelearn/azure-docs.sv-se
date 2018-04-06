---
title: PowerShell-skript för att distribuera Linux HPC-kluster | Microsoft Docs
description: Köra ett PowerShell-skript för att distribuera ett Linux HPC Pack 2012 R2-kluster i Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 66affb47190ba0c6fccaae8e8267b310682aee46
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Skapa en Linux högpresterande datorbearbetning (HPC) kluster med HPC Pack IaaS-distributionsskriptet
Kör PowerShell-skript för att distribuera en fullständig HPC Pack 2012 R2-kluster för Linux arbetsbelastningar på virtuella Azure-datorer för distributionen av HPC Pack IaaS. Klustret består av en Active Directory-anslutna huvudnod som kör Windows Server och Microsoft HPC Pack och compute-noder som kör något av de Linux-distributioner som stöds av HPC Pack. Om du vill distribuera ett HPC Pack kluster i Azure för Windows-arbetsbelastningar, se [skapa ett Windows HPC-kluster med HPC Pack IaaS-distributionsskriptet](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
> [!IMPORTANT] 
> PowerShell-skriptet som beskrivs i den här artikeln skapar ett Microsoft HPC Pack 2012 R2-kluster i Azure med hjälp av den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> Dessutom stöder inte i skriptet som beskrivs i den här artikeln HPC Pack 2016. Information om Resource Manager-mallar för HPC Pack 2012 R2 och HPC Pack 2016 finns i [HPC Pack distributionsalternativ för kluster i Azure](../hpcpack-cluster-options.md).


[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Exempel konfigurationsfilen
Följande konfigurationsfil skapar en domänkontrollant och domänskog och distribuerar ett HPC Pack-kluster som har en huvudnod med lokala databaser och 10 Linux compute-noder. Alla molntjänster skapas direkt i Östasien plats. Linux-datornoderna skapas i två molntjänster och två storage-konton (det vill säga *MyLnxCN 0001* till *MyLnxCN 0005* i *MyLnxCNService01* och *mylnxstorage01*, och *MyLnxCN 0006* till *MyLnxCN 0010* i *MyLnxCNService02* och *mylnxstorage02* ). Compute-noder har skapats från en OpenLogic CentOS version 7.0 Linux-bild. 

Ersätt värdena för din prenumerationsnamn och namnen på kontot och tjänsten.

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
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Felsökning
* **”Virtuella nätverk finns inte” fel**. Om du kör skriptet HPC Pack IaaS distribution för att distribuera flera kluster i Azure samtidigt under en prenumeration, en eller flera distributioner kan misslyckas med fel ”VNet *VNet\_namn* finns inte”.
  Om det här felet inträffar, kör du skriptet för misslyckad distribution.
* **Problem som har åtkomst till Internet från virtuella Azure-nätverket**. Om du skapar ett HPC Pack kluster med en ny domänkontrollant med hjälp av skriptet för distribution, eller manuellt upphöja huvudnod VM till domänkontrollant, kan det uppstå problem med att ansluta virtuella datorer i Azure-nätverket till Internet. Detta kan inträffa om en vidarebefordrare DNS-server konfigureras automatiskt på en domänkontrollant och DNS-servern vidarebefordraren löses inte korrekt.
  
    Att undvika det här problemet, logga in på domänkontrollanten och antingen ta bort inställningen vidarebefordrare konfiguration eller konfigurera en giltig vidarebefordrare DNS-server. Du gör detta i Serverhanteraren klickar du på **verktyg** > **DNS** att öppna DNS-hanteraren och dubbelklicka sedan på **vidarebefordrare**.

## <a name="next-steps"></a>Nästa steg
* Se [komma igång med Linux compute-noder i ett HPC Pack kluster i Azure](hpcpack-cluster.md) för information om Linux-distributioner som stöds, flyttar data och skicka jobb till ett HPC Pack kluster med Linux compute-noder.
* Självstudier som använder skriptet för att skapa ett kluster och köra en Linux HPC-arbetsbelastning finns:
  * [Kör NAMD med Microsoft HPC Pack på Linux compute-noder i Azure](hpcpack-cluster-namd.md)
  * [Kör OpenFOAM med Microsoft HPC Pack på Linux compute-noder i Azure](hpcpack-cluster-openfoam.md)
  * [Kör STAR-CCM + med Microsoft HPC Pack på Linux compute-noder i Azure](hpcpack-cluster-starccm.md)

