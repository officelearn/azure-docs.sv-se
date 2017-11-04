---
title: Konfigurera en hybrid HPC Pack kluster i Azure | Microsoft Docs
description: "Lär dig hur du använder Microsoft HPC Pack och Azure för att konfigurera en liten hybrid med höga prestanda HPC-kluster"
services: cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: hpc-pack
ms.assetid: 
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: danlep
ms.openlocfilehash: ad5c13723eef352148a40e3e7f4f2ff616867296
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Skapa en hybrid med höga prestanda HPC-kluster med Microsoft HPC Pack och på begäran Azure compute-noder
Använd Microsoft HPC Pack 2012 R2 och Azure för att konfigurera en liten, hybrid med höga prestanda HPC-kluster. Klustret visas i den här artikeln består av en lokal HPC Pack huvudnod och vissa compute-noder som du distribuerar på begäran i ett Azure-Molntjänsten. Sedan kan du köra beräkning jobb på hybrid-klustret.

![Hybrid HPC-kluster][Overview] 

Den här kursen visar en metod som kallas ibland klustret ”burst till molnet”, för att köra beräknings-intensiva program med skalbara, på begäran Azure-resurser.

Den här kursen förutsätter några tidigare erfarenheter med beräkningskluster eller HPC Pack. Den är avsedd endast för att distribuera ett hybrid beräkningskluster snabbt i exempelsyfte. Mer information och anvisningar för att distribuera ett kluster med hybrid HPC Pack i större skala i en produktionsmiljö, eller att använda HPC Pack 2016, finns det [detaljerad vägledning](http://go.microsoft.com/fwlink/p/?LinkID=200493). För andra scenarier med HPC Pack, inklusive automatisk Klusterdistribution i virtuella Azure-datorer, se [HPC-klusteralternativ with Microsoft HPC Pack i Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Krav
* **Azure-prenumeration** -om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) på bara några minuter.
* **En lokal dator som kör Windows Server 2012 R2 eller Windows Server 2012** -använder den här datorn som huvudnod i HPC-kluster. Om du inte redan kör Windows Server, kan du hämta och installera en [utvärderingsversion](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * Datorn måste vara ansluten till en Active Directory-domän. Du kan konfigurera datorn huvudnod för testning, som en domänkontrollant. Lägga till serverrollen Active Directory Domain Services och främja huvudnod datorn som en domänkontrollant, finns i dokumentationen för Windows Server.
  * För att stödja HPC Pack operativsystemet måste installeras i ett av dessa språk: engelska, japanska eller kinesiska (förenklad).
  * Kontrollera att viktiga och viktiga uppdateringar har installerats.
* **HPC Pack 2012 R2** - [hämta](http://go.microsoft.com/fwlink/p/?linkid=328024) installation för den senaste versionen kostnadsfritt och kopiera filer till huvudnod datorn. Välj installationsfiler i samma språk som din installation av Windows Server.

    >[!NOTE]
    > Om du vill använda HPC Pack 2016 i stället för HPC Pack 2012 R2 krävs ytterligare konfiguration. Finns det [detaljerad vägledning](http://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Domänkonto** -kontot måste vara konfigurerad med behörighet som lokal administratör på huvudnoden att installera HPC Pack.
* **TCP-anslutningar på port 443** från huvudnod till Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Installera HPC Pack på huvudnoden
Du först installera Microsoft HPC Pack på din lokala dator som kör Windows Server. Den här datorn blir huvudnod i klustret.

1. Logga in till huvudnod genom att använda ett domänkonto som har lokal administratörsbehörighet.

2. Starta installationsguiden för HPC Pack genom att köra Setup.exe från HPC Pack installationsfilerna.

3. På den **installation av HPC Pack 2012 R2** klickar du på **ny installation eller lägga till nya funktioner i en befintlig installation**.

    ![HPC Pack 2012 installationen][install_hpc1]

4. På den **användaravtal för Microsoft-programvara sidan**, klickar du på **nästa**.

5. På den **Välj installationstyp** klickar du på **skapar ett nytt HPC-kluster genom att skapa en huvudnod**, och klicka sedan på **nästa**.

6. Flera förinstallation tester körs i guiden. Klicka på **nästa** på den **installationsregler** om alla tester godkänns. Annars granskar du informationen och gör nödvändiga ändringar i din miljö. Kör sedan testerna igen, eller om det behövs startar du installationsguiden igen.
7. På den **HPC DB Configuration** Kontrollera **huvudnod** har valts för alla HPC-databaser och klicka sedan på **nästa**. 

    ![DB-konfiguration][install_hpc4]

8. Standardinställningarna på övriga sidor i guiden. På den **installera nödvändiga komponenter** klickar du på **installera**.
   
    ![Installera][install_hpc6]

9. När installationen är klar, avmarkera **starta HPC Cluster Manager** och klicka sedan på **Slutför**. (Du startar HPC Cluster Manager i ett senare steg.)
   
    ![Slutför][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Förbereda den Azure-prenumerationen
Utför följande steg i den [Azure-portalen](https://portal.azure.com) med din Azure-prenumeration. När du har slutfört de här stegen kan du distribuera Azure-noder från lokala huvudnod. 
  
  > [!NOTE]
  > Även anteckna ditt Azure prenumerations-ID som du behöver senare. ID: t i **prenumerationer** i portalen.
  > 

### <a name="upload-the-default-management-certificate"></a>Överför standardhanteringscertifikat
HPC Pack installerar ett självsignerat certifikat på huvudnoden kallas standard Microsoft HPC Azure Management-certifikat som du kan ladda upp som ett Azure-hanteringscertifikat. Det här certifikatet används bara för testning och proof of concept distributioner till säker anslutning mellan huvudnod och Azure.

1. Från huvudnod datorn, logga in på den [Azure-portalen](https://portal.azure.com).

2. Klicka på **prenumerationer** > *your_subscription_name*.

3. Klicka på **hanteringscertifikat** > **överför**.

4. Bläddra på huvudnoden för filen C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer. Klicka på **överför**.

   
Den **standard HPC Azure Management** certifikatet visas i listan över hanteringscertifikat.

### <a name="create-an-azure-cloud-service"></a>Skapa en Azure-molntjänst
> [!NOTE]
> Skapa Molntjänsten och lagringskontot (i ett senare steg) i samma geografiska region för bästa prestanda.
> 
> 

1. I portalen klickar du på **molntjänster (klassisk)** > **+ Lägg till**.

2.  Ange ett DNS-namn för tjänsten, välja en resursgrupp och en plats och klicka sedan på **skapa**.


### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto
1. I portalen klickar du på **Storage-konton (klassiska)** > **+ Lägg till**.

2. Ange ett namn för kontot och välj den **klassiska** distributionsmodell.

3. Välj en resursgrupp och en plats och låta andra inställningar till standardvärden. Klicka sedan på **Skapa**.

## <a name="configure-the-head-node"></a>Konfigurera huvudnoden
Om du vill använda HPC Cluster Manager för att distribuera Azure-noder och skicka jobb måste du först göra konfigurationssteg krävs för klustret.

1. Starta HPC Cluster Manager på huvudnoden. Om den **Välj huvudnod** dialogrutan visas klickar du på **lokal dator**. Den **distribution uppgiftslista** visas.

2. Under **krävs distributionsuppgifter**, klickar du på **konfigurera nätverket**.
   
    ![Konfigurera nätverket][config_hpc2]

3. I guiden för konfiguration av nätverk väljer **alla noder i ett företagsnätverk endast** (topologi 5). Den här konfigurationen är den enklaste i exempelsyfte.
   
    ![Topologi 5][config_hpc3]
   
4. Klicka på **nästa** att godkänna standardvärdena på övriga sidor i guiden. Klicka sedan på den **granska** klickar du på **konfigurera** att slutföra nätverkskonfigurationen.

5. I den **distribution uppgiftslista**, klickar du på **ange autentiseringsuppgifter för installation**.

6. I den **autentiseringsuppgifter** dialogrutan Ange autentiseringsuppgifter för domänkontot som du använde för att installera HPC Pack. Klicka sedan på **OK**. 
   
    ![Installation av autentiseringsuppgifter][config_hpc6]
   
7. I den **distribution uppgiftslista**, klickar du på **konfigurera namngivning av nya noder**.

8. I den **ange nod Naming serien** dialogrutan godkänner du standardvärdet naming serien och klickar på **OK**. Slutför det här steget även om Azure noder som du lägger till i den här självstudiekursen namnges automatiskt.
   
    ![Namngivning av nod][config_hpc8]
   
9. I den **distribution uppgiftslista**, klickar du på **skapar en mall för noden**. Senare i självstudierna med nodmallen att lägga till Azure-noder i klustret.

10. I noden guiden Skapa mall, gör du följande:
    
    a. På den **välja mallen nodtypen** klickar du på **nodmallen för Windows Azure**, och klicka sedan på **nästa**.
    
    ![Noden mall][config_hpc10]
    
    b. Klicka på **nästa** att acceptera standardnamnet på mallen.
    
    c. På den **ange prenumerationsinformation** anger Azure-prenumeration-ID: T (tillgänglig i din Azure kontoinformation). I **hanteringscertifikat**, bläddra efter **standard Microsoft HPC Azure Management.** Klicka sedan på **Nästa**.
    
    ![Noden mall][config_hpc12]
    
    d. På den **ange tjänstinformation** väljer Molntjänsten och lagringskontot som du skapade i föregående steg. Klicka sedan på **Nästa**.
    
    ![Noden mall][config_hpc13]
    
    e. Klicka på **nästa** att godkänna standardvärdena på övriga sidor i guiden. Klicka sedan på den **granska** klickar du på **skapa** nodmallen ska skapas.
    
    > [!NOTE]
    > Standard innehåller Azure nodmallen inställningar att starta (tillhandahålla) och stoppa noder manuellt med hjälp av HPC Cluster Manager. Du kan också konfigurera ett schema för att starta och stoppa Azure-noder automatiskt.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Lägg till Azure-noder i klustret
Nu med nodmallen kan du lägga till Azure-noder i klustret. Lägga till noder i klustret lagrar konfigurationsinformation om deras så att du kan starta (tillhandahålla) dem när som helst i Molntjänsten. Din prenumeration hämtar endast debiteras för Azure-noder när instanserna körs i Molntjänsten.

Följ dessa steg för att lägga till två Small noder.

1. HPC Cluster Manager klickar du på **hantering** (kallas **resurshantering** i aktuella versioner av HPC Pack) > **Lägg till nod**.
   
    ![Lägg till nod][add_node1]

2. I guiden Lägg till nod på den **väljer distributionsmetoden** klickar du på **lägga till Windows Azure-noder**, och klicka sedan på **nästa**.
   
    ![Lägg till Azure nod][add_node1_1]

3. På den **ange nya noder** markerar du den Azure nod-mall som du skapade tidigare (kallas som standard **AzureNode standardmallen**). Ange **2** noder i storlek **små**, och klicka sedan på **nästa**.
   
    ![Ange noder][add_node2]
   
4. På den **Slutför guiden Lägg till nod** klickar du på **Slutför**.
    
     Två Azure noder med namnet **AzureCN 0001** och **AzureCN 0002**, visas nu i HPC Cluster Manager. Båda värdena i den **inte distribuerade** tillstånd.
   
    ![Tillagda noder][add_node3]

## <a name="start-the-azure-nodes"></a>Starta Azure-noder
När du vill använda klustrets resurser i Azure kan du använda HPC Cluster Manager för att starta (tillhandahålla) i Azure-noder och tar dem online.

1. HPC Cluster Manager klickar du på **hantering** (kallas **resurshantering** i aktuella versioner av HPC Pack), och välj Azure-noder.

2. Klicka på **starta**, och klicka sedan på **OK**.
   
   ![Starta noder][add_node4]
   
    Noderna övergång till den **etablering** tillstånd. Visa allokering loggen om du vill spåra etablering pågår.
   
    ![Etablera noder][add_node6]

3. Efter några minuter Azure-noder Slutför etablering och finns i den **Offline** tillstånd. I det här tillståndet rollinstanserna kör men ännu Acceptera inte klustret jobb.

4. Om du vill kontrollera att rollinstanser körs i Azure-portalen klickar du på **molntjänster (klassisk)** > *your_cloud_service_name*.
   
   Du bör se två **HpcWorkerRole** instanser (noder) körs i tjänsten. HPC Pack också automatiskt distribuerar två **HpcProxy** instanser (storlek medel) för att hantera kommunikationen mellan huvudnod och Azure.

   ![Kör instanser][view_instances1]

5. Om du vill placera Azure noderna online kör jobb för klustret, väljer noder, högerklicka och klicka sedan på **Anslut**.
   
    ![Offline-noder][add_node7]
   
    HPC Cluster Manager betyder att noderna är under den **Online** tillstånd.

## <a name="run-a-command-across-the-cluster"></a>Köra ett kommando i klustret
Om du vill kontrollera installationen använder HPC Pack **clusrun** kommando för att köra ett kommando eller ett program på en eller flera klusternoder. Ett enkelt exempel använder **clusrun** att hämta IP-adresskonfigurationen för Azure-noder.

1. Öppna en kommandotolk som administratör på huvudnoden.

2. Ange följande kommando:
   
    `clusrun /nodes:azurecn* ipconfig`

3. Om du uppmanas ange administratörslösenordet klustret. Du bör se utdata liknar följande.
   
    ![Clusrun][clusrun1]

## <a name="run-a-test-job"></a>Kör ett testjobb
Nu skicka ett testjobb som körs på hybrid-klustret. Det här exemplet är ett enkelt parametrisk rensning jobb (en typ av filsystemen parallella beräkning). Det här exemplet körs underaktiviteter som lägger till ett heltal till sig själv med hjälp av den **ange /a** kommando. Alla noder i klustret bidrar till att slutföra underaktiviteter för heltal mellan 1 och 100.

1. HPC Cluster Manager klickar du på **jobbhantering** > **nytt parametrisk Sopa jobb**.
   
    ![Nytt jobb][test_job1]

2. I den **nytt parametrisk Sopa jobb** i dialogrutan **kommandoraden**, typen `set /a *+*` (skriva över standardkommandoraden). Lämna standardvärdena för de återstående inställningarna och klicka sedan på **skicka** skicka jobbet.
   
    ![Parametrisk rensning][param_sweep1]

3. När jobbet har slutförts, dubbelklickar du på den **Mina Sopa uppgiften** jobb.

4. Klicka på **uppgiftsvyn**, och klicka sedan på en underaktivitet för att visa de beräknade utdata på samma nivå.
   
    ![Uppgiften resultat][view_job361]

5. Klicka för att se vilken nod som utförs beräkningen för den underaktiviteten **allokerade noder**. (Klustret kan visa en annan nod-namn).
   
    ![Uppgiften resultat][view_job362]

## <a name="stop-the-azure-nodes"></a>Stoppa Azure-noder
När du testa klustret sluta Azure-noder för att undvika onödiga kostnader till ditt konto. Det här steget stoppar Molntjänsten och tar bort Azure rollinstanserna.

1. HPC Cluster Manager i **hantering** (kallas **resurshantering** i tidigare versioner av HPC Pack), Välj både Azure-noder. Klicka på **stoppa**.
   
    ![Stoppa noder][stop_node1]

2. I den **stoppar Windows Azure-noder** dialogrutan klickar du på **stoppa**. 
   
3. Noderna övergång till den **stoppar** tillstånd. Efter några minuter HPC Cluster Manager visar att noderna är **inte distribuerade**.
   
    ![Inte distribuerad noder][stop_node4]

4. Bekräfta att rollinstanserna är inte längre körs i Azure och i Azure-portalen klickar du på **molntjänster (klassisk)** > *your_cloud_service_name*. Inga instanser har distribuerats i produktionsmiljön. 
   
    Detta Slutför kursen.

## <a name="next-steps"></a>Nästa steg
* Utforska dokumentationen för [HPC Pack](https://technet.microsoft.com/library/cc514029).
* Om du vill konfigurera en hybrid HPC Pack Klusterdistribution i större skala, se [Burst to Azure Worker Rollinstanser with Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493).
* Andra sätt att skapa ett HPC Pack-kluster i Azure, inklusive med Azure Resource Manager-mallar finns i [HPC-klusteralternativ with Microsoft HPC Pack i Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


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
