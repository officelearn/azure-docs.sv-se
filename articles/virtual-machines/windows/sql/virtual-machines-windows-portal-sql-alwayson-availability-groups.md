---
title: Konfigurera hög tillgänglighet för virtuella datorer i Azure Resource Manager | Microsoft Docs
description: Den här självstudien visar hur du skapar en Always On-tillgänglighetsgrupp med Azure-datorer i Azure Resource Manager-läge.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: bddc83d55c8909412f7f935a4324a6f316a82cd7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129561"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Konfigurera Always On-Tillgänglighetsgrupper i Azure Virtual Machines automatiskt: Resource Manager

Den här självstudien visar hur du skapar en SQL Server-tillgänglighetsgrupp som använder Azure Resource Manager-datorer. I självstudiekursen används bladen på Azure för att konfigurera en mall. Du kan granska standardinställningarna, ange inställningar som krävs och uppdatera blad i portalen när du går igenom den här självstudien.

Fullständig genomgång skapar en tillgänglighetsgrupp för SQL Server på Azure virtuella datorer som innehåller följande element:

* Ett virtuellt nätverk som har flera undernät, inklusive en klientdel och en backend-undernät
* Två domänkontrollanter som har en Active Directory-domän
* Två virtuella datorer som kör SQL Server och distribueras till backend-undernät och anslutna till Active Directory-domän
* Ett redundanskluster med tre noder med kvorummodellen Nodmajoritet
* En tillgänglighetsgrupp som har två repliker för synkront genomförande av en tillgänglighetsdatabas

Följande bild visar den fullständiga lösningen.

![Test lab-arkitektur för Tillgänglighetsgrupper i Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Alla resurser i den här lösningen tillhör en enskild resursgrupp.

Innan du börjar den här självstudien måste du kontrollera följande:

* Du har redan ett Azure-konto. Om du inte har någon, [registrera dig för ett utvärderingskonto](https://azure.microsoft.com/pricing/free-trial/).
* Vet du redan hur du använder det grafiska Användargränssnittet för att etablera en SQL Server-dator från galleriet för virtuella datorer. Mer information finns i [etablera en virtuell dator i SQL Server på Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Du har redan en djupare förståelse för Tillgänglighetsgrupper. Mer information finns i [Always On-Tillgänglighetsgrupper (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Om du är intresserad av att använda Tillgänglighetsgrupper med SharePoint kan också se [Konfigurera SQL Server 2012 Always On-Tillgänglighetsgrupper för SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).
>
>

I den här självstudien använder du Azure portal för att:

* Välj Always On-mallen från portalen.
* Granska inställningarna för profilmallen och uppdatera några konfigurationsinställningar för din miljö.
* Övervaka Azure som skapas av hela miljön.
* Ansluta till en domänkontrollant och sedan till en server som kör SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Etablera kluster från galleriet
Azure tillhandahåller en bild i galleriet för hela lösningen. Att hitta mallen:

1. Logga in på Azure Portal med ditt konto.
2. I Azure-portalen klickar du på **skapa en resurs** att öppna den **New** fönstret.
3. På den **New** fönstret och Sök efter **AlwaysOn**.
   ![Hitta AlwaysOn-mall](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Leta upp i sökresultaten **SQL Server AlwaysOn-kluster**.
   ![AlwaysOn-mall](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. På **Välj en distributionsmodell**, Välj **Resource Manager**.

### <a name="basics"></a>Grundläggande inställningar
Klicka på **grunderna** och konfigurera följande inställningar:

* **Administratörsanvändarnamn** är ett användarkonto som har administratörsbehörighet för domänen och är medlem i den fasta serverrollen SQL Server skulle sysadmin på båda instanserna av SQL Server. Den här självstudien använder **DomainAdmin**.
* **Lösenord** är lösenordet för administratörskontot för domänen. Använd ett komplext lösenord. Bekräfta lösenordet.
* **Prenumeration** är prenumerationen som Azure-fakturor att köra alla distribuerade resurser för tillgänglighetsgruppen. Om ditt konto har flera prenumerationer, kan du ange en annan prenumeration.
* **Resursgrupp** är namnet på gruppen som alla Azure-resurser som skapas av den här mallen tillhör. Den här självstudien använder **SQL-HA-RG**. Mer information finns i [Översikt över Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Plats** är Azure-regionen där kursen skapar resurserna. Välj en Azure-region.

Skärmbilden nedan är en slutförd **grunderna** bladet:

![Grundläggande inställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Klicka på **OK**.

### <a name="domain-and-network-settings"></a>Domän-och nätverksinställningarna
Den här Azure-gallerimallen skapar en domän och domänkontrollanter. Det skapar också ett nätverk och två undernät. Mallen kan inte skapa servrar i en befintlig domän eller ett virtuellt nätverk. Nästa steg konfigurerar inställningarna för domänen och nätverk.

På den **domän-och nätverksinställningarna** bladet granska de förinställda värdena för domänen och nätverksinställningar:

* **Skogsrotsdomännamn** är domännamnet för den Active Directory-domän som är värd för klustret. För den här självstudien använder **contoso.com**.
* **Namn på virtuellt nätverk** är nätverksnamnet för virtuella Azure-nätverket. För den här självstudien använder **autohaVNET**.
* **Namn på undernät** är namnet på en del av det virtuella nätverket som är värd för domänkontrollanten. Använd **subnet-1**. Det här undernätet använder adressprefixet **10.0.0.0/24**.
* **SQL Server-undernätsnamn** är namnet på en del av det virtuella nätverket som är värd för de servrar som kör SQL Server och filen delar vittne. Använd **undernät 2**. Det här undernätet använder adressprefixet **10.0.1.0/26**.

Läs mer om virtuella nätverk i Azure i [översikt över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md).  

Den **domän-och nätverksinställningarna** bör se ut som följande skärmbild:

![Domän-och nätverksinställningarna](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Du kan ändra dessa värden om det behövs. Använd de förinställda värdena i den här självstudien.

Granska inställningarna och klicka sedan på **OK**.

### <a name="availability-group-settings"></a>Tillgänglighet gruppinställningar
På **tillgänglighet gruppinställningar**, granska de förinställda värdena för tillgänglighetsgruppen och lyssnaren.

* **Tillgänglighetsgruppens namn** är klustrade resursnamnet för tillgänglighetsgruppen. Den här självstudien använder **Contoso ag**.
* **Namnet tillgänglighetsgruppens lyssnare** används av klustret och den interna belastningsutjämnaren. Klienter som ansluter till SQL Server kan använda det här namnet för att ansluta till lämplig replik av databasen. Den här självstudien använder **Contoso-listener**.
* **Porten för tillgänglighetsgruppens lyssnare** anger TCP-port för SQL Server-lyssnare. Den här självstudien använder standardporten, **1433**.

Du kan ändra dessa värden om det behövs. Använd de förinställda värdena i den här självstudien.  

![Tillgänglighet gruppinställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Klicka på **OK**.

### <a name="virtual-machine-size-storage-settings"></a>VM-storlek Lagringsinställningar
På **VM-storlek, Lagringsinställningar**, Välj en SQL Server VM-storlek och granska de andra inställningarna.

* **SQL Server VM-storlek** är storleken för både virtuella datorer som kör SQL Server. Välj en storlek på lämplig virtuell dator för din arbetsbelastning. Om du skapar den här miljön för den här självstudien kan du använda **DS2**. Välj en storlek för virtuell dator som har stöd för arbetsbelastningen för produktionsarbetsbelastningar. Många produktionsarbetsbelastningar kräver **DS4** eller större. Mallen skapar två virtuella datorer av den här storleken och installerar SQL Server på var och en. Mer information finns i [storlekar för virtuella datorer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure installerar Enterprise-utgåvan av SQL Server. Kostnaderna beror på versionen av och storleken på virtuella datorn. Detaljerad information om aktuella kostnader finns i [priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Domain controller VM-storlek** är storleken på virtuella datorn för domänkontrollanter. För den här självstudien används **D2**.
* **Dela vittne VM filstorlek** är storleken på virtuella datorn för filresursvittnet. Den här självstudien använder **A1**.
* **SQL Storage-konto** är namnet på lagringskontot som innehåller SQL Server-data och operativsystemdiskar. Den här självstudien använder **alwaysonsql01**.
* **DC-lagringskonto** är namnet på lagringskontot för domänkontrollanter. Den här självstudien använder **alwaysondc01**.
* **SQL Server-data diskstorlek** TB är storleken på disken för SQL Server-data i TB. Ange ett tal mellan 1 och 4. Den här självstudien använder **1**.
* **Lagringsoptimering** anger specifika lagringsutrymmen konfigurationsinställningarna för de beroende på vilken arbetsbelastning i SQL Server-datorer. Alla SQL Server-datorer i det här scenariot kan du använda premium storage med Azure-värd diskcache inställd på skrivskyddat läge. Dessutom kan du optimera SQL Server-inställningar för arbetsbelastningen genom att välja ett av dessa tre inställningar:

  * **Allmän arbetsbelastning** anger inga specifika konfigurationsinställningar.
  * **Transaktionsbearbetning** uppsättningar spåra flaggan 1117 och 1118.
  * **Datalagerhantering** uppsättningar spåra flaggan 1117 och 610.

Den här självstudien använder **Allmänt arbetsbelastning**.

![Lagringsinställningar för VM-storlek](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Granska inställningarna och klicka sedan på **OK**.

#### <a name="a-note-about-storage"></a>En anteckning om storage
Ytterligare optimeringar beror på storleken på diskar för SQL Server-data. För varje terabyte datadisk, lägger Azure till en ytterligare 1 TB premium storage. När en server kräver 2 TB eller mer, skapar en lagringspool på varje SQL Server-dator med hjälp av mallen. En lagringspool är en form av lagringsvirtualisering där flera skivor är konfigurerad för att ge högre kapacitet, återhämtning och prestanda.  Mallen skapar ett lagringsutrymme på lagringspoolen sedan och anger en enda datadisk för operativsystemet. Mallen anger den här disken att datadisken för SQL Server. Mallen justerar lagringspoolen för SQL Server med hjälp av följande inställningar:

* Stripestorleken är interfoliering inställningen för den virtuella disken. Transaktionsbelastningar använda 64 KB. Arbetsbelastningar i informationslager använder 256 KB.
* Elasticitet är enkel (ingen återhämtning).

> [!NOTE]
> Azure premium storage är lokalt redundant och behåller tre kopior av data inom en region, så inte krävs ytterligare återhämtning i lagringspoolen.
>
>

* Kolumnantal är lika med antalet diskar i lagringspoolen.

Mer information om lagringsutrymme och lagringspooler finns:

* [Översikt över lagringsutrymmen](https://technet.microsoft.com/library/hh831739.aspx)
* [Windows Serverbackup och lagringspooler](https://technet.microsoft.com/library/dn390929.aspx)

Mer information om metodtips för SQL Server-konfiguration finns i [prestandametodtips för SQL Server i Azure virtual machines](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>SQL Server-inställningar
På **SQL Server-inställningar**, granska och ändra SQL Server virtual machine-namnprefixet, version av SQL Server, SQL Server-tjänstkontot och lösenord och automatiska korrigeringar underhållsschema för SQL.

* **SQL Server-namnet Prefix** används för att skapa ett namn för varje SQL Server-dator. Den här självstudien använder **sqlserver**. Mallen namn SQL Server-datorer *sqlserver-0* och *sqlserver-1*.
* **SQL Server-version** är versionen av SQL Server. För den här självstudien används **SQL Server 2014**. Du kan också välja **SQL Server 2012** eller **SQL Server 2016**.
* **Användarnamn för SQL Server-tjänsten** är domänkontonamnet för SQL Server-tjänsten. Den här självstudien använder **sqlservice**.
* **Lösenord** är lösenordet för SQL Server-tjänstkontot.  Använd ett komplext lösenord. Bekräfta lösenordet.
* **Automatisk uppdatering för SQL underhållsschema** identifierar veckodagen att Azure automatiskt korrigeringar SQL-servrar. Den här självstudien anger **söndag**.
* **Starttimme för underhåll för automatisk uppdatering för SQL** är tid på dagen för Azure-regionen när automatisk uppdatering börjar.

> [!NOTE]
> Uppdateringsperiod för varje virtuell dator ut med en timme. Enbart en virtuell dator är uppdaterad åt gången för att förhindra avbrott i tjänsterna.
>
>

![SQL Server-inställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Granska inställningarna och klicka sedan på **OK**.

### <a name="summary"></a>Sammanfattning
På sidan Översikt verifierar inställningarna i Azure. Du kan också hämta mallen. Granska sammanfattningen. Klicka på **OK**.

### <a name="buy"></a>Köp
Det här sista bladet innehåller **användningsvillkoren**, och **sekretesspolicy**. Granska den här informationen. När du är redo för Azure att skapa de virtuella datorerna och alla andra nödvändiga resurser för tillgänglighetsgruppen, klickar du på **skapa**.

Azure-portalen skapar resursgruppen och alla resurser.

## <a name="monitor-deployment"></a>Övervaka distributionen
Övervaka förloppet för distributionen från Azure-portalen. En ikon som representerar distributionen blir automatiskt Fäst till instrumentpanelen för Azure portal.

![Azure-instrumentpanelen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ansluta till SQL Server
De nya instanserna av SQL Server körs på virtuella datorer som har Internetanslutning IP-adresser. Du kan fjärrskrivbord (RDP) direkt till varje SQL Server-dator.

Följ dessa steg för RDP till en SQL Server:

1. Från instrumentpanelen för Azure portal, kontrollerar du att distributionen har slutförts.
2. Klicka på **resurser**.
3. I den **resurser** bladet klickar du på **sqlserver-0**, vilket är namnet på en av de virtuella datorer som kör SQL Server.
4. På bladet för **sqlserver-0**, klickar du på **Connect**. Webbläsaren ber om du vill öppna eller spara objektet för fjärranslutning. Klicka på **Open** (Öppna).
5. **Anslutning till fjärrskrivbord** kan varna dig att utgivaren av den här fjärranslutningen inte kan identifieras. Klicka på **Anslut**.
6. Windows security uppmanas du att ange dina autentiseringsuppgifter för att ansluta till IP-adressen för den primära domänkontrollanten. Klicka på **Använd ett annat konto**. För **användarnamn**, typ **contoso\DomainAdmin**. Du har konfigurerat det här kontot när du ställer in administratören användarnamn i mallen. Använd komplexa lösenord som du valde när du konfigurerade mallen.
7. **Fjärrskrivbord** kan varna dig fjärrdatorn inte kunde autentiseras på grund av problem med dess säkerhetscertifikat. Den visar namnet på säkerhet. Om du har följt självstudierna namnet är **sqlserver-0.contoso.com**. Klicka på **Ja**.

Du är nu ansluten med RDP till den SQL Server-datorn. Du kan öppna SQL Server Management Studio, Anslut till standardinstansen av SQL Server och kontrollera att tillgänglighetsgruppen har konfigurerats.
