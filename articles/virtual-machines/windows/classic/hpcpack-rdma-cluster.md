---
title: "Ställa in ett RDMA-Windows-kluster som kör MPI program | Microsoft Docs"
description: "Lär dig hur du skapar ett Windows HPC Pack kluster med storlek H16r H16mr, A8 eller A9 virtuella datorer för att köra MPI appar med Azure RDMA-nätverket."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: 19be1d693fe13af0f6c1ab0cb6f7bc829b9fad5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Ställa in ett RDMA-Windows-kluster med HPC Pack som kör MPI-program
Konfigurera ett RDMA för Windows-kluster i Azure med [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) och [högpresterande compute VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) att köra parallella Message Passing Interface (MPI) program. När du ställer in RDMA-kompatibla, Windows Server-baserade noder i ett kluster med HPC Pack kommunikation MPI program effektiv via en låg latens, hög genomströmning nätverk i Azure som är baserad på remote direct memory access (RDMA)-teknik.

Om du vill köra MPI arbetsbelastningar på virtuella Linux-datorer som kommer åt nätverket Azure RDMA, se [ställa in ett Linux RDMA-kluster som kör MPI program](../../linux/classic/rdma-cluster.md).

## <a name="hpc-pack-cluster-deployment-options"></a>HPC Pack distributionsalternativ för kluster
Microsoft HPC Pack är ett verktyg utan extra kostnad för att skapa HPC-kluster lokalt eller i Azure för att köra Windows eller Linux HPC-program. HPC Pack innehåller en körningsmiljö för Microsofts implementering av meddelandet passerar gränssnittet för Windows (MS-MPI). När det används med RDMA-kompatibla instanser med en Windows Server-operativsystem som stöds, ger HPC Pack ett effektivt alternativ för att köra Windows MPI-program som ansluter till Azure RDMA-nätverket. 

Den här artikeln introducerar två scenarier och länkar till detaljerade riktlinjer för att konfigurera ett RDMA för Windows-kluster med Microsoft HPC Pack. 

* Scenario 1. Distribuera beräkningsintensiva worker rollinstanser (PaaS)
* Scenario 2. Distribuera beräkningsnoder i beräkningsintensiva virtuella datorer (IaaS)

Allmänna krav för att använda beräkningsintensiva instanser med Windows, se [högpresterande compute VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scenario 1: Distribuera beräkningsintensiva worker rollinstanser (PaaS)
Lägga till extra beräkningsresurser i Azure worker rollinstanser (Azure noder) körs i en molntjänst (PaaS) från ett befintligt paket för HPC-kluster. Den här funktionen kallas även ”burst to Azure” från HPC Pack stöder flera olika storlekar för rollen arbetarinstanser. Ange en av RDMA-kompatibla storlekar när du lägger till Azure-noder.

Följande är överväganden och steg för att burst till RDMA-kompatibla Azure-instanser från en befintlig (vanligtvis lokalt) klustret. Använd liknande förfaranden för att lägga till rollen arbetarinstanser en huvudnod i HPC Pack som distribueras i en Azure VM.

> [!NOTE]
> Självstudier för burst till Azure med HPC Pack, se [ställer in en hybrid-kluster med HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Observera överväganden i följande steg gäller särskilt för RDMA-kompatibla Azure-noder.
> 
> 

![Burst till Azure][burst]

### <a name="steps"></a>Steg
1. **Distribuera och konfigurera en huvudnod i HPC Pack 2012 R2**
   
    Hämta det senaste installationspaketet för HPC Pack från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922). Krav och anvisningar för att förbereda för distribution av en Azure burst finns [Burst to Azure Worker-instanser med Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).
2. **Konfigurera ett certifikat i Azure-prenumeration**
   
    Konfigurera ett certifikat för säker anslutning mellan huvudnod och Azure. Alternativ och procedurer finns [scenarier för att konfigurera Azure-Hanteringscertifikatet för HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). HPC Pack installeras testdistributioner kan en standard Microsoft HPC Azure-Hanteringscertifikatet du snabbt kan överföra till din Azure-prenumeration.
3. **Skapa en ny molntjänst och ett lagringskonto**
   
    Använda Azure portal för att skapa en tjänst i molnet och storage-konto för distribution i en region där RDMA-kompatibla instanserna är tillgängliga.
4. **Skapa en mall för Azure nod**
   
    Använd den noden guiden Skapa i HPC Cluster Manager. Anvisningar finns [skapar en mall för Azure noden](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) i ”steg för att distribuera Azure noder med Microsoft HPC Pack”.
   
    Första testerna föreslår vi hur du konfigurerar en princip för manuell tillgänglighet i mallen.
5. **Lägga till noder i klustret**
   
    Användning av guiden Lägg till noder i HPC Cluster Manager. Mer information finns i [lägga till Azure-noder till Windows HPC-kluster](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    När du anger storleken på noderna, Välj en av RDMA-kompatibla instans storlekar.
   
   > [!NOTE]
   > I varje burst till Azure-distribution med beräkningsintensiva instanser distribuerar HPC Pack automatiskt minst två RDMA-kompatibla instanser (till exempel A8) som proxy noder, utöver Azure worker-rollinstanser som du anger. Proxy-noder använder kärnor som är allokerade till prenumerationen och avgifter tillsammans med rollinstanser Azure worker.
   > 
   > 
6. **Starta (tillhandahålla) noder och anpassa dem online för att köra jobb**
   
    Markera noderna och använda den **starta** åtgärd i HPC Cluster Manager. När etableringen är klar, markera noderna och använda den **Anslut** åtgärd i HPC Cluster Manager. Noderna är redo att köra jobb.
7. **Skicka jobb till klustret**
   
   Använda HPC Pack jobbet skicka verktyg för att köra kluster-jobb. Se [Microsoft HPC Pack: jobbhantering](http://technet.microsoft.com/library/jj899585.aspx).
8. **Stoppa (avetablering) noder**
   
   När du är klar jobb som körs, noderna offline och använda den **stoppa** åtgärd i HPC Cluster Manager.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scenario 2: Distribuera datornoder i beräkningsintensiva virtuella datorer (IaaS)
I det här scenariot kan distribuera du huvudnod i HPC Pack och beräkning klusternoder på virtuella datorer i Azure-nätverk. HPC Pack innehåller flera [distributionsalternativ i Azure Virtual Machines](../../linux/hpcpack-cluster-options.md), inklusive skript för automatisk distribution och Azure quickstart mallar. Exempelvis följande information och anvisningar för hur du kan använda den [HPC Pack IaaS distributionsskriptet](hpcpack-cluster-powershell-script.md) att automatisera distributionen av ett HPC Pack 2012 R2-kluster i Azure.

![Klustret i virtuella Azure-datorer][iaas]

### <a name="steps"></a>Steg
1. **Skapa en klustrets huvudnod och compute-nod virtuella datorer genom att köra skriptet HPC Pack IaaS distribution på en klientdator**
   
    Hämta HPC Pack IaaS-distributionsskriptet paketet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922).
   
    För att förbereda klientdatorn, skapa konfigurationsfilen och kör skriptet, se [skapa ett HPC-kluster med HPC Pack IaaS-distributionsskriptet](hpcpack-cluster-powershell-script.md). 
   
    Observera följande ytterligare överväganden för att distribuera RDMA-kompatibla compute-noder:
   
   * **Virtuellt nätverk**: Ange ett nytt virtuellt nätverk i en region där du vill använda RDMA-kompatibla instansstorleken är tillgänglig.
   * **Operativsystemet Windows Server**: Ange ett Windows Server 2012 R2 eller Windows Server 2012-operativsystem för beräkningsnod virtuella datorer för att stödja RDMA-anslutning.
   * **Molntjänster**: Vi rekommenderar att distribuera din huvudnod i en molntjänst och dina compute-noder i en annan molntjänst.
   * **Gå nodstorlek**: det här scenariot bör du en storlek på minst A4 (Extra stor) för huvudnoden.
   * **HpcVmDrivers tillägget**: distributionsskriptet installerar Azure VM-agenten och tillägget HpcVmDrivers automatiskt när du distribuerar storlek A8 eller A9 compute-noder med en Windows Server-operativsystem. HpcVmDrivers installerar drivrutiner på Beräkningsnoden virtuella datorer så att de kan ansluta till nätverkets RDMA. På RDMA-kompatibla H-serien virtuella datorer, måste du manuellt installera tillägget HpcVmDrivers. Se [högpresterande compute VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Klusternätverkskonfigurationen**: distributionsskriptet ställer automatiskt in HPC Pack klustret i topologin 5 (alla noder på företagsnätverket). Den här topologin krävs för alla HPC Pack klustret distributioner i virtuella datorer. Ändra inte klustret nätverkets topologi senare.
2. **Kör jobb gör datornoderna online**
   
    Markera noderna och använda den **Anslut** åtgärd i HPC Cluster Manager. Noderna är redo att köra jobb.
3. **Skicka jobb till klustret**
   
    Ansluta till huvudnoden att skicka jobb eller konfigurera den lokala datorn för att göra detta. Mer information finns i [skicka jobb till ett HPC-kluster i Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Koppla noderna och stoppa (frigöra) dem.**
   
    När du är klar jobb som körs, och ta offline noder i HPC Cluster Manager. Använd sedan Azure-hanteringsverktyg kan stängas av.

## <a name="run-mpi-applications-on-the-cluster"></a>Kör MPI-program i klustret
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Exempel: Kör mpipingpong i ett HPC Pack-kluster
För att verifiera ett HPC Pack distribution av RDMA-kompatibla instanser, kör du HPC Pack **mpipingpong** på klustret. **mpipingpong** skickar datapaket mellan parad noderna flera gånger för att beräkna svarstid och genomströmning mått och statistik för nätverkets RDMA-aktiverade program. Det här exemplet illustrerar ett typiskt mönster för att köra ett MPI-jobb (i det här fallet **mpipingpong**) med hjälp av klustret **mpiexec** kommando.

Det här exemplet förutsätter att du har lagt till Azure-noder i en konfiguration för ”burst till Azure” ([Scenario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Om du har distribuerat HPC Pack på ett kluster med virtuella Azure-datorer måste du ändra kommandosyntax för att ange en annan nod och ställa in ytterligare miljövariabler för att dirigera nätverkstrafik till nätverkets RDMA.

Köra mpipingpong i klustret:

1. Öppna en kommandotolk på huvudnoden eller på en korrekt konfigurerad klientdator.
2. Skriv följande kommando för att skicka ett jobb för att köra mpipingpong med en liten paketstorlek och många iterationer för att beräkna fördröjning mellan två noder i en Azure burst-distribution med fyra noder:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    Kommandot returnerar ID för det jobb som har skickats.
   
    Om du har distribuerat HPC Pack klustret distribueras på Azure Virtual Machines, ange en nodgrupp som innehåller compute-nod virtuella datorer som distribueras i en enda molntjänst och ändra den **mpiexec** kommandot på följande sätt:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. När jobbet har slutförts, om du vill visa resultatet (i det här fallet utdata för uppgift 1 för jobbet), skriver du följande
   
    ```Command
    task view <JobID>.1
    ```
   
    där &lt; *JobID* &gt; är ID för det jobb som har skickats.
   
    Utdata innehåller latens resultat som liknar följande.
   
    ![Svarstid för ping-pong][pingpong1]
4. Om du vill beräkna dataflödet mellan två Azure burst noder, skriver du följande kommando för att skicka ett jobb för att köra **mpipingpong** med ett stort paket, storlek och några iterationer:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    Kommandot returnerar ID för det jobb som har skickats.
   
    Ändra kommandot som du antecknade i steg 2 i ett HPC Pack-kluster som distribueras på virtuella Azure-datorer.
5. När jobbet har slutförts, om du vill visa resultatet (i det här fallet utdata för uppgift 1 för jobbet), skriver du följande:
   
    ```Command
    task view <JobID>.1
    ```
   
   Utdata innehåller genomströmning resultat som liknar följande.
   
   ![Ping pong genomflöde][pingpong2]

### <a name="mpi-application-considerations"></a>Överväganden för MPI-program
Följande är överväganden för MPI program som körs med HPC Pack i Azure. Vissa gäller endast för distributioner av Azure-noder (rollen arbetarinstanser som ska läggas till i en konfiguration för ”burst till Azure”).

* Worker-rollinstanser i en molntjänst är regelbundet etableras utan föregående meddelande av Azure (till exempel för Systemunderhåll eller om en instans misslyckas). Om en instans är etableras medan den körs ett MPI-jobb, instansen förlorar data och återgår till tillståndet när den första distribuerades, vilket kan orsaka MPI-jobb misslyckas. Flera noder som du använder för en enstaka MPI-jobb och desto längre tid jobbet körs, desto mer sannolikt som någon av instanserna är etableras medan ett jobb körs. Överväg också att detta om du anger en enskild nod i distributionen som en filserver.
* Om du vill köra MPI-jobb i Azure, behöver du använda RDMA-kompatibla instanser. Du kan använda valfri instans storlek som stöds av HPC Pack. RDMA-kompatibla instanser bör dock för att köra relativt stora MPI-jobb som är känsliga för svarstiden och bandbredden för nätverket som ansluter noderna. Om du använder andra storlekar för att köra och bandbredd-känslig för fördröjningar MPI-jobb, rekommenderar vi kör små jobb, där en enskild aktivitet körs på bara några noder.
* Program som distribueras till Azure-instanser regleras licensvillkor som är associerade med programmet. Kontrollera med leverantören av kommersiella program för licensiering eller andra begränsningar för körs i molnet. Alla leverantörer erbjuder inte licensiering enligt modellen Betala per användning.
* Azure-instanser måste ytterligare inställningar för åtkomst till lokala noder, resurser och servrar för fjärrskrivbordslicenser. Du kan till exempel konfigurera en Azure-nätverk för plats-till-plats om du vill aktivera Azure-noder till ett lokalt licensservern.
* Om du vill köra MPI-program på Azure-instanser måste registrera varje MPI-program med Windows-brandväggen för instanser genom att köra den **hpcfwutil** kommando. Detta gör att MPI kommunikation ska kunna utföras på en port som tilldelas dynamiskt av brandväggen.
  
  > [!NOTE]
  > Du kan också konfigurera brandväggen undantag kommandon automatiskt på alla nya Azure noder som har lagts till i klustret för burst till Azure-distributioner. När du har kört den **hpcfwutil** och kontrollera att dina program fungerar, Lägg till kommandot ett startskript för din Azure-noder. Mer information finns i [använder ett startskript för Azure-noder](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* HPC Pack använder miljövariabeln CCP_MPI_NETMASK klustret för att ange ett intervall med godkända adresser för MPI-kommunikation. Från och med HPC Pack 2012 R2, miljövariabeln CCP_MPI_NETMASK klustret påverkar endast MPI kommunikationen mellan noder domänanslutna beräkning (antingen lokalt eller i virtuella Azure-datorer). Variabeln ignoreras av noder som lagts till i en burst till Azure-konfiguration.
* MPI-jobb kan inte köras i Azure-instanser som har distribuerats i annat moln services (till exempel burst till Azure-distributioner med olika noden mallar eller Azure VM datornoderna distribueras i flera molntjänster). Om du har flera Azure noden distributioner som startas med olika noden mallar måste MPI-jobb köras på en enda uppsättning Azure-noder.
* När du lägger till Azure-noder i klustret och tar dem online försöker schemaläggningstjänsten för HPC-jobb direkt Starta jobb på noderna. Om bara en del av din arbetsbelastning kan köra på Azure, kontrollera att du uppdaterar eller skapar du jobbmallar för att definiera vilka typer av jobb kan köras på Azure. För att säkerställa att jobb som skickats med en jobbmall för bara köras på Azure-noder, till exempel lägga till egenskapen noden grupper till mall och välj AzureNodes som obligatoriskt värde. Använd Lägg till HpcGroup HPC PowerShell-cmdleten för att skapa anpassade grupper för din Azure-noder.

## <a name="next-steps"></a>Nästa steg
* Som ett alternativ till att använda HPC Pack kan du utveckla Azure Batch-tjänsten för att köra MPI program på hanterade pooler med beräkningsnoder i Azure. Se [använder flera instanser uppgifter för att köra program för Message Passing Interface (MPI) i Azure Batch](../../../batch/batch-mpi.md).
* Om du vill köra Linux MPI program som kommer åt nätverket Azure RDMA, se [ställa in ett Linux RDMA-kluster som kör MPI program](../../linux/classic/rdma-cluster.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
