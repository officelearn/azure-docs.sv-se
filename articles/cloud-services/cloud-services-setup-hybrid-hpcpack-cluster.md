---
title: Konfigurera ett hybridkluster med HPC Pack i Azure | Microsoft Docs
description: Lär dig hur du använder Microsoft HPC Pack och Azure för att konfigurera en liten hybrid med höga prestanda (HPC)-kluster
services: cloud-services
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: hpc-pack
ms.assetid: ''
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: danlep
ms.openlocfilehash: 55dfd7e5ea93ae941d73612cc70ed82d48db725a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236746"
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Konfigurera ett hybridkluster för databehandling med höga prestanda med Microsoft HPC Pack och Azure-beräkningsnoder på begäran
Använda Microsoft HPC Pack 2012 R2 och Azure för att ställa in en liten, hybrid databehandling med höga prestanda (HPC)-kluster. Klustret visas i den här artikeln består av en lokal HPC Pack-huvudnod och vissa compute-noder som du distribuerar på begäran i en Azure-molntjänst. Du kan sedan köra Beräkningsjobb i hybridklustret.

![HPC-hybridkluster][Overview] 

Den här kursen visar en metod som kallas ibland kluster ”överföras till molnet”, för att köra beräkningsintensiva program med skalbara, on demand Azure-resurser.

Den här självstudien förutsätter att några tidigare erfarenheter med beräkningskluster eller HPC Pack. Det är endast avsedd att hjälpa dig att distribuera ett hybridberäkningskluster snabbt i exempelsyfte. Mer information och anvisningar för att distribuera ett hybridkluster med HPC Pack i större skala i en produktionsmiljö eller att använda HPC Pack 2016 finns i den [detaljerad vägledning](https://go.microsoft.com/fwlink/p/?LinkID=200493). För andra scenarier med HPC Pack, inklusive automatisk distribution av kluster i Azure virtual machines, se [alternativ för HPC-kluster med Microsoft HPC Pack i Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration** -om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) på bara några minuter.
* **Den lokala datorn som kör Windows Server 2012 R2 eller Windows Server 2012** – Använd den här datorn som klustrets huvudnod i HPC-kluster. Om du inte redan kör Windows Server, kan du hämta och installera en [utvärderingsversion](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * Datorn måste vara ansluten till en Active Directory-domän. För testning, kan du konfigurera huvudnoden datorn som en domänkontrollant. Lägga till rollen Active Directory Domain Services och göra huvudnoden datorn som en domänkontrollant, finns i dokumentationen för Windows Server.
  * För HPC Pack måste operativsystemet måste installeras på något av följande språk: engelska, japanska eller kinesiska (förenklad).
  * Kontrollera att viktiga och kritiska uppdateringar har installerats.
* **HPC Pack 2012 R2** - [hämta](https://go.microsoft.com/fwlink/p/?linkid=328024) installationen Paketera för den senaste versionen som är kostnadsfritt och kopiera filer till huvudnoden datorn. Välj installationsfiler i samma språk som din installation av Windows Server.

    >[!NOTE]
    > Om du vill använda HPC Pack 2016 i stället för HPC Pack 2012 R2 krävs ytterligare konfiguration. Se den [detaljerad vägledning](https://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Domänkonto** – det här kontot måste vara konfigurerad med lokal administratörsbehörighet på huvudnoden installera HPC Pack.
* **TCP-anslutningar på port 443** från klustrets huvudnod till Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Installera HPC Pack på huvudnoden
Du först installera Microsoft HPC Pack på den lokala datorn som kör Windows Server. Den här datorn blir huvudnoden för klustret.

1. Logga in på huvudnoden genom att använda ett domänkonto som har lokal administratörsbehörighet.

2. Starta installationsguiden för HPC Pack genom att köra Setup.exe i HPC Pack-installationsfiler.

3. På den **installationsprogram för HPC Pack 2012 R2** klickar du på **nyinstallation eller lägga till nya funktioner till en befintlig installation**.

    ![HPC Pack 2012 installationen][install_hpc1]

4. På den **användaravtal för Microsoft-programvara sidan**, klickar du på **nästa**.

5. På den **Välj installationstyp** klickar du på **skapar ett nytt HPC-kluster genom att skapa en huvudnod**, och klicka sedan på **nästa**.

6. Guiden har körts flera förinstallation tester. Klicka på **nästa** på den **installationsregler** om alla tester godkänns. Annars, Läs igenom informationen och gör nödvändiga ändringar i din miljö. Kör sedan testerna igen, eller om det behövs startar du installationsguiden igen.
7. På den **HPC databaskonfiguration** kontrollerar **huvudnod** har valts för alla HPC-databaser och klicka sedan på **nästa**. 

    ![Databaskonfiguration][install_hpc4]

8. Acceptera standardinställningarna på de återstående sidorna i guiden. På den **installera nödvändiga komponenter** klickar du på **installera**.
   
    ![Installera][install_hpc6]

9. När installationen är klar kan du avmarkera **starta HPC Cluster Manager** och klicka sedan på **Slutför**. (Du startar HPC Cluster Manager i ett senare steg.)
   
    ![Slutför][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Förbereda Azure-prenumeration
Utför följande steg i den [Azure-portalen](https://portal.azure.com) med din Azure-prenumeration. När du har slutfört de här stegen kan du distribuera Azure-noder från en lokal huvudnod. 
  
  > [!NOTE]
  > Även anteckna din Azure-prenumerations-ID som du behöver senare. ID: t i **prenumerationer** i portalen.
  > 

### <a name="upload-the-default-management-certificate"></a>Överföra hanteringscertifikatet för standard
HPC Pack installerar ett självsignerat certifikat på huvudnoden, kallas det standard Microsoft HPC Azure Management-certifikat som du kan överföra som en Azure-hanteringscertifikatet. Det här certifikatet har angetts för testning och proof of concept distributioner till säker anslutning mellan huvudnoden och Azure.

1. Från den huvudnod datorn, logga in på den [Azure-portalen](https://portal.azure.com).

2. Klicka på **prenumerationer** > *your_subscription_name*.

3. Klicka på **hanteringscertifikat** > **överför**.

4. Bläddra på huvudnoden för filen C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer. Klicka sedan på **överför**.

   
Den **standard HPC Azure Management** certifikatet visas i listan över hanteringscertifikat.

### <a name="create-an-azure-cloud-service"></a>Skapa en Azure-molntjänst
> [!NOTE]
> Skapa Molntjänsten och lagringskontot (i ett senare steg) i samma geografiska region för bästa prestanda.
> 
> 

1. I portalen klickar du på **molntjänster (klassisk)** > **+ Lägg till**.

2.  Ange ett DNS-namn för tjänsten, Välj en resursgrupp och en plats och klicka sedan på **skapa**.


### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto
1. I portalen klickar du på **lagringskonton (klassisk)** > **+ Lägg till**.

2. Skriv ett namn för kontot och välj den **klassiska** distributionsmodell.

3. Välj en resursgrupp och en plats och låta andra inställningar på standardvärden. Klicka sedan på **Skapa**.

## <a name="configure-the-head-node"></a>Konfigurera huvudnoden
För att använda HPC Cluster Manager att distribuera Azure-noder och skicka jobb måste du först göra vissa konfigurationssteg krävs för klustret.

1. Starta HPC Cluster Manager på klustrets huvudnod. Om den **Välj huvudnod** dialogrutan visas klickar du på **lokala**. Den **distribution uppgiftslista** visas.

2. Under **krävs distributionsuppgifter**, klickar du på **konfigurera nätverket**.
   
    ![Konfigurera nätverk][config_hpc2]

3. I guiden nätverkskonfiguration, väljer **noderna bara på ett företagsnätverk** (topologi 5). Den här konfigurationen är den enklaste i exempelsyfte.
   
    ![Topologi 5][config_hpc3]
   
4. Klicka på **nästa** att acceptera standardvärdena på de återstående sidorna i guiden. Klicka på den **granska** fliken **konfigurera** att slutföra nätverkskonfigurationen.

5. I den **distribution uppgiftslista**, klickar du på **ange autentiseringsuppgifter för installation**.

6. I den **autentiseringsuppgifter** dialogrutan skriver du autentiseringsuppgifterna för det domänkonto som du använde för att installera HPC Pack. Klicka sedan på **OK**. 
   
    ![Autentiseringsuppgifter][config_hpc6]
   
7. I den **distribution uppgiftslista**, klickar du på **konfigurera namngivning av nya noder**.

8. I den **ange noden namngivning av serien** dialogrutan godkänner du standardvärdet namngivning av serien och klicka på **OK**. Slutför det här steget även om Azure-noder du lägger till i den här självstudien är namngivna automatiskt.
   
    ![Namngivning av nod][config_hpc8]
   
9. I den **distribution uppgiftslista**, klickar du på **skapar en mall för noden**. Senare i självstudien får använda du nodmallen att lägga till Azure-noder i klustret.

10. I noden guiden Skapa mall, gör du följande:
    
    a. På den **väljer mallen nodtyp** klickar du på **nodmallen för Windows Azure**, och klicka sedan på **nästa**.
    
    ![Nodmallen][config_hpc10]
    
    b. Klicka på **nästa** att acceptera standardnamnet för mallen.
    
    c. På den **ange prenumerationsinformation** anger du ditt Azure prenumerations-ID (tillgängligt i dina Azure-konto). I **hanteringscertifikat**, bläddra efter **standard Microsoft HPC Azure Management.** Klicka sedan på **Nästa**.
    
    ![Nodmallen][config_hpc12]
    
    d. På den **ange tjänstinformation** väljer Molntjänsten och lagringskontot som du skapade i föregående steg. Klicka sedan på **Nästa**.
    
    ![Nodmallen][config_hpc13]
    
    e. Klicka på **nästa** att acceptera standardvärdena på de återstående sidorna i guiden. Klicka på den **granska** fliken **skapa** Skapa mall för noden.
    
    > [!NOTE]
    > Som standard innehåller inställningar som du kan starta (tillhandahålla) och stoppa noder manuellt, med hjälp av HPC Cluster Manager i Azure-nod-mallen. Du kan också konfigurera ett schema för att starta och stoppa Azure-noder automatiskt.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Lägg till Azure-noder i klustret
Nu med mallen noden kan du lägga till Azure-noder i klustret. Lägga till noder i klustret lagrar sina konfigurationsinformationen så att du kan starta (tillhandahålla) dem när som helst i Molntjänsten. Din prenumeration hämtar endast debiteras för Azure-noder när instanserna som körs i Molntjänsten.

Följ dessa steg för att lägga till två små noder.

1. HPC Cluster Manager klickar du på **hantering av** (kallas **resurshantering** i aktuella versioner av HPC Pack) > **Lägg till nod**.
   
    ![Lägg till Node][add_node1]

2. I guiden Lägg till nod på den **väljer distributionsmetoden** klickar du på **lägga till Windows Azure-noder**, och klicka sedan på **nästa**.
   
    ![Lägg till Azure-nod][add_node1_1]

3. På den **ange nya noder** väljer du den Azure-nod-mall som du skapade tidigare (kallas som standard **AzureNode standardmallen**). Ange sedan **2** noder i storleken **små**, och klicka sedan på **nästa**.
   
    ![Ange noder][add_node2]
   
4. På den **du har slutfört guiden Lägg till nod** klickar du på **Slutför**.
    
     Två Azure-noder, med namnet **AzureCN 0001** och **AzureCN 0002**, visas nu i HPC Cluster Manager. Båda finns i den **inte distribuerats** tillstånd.
   
    ![Har lagts till noder][add_node3]

## <a name="start-the-azure-nodes"></a>Starta Azure-noder
När du vill använda klusterresurserna i Azure kan du använda HPC Cluster Manager för att starta (tillhandahålla) Azure-noder och tar dem online.

1. HPC Cluster Manager klickar du på **hantering av** (kallas **resurshantering** i aktuella versioner av HPC Pack), och välj de Azure-noderna.

2. Klicka på **starta**, och klicka sedan på **OK**.
   
   ![Starta noder][add_node4]
   
    Noderna övergång till den **etablering** tillstånd. Visa etablering loggen om du vill spåra förloppet etablering.
   
    ![Etablera noder][add_node6]

3. Efter några minuter, Azure-noder avsluta etablering och finns i den **Offline** tillstånd. I det här tillståndet rollinstanserna körs men ännu har stöd inte för kluster-jobb.

4. För att bekräfta att rollinstanserna körs i Azure-portalen klickar du på **molntjänster (klassiska)** > *your_cloud_service_name*.
   
   Du bör se två **HpcWorkerRole** instanser (noder) som körs i tjänsten. HPC Pack också automatiskt distribuerar två **HpcProxy** instanser (storlek medel) för att hantera kommunikationen mellan huvudnoden och Azure.

   ![Instanser som körs][view_instances1]

5. För att göra Azure noderna online för att köra jobb för klustret, väljer du de noder, högerklicka och klicka sedan på **Anslut**.
   
    ![Offline noder][add_node7]
   
    HPC Cluster Manager indikerar att noderna finns i den **Online** tillstånd.

## <a name="run-a-command-across-the-cluster"></a>Köra ett kommando i klustret
Du kan kontrollera installationen genom att använda HPC Pack **clusrun** kommando för att köra ett kommando eller ett program på en eller flera klusternoder. Som ett enkelt exempel kan du använda **clusrun** får IP-konfigurationen av Azure-noder.

1. Öppna en kommandotolk som administratör på klustrets huvudnod.

2. Ange följande kommando:
   
    `clusrun /nodes:azurecn* ipconfig`

3. Om du uppmanas, anger du ditt administratörslösenord för klustret. Du bör se utdata liknar följande.
   
    ![Clusrun][clusrun1]

## <a name="run-a-test-job"></a>Köra ett testjobb
Nu ska du skicka ett testjobb som körs i hybridklustret. Det här exemplet är ett enkelt parametrisk rensning jobb (en typ av parallella beräkning). Det här exemplet körs underaktiviteter som lägger till ett heltal till sig själv med hjälp av den **ange /a** kommando. Alla noder i klustret kan ingå i slutförs underaktiviteter för heltal mellan 1 och 100.

1. HPC Cluster Manager klickar du på **jobbhantering** > **nytt parametrisk Svepvinkeln jobb**.
   
    ![Nytt jobb][test_job1]

2. I den **nytt parametrisk Svepvinkeln jobb** i dialogrutan **kommandoraden**, typ `set /a *+*` (skriva över standardkommandoraden som visas). Lämna standardvärdena för de återstående inställningarna och klicka sedan på **skicka** att skicka jobbet.
   
    ![Parametrisk rensning][param_sweep1]

3. När jobbet är klar dubbelklickar du på den **Mina Svepvinkeln uppgift** jobbet.

4. Klicka på **uppgiftsvyn**, och klicka sedan på en underaktivitet för att visa beräknad utdata på samma nivå.
   
    ![Uppgiftsresultat][view_job361]

5. Klicka för att se vilken nod utförs beräkningen för den underaktiviteten **allokerade noder**. (Klustret kan visa en annan nod-namn.)
   
    ![Uppgiftsresultat][view_job362]

## <a name="stop-the-azure-nodes"></a>Stoppa Azure-noder
Stoppa Azure-noder för att undvika onödiga kostnader till ditt konto när du prova på klustret. Det här steget stoppar Molntjänsten och tar bort Azure rollinstanserna.

1. HPC Cluster Manager i **hantering av** (kallas **resurshantering** i tidigare versioner av HPC Pack), Välj både Azure-noder. Klicka sedan på **stoppa**.
   
    ![Stoppa noder][stop_node1]

2. I den **stoppar Windows Azure-noder** dialogrutan klickar du på **stoppa**. 
   
3. Noderna övergång till den **stoppar** tillstånd. Efter några minuter, HPC Cluster Manager visar att noderna är **Not-distribuerade**.
   
    ![Inte distribuerad noder][stop_node4]

4. Bekräfta att rollinstanserna finns inte längre som körs i Azure, Azure-portalen klickar du på **molntjänster (klassisk)** > *your_cloud_service_name*. Inga instanser har distribuerats i produktionsmiljön. 
   
    Nu är du klar med självstudien.

## <a name="next-steps"></a>Nästa steg
* Utforska dokumentationen för [HPC Pack](https://technet.microsoft.com/library/cc514029).
* Om du vill konfigurera en hybrid HPC Pack Klusterdistribution i större skala, se [Burst to Azure Worker-Rollinstanser med Microsoft HPC Pack](https://go.microsoft.com/fwlink/p/?LinkID=200493).
* Andra sätt att skapa ett HPC Pack-kluster i Azure, inklusive med Azure Resource Manager-mallar finns i [alternativ för HPC-kluster med Microsoft HPC Pack i Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png
