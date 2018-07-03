---
title: ta med fil
description: ta med fil
services: virtual-machines-linux, virtual-machines-windows
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 07/02/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 496c32e70ad58f64b31c1f6fcf913884fcd4abc1
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37348243"
---
Organisationer har storskaliga behov. Dessa Big Compute-arbetsbelastningar omfattar teknisk design och analys, finansiella risker, bildrendering, komplex modellering, Monte Carlo-simuleringar och mycket mer. 

Använd Azure-molnet för att effektivt köra beräkningsintensiva Linux och Windows-arbetsbelastningar, från parallella batchjobb för traditionella HPC-simuleringar. Kör dina HPC och batch-arbetsbelastningar på Azure-infrastrukturen med valfri compute services, grid chefer, Marketplace-lösningar och leverantör (SaaS)-program. Azure tillhandahåller flexibla lösningar för att fördela arbetet och skala till tusentals virtuella datorer eller kärnor och sedan skala ned när du behöver färre resurser. 



## <a name="solution-options"></a>Lösningsalternativ



* **Gör det själv-lösningar**
    * Konfigurera din egen klustermiljö i Azure virtual machines eller [VM-skalningsuppsättningar](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Lyfta och skifta ett lokalt kluster, eller distribuera ett nytt kluster i Azure för ytterligare kapacitet. 
    * Använda Azure Resource Manager-mallar för att distribuera ledande [arbetsbelastning chefer](#workload-managers), infrastruktur, och [program](#hpc-applications). 
    * Välj [HPC och GPU VM-storlekar](#hpc-and-gpu-sizes) som omfattar specialiserad maskinvara och nätverksanslutningar för MPI- eller GPU-arbetsbelastningar. 
    * Lägg till [högpresterande lagring](#hpc-storage) för I/O-intensiva arbetsbelastningar.
* **Hybridlösningar**
    * Utöka din lokala lösning för att avlasta (”burst”) tunga arbetsbelastningar till Azure-infrastrukturen
    * Använda molnet databearbetning på begäran med din befintliga [arbetsbelastning manager](#workload-manager).
    * Dra nytta av [HPC och GPU VM-storlekar](#hpc-and-gpu-sizes) för MPI- eller GPU-arbetsbelastningar.
* **Big Compute-lösningar som en tjänst**
    * Utveckla anpassade Big Compute-lösningar och arbetsflöden med hjälp av [Azure Batch](#azure-batch) och relaterade [Azure-tjänster](#related-azure-services).
    * Kör Azure-aktiverade tekniker och simulering lösningar från leverantörer som [Altair](http://www.altair.com/), [skala om](https://www.rescale.com/azure/), och [Cycle Computing](https://cyclecomputing.com/) (nu [ansluten med Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
    * Använd en [Cray-superdator](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure) som en tjänst i Azure.
* **Marketplace-lösningar**
    * Använd skalan för [HPC-program](#hpc-applications) och [lösningar](#marketplace-solutions) erbjuds i den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 
    


Följande avsnitt innehåller mer information om de tekniker och länkar till information.



## <a name="marketplace-solutions"></a>Marketplace-lösningar

Gå till den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) för Linux och Windows-VM-avbildningar och utformad för HPC-lösningar. Exempel:

* [RogueWave CentOS-baserade HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server för HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [TIBCO Grid Server-motorn](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Azure Data Science VM för Windows och Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)
* [Intel Cloud Edition for Lustre](https://azuremarketplace.microsoft.com/marketplace/apps/intel.lustre-cloud-edition-eval?tab=Overview)


 
## <a name="hpc-applications"></a>HPC-program

Köra anpassade eller kommersiella HPC-program i Azure. Flera exempel i det här avsnittet är jämfört med om du vill skala effektivt med ytterligare virtuella datorer eller compute-kärnor. Gå till den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) för redo att distribuera lösningar.

> [!NOTE]
> Kontrollera med leverantören av kommersiella program om licensiering krävs eller andra begränsningar för att köra i molnet. Alla leverantörer erbjuder inte licensiering enligt modellen Betala per användning. Du kanske behöver en licensserver i molnet för din lösning eller ansluta till en lokal server.

### <a name="engineering-applications"></a>Tekniker program


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB distribuerad databehandling Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM +](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Grafik och återgivning

* [Autodesk Maya, 3ds Max och Arnold](../articles/batch/batch-rendering-service.md) på Azure Batch 

### <a name="ai-and-deep-learning"></a>AI och djupinlärning

* [Batch AI](../articles/batch-ai/overview.md) utbildning djupinlärningsmodeller
* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Virtuell dator för djupinlärning](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Batch skeppsvarv recept för djupinlärning](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)






## <a name="hpc-and-gpu-vm-sizes"></a>HPC och GPU VM-storlekar
Azure erbjuder flera olika storlekar för [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) och [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) virtuella datorer, inklusive storlekar som utformats för beräkningsintensiva arbetsbelastningar. Till exempel kan H16r och H16mr virtuella datorer ansluta till ett högt dataflöde backend-RDMA-nätverk. Det här nätverket i molnet kan förbättra prestandan för tätt sammansatta parallella program som körs under [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) eller Intel MPI. 

Virtuella datorer i N-serien funktionen NVIDIA-grafikprocessorer som är utformad för beräkningsintensiva och grafikintensiva program inklusive learning artificiell intelligens (AI) och visualisering. 

Läs mer:

* Högpresterande compute storlekar för [Linux](../articles/virtual-machines/linux/sizes-hpc.md) och [Windows](../articles/virtual-machines/windows/sizes-hpc.md) virtuella datorer 
* GPU-aktiverade storlekar för [Linux](../articles/virtual-machines/linux/sizes-gpu.md) och [Windows](../articles/virtual-machines/windows/sizes-gpu.md) virtuella datorer 

Lär dig att:

* [Konfigurera ett Linux RDMA-kluster för att köra MPI-program](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Konfigurera ett Windows RDMA-kluster med Microsoft HPC Pack för att köra MPI-program](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Använda beräkningsintensiva virtuella datorer i Batch-pooler](../articles/batch/batch-pool-compute-intensive-sizes.md)



## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) är en plattform som tjänst för att köra storskaliga parallella och högpresterande databehandlingsprogram (HPC) effektivt i molnet. Azure Batch schemalägger beräkningsintensivt arbete för körning på en hanterad pool med virtuella datorer och kan automatiskt skala beräkningsresurser för att uppfylla behoven i dina jobb. 

SaaS-leverantörer eller utvecklare kan använda Batch SDK: er och verktyg för att integrera HPC-program eller arbetsbelastningar med Azure, organisera data i Azure, och bygga jobbkörningspipelines. 

Lär dig att:

* [Börja utveckla med Batch](../articles/batch/quick-run-dotnet.md)
* [Använda Azure Batch-kodexempel](https://github.com/Azure/azure-batch-samples)
* [Använda lågprioriterade virtuella datorer med Batch](../articles/batch/batch-low-pri-vms.md)
* [Kör behållare HPC-arbetsbelastningar med Batch skeppsvarv](https://github.com/Azure/batch-shipyard)
* [Kör parallella R-arbetsbelastningar på Batch](https://github.com/Azure/doAzureParallel)
* [Köra på begäran Spark-jobb på Batch](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>Hanterare av arbetsbelastning

Här följer några exempel på klustret och arbetsbelastningen chefer som kan köras i Azure-infrastrukturen. Skapa fristående kluster i Azure virtuella datorer eller överföras till virtuella Azure-datorer från ett lokalt kluster. 
* [Alces flygning beräkning](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Ljus Klusterhanterare](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM Spectrum Symphony och Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://technet.microsoft.com/library/mt744885.aspx) – se alternativ för att köra i [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) virtuella datorer 



## <a name="hpc-storage"></a>HPC-lagring

Storskaliga Batch- och HPC-arbetsbelastningar har krav på datalagring och åtkomst som överskrider funktionerna i traditionella cloud-filsystem. Implementera parallella file systemlösningar i Azure som [Lustre](http://lustre.org/) och [BeeGFS](http://www.beegfs.com/content/).

Läs mer:

* [Parallella virtuellt filsystem på Azure](https://azure.microsoft.com/resources/parallel-virtual-file-systems-on-microsoft-azure/)
* Högpresterande molnlagringslösningar från [Avere](http://www.averesystems.com/about-us/about-avere) (nu [ansluten med Microsoft](https://blogs.microsoft.com/blog/2018/01/03/microsoft-to-acquire-avere-systems-accelerating-high-performance-computing-innovation-for-media-and-entertainment-industry-and-beyond/))


## <a name="related-azure-services"></a>Relaterade Azure-tjänster

Azure-datorer, skalningsuppsättningar för virtuella datorer, Batch och relaterade compute services som utgör basen för de flesta Azure HPC-lösningar. Din lösning kan dock dra nytta av många relaterade Azure-tjänster. Här är en lista:

### <a name="storage"></a>Storage

* [BLOB-, tabell- och queue storage](../articles/storage/storage-introduction.md)
* [Fillagring](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Data och analys
* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Data Factory](../articles/data-factory/introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Databricks](../articles/azure-databricks/what-is-azure-databricks.md)
* [SQL Database](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-machine-learning"></a>AI och machine learning
* [Machine Learning-tjänster](../articles/machine-learning/service/overview-what-is-azure-ml.md)
* [Batch AI](../articles/batch-ai/overview.md)
* [Genomics](../articles/genomics/overview-what-is-genomics.md)

### <a name="networking"></a>Nätverk
* [Virtual Network](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Behållare
* [Container Service](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Azure Kubernetes Service (AKS)](../articles/aks/intro-kubernetes.md)
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
* [NeuroInitiative](https://customers.microsoft.com/en-us/story/neuroinitiative-health-provider-azure)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Nästa steg
* Läs mer om Big Compute-lösningar för [tekniska simulering](https://simulation.azure.com/), [rendering](https://azure.microsoft.com/solutions/big-compute/rendering/), [bankväsende och kapitalmarknader](https://finance.azure.com/), och [genomics](https://enterprise.microsoft.com/en-us/industries/health/genomics/) .
* Du hittar den senaste informationen i [Microsoft HPC- och Batch-bloggen](http://blogs.technet.com/b/windowshpc/) och i [Azure-bloggen](https://azure.microsoft.com/blog/tag/hpc/).

* Använd hanterade och skalbara Azure [Batch](https://azure.microsoft.com/services/batch/) -tjänsten att köra beräkningsintensiva arbetsbelastningar utan att behöva hantera underliggande infrastruktur [Läs mer](https://azure.microsoft.com/solutions/architecture/hpc-big-compute-saas/)



