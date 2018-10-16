---
title: Konfigurera ett Windows RDMA-kluster för att köra MPI-program | Microsoft Docs
description: Lär dig hur du skapar ett Windows HPC Pack-kluster med storleken H16r, H16mr, A8 eller A9-datorer för att köra MPI-appar med Azure RDMA-nätverk.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/06/2018
ms.author: danlep
ms.openlocfilehash: 7bb49e48f3777304aa6f40cee40e0b7147994201
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345245"
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Konfigurera ett Windows RDMA-kluster med HPC Pack för att köra MPI-program
Konfigurera ett Windows RDMA-kluster i Azure med [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) och [RDMA-kompatibla HPC-VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) att köra program med parallella Message Passing Interface (MPI). När du konfigurerar RDMA-kompatibla, Windows Server-baserad noder i ett HPC Pack-kluster, kommunicerar effektivt MPI-program via en låg latens, högt dataflöde nätverk i Azure som baseras på teknik för direkt fjärråtkomst till minne (RDMA).

## <a name="hpc-pack-cluster-deployment-options"></a>Distributionsalternativ för HPC Pack-kluster
Microsoft HPC Pack är ett verktyg utan extra kostnad för att skapa HPC-kluster lokalt eller i Azure för att köra Windows eller Linux HPC-program. HPC Pack innehåller en körningsmiljö för Microsofts implementering av den Message Passing Interface för Windows (MS-MPI). När det används med RDMA-kompatibla-instanser som körs på en Windows Server-operativsystem som stöds, ger hcp Pack en effektiv möjlighet att köra Windows MPI-program som har åtkomst till Azure RDMA-nätverk. 

Den här artikeln beskrivs två scenarier och länkar till detaljerade riktlinjer för att konfigurera ett Windows RDMA-kluster med Microsoft HPC Pack 2012 R2. 

* Scenario 1. Distribuera beräkningsintensiva arbetsrollinstanser (PaaS)
* Scenario 2. Distribuera beräkningsnoder i beräkningsintensiva virtuella datorer (IaaS)

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scenario 1: Distribuera beräkningsintensiva arbetsrollinstanser (PaaS)
Lägga till extra beräkningsresurser i Azure worker-rollinstanser (Azure-noder) som körs i en molntjänst (PaaS) från ett befintligt HPC Pack-kluster. Den här funktionen kallas även ”utöka till Azure” från HPC Pack stöder olika storlekar för arbetsrollinstanser. När du lägger till Azure-noder, ange ett av storlekarna som RDMA-kompatibla.

Följande är information och anvisningar för att utöka till RDMA-kompatibelt Azure-instanser från en befintlig (vanligtvis lokalt) klustret. Använd liknande procedurer för att lägga till arbetsrollinstanser i en huvudnod i HPC Pack som distribueras i en Azure VM.

> [!NOTE]
> En självstudiekurs om att utöka till Azure med HPC Pack finns i [konfigurera ett hybridkluster med HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Observera att tänka på i följande steg som gäller särskilt för RDMA-kompatibelt Azure-noder.
> 
> 

![Utöka till Azure][burst]

### <a name="steps"></a>Steg
1. **Distribuera och konfigurera en huvudnod i HPC Pack 2012 R2**
   
    Hämta installationspaketet för HPC Pack från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922). Krav och anvisningar för att förbereda för distribution av Azure burst finns i [Burst to Azure Worker-instanser med Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).
2. **Konfigurera ett certifikat i Azure-prenumeration**
   
    Konfigurera ett certifikat för säker anslutning mellan huvudnoden och Azure. Alternativ och procedurer finns i [scenarier för att konfigurera Azure-Hanteringscertifikatet för HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). HPC Pack installerar testdistributioner kan en standard Microsoft HPC Azure-Hanteringscertifikatet du snabbt kan överföra till din Azure-prenumeration.
3. **Skapa en ny molntjänst och ett lagringskonto**
   
    Använd Azure-portalen för att skapa en molntjänst (klassisk) och ett lagringskonto (klassiskt) för distributionen. Skapa dessa resurser i en region där H-serien, A8 och A9-storlek som du vill använda är tillgänglig. Se [Azure-produkter per region](https://azure.microsoft.com/regions/services/).

4. **Skapa en Azure-nod-mall**
   
    Använd den noden guiden Skapa i HPC Cluster Manager. Anvisningar finns i [skapa en mall för Azure-nod](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) i ”steg för att distribuera Azure-noder med Microsoft HPC Pack”.
   
    För inledande tester föreslår vi att du konfigurerar en princip för manuell tillgänglighet i mallen.
5. **Lägga till noder i klustret**
   
    Använd den guiden Lägg till noder i HPC Cluster Manager. Mer information finns i [lägga till Azure-noder i Windows HPC-klustret](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    När du anger storleken på noderna, Välj en av RDMA-kompatibla instansstorlekar.
   
   > [!NOTE]
   > I varje överföras till Azure-distribution med beräkningsintensiva instanser distribuerar HPC Pack automatiskt minst två RDMA-kompatibla förekomster (till exempel A8) som proxy noder, förutom Azure worker-rollinstanser som du anger. Proxy-noder använder kärnor som allokeras till prenumerationen och betalar avgifter tillsammans med Azure worker-rollinstanser.
   > 
   > 
6. **Starta (tillhandahålla) noder och anpassa dem online för att köra jobb**
   
    Markera noderna och använda den **starta** åtgärd i HPC Cluster Manager. När etableringen har slutförts, markera noderna och använda den **Anslut** åtgärd i HPC Cluster Manager. Noderna är redo att köra jobb.
7. **Skicka jobb till klustret**
   
   Använd verktygen i HPC Pack jobb skickas för att köra klustret jobb. Se [Microsoft HPC Pack: jobbhantering](http://technet.microsoft.com/library/jj899585.aspx).
8. **Stoppa (avetablering) noderna**
   
   När du är klar jobb som körs, koppla noderna och använda den **stoppa** åtgärd i HPC Cluster Manager.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scenario 2: Distribuera beräkningsnoder i beräkningsintensiva virtuella datorer (IaaS)
I det här scenariot kan distribuera du huvudnod i HPC Pack och beräkning klusternoder på virtuella datorer i Azure-nätverk. HPC Pack innehåller flera [distributionsalternativ i Azure virtuella datorer](../../windows/hpcpack-cluster-options.md), inklusive skript för automatisk distribution och Azure-snabbstartsmallar. Exempelvis följande information och anvisningar hjälper dig att använda den [HPC Pack IaaS-distributionsskriptet](hpcpack-cluster-powershell-script.md) att automatisera distributionen av ett HPC Pack 2012 R2-kluster i Azure.

![Kluster i Azure virtuella datorer][iaas]

### <a name="steps"></a>Steg
1. **Skapa en klustrets huvudnod och beräkna nod virtuella datorer genom att köra skriptet för HPC Pack IaaS-distribution på en klientdator**
   
    Ladda ned skriptet för HPC Pack IaaS-distribution-paketet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949).
   
    För att förbereda klientdatorn, skapa skriptet konfigurationsfilen och kör skriptet, se [skapa ett HPC-kluster med skriptet för HPC Pack IaaS-distribution](hpcpack-cluster-powershell-script.md). 
   
    Överväganden att distribuera RDMA-kompatibla beräkningsnoder, se [högpresterande compute VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) och Tänk på följande:
   
   * **Virtuellt nätverk**: Ange ett nytt virtuellt nätverk i en region där den H-serien, A8 eller A9 storleken som du vill använda är tillgänglig. Se [Azure-produkter per region](https://azure.microsoft.com/regions/services/).

   * **Operativsystemet Windows Server**: stöd för RDMA-anslutning kan ange ett kompatibelt Windows Server operativsystem, till exempel Windows Server 2012 R2 för beräkningsnod virtuella datorer.
   * **Molntjänster**: eftersom skriptet använder den klassiska distributionsmodellen kan de virtuella datorerna som klustret distribueras med Azure-molntjänster (`ServiceName` inställningarna i konfigurationsfilen). Vi rekommenderar att du distribuerar din huvudnod i en enda molntjänst och dina beräkningsnoder i en annan molntjänst. 
   * **Gå nodstorlek**: det här scenariot kan du överväga en storlek på minst A4 (Extra stor) för huvudnoden.
   * **HpcVmDrivers tillägget**: distributionsskriptet installerar Azure VM-agenten och tillägget HpcVmDrivers automatiskt när du distribuerar storlek A8 eller A9 compute-noder med en Windows Server-operativsystem. HpcVmDrivers installerar drivrutiner på Beräkningsnoden virtuella datorer så att de kan ansluta till RDMA-nätverk. På RDMA-kompatibla H-serien virtuella datorer, måste du installera tillägget HpcVmDrivers manuellt. Se [högpresterande compute VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Klusternätverkskonfigurationen**: distributionsskriptet ställer automatiskt in HPC Pack-kluster i topologin 5 (alla noder på företagsnätverket). Den här topologin krävs för alla HPC Pack-kluster-distributioner i virtuella datorer. Ändra inte klustret nätverkets topologi senare.
1. **Ta med compute-noderna online för att köra jobb**
   
    Markera noderna och använda den **Anslut** åtgärd i HPC Cluster Manager. Noderna är redo att köra jobb.
3. **Skicka jobb till klustret**
   
    Ansluta till huvudnod för att skicka jobb eller konfigurera den lokala datorn för att göra detta. Mer information finns i [skicka jobb till ett HPC-kluster i Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Koppla noderna och stoppa (frigöra) dem.**
   
    När du är klar jobb som körs ta noderna offline i HPC Cluster Manager. Använd Azure-hanteringsverktyg kan stängas av.

## <a name="run-mpi-applications-on-the-cluster"></a>Köra MPI-program i klustret
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Exempel: Kör mpipingpong på ett HPC Pack-kluster
Kontrollera ett HPC Pack på distribution av RDMA-kompatibla förekomster med HPC Pack **mpipingpong** på klustret. **mpipingpong** skickar paket av data mellan länkade noder flera gånger för att beräkna svarstid och dataflöde mätning av faktisk användning och statistik för RDMA-aktiverade program-nätverket. Det här exemplet visar ett typiskt mönster för att köra MPI-jobb (i det här fallet **mpipingpong**) med hjälp av klustret **mpiexec** kommando.

Det här exemplet förutsätts att du har lagt till Azure-noder i en konfiguration med ”överföras till Azure” ([Scenario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Om du har distribuerat HPC Pack på ett kluster med virtuella Azure-datorer behöver du ändra kommandosyntax för att ange en annan nod-grupp och ange ytterligare miljövariabler för att dirigera nätverkstrafik till RDMA-nätverk.

Så här kör mpipingpong i klustret:

1. Öppna en kommandotolk på huvudnoden eller på en klientdator som är korrekt konfigurerade.
2. Du kan beräkna fördröjning mellan två noder i en Azure burst-distribution med fyra noder, skriver du följande kommando för att skicka ett jobb för att köra mpipingpong med en liten paketstorlek och många iterationer:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    Kommandot returnerar ID för det jobb som har skickats.
   
    Om du har distribuerat HPC Pack-kluster som distribueras på Azure Virtual Machines, ange en nodgrupp som innehåller compute-nod-datorer som distribuerats i en enda molntjänst och ändra den **mpiexec** -kommandot enligt följande:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. När jobbet har slutförts, om du vill visa utdata (i det här fallet, utdata från uppgift 1 för jobbet), skriver du följande
   
    ```Command
    task view <JobID>.1
    ```
   
    där &lt; *JobID* &gt; är ID för det jobb som har skickats.
   
    Utdata innehåller svarstid resultat som liknar följande.
   
    ![Ping pong svarstid][pingpong1]
4. Du kan beräkna dataflödet mellan par med Azure burst-noder, skriver du följande kommando för att skicka ett jobb att köra **mpipingpong** med en stor paketstorlek och några iterationer:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    Kommandot returnerar ID för det jobb som har skickats.
   
    Ändra kommandot som anges i steg 2 i ett HPC Pack-kluster som distribueras på Azure Virtual Machines.
5. När jobbet har slutförts, om du vill visa utdata (i det här fallet, utdata från uppgift 1 för jobbet), skriver du följande:
   
    ```Command
    task view <JobID>.1
    ```
   
   Utdata innehåller liknar följande resultat för dataflödet.
   
   ![Ping pong dataflöde][pingpong2]

### <a name="mpi-application-considerations"></a>Överväganden för MPI-program
Nedan följer för att köra MPI-program med HPC Pack i Azure. Vissa gäller endast för distributioner av Azure-noder (worker-rollinstanser som lagts till i en ”överföras till Azure”-konfiguration).

* Worker-rollinstanser i en molntjänst är regelbundet nätverkskonfigurationsinställningar utan föregående meddelande av Azure (till exempel för Systemunderhåll eller om en instans misslyckas). Om en instans är nätverkskonfigurationsinställningar medan den körs en MPI-jobb, instansen förlorar data och återgår till tillståndet när den först distribuerades, vilket kan orsaka MPI-jobb misslyckas. Fler noder som du använder för ett enskilt MPI-jobb och desto längre tid att jobbet har körts, desto mer sannolikt som någon av instanserna är nätverkskonfigurationsinställningar medan ett jobb körs. Överväg också att detta om du anger en enskild nod i distributionen som en filserver.
* För att köra MPI-jobb i Azure, har du inte använda RDMA-kompatibla förekomster. Du kan använda valfri instansstorlek som stöds av HPC Pack. RDMA-kompatibla förekomster bör dock för att köra storskaliga relativt MPI-jobb som är känsliga för svarstiden och bandbredden för nätverket som ansluter noderna. Om du använder andra storlekar för att köra MPI-jobb för svarstid och bandbredd känsligt, rekommenderar vi små jobb, där en aktivitet körs på bara några få noder som körs.
* Program som distribueras till Azure-instanser omfattas av licensvillkor som är kopplade till programmet. Kontrollera med leverantören av kommersiella program om licensiering krävs eller andra begränsningar för att köra i molnet. Alla leverantörer erbjuder inte licensiering enligt modellen Betala per användning.
* Azure-instanser måste ytterligare installationsprogrammet till åtkomst till lokala noder, resurser och licensservrar. Du kan till exempel konfigurera en Azure-nätverk för plats-till-plats om du vill aktivera Azure-noder att komma åt en lokal licensserver.
* För att köra MPI-program på Azure-instanser, registrera varje MPI-program med Windows-brandväggen på instanser genom att köra den **hpcfwutil** kommando. På så sätt kan MPI kommunikation ska kunna utföras på en port som tilldelas dynamiskt av brandväggen.
  
  > [!NOTE]
  > Du kan också konfigurera brandväggen undantag kommandon automatiskt på alla nya Azure-noder som har lagts till i klustret för överföras till Azure-distributioner. När du har kört den **hpcfwutil** och kontrollera att dina program fungerar, lägger till kommandot till ett startskript för din Azure-noder. Mer information finns i [använder ett startskript för Azure-noder](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* HPC Pack använder miljövariabeln CCP_MPI_NETMASK kluster för att ange ett intervall med godkända adresser för MPI-kommunikation. Från och med HPC Pack 2012 R2, miljövariabeln CCP_MPI_NETMASK kluster påverkar endast MPI kommunikation mellan domänanslutet kluster compute-noder (antingen lokalt eller i virtuella Azure-datorer). Variabeln ignoreras av noder som har lagts till i en burst Azure-konfiguration.
* MPI-jobb kan inte köras i Azure-instanser som har distribuerats i olika molntjänster (till exempel i överföras till Azure-distributioner med olika noden mallar eller Azure VM-beräkningsnoder distribueras i flera molntjänster). Om du har flera Azure-nod-distributioner som startas med olika noden mallar måste köra MPI-jobb på endast en uppsättning Azure-noder.
* När du lägger till Azure-noder i klustret och tar dem online, försöker Schemaläggaren för HPC-jobb omedelbart att starta jobb på noderna. Om det bara en del av din arbetsbelastning kan köra på Azure, se till att du uppdaterar skapa jobbmallar för att definiera vilka typer av jobb kan köras på Azure. Till exempel för att säkerställa att jobb som skickas med en jobbmallen endast köras på Azure-noder, Lägg till noden grupper-egenskap till mall och välj AzureNodes som det obligatoriska värdet. Använd Lägg till HpcGroup HPC PowerShell-cmdlet för att skapa anpassade grupper för Azure-noderna.

## <a name="next-steps"></a>Nästa steg
* Som ett alternativ till att använda HPC Pack kan du utveckla med Azure Batch-tjänsten att köra MPI-program på hanterade pooler av beräkningsnoder i Azure. Se [använda aktiviteter med flera instanser för att köra program med Message Passing Interface (MPI) i Azure Batch](../../../batch/batch-mpi.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
