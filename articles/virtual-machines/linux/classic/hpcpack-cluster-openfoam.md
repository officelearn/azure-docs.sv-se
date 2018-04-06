---
title: Kör OpenFOAM med HPC Pack på virtuella Linux-datorer | Microsoft Docs
description: Distribuera ett kluster för Microsoft HPC Pack på Azure och kör en OpenFOAM jobb på flera Linux compute-noder i ett nätverk med RDMA.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: c0bb1637-bb19-48f1-adaa-491808d3441f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/22/2016
ms.author: danlep
ms.openlocfilehash: f43790d3495e1c09730e90b5077ec840731a7d83
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Kör OpenFoam med Microsoft HPC Pack på ett Linux RDMA-kluster i Azure
Den här artikeln får du ett sätt att köra OpenFoam i virtuella Azure-datorer. Här kan du distribuera ett Microsoft HPC Pack kluster med Linux compute-noder på Azure och kör en [OpenFoam](http://openfoam.com/) jobbet med Intel MPI. Du kan använda RDMA-kompatibla virtuella Azure-datorer för compute-noder så att datornoderna kommunicera över nätverket Azure RDMA. Andra alternativ för att köra OpenFoam i Azure innehåller helt konfigurerade kommersiella bilder som finns på marknaden, till exempel Ubercloud's [OpenFoam 2.3 på CentOS 6](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/), och genom att köra på [Azure Batch](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (för åtgärden Öppna fältet och manipulering) är en öppen källkod beräkningsprogram (CFD)-programpaket som används ofta i tekniker och vetenskap i både kommersiella och academic organisationer. Den innehåller verktyg för meshing, särskilt snappyHexMesh en parallelized mesher för komplexa CAD-geometrier och för före och efter bearbetning. Nästan alla processer som körs parallellt, så att användarna kan dra full nytta av datormaskinvara tillgång.  

Microsoft HPC Pack tillhandahåller funktioner för att köra storskaliga HPC och parallella program, inklusive MPI program på kluster av Microsoft Azure-datorer. HPC Pack stöder också körs Linux HPC-program på Linux-beräkningsnod virtuella datorer distribueras i ett kluster med HPC Pack. Se [komma igång med Linux compute-noder i ett HPC Pack kluster i Azure](hpcpack-cluster.md) för en introduktion till Linux compute-noder med HPC Pack.

> [!NOTE]
> Den här artikeln visar hur du kör en Linux MPI arbetsbelastning med HPC Pack. Det förutsätts att du vara bekant med Linux systemadministration och MPI arbetsbelastningar som körs på Linux-kluster. Om du använder versioner av MPI och OpenFOAM skiljer sig från de som visas i den här artikeln kan behöva du ändra vissa installations- och konfigurationssteg. 
> 
> 

## <a name="prerequisites"></a>Krav
* **HPC Pack kluster med RDMA-kompatibla Linux datornoder** – distribuera ett HPC Pack kluster med storlek A8 A9, H16r, eller H16rm Linux compute-noder med antingen en [Azure Resource Manager-mall](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) eller en [Azure PowerShell-skriptet](hpcpack-cluster-powershell-script.md). Se [komma igång med Linux compute-noder i ett HPC Pack kluster i Azure](hpcpack-cluster.md) för kraven och steg för något av alternativen. Om du väljer distributionsalternativ för PowerShell-skript finns i exempelkonfigurationsfilen i exempelfiler i slutet av den här artikeln. Använd den här konfigurationen för att distribuera ett Azure-baserade HPC Pack kluster som består av en storlek A8 Windows Server 2012 R2 huvudnod och 2 storlek A8 SUSE Linux Enterprise Server 12 compute-noder. Ersätt lämpliga värden för namn på din prenumeration och tjänsten. 
  
  **Ytterligare viktiga punkter**
  
  * Linux RDMA nätverk krav i Azure, se [högpresterande compute VM-storlekar](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  * Om du använder distributionsalternativet för Powershell-skript kan du distribuera alla Linux compute-noderna i ett moln-tjänsten ska använda RDMA-nätverksanslutning.
  * Anslut med SSH att utföra några ytterligare administrativa uppgifter när du har distribuerat Linux-noder. Hitta information för SSH-anslutning för varje Linux-VM i Azure-portalen.  
* **Intel MPI** - om du vill köra OpenFOAM på SLES 12 HPC compute-noder i Azure, måste du installera Intel MPI biblioteket 5 runtime från den [Intel.com plats](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 är förinstallerat på CentOS-baserade HPC bilder.)  Installera Intel MPI på dina Linux compute-noder i ett senare steg om det behövs. För att förbereda för det här steget när du har registrerat med Intel, på länken i e-bekräftelse till relaterade webbsidan. Kopiera sedan hämtningslänken för filen .tgz lämplig version av Intel MPI. Den här artikeln är baserad på Intel MPI version 5.0.3.048.
* **OpenFOAM källa Pack** -ladda ned OpenFOAM källa Pack för Linux från den [OpenFOAM Foundation-webbplatsen](http://openfoam.org/download/2-3-1-source/). Den här artikeln är baserad på källan Pack-version 2.3.1-baserad, hämtas som OpenFOAM 2.3.1.tgz. Följ anvisningarna nedan om du vill packa upp och kompilera OpenFOAM på datornoderna Linux.
* **EnSight** (valfritt) – att se resultatet av dina OpenFOAM simulering, hämta och installera den [EnSight](https://www.ceisoftware.com/download/) visualisering och analys av programmet. Licensierings-och hämta finns på webbplatsen EnSight.

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Ställ in ömsesidigt förtroende mellan beräkningsnoder
Köra ett jobb mellan noder på flera Linux-noder måste noderna till förtroende för varandra (av **rsh** eller **ssh**). När du skapar klustret HPC Pack med Microsoft HPC Pack IaaS-distributionsskriptet konfigurerar skriptet automatiskt permanenta ömsesidigt förtroende för administratörskontot som du anger. För vanliga användare som du skapar i klustrets domän, har att ställa in tillfällig ömsesidigt förtroende mellan noder när ett jobb har tilldelats dem och ta bort relationen när jobbet har slutförts. Ange ett RSA-nyckelpar till klustret med HPC Pack för förtroendet för att upprätta förtroende för varje användare.

### <a name="generate-an-rsa-key-pair"></a>Generera en RSA-nyckelpar
Det är enkelt att generera ett RSA-nyckelpar, som innehåller en offentlig nyckel och en privat nyckel genom att köra Linux **ssh-keygen** kommando.

1. Logga in på en Linux-dator.
2. Kör följande kommando:
   
   ```
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Tryck på **RETUR** att använda standardinställningarna tills kommandot har slutförts. Ange inte en lösenfras här. När du uppmanas att ange ett lösenord trycker du bara på **RETUR**.
   > 
   > 
   
   ![Generera en RSA-nyckelpar][keygen]
3. Ändra katalog till katalogen ~/.ssh. Den privata nyckeln lagras i id_rsa och den offentliga nyckeln i id_rsa.pub.
   
   ![Privata och offentliga nycklar][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Lägga till nyckelparet i HPC Pack klustret
1. Gör en fjärrskrivbordsanslutning till din huvudnod med ditt administratörskonto HPC Pack (administratörskontot som du ställer in när du körde distributionsskriptet).
2. Använd standard Windows Server procedurer för att skapa ett domänanvändarkonto i klustrets Active Directory-domän. Till exempel använda verktyget Active Directory-användare och datorer i huvudnod. Exemplen i den här artikeln förutsätter att du skapar en domänanvändare med namnet hpclab\hpcuser.
3. Skapa en fil med namnet C:\cred.xml och kopiera RSA viktiga data till den. En exempelfil cred.xml är i slutet av den här artikeln.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
4. Öppna en kommandotolk och ange följande kommando för att ange autentiseringsuppgifter för kontot hpclab\hpcuser. Du använder den **extendeddata** parametern för att skicka namnet på C:\cred.xml fil som du skapade för viktiga data.
   
   ```
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Det här kommandot har slutförts utan utdata. Lagra filen cred.xml på en säker plats när du har angett autentiseringsuppgifterna för de användarkonton som du behöver köra jobb, eller ta bort den.
5. Glöm inte att ta bort nycklar när du är klar med dem. Om du genererade RSA-nyckelpar på en Linux-noder. Om HPC Pack hittar en befintlig id_rsa- eller id_rsa.pub fil, anger den inte ömsesidigt förtroende.

> [!IMPORTANT]
> Vi rekommenderar inte kör ett Linux-jobb som en Klusteradministratör i ett delat kluster, eftersom ett jobb som skickats av en administratör köras under rotkontot på Linux-noder. Dock körs ett jobb som skickats av en icke-administratörer under ett lokalt användarkonto för Linux med samma namn som användaren som jobbet. I det här fallet konfigurerar HPC Pack ömsesidigt förtroende för den här användaren Linux mellan noderna som allokerats till jobbet. Du kan konfigurera Linux-användare manuellt på Linux-noder innan du kör jobbet eller HPC Pack skapar användaren automatiskt när jobbet har skickats. Om HPC Pack skapar användaren, HPC Pack tar bort den när jobbet har slutförts. Tar bort tangenterna när jobbet har slutförts för att minska säkerhetsrisker HPC Pack.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Skapa en filresurs för Linux-noder
Nu ställa in en vanlig SMB-resurs på en mapp på huvudnoden. Montera den delade mappen så att de Linux-noderna till programfiler med en sökväg på Linux-noder. Om du vill kan använda du ett annat alternativ, till exempel filer för Azure-resurs - rekommenderas för många scenarier- eller en NFS-resurs för fildelning. Se filen dela information och detaljerade anvisningar i [komma igång med Linux compute-noder i ett HPC Pack-kluster i Azure](hpcpack-cluster.md).

1. Skapa en mapp på huvudnoden och dela den för alla genom att ange behörighet för läsning och skrivning. Till exempel dela C:\OpenFOAM på huvudnoden som \\ \\SUSE12RDMA HN\OpenFOAM. Här, *SUSE12RDMA HN* är värdnamnet på huvudnoden.
2. Öppna Windows PowerShell-fönstret och kör följande kommandon:
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
   clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
   ```

Det första kommandot skapar en mapp med namnet /openfoam på alla noder i gruppen LinuxNodes. Det andra kommandot monterar delad mapp //SUSE12RDMA-HN/OpenFOAM på Linux-noder med dir_mode och file_mode bits anger 777. Den *användarnamn* och *lösenord* kommandot ska vara autentiseringsuppgifterna för en användare på huvudnoden.

> [!NOTE]
> Den ”\`” symbol i det andra kommandot är en symbolen för PowerShell. ”\`”, betyder ””, (kommatecken tecken) är en del av kommandot.
> 
> 

## <a name="install-mpi-and-openfoam"></a>Installera MPI och OpenFOAM
Om du vill köra OpenFOAM som ett MPI-jobb i RDMA-nätverket, måste du kompilera OpenFOAM med Intel MPI-bibliotek. 

Kör först flera **clusrun** kommandon för att installera Intel MPI-bibliotek (om det inte redan är installerat) och OpenFOAM på Linux-noder. Använda resursen huvudnod konfigurerats tidigare för att dela installationsfilerna mellan Linux-noder.

> [!IMPORTANT]
> Dessa installation och kompilerar stegen är exempel. Du måste viss erfarenhet av Linux systemadministration så att beroende kompilerare och bibliotek har installerats korrekt. Du kan behöva ändra vissa miljövariabler eller andra inställningar för din version av Intel MPI och OpenFOAM. Mer information finns i [Intel MPI-biblioteket för Linux-installationsguiden](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) och [OpenFOAM källa installationen](http://openfoam.org/download/2-3-1-source/) för din miljö.
> 
> 

### <a name="install-intel-mpi"></a>Installera Intel MPI
Spara det hämtade installationspaketet för Intel MPI (l_mpi_p_5.0.3.048.tgz i det här exemplet) i C:\OpenFoam på huvudnoden så att Linux-noder kan komma åt den här filen från /openfoam. Kör sedan **clusrun** installera Intel MPI-biblioteket på alla Linux-noder.

1. Följande kommandon kopiera installationspaket och extrahera till /opt/intel på varje nod.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
   ```
2. Installera Intel MPI biblioteket tyst genom att använda en silent.cfg-fil. Du hittar ett exempel i exempelfiler i slutet av den här artikeln. Placera filen i den delade mappen /openfoam. Mer information om filen silent.cfg finns [Intel MPI-biblioteket för installationsguiden för Linux - tyst Installation](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).
   
   > [!TIP]
   > Kontrollera att du sparar filen som en textfil med Linux silent.cfg radbrytningar (endast LF, inte CR LF). Det här steget säkerställer att den körs korrekt på Linux-noder.
   > 
   > 
3. Installera Intel MPI biblioteket i tyst läge.
   
   ```
   clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
   ```

### <a name="configure-mpi"></a>Konfigurera MPI
För att testa, bör du lägga till följande rader /etc/security/limits.conf på varje Linux-nod:

    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Starta om Linux-noder när du uppdaterar filen limits.conf. Till exempel använda följande **clusrun** kommando:

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Se till att den delade mappen är monterade som /openfoam efter omstart.

### <a name="compile-and-install-openfoam"></a>Kompilera och installera OpenFOAM
Spara det hämtade installationspaketet för OpenFOAM källa Pack (OpenFOAM-2.3.1.tgz i det här exemplet) till C:\OpenFoam på huvudnoden så att Linux-noder kan komma åt den här filen från /openfoam. Kör sedan **clusrun** kommandon för att kompilera OpenFOAM på Linux-noder.

1. Skapa en mapp /opt/OpenFOAM på varje Linux-nod, kopiera paketet källan till den här mappen och extrahera det det.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
   ```
2. Kompilera OpenFOAM med Intel MPI bibliotek först ställa in miljövariabler för både Intel MPI och OpenFOAM. Använd ett bash-skript som heter settings.sh för att ange variabler. Du hittar ett exempel i exempelfiler i slutet av den här artikeln. Placera filen (som sparas med radbrytningar för Linux) i den delade mappen /openfoam. Den här filen innehåller inställningar för MPI och OpenFOAM körningar som du senare använda för att köra ett OpenFOAM-jobb.
3. Installera beroende paket som behövs för att kompilera OpenFOAM. Beroende på Linux-distribution kan du först behöver lägga till en databas. Kör **clusrun** kommandon som liknar följande:
   
    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
   
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
   
    Vid behov SSH till varje Linux-nod för att köra kommandon för att bekräfta att de fungerar.
4. Kör följande kommando för att kompilera OpenFOAM. Kompileringsprocessen tar en stund att slutföra och genererar en stor mängd logginformation till standardutdata, så den **/ överlagrat** alternativet för att visa utdata överlagrat.
   
   ```
   clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
   ```
   
   > [!NOTE]
   > Den ”\`” symbol i kommandot är en symbolen för PowerShell. ”\`&” innebär det ”&” är en del av kommandot.
   > 
   > 

## <a name="prepare-to-run-an-openfoam-job"></a>Förbereda för att köra ett OpenFOAM-jobb
Hämta nu redo att köra ett MPI-jobb som kallas sloshingTank3D som är en av OpenFoam exemplen på två Linux-noder. 

### <a name="set-up-the-runtime-environment"></a>Ställ in körningsmiljön
Om du vill konfigurera runtime-miljöer för MPI och OpenFOAM på Linux-noder, kör du följande kommando i Windows PowerShell-fönstret i huvudnod. (Det här kommandot är giltig för SUSE Linux endast.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Förbereda exempeldata
Använd huvudnod resursen som du tidigare konfigurerat för att dela filer mellan Linux-noder (monterade som /openfoam).

1. SSH till någon av dina Linux compute-noder.
2. Kör följande kommando för att ställa in körningsmiljö OpenFOAM om du inte redan har gjort detta.
   
   ```
   $ source /openfoam/settings.sh
   ```
3. Kopiera sloshingTank3D provet till den delade mappen och navigera till den.
   
   ```
   $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/
   
   $ cd /openfoam/sloshingTank3D
   ```
4. När du använder standardparametrarna för det här exemplet kan ta det flera minuter att köra, så kanske du vill ändra vissa parametrar för att göra det snabbare. Ett enkelt alternativ är att ändra tid step variabler deltaT och writeInterval i filen system/controlDict. Den här filen lagras alla indata som rör kontroll över tid och läsning och skrivning av data för lösningen. Du kan till exempel ändra värdet för deltaT från 0,05 till 0,5 och värdet för writeInterval från 0,05 till 0,5.
   
   ![Ändra steg variabler][step_variables]
5. Ange önskade värden för variabler i filen system/decomposeParDict. Det här exemplet använder två Linux noder varje med 8 kärnor, så anger du numberOfSubdomains 16 och n för hierarchicalCoeffs att (1 1 16), vilket betyder att köra OpenFOAM parallellt med 16 processer. Mer information finns i [OpenFOAM användarhandboken: 3,4 program som körs parallellt](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).
   
   ![Dela upp processer][decompose]
6. Kör följande kommandon från katalogen sloshingTank3D för att förbereda exempeldata.
   
   ```
   $ . $WM_PROJECT_DIR/bin/tools/RunFunctions
   
   $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict
   
   $ runApplication blockMesh
   
   $ cp 0/alpha.water.org 0/alpha.water
   
   $ runApplication setFields  
   ```
7. Du bör se exempelfilerna data kopieras till C:\OpenFoam\sloshingTank3D på noden head. (C:\OpenFoam är den delade mappen på huvudnoden.)
   
   ![Datafiler på huvudnoden][data_files]

### <a name="host-file-for-mpirun"></a>Värdfilen för mpirun
I det här steget skapar du en värd-fil (en lista över compute-noder) som den **mpirun** kommando använder.

1. På en Linux-noder, skapa en fil med namnet hostfile under /openfoam, så den här filen kan nås på /openfoam/hostfile på alla noder i Linux.
2. Skriv din Linux nod-namn i den här filen. Filen innehåller följande namn i det här exemplet:
   
   ```       
   SUSE12RDMA-LN1
   SUSE12RDMA-LN2
   ```
   
   > [!TIP]
   > Du kan också skapa den här filen på C:\OpenFoam\hostfile i huvudnod. Om du väljer det här alternativet, spara den som en textfil med Linux radbrytningar (endast LF, inte CR LF). Detta säkerställer att den körs korrekt på Linux-noder.
   > 
   > 
   
   **Omslutning för Bash-skript**
   
   Om du har många Linux noder och du vill att jobbet ska köras på bara några av dem, är det inte en bra idé att använda en fast värd-fil, eftersom du inte vet vilka noder som kommer att tilldelas ditt jobb. I det här fallet skriva ett bash-skript Omslutning för **mpirun** att skapa värdfilen automatiskt. Du kan hitta ett exempel bash-skript wrapper kallas hpcimpirun.sh i slutet av den här artikeln och spara den som /openfoam/hpcimpirun.sh. Det här exempelskriptet gör följande:
   
   1. Ställer in miljövariablerna för **mpirun**, och vissa parametrar för att lägga till kör MPI-jobb via nätverket RDMA. I så fall anger du följande variabler:
      
      * I_MPI_FABRICS=shm:dapl
      * I_MPI_DAPL_PROVIDER=ofa-v2-ib0
      * I_MPI_DYNAMIC_CONNECTION=0
   2. Skapar en värd-fil enligt miljön variabeln $CCP_NODES_CORES som anges av HPC-huvudnoden när jobbet är aktiverad.
      
      Formatet för $CCP_NODES_CORES följer detta mönster:
      
      ```
      <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
      ```
      
      där
      
      * `<Number of nodes>` -antalet noder som allokerats till det här jobbet.  
      * `<Name of node_n_...>` -namnet på varje nod som allokerats till det här jobbet.
      * `<Cores of node_n_...>` -antal kärnor på den nod som allokerats till det här jobbet.
      
      Till exempel om jobbet måste två noder för att köra, liknar $CCP_NODES_CORES
      
      ```
      2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
      ```
   3. Anrop av **mpirun** kommando och lägger till två parametrar på kommandoraden.
      
      * `--hostfile <hostfilepath>: <hostfilepath>` – sökvägen till värdfilen skapar skriptet
      * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` -en miljövariabel som angetts av huvudnod HPC Pack, som lagrar antalet Totalt antal kärnor som allokerats till det här jobbet. I det här fallet anger antalet processer för **mpirun**.

## <a name="submit-an-openfoam-job"></a>Skicka ett OpenFOAM-jobb
Nu kan du skicka ett jobb i HPC Cluster Manager. Du måste ange skript hpcimpirun.sh kommandorader för vissa av uppgifterna i jobbet.

1. Anslut till din klustrets huvudnod och starta HPC Cluster Manager.
2. **I resurshantering**, kontrollera att datornoderna Linux finns i den **Online** tillstånd. Om du inte markerar du dem och klickar på **Anslut**.
3. I **jobbhantering**, klickar du på **nytt jobb**.
4. Ange ett namn för jobbet som *sloshingTank3D*.
   
   ![Jobbinformation][job_details]
5. I **jobbet resurser**, Välj typ av resurs som ”nod” och ange minst till 2. Den här konfigurationen körs jobbet på två noder i Linux, var och en har åtta kärnor i det här exemplet.
   
   ![Jobbresurser][job_resources]
6. Klicka på **redigera uppgifter** i det vänstra navigeringsfönstret och klicka sedan på **Lägg till** att lägga till en aktivitet i jobbet. Lägga till fyra uppgifter i jobbet med följande kommandorader och inställningar.
   
   > [!NOTE]
   > Kör `source /openfoam/settings.sh` ställer in OpenFOAM och MPI runtime miljöer så att var och en av följande uppgifter anropar den före kommandot OpenFOAM.
   > 
   > 
   
   * **Uppgift 1**. Kör **decomposePar** att generera filer för att köra **interDyMFoam** parallellt.
     
     * Tilldela en nod till aktiviteten
     * **Kommandorad** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
     * **Arbetskatalogen** -/ openfoam/sloshingTank3D
     
     I följande bild. Du konfigurerar de återstående uppgifterna på samma sätt.
     
     ![Uppgift 1 information][task_details1]
   * **Uppgift 2**. Kör **interDyMFoam** parallellt att beräkna exemplet.
     
     * Tilldela aktiviteten två noder
     * **Kommandorad** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`
     * **Arbetskatalogen** -/ openfoam/sloshingTank3D
   * **Uppgift 3**. Kör **reconstructPar** att koppla uppsättningar med tid-kataloger från varje processor_N_ katalog till en enda uppsättning.
     
     * Tilldela en nod till aktiviteten
     * **Kommandorad** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`
     * **Arbetskatalogen** -/ openfoam/sloshingTank3D
   * **Uppgift 4**. Kör **foamToEnsight** parallellt konvertera OpenFOAM resultatet filerna till EnSight formatera och placera EnSight-filer i en katalog med namnet Ensight i katalogen case.
     
     * Tilldela aktiviteten två noder
     * **Kommandorad** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`
     * **Arbetskatalogen** -/ openfoam/sloshingTank3D
7. Lägga till beroenden i dessa uppgifter i stigande ordning.
   
   ![Aktivitetssamband][task_dependencies]
8. Klicka på **skicka** jobbet ska köras.
   
   Som standard skickar HPC Pack jobb som din aktuella inloggade användarkontot. När du klickar på **skicka**, du kan se en dialogruta där du uppmanas att ange användarnamn och lösenord.
   
   ![Jobbautentiseringsuppgifter][creds]
   
   Under vissa förhållanden HPC Pack kommer ihåg användarinformationen du inkommande innan och inte visa den här dialogrutan. HPC Pack visa igen, ange följande kommando vid en kommandotolk och skicka jobbet.
   
   ```
   hpccred delcreds
   ```
9. Jobbet tar från flera minuter till flera timmar enligt de parametrar som du har angett för. Termisk karta visas i jobbet körs på Linux-noder. 
   
   ![Termisk karta][heat_map]
   
   På varje nod startas åtta processer.
   
   ![Linux-processer][linux_processes]
10. Hitta jobbresultaten i mappar under C:\OpenFoam\sloshingTank3D och loggfilerna på C:\OpenFoam när jobbet har slutförts.

## <a name="view-results-in-ensight"></a>Visa resultat i EnSight
Du kan också använda [EnSight](https://www.ceisoftware.com/) att visualisera och analysera resultaten av OpenFOAM jobbet. Finns det mer information om visualisering och animeringen i EnSight [video guiden](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1. När du har installerat EnSight på huvudnoden startar du den.
2. Open C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.
   
   Du kan se en behållare i visningsprogrammet.
   
   ![Tanken i EnSight][tank]
3. Skapa en **Isosurface** från **internalMesh**, och välj sedan variabeln **alpha_water**.
   
   ![Skapa en isosurface][isosurface]
4. Ange färgen för **Isosurface_part** skapade i föregående steg. Till exempel den till vattenstämplar blå.
   
   ![Redigera isosurface färg][isosurface_color]
5. Skapa en **Iso-volym** från **väggar** genom att välja **väggar** i den **delar** panelen och klicka på den **Isosurfaces** i verktygsfältet.
6. I dialogrutan Välj **typen** som **Isovolume** och ange det minsta värdet av **Isovolume intervallet** till 0,5. Klicka för att skapa isovolume **skapa med valda delar**.
7. Ange färgen för **Iso_volume_part** skapade i föregående steg. Till exempel den till djup vattenstämplar blå.
8. Ange färgen för **väggar**. Till exempel den transparent vit.
9. Klicka på **spela upp** att se resultatet av simuleringen.
   
    ![Tanken resultat][tank_result]

## <a name="sample-files"></a>Exempelfiler
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Exempel XML-konfigurationsfilen för kluster-distributionen med PowerShell-skript
 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

### <a name="sample-credxml-file"></a>Exempelfilen cred.xml
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### <a name="sample-silentcfg-file-to-install-mpi"></a>Exempelfilen silent.cfg installera MPI
```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Exempelskript för settings.sh
```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


### <a name="sample-hpcimpirunsh-script"></a>Exempelskript för hpcimpirun.sh
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]:media/hpcpack-cluster-openfoam/keygen.png
[keys]:media/hpcpack-cluster-openfoam/keys.png
[step_variables]:media/hpcpack-cluster-openfoam/step_variables.png
[data_files]:media/hpcpack-cluster-openfoam/data_files.png
[decompose]:media/hpcpack-cluster-openfoam/decompose.png
[job_details]:media/hpcpack-cluster-openfoam/job_details.png
[job_resources]:media/hpcpack-cluster-openfoam/job_resources.png
[task_details1]:media/hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]:media/hpcpack-cluster-openfoam/task_dependencies.png
[creds]:media/hpcpack-cluster-openfoam/creds.png
[heat_map]:media/hpcpack-cluster-openfoam/heat_map.png
[tank]:media/hpcpack-cluster-openfoam/tank.png
[tank_result]:media/hpcpack-cluster-openfoam/tank_result.png
[isosurface]:media/hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]:media/hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]:media/hpcpack-cluster-openfoam/linux_processes.png
