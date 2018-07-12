---
title: HPC Pack-kluster för Excel och SOA | Microsoft Docs
description: Kom igång med att köra storskaliga Excel och SOA-arbetsbelastningar på ett HPC Pack-kluster i Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: aaf26e04fdb38fd76f4ab8211f9fdda8ebafd668
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971867"
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Kom igång med att köra Excel och SOA-arbetsbelastningar på ett HPC Pack-kluster i Azure
Den här artikeln visar hur du distribuerar ett Microsoft HPC Pack 2012 R2-kluster på Azure virtual machines med hjälp av en Azure-snabbstartsmall eller du kan också en Azure PowerShell-distributionsskriptet. Klustret använder Azure Marketplace-VM-avbildningar som utformats för att köra Microsoft Excel eller tjänstorienterad arkitektur (SOA) arbetsbelastningar med HPC Pack. Du kan använda klustret för att köra Excel HPC- och SOA-tjänster från en lokal klientdator. Excel-HPC-tjänster är Excel arbetsboksavlastning och användardefinierade funktioner i Excel eller UDF: er.

> [!IMPORTANT] 
> Den här artikeln är baserad på funktioner, mallar och skript för HPC Pack 2012 R2. Det här scenariot stöds inte för närvarande i HPC Pack 2016.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

På en hög nivå i följande diagram visas HPC Pack-kluster du skapar.

![HPC-kluster med noder som kör arbetsbelastningar för Excel][scenario]

## <a name="prerequisites"></a>Förutsättningar
* **Klientdatorn** -behöver du en Windows-baserad klient-dator för att skicka exempel Excel och SOA-jobb i klustret. Du behöver också en Windows-dator för att köra Azure PowerShell-distributionsskriptet för kluster (om du väljer den distributionsmetoden).
* **Azure-prenumeration** -om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.
* **Kärnkvot** – du kan behöva öka kärnkvoten kärnor, särskilt om du distribuerar flera klusternoder med flera kärnor VM-storlekar. Om du använder en Azure-snabbstartsmall är kärnkvoten i Resource Manager per Azure-region. I så fall kan behöva du öka kärnkvoten i en viss region. Se [Azure-prenumeration gränser, kvoter och begränsningar](../../azure-subscription-service-limits.md). Att öka en kvot [öppna en supportbegäran online-kund](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) utan kostnad.
* **Microsoft Office-licens** – om du distribuerar compute-noder med en Marketplace HPC Pack 2012 R2 VM-avbildning med Microsoft Excel, en 30-dagars utvärderingsversion av Microsoft Excel Professional Plus 2013 har installerats. När utvärderingsperioden måste du ange en giltig licens för Microsoft Office för att aktivera Excel för att fortsätta att köra arbetsbelastningar. Se [Excel-aktivering](#excel-activation) senare i den här artikeln. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Steg 1. Konfigurera ett HPC Pack-kluster i Azure
Vi visar två alternativ för att konfigurera HPC Pack 2012 R2-kluster: första, med hjälp av en Snabbstart för Azure-mall och Azure-portalen; och sekund, med hjälp av en Azure PowerShell-distributionsskriptet.

### <a name="option-1-use-a-quickstart-template"></a>Alternativ 1. Använd en snabbstartsmall
Använda en Azure-snabbstartsmall för att snabbt distribuera ett HPC Pack-kluster i Azure-portalen. När du öppnar mallen i portalen kan få du ett enkelt gränssnitt där du kan ange inställningarna för ditt kluster. Här följer stegen. 

> [!TIP]
> Om du vill använda en [Azure Marketplace-mall](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) som skapar ett kluster med liknande specifikt för Excel-arbetsbelastningar. Stegen skiljer sig från följande.
> 
> 

1. Gå till den [skapa HPC-kluster mallsidan på GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Om du vill kan du granska information om mallen och källkoden.
2. Klicka på **distribuera till Azure** att starta en distribution med hjälp av mallen i Azure-portalen.
   
   ![Distribuera mall till Azure][github]
3. Följ stegen nedan för att ange parametrar för mallen HPC-kluster i portalen.
   
   a. På den **parametrar** sidan, anger eller ändrar värden för mallparametrarna. (Klicka på ikonen bredvid varje inställning för hjälpinformation.) I följande skärmbild visas exempelvärden. Det här exemplet skapar ett kluster med namnet *hpc01* i den *hpc.local* domän som består av en huvudnod och 2 compute-noder. Compute-noder har skapats från en HPC Pack VM-avbildning som innehåller Microsoft Excel.
   
   ![Ange parametrar][parameters-new-portal]
   
   > [!NOTE]
   > Huvudnoden VM skapas automatiskt från den [senaste Marketplace-avbildning](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) HPC Pack 2012 R2 på Windows Server 2012 R2. Bilden är för närvarande baserat på HPC Pack 2012 R2 uppdatering 3.
   > 
   > Compute-nod virtuella datorer skapas från den senaste avbildningen i valda compute-nod-familjen. Välj den **ComputeNodeWithExcel** alternativ för den senaste HPC Pack compute-nod-avbildning som innehåller en utvärderingsversion av Microsoft Excel Professional Plus 2013. Om du vill distribuera ett kluster för allmänna SOA-sessionerna eller Excel-UDF-avlastning kan du välja den **ComputeNode** alternativet (utan Excel installerat).
   > 
   > 
   
   b. Välj prenumerationen.
   
   c. Skapa en resursgrupp för klustret, till exempel *hpc01RG*.
   
   d. Välj en plats för resursgruppen, till exempel USA, centrala.
   
   e. På den **juridiska villkor** läser du igenom villkoren. Om du godkänner villkoren klickar du på **köp**. När du har angett värden för mallen, klicka **skapa**.
4. När distributionen har slutförts (det tar vanligtvis cirka 30 minuter), exportera certifikatfilen klustret från klustrets huvudnod. I ett senare steg kan du importera den här offentliga certifikat på klientdatorn för att autentisera servern för säker HTTP-bindning.
   
   a. I Azure-portalen, gå till instrumentpanelen, väljer du huvudnoden och klicka på **Connect** överst på sidan för att ansluta med hjälp av fjärrskrivbord.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Använd standard procedurer i Certifikathanteraren för att exportera huvudnoden certifikatet (finns under Cert: \LocalMachine\My) utan den privata nyckeln. Exportera i det här exemplet *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![Exportera certifikatet][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Alternativ 2. Använd skript för HPC Pack IaaS-distribution
Skriptet för HPC Pack IaaS-distribution är en annan flexibelt sätt att distribuera ett HPC Pack-kluster. Det skapar ett kluster i den klassiska distributionsmodellen, medan mallen använder Azure Resource Manager-distributionsmodellen. Skriptet är också kompatibla med en prenumeration i tjänsten Azure Global eller Azure Kina.

**Ytterligare krav**

* **Azure PowerShell** - [installera och konfigurera Azure PowerShell](/powershell/azure/overview) (version 0.8.10 eller senare) på din klientdator.
* **HPC Pack IaaS-distributionsskriptet** – hämta och packa upp den senaste versionen av skriptet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Kontrollera vilken version av skriptet genom att köra `New-HPCIaaSCluster.ps1 –Version`. Den här artikeln är baserad på version 4.5.0 eller senare av skriptet.

**Skapa konfigurationsfilen**

 Skriptet för HPC Pack IaaS-distribution använder en XML-konfigurationsfil som indata som beskriver infrastrukturen för HPC-kluster. Om du vill distribuera ett kluster som består av en huvudnod och 18 compute-noder som skapats från den avbildning för beräkningsnod med Microsoft Excel, ersätter du värdena för din miljö i konfigurationsfilen i följande exempel. Mer information om konfigurationsfilen finns i filen Manual.rtf i mappen skript och [skapa ett HPC-kluster med skriptet för HPC Pack IaaS-distribution](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Information om konfigurationsfilen**

* Den **VMName** för huvudnoden **måste** vara samma som den **ServiceName**, SOA-jobb misslyckas att köra.
* Kontrollera att du anger **EnableWebPortal** så att certifikatet huvudnoden genereras och exporteras.
* Filen anger ett efter konfigurationen på en PowerShell-skript PostConfig.ps1 som körs på klustrets huvudnod. Följande exempelskript konfigurerar anslutningssträngen för Azure storage, tar bort noden beräkningsrollen från klustrets huvudnod och ger dig alla noder online när de har distribuerats. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Kör skript**

1. Öppna PowerShell-konsolen på klientdatorn som administratör.
2. Ändra katalogen till mappen skript (E:\IaaSClusterScript i det här exemplet).
   
   ```
   cd E:\IaaSClusterScript
   ```
3. Kör följande kommando för att distribuera HPC Pack-kluster. Det här exemplet förutsätts att konfigurationsfilen finns i E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

Skriptet för HPC Pack-distribution som körs under en viss tid. En sak som skriptet gör är att exportera och ladda ned certifikatet för klustret och spara den i den aktuella användarens dokument-mappen på klientdatorn. Skriptet genererar ett meddelande som liknar följande. I följande steg importerar du certifikatet i certifikatarkivet lämplig.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Steg 2. Avlasta Excel-arbetsböcker och kör UDF: er från en lokal klient
### <a name="excel-activation"></a>Excel-aktivering
När du använder ComputeNodeWithExcel VM-avbildning för produktionsarbetsbelastningar kan behöva du ange en giltig licensnyckel för Microsoft Office för att aktivera Excel på beräkningsnoderna. I annat fall utvärderingsversionen av Excel upphör att gälla efter 30 dagar och köra Excel-arbetsböcker misslyckas med COMException (0x800AC472). 

Du kan återställa Excel i 30 dagar för utvärderingstid: Logga in på huvudnoden och clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` på alla Excel compute-noder via HPC Cluster Manager. Du kan återställa högst två gånger. Därefter måste du ange en giltig licensnyckel för Office.

Office Professional Plus 2013 har installerats på VM-avbildning är en volymutgåva med en allmän nyckel GVLK (Volume License). Du kan aktivera den via nyckelhanteringstjänsten (KMS) / Active Directory-Based aktivering (AD BA) eller fleraktiveringsnyckel (MAK). 

    * Om du vill använda KMS/AD-BA, använda en befintlig KMS-server eller skapa en ny med hjälp av Microsoft Office 2013 Volume License Pack. (Om du vill ställa in server på huvudnoden.) Aktivera KMS-värdnyckeln via Internet eller per telefon. Sedan clusrun `ospp.vbs` att KMS-servern och porten och aktivera Office på alla Excel compute-noder. 

    * Kan använda MAK, första clusrun `ospp.vbs` att ange nyckeln och sedan aktivera alla Excel compute-noder via Internet eller per telefon. 

> [!NOTE]
> Detaljhandel produktnycklar för Office Professional Plus 2013 kan inte användas med den här Virtuella datorn. Om du har giltiga nycklar och installationsmediet Office eller Excel-versioner än den här Office Professional Plus 2013 volymutgåva kan använda du dem i stället. Först avinstallera den här volymen-versionen och installera den version som du har. Ominstallerade Excel Beräkningsnoden samlas in som en anpassad VM-avbildning som ska användas i en distribution i stor skala.
> 
> 

### <a name="offload-excel-workbooks"></a>Avlasta Excel-arbetsböcker
Följ dessa steg för att avlasta en Excel-arbetsbok så att den körs på HPC Pack-kluster i Azure. Om du vill göra detta måste måste du ha Excel 2010 eller 2013 som redan är installerad på klientdatorn.

1. Använd något av alternativen i steg 1 för att distribuera ett HPC Pack-kluster med Excel beräkningsavbildning noden. Hämta kluster-certifikatfil (.cer) och kluster användarnamn och lösenord.
2. Importera klustercertifikat under Cert: \CurrentUser\Root på klientdatorn.
3. Kontrollera att Excel har installerats. Skapa en Excel.exe.config-fil med följande innehåll i samma mapp som Excel.exe på klientdatorn. Det här steget säkerställer att HPC Pack 2012 R2 Excel COM-tillägg har lästs in.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. Konfigurera klienten för att skicka jobb till HPC Pack-kluster. Ett alternativ är att hämta fullständiga [HPC Pack 2012 R2 uppdatering 3 installation](http://www.microsoft.com/download/details.aspx?id=49922) och installera HPC Pack-klienten. Du kan också hämta och installera den [HPC Pack 2012 R2 uppdatering 3 klientverktyg](https://www.microsoft.com/download/details.aspx?id=49923) och den lämpliga Visual C++ 2010 redistributable för datorn ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555) ).
5. I det här exemplet använder vi en Excel-exempelarbetsboken med namnet ConvertiblePricing_Complete.xlsb. Du kan hämta det [här](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
6. Kopiera Excel-arbetsboken till en arbetsmapp, till exempel D:\Excel\Run.
7. Öppna Excel-arbetsboken. På den **utveckla** menyfliksområdet, klickar du på **COM-tillägg** och bekräfta att HPC Pack Excel COM-tillägg har lästs in.
   
   ![Excel-tillägg för HPC Pack][addin]
8. Redigera ett makro HPCControlMacros i Excel genom att ändra de kommenterade raderna enligt följande skript. Ersätt lämpliga värden för din miljö.
   
   ![Excel-makro för HPC Pack][macro]
   
   ```
   'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
   Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"
   
   'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
   Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.Initialize ActiveWorkbook
   HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles
   
   'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
   HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
   HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
   ```
9. Kopiera Excel-arbetsboken till en katalog för uppladdning, till exempel D:\Excel\Upload. Den här katalogen har angetts i HPC_DependsFiles konstant i ett makro.
10. Om du vill köra arbetsboken på klustret i Azure, klickar du på den **kluster** knappen i kalkylbladet.

### <a name="run-excel-udfs"></a>Kör Excel-UDF: er
Följ föregående steg 1 – 3 för att konfigurera datorn för att köra Excel-UDF: er. För Excel-UDF: er behöver du inte har installerat på beräkningsnoder Excel-program. Så när du skapar klustret compute-noder, kan du välja en normal beräkningsavbildning noden i stället för avbildningen för beräkningsnod med Excel.

> [!NOTE]
> Det finns en 34 tecken i Excel 2010 och 2013-kluster connector-dialogrutan. Du kan använda den här dialogrutan för att ange det kluster som kör de UDF: er. Om fullständig klustrets namn är längre (till exempel hpcexcelhn01.southeastasia.cloudapp.azure.com) passar inte i dialogrutan. Lösningen är att ange en datoromfattande variabel som *CCP_IAASHN* med värdet för lång klustrets namn. Ange sedan *% CCP_IAASHN %* i dialogrutan som klustrets huvudnod namn. 
> 
> 

När klustret har distribuerats, fortsätter du med följande steg för att köra ett exempel som är inbyggda Excel-UDF. Anpassade Excel UDF: er, finns i dessa [resurser](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) och skapa XLL som du kan distribuera dem på IaaS-kluster.

1. Öppna en ny Excel-arbetsbok. På den **utveckla** menyfliksområdet, klickar du på **tillägg**. Klicka i dialogrutan **Bläddra**, navigera till mappen %CCP_HOME%Bin\XLL32 och välj exempel ClusterUDF32.xll. Om ClusterUDF32 inte finns på klientdatorn, kopierar du den från mappen %CCP_HOME%Bin\XLL32 på huvudnoden.
   
   ![Välj en användardefinierad funktion][udf]
2. Klicka på **filen** > **alternativ** > **avancerade**. Under **formler**, kontrollera **Tillåt användardefinierade XLL funktioner att köra ett beräkningskluster**. Klicka sedan på **alternativ** och ange fullständig klusternamnet i **klustrets huvudnod namn**. (Som anges tidigare den här textrutan är begränsad till 34 tecken så långt klusternamnet inte kanske passar. Du kan använda en datoromfattande variabeln här för ett långt klusternamn.)
   
   ![Konfigurera en användardefinierad funktion][options]
3. Om du vill köra beräkningen UDF i klustret, klicka på cellen med värdet =XllGetComputerNameC() och tryck på RETUR. Funktionen hämtar bara namnet på Beräkningsnoden där en användardefinierad funktion körs. För den första körningen uppmanas en dialogruta för autentiseringsuppgifter för användarnamn och lösenord att ansluta till IaaS-kluster.
   
   ![Köra UDF][run]
   
   När det finns många celler att beräkna, tryck på Alt-Shift-Ctrl + F9 för att köra beräkningen på alla celler.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Steg 3. Köra en SOA-arbetsbelastning från en lokal klient
Om du vill köra Allmänt SOA-program i HPC Pack IaaS-klustret, först använda någon av metoderna i steg 1 för att distribuera klustret. Ange en allmän beräkningsavbildning noden i det här fallet eftersom du inte behöver Excel på beräkningsnoderna. Följ dessa steg.

1. När du har hämtat klustercertifikatet, importerar du den på klientdatorn under Cert: \CurrentUser\Root.
2. Installera den [HPC Pack 2012 R2 uppdatering 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) och [HPC Pack 2012 R2 uppdatering 3 klientverktyg](https://www.microsoft.com/download/details.aspx?id=49923). Dessa verktyg kan du utveckla och kör SOA-klientprogram.
3. Ladda ned HelloWorldR2 [exempelkoden](https://www.microsoft.com/download/details.aspx?id=41633). Öppna HelloWorldR2.sln i Visual Studio 2010 eller 2012. (Det här exemplet är inte kompatibelt med nyare versioner av Visual Studio.)
4. Skapa EchoService projektet först. Sedan distribuera tjänsten till IaaS-klustret på samma sätt som du distribuerar till ett lokalt kluster. Detaljerade anvisningar finns i Viktigt.doc i HelloWordR2. Ändra och skapa HellWorldR2 och andra projekt som beskrivs i följande avsnitt för att generera SOA-klientprogram som körs på ett Azure IaaS-kluster.

### <a name="use-http-binding-with-azure-storage-queue"></a>Använd Http-bindning med Azure storage-kö
Om du vill använda Http-bindning med ett Azure storage-kö gör några ändringar i exempelkoden.

* Uppdatera klustrets namn.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* Du kan också använda standard TransportScheme i SessionStartInfo eller uttryckligen ställa in den till Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Använd standard-bindning för BrokerClient.
  
    ```
  // Before
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
  // After
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
  ```
  
    Eller ange uttryckligen med hjälp av basicHttpBinding.
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* Du kan också ställa in flaggan UseAzureQueue på Sant i SessionStartInfo. Om inte har det anges till SANT som standard när klustrets namn har Azure domänsuffix och TransportScheme är Http.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Använd Http-bindning utan Azure storage-kö
Om du vill använda Http-bindning utan en Azure storage-kö uttryckligen ställa in flaggan UseAzureQueue till false i SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Använd NetTcp bindning
Om du vill använda NetTcp bindning liknar konfigurationen ansluta till ett lokalt kluster. Du behöver öppna några slutpunkter på huvudnoden VM. Om du använder skriptet för HPC Pack IaaS-distribution för att skapa klustret, till exempel slutpunkterna i Azure-portalen på följande sätt.

1. Stoppa den virtuella datorn.
2. Lägg till TCP-portar 9090, 9087, 9091, 9094 för sessionen, Mäkla, Mäkla worker- och datatjänster, respektive
   
    ![Konfigurera slutpunkter][endpoint-new-portal]
3. Starta den virtuella datorn.

SOA-klientprogram kräver inga ändringar förutom ändra head namnet till det fullständiga namnet för IaaS-kluster.

## <a name="next-steps"></a>Nästa steg
* Se [resurserna](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) för mer information om hur du kör Excel arbetsbelastningar med HPC Pack.
* Se [hantera SOA-tjänster i Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) för mer information om distribution och hantering av SOA-tjänster med HPC Pack.

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png
