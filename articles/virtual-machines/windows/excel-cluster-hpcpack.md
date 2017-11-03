---
title: "HPC Pack kluster för Excel och SOA | Microsoft Docs"
description: "Kom igång storskaliga Excel- och SOA-arbetsbelastningar som körs i ett HPC Pack-kluster i Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: 63babd94fdab15217cfb0757e4cd6efe458a628d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Kom igång Excel- och SOA-arbetsbelastningar som körs i ett HPC Pack-kluster i Azure
Den här artikeln visar hur du distribuerar ett Microsoft HPC Pack 2012 R2-kluster på virtuella Azure-datorer med hjälp av en mall för Azure quickstart eller alternativt en Azure PowerShell-distributionsskriptet. Klustret använder Azure Marketplace VM-avbildningar som utformats för att köra Microsoft Excel eller tjänstorienterad arkitektur (SOA) arbetsbelastningar med HPC Pack. Du kan använda klustret för att köra Excel HPC- och SOA-tjänster från en klientdator för lokalt. Excel HPC-tjänster omfattar arbetsboksavlastning i Excel och Excel användardefinierade funktioner eller UDF: er.

> [!IMPORTANT] 
> Den här artikeln baseras på funktioner, mallar och skript för HPC Pack 2012 R2. Det här scenariot stöds inte för närvarande i HPC Pack 2016.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

På en hög nivå i följande diagram visas HPC Pack kluster du skapar.

![HPC-kluster med noder som kör Excel arbetsbelastningar][scenario]

## <a name="prerequisites"></a>Krav
* **Klientdatorn** -behöver du en Windows-baserad klientdator att skicka exempel Excel- och SOA-jobb till klustret. Du måste också Windows-datorer kör distributionsskriptet för Azure PowerShell-klustret (om du väljer att distributionsmetoden).
* **Azure-prenumeration** -om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.
* **Kärnor kvoten** – du kan behöva öka kvoten för kärnor, särskilt om du distribuerar flera klusternoder med flera kärnor VM-storlekar. Om du använder en mall för Azure quickstart är kärnor kvot i Resource Manager per Azure-region. I så fall kan du behöva öka kvoten i en viss region. Se [Azure-prenumeration gränser, kvoter och begränsningar](../../azure-subscription-service-limits.md). Att öka kvoten [öppna en supportbegäran online customer](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) utan kostnad.
* **Microsoft Office-licens** - om du distribuerar compute-noder som använder en Marketplace HPC Pack 2012 R2 VM-avbildning med Microsoft Excel, en 30-dagars utvärderingsversion av Microsoft Excel Professional Plus 2013 har installerats. Du måste ange en giltig licens för Microsoft Office för att aktivera Excel för att fortsätta att köra arbetsbelastningar efter utvärderingsperioden. Se [Excel aktivering](#excel-activation) senare i den här artikeln. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Steg 1. Konfigurera ett HPC Pack kluster i Azure
Visar vi två alternativ för att konfigurera HPC Pack 2012 R2-kluster: första, med hjälp av en mall för Azure quickstart och Azure-portalen. och andra som använder ett skript för distribution av Azure PowerShell.

### <a name="option-1-use-a-quickstart-template"></a>Alternativ 1. Använd en mall för Snabbstart
Använd en mall för Azure quickstart för att snabbt distribuera ett HPC Pack kluster i Azure-portalen. När du öppnar mallen i portalen visas ett enkelt gränssnitt där du kan ange inställningarna för klustret. Här följer stegen. 

> [!TIP]
> Om du vill använda en [Azure Marketplace mallen](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) som skapar ett liknande kluster specifikt för Excel-arbetsbelastningar. Stegen skiljer sig från följande.
> 
> 

1. Besök den [skapa HPC-kluster mallsida på GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Om du vill granska information om mallen och källkoden.
2. Klicka på **till Azure** att starta en distribution med hjälp av mallen i Azure-portalen.
   
   ![Distribuera mallen till Azure][github]
3. Följ dessa steg om du vill ange parametrar för mallen för HPC-kluster i portalen.
   
   a. På den **parametrar** anger eller ändrar värdena för mallparametrarna. (Klicka på ikonen bredvid varje inställning för hjälp.) I följande skärmbild visas exempelvärden. Det här exemplet skapar ett kluster med namnet *hpc01* i den *hpc.local* domän som består av en huvudnod och 2 compute-noder. Compute-noder har skapats från en HPC Pack VM-avbildning som omfattar Microsoft Excel.
   
   ![Ange parametrar][parameters-new-portal]
   
   > [!NOTE]
   > Huvudnoden VM skapas automatiskt från den [senaste Marketplace-avbildning](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) av HPC Pack 2012 R2 på Windows Server 2012 R2. Bilden är för närvarande baserat på HPC Pack 2012 R2 uppdatering 3.
   > 
   > Compute-nod virtuella datorer skapas från den senaste bilden i den valda beräkning nod familjen. Välj den **ComputeNodeWithExcel** alternativ för den senaste HPC Pack compute-nod-avbildning som omfattar en utvärderingsversion av Microsoft Excel Professional Plus 2013. Om du vill distribuera ett kluster för allmän SOA-sessioner eller Excel-UDF-avlastning, Välj den **ComputeNode** alternativ (utan Excel installerat).
   > 
   > 
   
   b. Välj prenumerationen.
   
   c. Skapa en resursgrupp för klustret, t.ex *hpc01RG*.
   
   d. Välj en plats för resursgruppen, till exempel centrala USA.
   
   e. På den **juridiska villkor** läser du igenom licensvillkoren. Om du godkänner villkoren klickar du på **inköp**. När du är klar med att ange värden för mallen, klicka på **skapa**.
4. När distributionen är klar (det tar vanligtvis cirka 30 minuter), exportera klustret certifikatfilen från klustrets huvudnod. I ett senare steg kan du importera den här offentliga certifikat på klientdatorn för serversidan autentisering av säker HTTP-bindning.
   
   a. Gå till instrumentpanelen, markera huvudnoden och klicka i Azure-portalen **Anslut** längst upp på sidan för att ansluta med hjälp av fjärrskrivbord.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Använd standard procedurer i Certifikathanteraren för att exportera huvudnod certifikatet (finns under Cert: \LocalMachine\My) utan att den privata nyckeln. Exportera i det här exemplet *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![Exportera certifikatet][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Alternativ 2. Använd skriptet HPC Pack IaaS-distribution
HPC Pack IaaS-distributionsskriptet tillhandahåller flexibel kan också distribuera ett kluster med HPC Pack. Den skapar ett kluster i den klassiska distributionsmodellen medan mallen använder Azure Resource Manager-distributionsmodellen. Skriptet är också kompatibla med en prenumeration i Azure Global eller Azure Kina service.

**Ytterligare krav**

* **Azure PowerShell** - [installera och konfigurera Azure PowerShell](/powershell/azure/overview) (version 0.8.10 eller senare) på din klientdator.
* **HPC Pack IaaS distributionsskriptet** – hämta och packa upp den senaste versionen av skriptet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Kontrollera vilken version av skriptet genom att köra `New-HPCIaaSCluster.ps1 –Version`. Den här artikeln är baserat på version 4.5.0 eller senare av skriptet.

**Skapa konfigurationsfil**

 HPC Pack IaaS-distributionsskriptet använder en XML-konfigurationsfil som indata som beskriver infrastrukturen i HPC-kluster. Ersätt värdena för din miljö i konfigurationsfilen i följande exempel för att distribuera ett kluster som består av en huvudnod och 18 compute-noder som skapas från noden beräknings-avbildningen som innehåller Microsoft Excel. Mer information om konfigurationsfilen finns i Manual.rtf-filen i mappen skript och [skapa ett HPC-kluster med HPC Pack IaaS-distributionsskriptet](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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

* Den **VMName** av huvudnoden **måste** vara samma som den **ServiceName**, SOA-jobb misslyckas att köra.
* Kontrollera att du anger **EnableWebPortal** så att huvudnod certifikatet genereras och exporteras.
* Filen anger ett efterkonfiguration PowerShell-skript PostConfig.ps1 som körs på huvudnoden. Följande exempelskript konfigurerar anslutningssträngen för Azure storage, tar du bort noden beräkningsrollen från huvudnod och ger alla noder online när de distribueras. 

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

**Kör skriptet**

1. Öppna konsolen PowerShell på datorn som administratör.
2. Ändra katalogen till mappen skript (E:\IaaSClusterScript i det här exemplet).
   
   ```
   cd E:\IaaSClusterScript
   ```
3. Kör följande kommando för att distribuera HPC Pack klustret. Det här exemplet förutsätter att konfigurationsfilen finns i E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

HPC Pack distributionsskriptet körs under en viss tid. En sak som skriptet gör är att exportera och hämta certifikat för klustret och spara den i den aktuella användarens dokument-mappen på klientdatorn. Skriptet genererar ett meddelande som liknar följande. I följande steg ska importera du certifikatet i certifikatarkivet lämplig.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Steg 2. Avlastning Excel-arbetsböcker och köra UDF: er från en lokal klient
### <a name="excel-activation"></a>Excel-aktivering
Du måste ange en giltig licensnyckel för Microsoft Office för att aktivera Excel på datornoderna när du använder ComputeNodeWithExcel VM-avbildning för produktionsarbetsbelastningar. Annars utvärderingsversionen av Excel upphör att gälla efter 30 dagar och kör Excel-arbetsböcker misslyckas med COMException (0x800AC472). 

Du kan återställa Excel med ytterligare 30 dagar utvärdering tid: Logga in på huvudnod och clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` på alla Excel datornoder via HPC Cluster Manager. Du kan återställa högst två gånger. Därefter måste du ange en giltig licensnyckel Office.

Office Professional Plus 2013 har installerats på VM-avbildning är en volymutgåva med en allmän nyckel GVLK (Volume License). Du kan aktivera den via nyckelhanteringstjänsten (KMS) / Active Directory-Based aktivering (AD BA) eller fleraktiveringsnyckel (MAK). 

    * Använd en befintlig KMS-server för att använda AD/KMS-BA eller Ställ in en ny med hjälp av Microsoft Office 2013 Volume License Pack. (Om du vill ställa in server på huvudnoden.) Aktivera sedan KMS-värdnyckeln via Internet eller per telefon. Sedan clusrun `ospp.vbs` göra KMS-servern och porten och aktivera Office på alla Excel compute-noder. 

    * Att använda MAK första clusrun `ospp.vbs` att ange nyckeln och sedan aktivera alla Excel datornoder via Internet eller per telefon. 

> [!NOTE]
> Retail produktnycklar för Office Professional Plus 2013 kan inte användas med den här Virtuella datorn. Om du har giltiga nycklar och installationsmediet för Office eller Excel-versioner än den här Office Professional Plus 2013 volym-versionen kan använda du dem i stället. Först avinstallera den här volymen-versionen och installera den version som du har. Ominstallerade Excel Beräkningsnoden kan sparas som en anpassad VM-avbildning som ska användas i en distribution i stor skala.
> 
> 

### <a name="offload-excel-workbooks"></a>Avlastning Excel-arbetsböcker
Följ dessa steg för att avlasta en Excel-arbetsbok så att den körs på klustret HPC Pack i Azure. Om du vill göra detta måste du ha Excel 2010 eller 2013 redan installerat på klientdatorn.

1. Använd något av alternativen i steg 1 för att distribuera ett HPC Pack kluster med Excel compute-nod avbildningen. Hämta klustret certifikatfil (.cer) och klustret användarnamn och lösenord.
2. Importera certifikatet kluster under Cert: \CurrentUser\Root på klientdatorn.
3. Kontrollera att Excel har installerats. Skapa en Excel.exe.config-fil med följande innehåll i samma mapp som Excel.exe på klientdatorn. Det här steget säkerställer att HPC Pack 2012 R2 Excel COM-tillägg har lästs in.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. Konfigurera klienten för att skicka jobb till klustret HPC Pack. Ett alternativ är att hämta fullständigt [HPC Pack 2012 R2 uppdatering 3 installation](http://www.microsoft.com/download/details.aspx?id=49922) och installera klienten HPC Pack. Du kan också hämta och installera den [HPC Pack 2012 R2 uppdatering 3 klientverktyg](https://www.microsoft.com/download/details.aspx?id=49923) och den lämpliga Visual C++ 2010 redistributable för datorn ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555) ).
5. Vi använder en exempel Excel-arbetsbok med namnet ConvertiblePricing_Complete.xlsb i det här exemplet. Du kan ladda ned det [här](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
6. Kopiera Excel-arbetsboken till en arbetsmapp, till exempel D:\Excel\Run.
7. Öppna Excel-arbetsboken. På den **utveckla** band klickar du på **COM-tillägg** och bekräfta att HPC Pack Excel COM-tillägg har lästs in.
   
   ![Excel-tillägg för HPC Pack][addin]
8. Redigera ett makro HPCControlMacros i Excel genom att ändra kommenterade rader som visas i följande skript. Ersätt lämpliga värden för din miljö.
   
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
9. Kopiera Excel-arbetsboken till en överför katalog, t.ex D:\Excel\Upload. Den här katalogen har angetts i HPC_DependsFiles konstant i ett makro.
10. Om du vill köra arbetsboken på klustret i Azure, klickar du på den **klustret** knappen i kalkylbladet.

### <a name="run-excel-udfs"></a>Kör Excel-UDF: er
Så ovanstående steg 1 – 3 för att konfigurera klientdatorn kör Excel-UDF: er. För Excel-UDF: er behöver du inte har installerat på datornoderna Excel-program. Så när du skapar klustret compute-noder, kan du välja en normal compute-nod bilden i stället för att beräkna nod avbildningen med Excel.

> [!NOTE]
> Det finns en 34 tecken i Excel 2010 och 2013 klustret connector dialogrutan. Du kan använda den här dialogrutan för att ange det kluster som körs i UDF: er. Om fullständig klustrets namn är längre (till exempel hpcexcelhn01.southeastasia.cloudapp.azure.com) passar inte i dialogrutan. Lösningen är att ange en variabel datoromfattande *CCP_IAASHN* med värdet för lång klustrets namn. Ange sedan *CCP_IAASHN %* i dialogrutan som klustrets huvudnod namn. 
> 
> 

När klustret har distribuerats, fortsätter du med följande steg för att köra ett exempel på inbyggda Excel-UDF. Anpassade Excel-UDF finns dessa [resurser](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) att skapa XLL och distribuera dem på IaaS-klustret.

1. Öppna en ny Excel-arbetsbok. På den **utveckla** band klickar du på **tillägg**. Klicka i dialogrutan **Bläddra**, navigera till mappen %CCP_HOME%Bin\XLL32 och välj exempel ClusterUDF32.xll. Om ClusterUDF32 inte finns på klientdatorn måste du kopiera den från mappen %CCP_HOME%Bin\XLL32 på huvudnoden.
   
   ![Välj en användardefinierad funktion][udf]
2. Klicka på **filen** > **alternativ** > **avancerade**. Under **formler**, kontrollera **Tillåt XLL användardefinierade funktioner att köra ett beräkningskluster**. Klicka på **alternativ** och ange det fullständiga klusternamnet i **klustrets huvudnod namn**. (Som anges tidigare inkommande rutan är begränsad till 34 tecken så långt klusternamnet inte kanske får plats. Du kan använda den datoromfattande här för ett långt klusternamn.)
   
   ![Konfigurera en användardefinierad funktion][options]
3. Klicka på cellen med värdet =XllGetComputerNameC() kör UDF-beräkningen i klustret, och tryck på RETUR. Funktionen hämtar bara namnet på Beräkningsnoden som körs på en användardefinierad funktion. För den första körningen tillfrågas autentiseringsuppgifter om användarnamn och lösenord att ansluta till IaaS-kluster.
   
   ![Kör UDF][run]
   
   Tryck på Alt-Skift-Ctrl + F9 för att köra beräkningen på alla celler när det finns många celler att beräkna.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Steg 3. Kör en SOA-arbetsbelastning från en lokal klient
Om du vill köra allmänna SOA-program i klustret HPC Pack IaaS först använda någon av metoderna i steg 1 för att distribuera klustret. Ange en allmän compute-nod avbildningen i det här fallet eftersom du inte behöver Excel på datornoderna. Följ dessa steg.

1. När du har hämtat certifikat för klustret, importerar du den på klientdatorn under Cert: \CurrentUser\Root.
2. Installera den [HPC Pack 2012 R2 uppdatering 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) och [HPC Pack 2012 R2 uppdatering 3 klientverktyg](https://www.microsoft.com/download/details.aspx?id=49923). Dessa verktyg kan du utveckla och kör SOA-klientprogram.
3. Hämta HelloWorldR2 [exempelkoden](https://www.microsoft.com/download/details.aspx?id=41633). Öppna HelloWorldR2.sln i Visual Studio 2010 eller 2012. (Det här exemplet är inte kompatibelt med nyare versioner av Visual Studio.)
4. Skapa projektet EchoService först. Distribuera sedan tjänsten till IaaS-kluster på samma sätt som du distribuerar till ett kluster som är lokalt. Detaljerade anvisningar finns i Viktigt.doc i HelloWordR2. Ändra och skapa HellWorldR2 och andra projekt som beskrivs i följande avsnitt för att generera SOA-klientprogram som körs på ett Azure IaaS-kluster.

### <a name="use-http-binding-with-azure-storage-queue"></a>Använd Http-bindning med Azure storage-kö
Om du vill använda Http-bindning med ett Azure storage-kö, göra några ändringar i exempelkoden.

* Uppdatera klustrets namn.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* Du kan också använda standard TransportScheme i SessionStartInfo eller explicit anger det till Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Använd standard bindningen för BrokerClient.
  
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
* Du kan också ange flaggan UseAzureQueue till true i SessionStartInfo. Om den inte ska anges till SANT som standard när klustrets namn har Azure domänsuffix och TransportScheme Http.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Använd Http-bindning utan Azure storage-kö
Om du vill använda Http-bindning utan en Azure storage-kö uttryckligen anger flaggan UseAzureQueue till false i SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Använda NetTcp bindning
Konfigurationen är för att använda NetTcp bindning till att ansluta till ett lokalt kluster. Du måste öppna några slutpunkter i huvudnod VM. Om du använde HPC Pack IaaS-distributionsskriptet för att skapa klustret, till exempel slutpunkterna i Azure-portalen på följande sätt.

1. Stoppa den virtuella datorn.
2. Lägg till TCP-portarna 9090, 9087, 9091, 9094 för sessionen Broker, respektive Broker worker och Data services
   
    ![Konfigurera slutpunkter][endpoint-new-portal]
3. Starta den virtuella datorn.

SOA-klientprogrammet kräver inga ändringar förutom ändra head namnet på det fullständiga namnet för IaaS-klustret.

## <a name="next-steps"></a>Nästa steg
* Se [resurserna](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) för mer information om hur du kör Excel arbetsbelastningar med HPC Pack.
* Se [hantera SOA-tjänster i Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) mer information om att distribuera och hantera SOA-tjänster med HPC Pack.

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
