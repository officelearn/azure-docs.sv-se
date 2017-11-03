---
title: "NAMD Microsoft HPC Pack på virtuella Linux-datorer | Microsoft Docs"
description: "Distribuera ett Microsoft HPC Pack kluster på Azure och köra en NAMD simulering med charmrun på flera Linux compute-noder"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.openlocfilehash: 0c0b9875b4153edcc0ec0096577d041d394a842f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Kör NAMD med Microsoft HPC Pack på beräkningsnoder för Linux i Azure
Den här artikeln får du ett sätt att köra en Linux högpresterande datorbearbetning (HPC) arbetsbelastningen på virtuella Azure-datorer. Här kan du ställa in en [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) på Azure-kluster med beräkningsnoder för Linux och köra en [NAMD](http://www.ks.uiuc.edu/Research/namd/) simuleringen beräknas och visualisera strukturen för en stor biomolecular system.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (för Nanoscale molekylära Dynamics program) är en parallell molekylära dynamics paket designat för högpresterande simulering av stora biomolecular system med upp till miljontals atomer. Exempel på dessa system är virus, cell strukturer och stora protein. NAMD skalas hundratals kärnor för vanliga simulering och mer än 500 000 kärnor för största simulering.
* **Microsoft HPC Pack** tillhandahåller funktioner för att köra storskaliga HPC och parallella program i kluster av lokala datorer eller virtuella Azure-datorer. Ursprungligen utvecklades som en lösning för Windows HPC-arbetsbelastningar, HPC Pack nu stöder som kör Linux HPC-program på Linux compute-nod virtuella datorer som distribueras i ett HPC Pack-kluster. Se [komma igång med Linux compute-noder i ett HPC Pack kluster i Azure](hpcpack-cluster.md) en introduktion.

## <a name="prerequisites"></a>Krav
* **HPC Pack kluster med Linux datornoder** -distribuera ett kluster med HPC Pack med Linux compute-noder på Azure med hjälp av antingen en [Azure Resource Manager-mall](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) eller en [Azure PowerShell-skriptet](hpcpack-cluster-powershell-script.md). Se [komma igång med Linux compute-noder i ett HPC Pack kluster i Azure](hpcpack-cluster.md) för kraven och steg för något av alternativen. Om du väljer distributionsalternativ för PowerShell-skript finns i exempelkonfigurationsfilen i exempelfiler i slutet av den här artikeln. Den här filen konfigurerar ett Azure-baserade HPC Pack kluster som består av en Windows Server 2012 R2 huvudnod och fyra storlek stora CentOS 6.6 compute-noder. Anpassa den här filen behövs för din miljö.
* **NAMD programvara och kursen filer** -hämta NAMD programvara för Linux från den [NAMD](http://www.ks.uiuc.edu/Research/namd/) plats (registrering krävs). Den här artikeln är baserad på NAMD version 2.10 och använder den [Linux-x86_64 (64-bitars Intel/AMD med Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) Arkiv. Också ladda ned den [NAMD självstudiekursen filer](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Hämtningsbara filer på är .tar-filer, och du behöver ett Windows-verktyg för att extrahera filerna på klustrets huvudnod. Följ anvisningarna nedan om du vill extrahera filerna. 
* **VMD** (valfritt) – se resultatet av jobbet NAMD, hämta och installera programmet molekylära visualiseringen [VMD](http://www.ks.uiuc.edu/Research/vmd/) på en dator som du väljer. Den aktuella versionen är 1.9.2. Se VMD hämtningsplatsen för att komma igång.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Ställ in ömsesidigt förtroende mellan beräkningsnoder
Köra ett jobb mellan noder på flera Linux-noder måste noderna till förtroende för varandra (av **rsh** eller **ssh**). När du skapar klustret HPC Pack med Microsoft HPC Pack IaaS-distributionsskriptet konfigurerar skriptet automatiskt permanenta ömsesidigt förtroende för administratörskontot som du anger. För icke-administratörer du skapar i klustrets domän, måste du ställa in tillfällig ömsesidigt förtroende mellan noder när ett jobb har tilldelats dem. Förstör relationen när jobbet har slutförts. Om du vill göra detta för varje användare, ange ett RSA-nyckelpar till klustret som HPC Pack används för att upprätta en förtroenderelation. Följ instruktionerna.

### <a name="generate-an-rsa-key-pair"></a>Generera en RSA-nyckelpar
Det är enkelt att generera ett RSA-nyckelpar, som innehåller en offentlig nyckel och en privat nyckel genom att köra Linux **ssh-keygen** kommando.

1. Logga in på en Linux-dator.
2. Kör följande kommando:
   
   ```bash
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
1. [Ansluta med Remote Desktop](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) till huvudnod VM med domänen autentiseringsuppgifter du anges när du distribuerade klustret (till exempel hpc\clusteradmin). Du kan hantera klustret från huvudnod.
2. Använd standard Windows Server procedurer för att skapa ett domänanvändarkonto i klustrets Active Directory-domän. Till exempel använda verktyget Active Directory-användare och datorer i huvudnod. Exemplen i den här artikeln förutsätter att du skapar en domänanvändare med namnet hpcuser i domänen hpclab (hpclab\hpcuser).
3. Lägg till domänanvändaren i HPC Pack klustret som en användare i klustret. Instruktioner finns i [Lägg till eller ta bort klustret användare](https://technet.microsoft.com/library/ff919330.aspx).
4. Skapa en fil med namnet C:\cred.xml och kopiera RSA viktiga data till den. Du hittar ett exempel i exempelfiler i slutet av den här artikeln.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. Öppna en kommandotolk och ange följande kommando för att ange autentiseringsuppgifter för kontot hpclab\hpcuser. Du använder den **extendeddata** parametern för att skicka namnet på filen C:\cred.xml du skapade för viktiga data.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Det här kommandot har slutförts utan utdata. Lagra filen cred.xml på en säker plats när du har angett autentiseringsuppgifterna för de användarkonton som du behöver köra jobb, eller ta bort den.
6. Glöm inte att ta bort nycklar när du är klar med dem. Om du genererade RSA-nyckelpar på en Linux-noder. HPC Pack inte ställts in ömsesidigt förtroende om den hittar en befintlig id_rsa- eller id_rsa.pub-fil.

> [!IMPORTANT]
> Vi rekommenderar inte kör ett Linux-jobb som en Klusteradministratör i ett delat kluster, eftersom ett jobb som skickats av en administratör köras under rotkontot på Linux-noder. Ett jobb som skickats av en icke-administratörer körs under ett lokalt användarkonto för Linux med samma namn som användaren som jobbet. I det här fallet konfigurerar HPC Pack ömsesidigt förtroende för den här Linux-användare på alla noder som allokerats till jobbet. Du kan konfigurera Linux-användare manuellt på Linux-noder innan du kör jobbet eller HPC Pack skapar användaren automatiskt när jobbet har skickats. Om HPC Pack skapar användaren, HPC Pack tar bort den när jobbet har slutförts. Om du vill minska säkerhetshot tas nycklarna bort när jobbet har slutförts på noderna.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Skapa en filresurs för Linux-noder
Nu konfigurera en SMB-filresurs och montera den delade mappen på alla Linux-noder för att tillåta Linux-noder att komma åt NAMD filer med en sökväg. Nedan följer stegen för att ansluta till en delad mapp på huvudnoden. En resurs som rekommenderas för distributioner, till exempel CentOS 6.6 som för närvarande inte stöd för tjänsten för Azure. Om Linux-noder stöder en Azure-filresurs, se [använda Azure File storage med Linux](../../../storage/files/storage-how-to-use-files-linux.md). För ytterligare alternativ för delning med HPC Pack-fil, se [komma igång med Linux compute-noder i ett HPC Pack-kluster i Azure](hpcpack-cluster.md).

1. Skapa en mapp på huvudnoden och dela den för alla genom att ange behörighet för läsning och skrivning. I det här exemplet \\ \\CentOS66HN\Namd är namnet på mappen där CentOS66HN är värdnamnet för huvudnoden.
2. Skapa en undermapp som heter namd2 i den delade mappen. Skapa en annan undermapp som heter namdsample i namd2.
3. Extrahera NAMD filerna i mappen med hjälp av en Windows-versionen av **tar** eller något annat verktyg i Windows som körs på .tar Arkiv. 
   
   * Extrahera NAMD tar arkivet till \\ \\CentOS66HN\Namd\namd2.
   * Extraheras självstudier under \\ \\CentOS66HN\Namd\namd2\namdsample.
4. Öppna Windows PowerShell-fönstret och kör följande kommandon för att montera den delade mappen på Linux-noder.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Det första kommandot skapar en mapp med namnet /namd2 på alla noder i gruppen LinuxNodes. Det andra kommandot monterar delade mappen //CentOS66HN/Namd/namd2 till mappen med dir_mode och file_mode bits anger 777. Den *användarnamn* och *lösenord* kommandot ska vara autentiseringsuppgifterna för en användare på huvudnoden.

> [!NOTE]
> Den ”\`” symbol i det andra kommandot är en symbolen för PowerShell. ”\`”, betyder ””, (kommatecken tecken) är en del av kommandot.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>Skapa ett Bash-skript för att köra ett NAMD-jobb
Jobbet NAMD måste en *nodelist* för **charmrun** att fastställa antalet noder som ska användas när du startar NAMD processer. Du använder ett Bash-skript som genererar nodlistfilen och kör **charmrun** med den här nodlistfilen. Du kan sedan skicka ett NAMD jobb i HPC Cluster Manager som anropar det här skriptet.

Med hjälp av en textredigerare önskat, skapa ett Bash-skript i mappen /namd2 som innehåller NAMD programfiler och ger den namnet hpccharmrun.sh. Kopiera hpccharmrun.sh exempelskriptet tillhandahålls i slutet av den här artikeln för en snabb konceptbevis och gå till [skickar ett jobb för NAMD](#submit-a-namd-job).

> [!TIP]
> Spara skriptet som en textfil med Linux radbrytningar (endast LF, inte CR LF). Detta säkerställer att den körs korrekt på Linux-noder.
> 
> 

Nedan visas information om den här bash-skript har. 

1. Definiera några variabler.
   
   ```bash
   #!/bin/bash
   
   # The path of this script
   SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
   # Charmrun command
   CHARMRUN=${SCRIPT_PATH}/charmrun
   # Argument of ++nodelist
   NODELIST_OPT="++nodelist"
   # Argument of ++p
   NUMPROCESS="+p"
   ```
2. Hämta information om noden från miljövariablerna. $NODESCORES lagras en lista över delade ord från $CCP_NODES_CORES. $COUNT är storleken på $NODESCORES.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   Formatet för $CCP_NODES_CORES variabeln är följande:
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   Den här variabeln visas det totala antalet noder, nodnamn och antalet kärnor på varje nod som är allokerade till jobbet. Till exempel om jobbet måste 10 kärnor att köra, liknar värdet för $CCP_NODES_CORES:
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. Starta om $CCP_NODES_CORES variabeln inte anges **charmrun** direkt. (Detta bör endast inträffa när du kör det här skriptet direkt på Linux-noder.)
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. Eller skapa en nodlistfilen för **charmrun**.
   
   ```
   else
     # Create the nodelist file
     NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$
   
     # Write the head line
     echo "group main" > ${NODELIST_PATH}
   
     # Get every node name and number of cores and write into the nodelist file
     I=1
     while [ ${I} -lt ${COUNT} ]
     do
         echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
         let "I=${I}+2"
     done
   ```
5. Kör **charmrun** med nodlistfilen, hämta dess returstatus och ta bort nodlistfilen i slutet.
   
   ${CCP_NUMCPUS} är en annan miljövariabel som angetts av huvudnod i HPC Pack. Antal Totalt antal kärnor som tilldelats det här jobbet lagras. Vi använder för att ange antalet processer för charmrun.
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. Avsluta med den **charmrun** returstatus.
   
   ```
   exit ${RTNSTS}
   ```

Följande är nodlistfilen som skriptet genererar information:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Exempel:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Skicka ett NAMD-jobb
Du är nu redo att skicka en NAMD jobbet i HPC Cluster Manager.

1. Anslut till din klustrets huvudnod och starta HPC Cluster Manager.
2. I **resurshantering**, kontrollera att datornoderna Linux finns i den **Online** tillstånd. Om du inte markerar du dem och klickar på **Anslut**.
3. I **jobbhantering**, klickar du på **nytt jobb**.
4. Ange ett namn för jobbet som *hpccharmrun*.
   
   ![Nytt HPC-jobb][namd_job]
5. På den **jobbinformation** sidan under **jobbet resurser**, Välj typ av resurs som **nod** och ange den **minsta** till 3. , vi kör jobbet på tre noder för Linux och varje nod har fyra kärnor.
   
   ![Jobbet resurser][job_resources]
6. Klicka på **redigera uppgifter** i det vänstra navigeringsfönstret och klicka sedan på **Lägg till** att lägga till en aktivitet i jobbet.    
7. På den **information om aktiviteter och i/o-omdirigering** ställer du in följande värden:
   
   * **Kommandoraden**-
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > Föregående kommandoraden är ett enda kommando utan radbrytningar. Den radbryts ska visas på flera rader under **kommandoraden**.
     > 
     > 
   * **Arbetskatalogen** -/namd2
   * **Minsta** – 3
     
     ![Uppgiftsinformation][task_details]
     
     > [!NOTE]
     > Du har angett arbetskatalogen här eftersom **charmrun** försöker att navigera till samma arbetskatalogen på varje nod. Om arbetskatalogen har inte angetts, startas HPC Pack kommandot i en mapp för slumpmässigt namn som skapats på en Linux-noder. Detta gör följande fel på de andra noderna: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Undvik problemet genom att ange en mappsökväg som kan nås av alla noder som arbetskatalogen.
     > 
     > 
8. Klicka på **OK** och klicka sedan på **skicka** jobbet ska köras.
   
   Som standard skickar HPC Pack jobb som din aktuella inloggade användarkontot. En dialogruta kanske uppmanas du att ange användarnamn och lösenord när du klickar på **skicka**.
   
   ![Jobbet autentiseringsuppgifter][creds]
   
   Under vissa förhållanden HPC Pack kommer ihåg användarinformationen du inkommande innan och inte visa den här dialogrutan. HPC Pack visa igen, ange följande kommando vid en kommandotolk och skicka jobbet.
   
   ```command
   hpccred delcreds
   ```
9. Det tar flera minuter att slutföra jobbet.
10. Hitta jobblogg på \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log och utdatafiler i \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.
11. Du kan också starta VMD om du vill visa resultatet av jobbet. Stegen för att visualisera NAMD utdata filer (i det här fallet en ubiquitin protein molekylen vattenstämplar gäller) ligger utanför omfånget för den här artikeln. Se [NAMD kursen](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) mer information.
    
    ![Jobbet resultat][vmd_view]

## <a name="sample-files"></a>Exempelfiler
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Exempel XML-konfigurationsfilen för kluster-distributionen med PowerShell-skript
```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
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

### <a name="sample-hpccharmrunsh-script"></a>Exempelskript för hpccharmrun.sh
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 

<!--Image references-->
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png
