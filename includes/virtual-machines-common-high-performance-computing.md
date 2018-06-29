---
title: ta med fil
description: ta med fil
services: virtual-machines-linux, virtual-machines-windows
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 05/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b8bbcf81f48e82417a0d51e29f7dd8ce76681fed
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37095343"
---
Organisationer har storskaliga behov. De här stort Compute arbetsbelastningarna inkluderar teknisk design och analys, finansiella risk beräkningar avbildningen återgivning, komplexa modellering, Monte Carlo-simulering och mer. 

Använd Azure-molnet för att köra effektivt beräkningsintensiva Linux och Windows arbetsbelastningar från parallella batchjobb till traditionella HPC simulering. Kör ditt HPC och batch arbetsbelastningar på Azure-infrastrukturen med ditt val av compute-tjänster, rutnätet chefer, Marketplace lösningar och program för leverantören värd (SaaS). Azure tillhandahåller flexibla lösningar för fördelar arbete och skala till tusentals virtuella datorer eller kärnor och skala när du behöver färre resurser. 



## <a name="solution-options"></a>Lösningsalternativ för



* **Själv lösningar**
    * Konfigurera klustermiljön i virtuella Azure-datorer eller [skalningsuppsättningar i virtuella](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Lyfta och flytta ett lokalt kluster, eller distribuera ett nytt kluster i Azure för ytterligare kapacitet. 
    * Använda Azure Resource Manager-mallar för att distribuera inledande [arbetsbelastning chefer](#workload-managers), infrastruktur och [program](#hpc-applications). 
    * Välj [HPC och GPU VM-storlekar](#hpc-and-gpu-sizes) som inkluderar specialiserad maskinvara och nätverksanslutningar för MPI eller GPU arbetsbelastningar. 
    * Lägg till [högpresterande lagring](#hpc-storage) för I/O-intensiva arbetsbelastningar.
* **Hybridlösningar**
    * Utöka din lokal lösning för att avlasta (”burst”) belastning arbetsbelastningar till Azure-infrastrukturen
    * Använda molntjänster beräkning på begäran med din befintliga [arbetsbelastning manager](#workload-manager).
    * Dra nytta av [HPC och GPU VM-storlekar](#hpc-and-gpu-sizes) för MPI eller GPU arbetsbelastningar.
* **Stor beräknings-lösningar som en tjänst**
    * Utveckla anpassade stort Compute lösningar och arbetsflöden med hjälp av [Azure Batch](#azure-batch) och relaterade [Azure-tjänster](#related-azure-services).
    * Kör Azure-aktiverade tekniker och simulering lösningar från leverantörer inklusive [Altair](http://www.altair.com/), [skala om](https://www.rescale.com/azure/), och [cykel Computing](https://cyclecomputing.com/) (nu [kopplas till Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
    * Använd en [Cray superdator](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure) som en tjänst som finns i Azure.
* **Marketplace-lösningar**
    * Använd skala [HPC-program](#hpc-applications) och [lösningar](#marketplace-solutions) erbjuds i den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 
    


Följande avsnitt innehåller mer information om stödjande tekniker och länkar till information.



## <a name="marketplace-solutions"></a>Marketplace-lösningar

Besök den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) för Linux och Windows VM-avbildningar och utformad för HPC-lösningar. Exempel:

* [RogueWave CentOS-baserade HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server för HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [TIBCO rutnätet Server Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Azure datavetenskap virtuell dator för Windows och Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)
* [Intel molnet Edition för Lyster](https://azuremarketplace.microsoft.com/marketplace/apps/intel.lustre-cloud-edition-eval?tab=Overview)


 
## <a name="hpc-applications"></a>HPC-program

Kör anpassade eller kommersiella HPC-program i Azure. Flera exempel i det här avsnittet är jämfört med om du vill skala ett effektivt sätt med ytterligare virtuella datorer eller compute kärnor. Besök den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) redo att distribuera lösningar.

> [!NOTE]
> Kontrollera med leverantören av kommersiella program för licensiering eller andra begränsningar för körs i molnet. Alla leverantörer erbjuder inte licensiering enligt modellen Betala per användning. Du kanske behöver licensieringsservern i molnet för din lösning eller ansluta till en lokal server.

### <a name="engineering-applications"></a>Tekniker program


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB distribuerad databehandling Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM +](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Grafik och återgivning

* [Autodesk Maya 3ds Max och Arnold](../articles/batch/batch-rendering-service.md) på Azure Batch 

### <a name="ai-and-deep-learning"></a>AI och djup learning

* [Batch-AI](../articles/batch-ai/overview.md) utbildning för djup learning-modeller
* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Djup Learning VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Batch skeppsvarv recept för djup](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)






## <a name="hpc-and-gpu-vm-sizes"></a>HPC-och GPU VM
Azure erbjuder en mängd storlekar för [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) och [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) virtuella datorer, inklusive storlekar som utformats för beräkningsintensiva arbetsbelastningar. Till exempel kan H16r och H16mr virtuella datorer ansluta till ett nätverk med hög genomströmning backend-RDMA. Det här molnet nätverket kan förbättra prestanda för tätt kopplade parallella program som körs [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) eller Intel MPI. 

N-serien VMs funktion NVIDIA GPU-kort som utformats för beräkningsintensiva eller grafikintensiva program, inklusive artificiell intelligence (AI) learning och visualisering. 

Läs mer:

* Högpresterande compute storlekar för [Linux](../articles/virtual-machines/linux/sizes-hpc.md) och [Windows](../articles/virtual-machines/windows/sizes-hpc.md) virtuella datorer 
* GPU-aktiverade storlekar för [Linux](../articles/virtual-machines/linux/sizes-gpu.md) och [Windows](../articles/virtual-machines/windows/sizes-gpu.md) virtuella datorer 

Lär dig att:

* [Ställa in ett Linux RDMA-kluster som kör MPI-program](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Ställa in ett RDMA-Windows-kluster med Microsoft HPC Pack som kör MPI-program](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Använd beräkningsintensiva virtuella datorer i pooler för Batch](../articles/batch/batch-pool-compute-intensive-sizes.md)



## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) är en plattform som tjänsten för att köra storskaliga parallellt och högpresterande datorbearbetning (HPC) program effektivt i molnet. Azure Batch-scheman beräkningsintensiva fungerar om du vill köra på en hanterad pool för virtuella datorer och kan automatiskt skala beräkna resurser för att möta behoven hos dina jobb. 

SaaS-providers och utvecklare kan använda Batch SDK: er och verktyg för att integrera HPC-program eller arbetsbelastningar i behållare med Azure, mellanlagra data till Azure, och skapa pipelines för körning av jobbet. 

Lär dig att:

* [Komma igång med Batch](../articles/batch/quick-run-dotnet.md)
* [Använd Azure Batch-kodexempel](https://github.com/Azure/azure-batch-samples)
* [Med Batch VM med låg prioritet](../articles/batch/batch-low-pri-vms.md)
* [Kör av HPC-arbetsbelastningar med Batch skeppsvarv](https://github.com/Azure/batch-shipyard)
* [Köra parallella R arbetsbelastningar i Batch](https://github.com/Azure/doAzureParallel)
* [Köra på begäran Spark på Batch](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>Arbetsbelastningen chefer

Följande är exempel på klustret och arbetsbelastningen chefer som kan köras i Azure-infrastrukturen. Skapa fristående kluster i virtuella Azure-datorer eller burst till virtuella Azure-datorer från ett kluster som är lokalt. 
* [Alces svarta beräkning](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Klara Klusterhanterare](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM spektrumet Symphony och Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://technet.microsoft.com/library/mt744885.aspx) -finns alternativ för att köra i [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) virtuella datorer 



## <a name="hpc-storage"></a>HPC-lagring

Storskaliga Batch och HPC-arbetsbelastning har krav på datalagring och åtkomst som överskrider funktionerna i traditionella molnet filsystem. Implementera lösningar för parallell file system i Azure som [Lyster](http://lustre.org/) och [BeeGFS](http://www.beegfs.com/content/).

Läs mer:

* [Parallell virtuellt filsystem på Azure](https://azure.microsoft.com/resources/parallel-virtual-file-systems-on-microsoft-azure/)
* Högpresterande lagringslösningar för molnet från [Avere](http://www.averesystems.com/about-us/about-avere) (nu [ansluten med Microsoft](https://blogs.microsoft.com/blog/2018/01/03/microsoft-to-acquire-avere-systems-accelerating-high-performance-computing-innovation-for-media-and-entertainment-industry-and-beyond/))


## <a name="related-azure-services"></a>Relaterade Azure-tjänster

Virtuella Azure-datorer, virtuella datorer, Batch och relaterade beräknings-tjänster är grunden för de flesta Azure HPC-lösningar. Din lösning kan dock dra nytta av många relaterade Azure-tjänster. Här är en ofullständig lista:

### <a name="storage"></a>Storage

* [BLOB-, tabell- och queue storage-](../articles/storage/storage-introduction.md)
* [Fillagring](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Data och analys
* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Data Factory](../articles/data-factory/introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Databricks](../articles/azure-databricks/what-is-azure-databricks.md)
* [SQL Database](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-machine-learning"></a>AI och maskininlärning
* [Machine Learning-tjänster](../articles/machine-learning/service/overview-what-is-azure-ml.md)
* [Batch AI](../articles/batch-ai/overview.md)
* [Genomik](../articles/genomics/overview-what-is-genomics.md)

### <a name="networking"></a>Nätverk
* [Virtual Network](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Behållare
* [Container Service](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Azure Kubernetes-tjänsten (AKS)](../articles/aks/intro-kubernetes.md)
* [Container Registry](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Kundberättelser

Exempel på kunder som har löst affärsproblem med Azure HPC-lösningar:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA globala P & C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [EFS](https://customers.microsoft.com/story/efs-professionalservices-azure)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi: A värdepapper International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Watson för mobiltelefoner](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Nästa steg
* Mer information om stora Compute lösningar för [engineering simuleringen](https://simulation.azure.com/), [återgivning](https://azure.microsoft.com/solutions/big-compute/rendering/), [bank- och kapital marknader](https://finance.azure.com/), och [genomik](https://enterprise.microsoft.com/en-us/industries/health/genomics/) .
* Du hittar den senaste informationen i [Microsoft HPC- och Batch-bloggen](http://blogs.technet.com/b/windowshpc/) och i [Azure-bloggen](https://azure.microsoft.com/blog/tag/hpc/).

* Använda hanterade och skalbar Azure [Batch](https://azure.microsoft.com/services/batch/) -tjänsten för att köra arbetsbelastningar för beräkningsintensiva, utan att hantera underliggande infrastruktur [Läs mer](https://azure.microsoft.com/solutions/architecture/hpc-big-compute-saas/)



