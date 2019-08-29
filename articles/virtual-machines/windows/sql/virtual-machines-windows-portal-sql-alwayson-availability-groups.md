---
title: Konfigurera hög tillgänglighet för Azure Resource Manager virtuella datorer | Microsoft Docs
description: I den här kursen får du lära dig hur du skapar en Always on-tillgänglighets grupp med virtuella Azure-datorer i Azure Resource Manager läge.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: a06ea59af0776fe3decb0b56a3ef886f08b2dfda
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100719"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Konfigurera alltid tillgänglighets grupper i Azure Virtual Machines automatiskt: Resource Manager

Den här självstudien visar hur du skapar en SQL Server tillgänglighets grupp som använder Azure Resource Manager virtuella datorer. I självstudien används Azure-blad för att konfigurera en mall. Du kan granska standardinställningarna, skriva nödvändiga inställningar och uppdatera bladet i portalen när du går igenom den här självstudien.

Den fullständiga självstudien skapar en SQL Server tillgänglighets grupp på Azure Virtual Machines som innehåller följande element:

* Ett virtuellt nätverk som har flera undernät, inklusive en klient del och ett Server dels undernät
* Två domänkontrollanter som har en Active Directory-domän
* Två virtuella datorer som kör SQL Server och distribueras till backend-undernätet och är anslutna till Active Directory-domänen
* Ett failover-kluster med tre noder och Nodmajoritet för Nodmajoritet
* En tillgänglighets grupp som har två repliker av synkront genomförande för en tillgänglighets databas

Följande bild visar den kompletta lösningen.

![Testa Lab-arkitektur för tillgänglighets grupper i Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Alla resurser i den här lösningen tillhör en enda resurs grupp.

Innan du börjar den här självstudien kontrollerar du följande:

* Du har redan ett Azure-konto. Om du inte har någon kan du [Registrera dig för ett utvärderings konto](https://azure.microsoft.com/pricing/free-trial/).
* Du vet redan hur du ska använda det grafiska användar gränssnittet för att etablera en SQL Server virtuell dator från galleriet för virtuella datorer. Mer information finns i [etablering av en SQL Server virtuell dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Du har redan en solid förståelse för tillgänglighets grupper. Mer information finns i [Always on Availability groups (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> Om du är intresse rad av att använda tillgänglighets grupper med SharePoint kan du också se [konfigurera SQL Server 2012 Always on-tillgänglighetsgrupper för SharePoint 2013](/SharePoint/administration/configure-an-alwayson-availability-group).
>
>

I den här självstudien använder du Azure Portal för att:

* Välj mallen Always in from the The Portal.
* Granska mal lin ställningar och uppdatera några konfigurations inställningar för din miljö.
* Övervaka Azure eftersom det skapar hela miljön.
* Anslut till en domänkontrollant och sedan till en server som kör SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Etablera klustret från galleriet
Azure tillhandahåller en galleri bild för hela lösningen. Så här hittar du mallen:

1. Logga in på Azure Portal med ditt konto.
2. Klicka på **skapa en resurs** i Azure Portal för att öppna det **nya** fönstret.
3. I det **nya** fönstret söker du efter **AlwaysOn**.
   ![Hitta AlwaysOn-mall](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Leta upp **SQL Server AlwaysOn-kluster**i Sök resultaten.
   ![AlwaysOn-mall](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Välj **Resource Manager**på **Välj en distributions modell**.

### <a name="basics"></a>Grundinställningar
Klicka på **grundläggande** och konfigurera följande inställningar:

* **Administratörens användar namn** är ett användar konto som har domän administratörs behörighet och som är medlem i den fasta Server rollen SQL Server Sysadmin på båda instanserna av SQL Server. I den här självstudien använder du **DomainAdmin**.
* **Password** är lösen ordet för domänens administratörs konto. Använd ett komplext lösen ord. Bekräfta lösenordet.
* **Prenumeration** är den prenumeration som Azure-fakturor använder för att köra alla distribuerade resurser för tillgänglighets gruppen. Om ditt konto har flera prenumerationer kan du ange en annan prenumeration.
* **Resurs grupp** är namnet på den grupp som alla Azure-resurser som skapas av den här mallen tillhör. I den här självstudien använder du **SQL-ha-RG**. Mer information finns i [Översikt över Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Plats** är den Azure-region där självstudien skapar resurserna. Välj en Azure-region.

Följande skärm bild är ett blad med färdiga **grunder** :

![Grundinställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Klicka på **OK**.

### <a name="domain-and-network-settings"></a>Domän-och nätverks inställningar
Den här Azure-Galleri mal len skapar en domän och domänkontrollanter. Det skapar också ett nätverk och två undernät. Mallen kan inte skapa servrar i en befintlig domän eller ett virtuellt nätverk. Nästa steg konfigurerar domän-och nätverks inställningarna.

På bladet **domän och nätverks inställningar** granskar du de förinställda värdena för domän-och nätverks inställningarna:

* **Skogs rots domänens namn** är domän namnet för den Active Directory domän som är värd för klustret. För självstudien använder du **contoso.com**.
* **Virtual Network namn** är nätverks namnet för det virtuella Azure-nätverket. För självstudien använder du **autohaVNET**.
* **Domänkontrollantens undernäts namn** är namnet på en del av det virtuella nätverk som är värd för domänkontrollanten. Använd **undernät-1**. Det här under nätet använder adressprefixet **10.0.0.0/24**.
* **SQL Server** -undernätets namn är namnet på en del av det virtuella nätverk som är värd för de servrar som kör SQL Server och fil resurs vittnet. Använd **undernät-2**. Det här under nätet använder adressprefixet **10.0.1.0/26**.

Mer information om virtuella nätverk i Azure finns i [Översikt över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md).  

**Domän-och nätverks inställningarna** bör se ut som på följande skärm bild:

![Domän-och nätverks inställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Om det behövs kan du ändra dessa värden. Använd de förinställda värdena för den här självstudien.

Granska inställningarna och klicka sedan på **OK**.

### <a name="availability-group-settings"></a>Inställningar för tillgänglighets grupp
I **Inställningar för tillgänglighets grupp**granskar du de förinställda värdena för tillgänglighets gruppen och lyssnaren.

* **Tillgänglighets gruppens namn** är det klustrade resurs namnet för tillgänglighets gruppen. I den här självstudien använder du **contoso-AG**.
* **Namnet** på tillgänglighets gruppens lyssnare används av klustret och den interna belastningsutjämnaren. Klienter som ansluter till SQL Server kan använda det här namnet för att ansluta till en lämplig replik av databasen. I den här självstudien använder du **contoso-Listener**.
* **Port** för tillgänglighets gruppens lyssnare anger TCP-porten för den SQL Server lyssnaren. I den här självstudien använder du standard porten **1433**.

Om det behövs kan du ändra dessa värden. Använd de förinställda värdena för den här självstudien.  

![inställningar för tillgänglighets grupp](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Klicka på **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Storlek på virtuell dator, lagrings inställningar
På **VM-storlek, lagrings inställningar**, Välj en storlek för SQL Server virtuell dator och granska de andra inställningarna.

* **Storlek på SQL Server virtuell dator** är storleken på de virtuella datorerna som kör SQL Server. Välj en lämplig storlek för virtuella datorer för din arbets belastning. Om du skapar den här miljön för självstudien använder du **DS2**. För produktions arbets belastningar väljer du en storlek på virtuell dator som har stöd för arbets belastningen. Många produktions arbets belastningar kräver **DS4** eller större. Mallen skapar två virtuella datorer av den här storleken och installerar SQL Server på var och en. Mer information finns i [storlekar för virtuella datorer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure installerar Enterprise-versionen av SQL Server. Kostnaden beror på versionen och storleken på den virtuella datorn. Detaljerad information om aktuella kostnader finns i [priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Storlek på virtuell dator** för domänkontrollant är den virtuella datorns storlek för domän kontrol Lanterna. I den här självstudien använder du **D2**.
* **Fil resurs vittnets virtuella dator storlek** är storleken på den virtuella datorn för fil resurs vittnet. Använd **a1**för den här självstudien.
* **SQL Storage-konto** är namnet på det lagrings konto som innehåller SQL Server data och operativ system diskar. I den här självstudien använder du **alwaysonsql01**.
* **Lagrings konto för domänkontrollant** är namnet på lagrings kontot för domän kontrol Lanterna. I den här självstudien använder du **alwaysondc01**.
* **SQL Server data disk storlek** i TB är storleken på den SQL Server data disken i TB. Ange en siffra mellan 1 och 4. I den här självstudien använder du **1**.
* **Lagrings optimering** anger vissa inställningar för lagrings konfiguration för de SQL Server virtuella datorerna baserat på arbets belastnings typen. Alla SQL Server virtuella datorer i det här scenariot använder Premium Storage med Azure disk Host cache inställd på skrivskyddad. Dessutom kan du optimera SQL Server inställningar för arbets belastningen genom att välja någon av dessa tre inställningar:

  * **Allmän arbets belastning** anger inga speciella konfigurations inställningar.
  * **Transaktions bearbetning** anger spårnings flaggan 1117 och 1118.
  * **Data lager** hantering anger spårnings flaggan 1117 och 610.

I den här självstudien använder du **allmän arbets belastning**.

![Lagrings inställningar för VM-storlek](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Granska inställningarna och klicka sedan på **OK**.

#### <a name="a-note-about-storage"></a>En kommentar om lagring
Ytterligare optimeringar beror på storleken på SQL Server data diskar. För varje terabyte data disk lägger Azure till ytterligare 1 TB Premium-lagring. När en server kräver 2 TB eller mer, skapar mallen en lagringspool på varje SQL Server virtuell dator. En lagringspool är en typ av lagrings virtualisering där flera diskar har kon figurer ATS för att ge högre kapacitet, återhämtning och prestanda.  Mallen skapar sedan ett lagrings utrymme i lagringspoolen och visar en enskild datadisk till operativ systemet. Mallen anger den här disken som data disk för SQL Server. Mallen justerar lagringspoolen för SQL Server med hjälp av följande inställningar:

* Rand storlek är inställningen för överlagring för den virtuella disken. Transaktions arbets belastningar använder 64 KB. Data lager arbets belastningar använder 256 KB.
* Återhämtning är enkel (ingen återhämtning).

> [!NOTE]
> Azure Premium Storage är lokalt redundant och lagrar tre kopior av data inom en enda region, så att det inte krävs ytterligare återhämtning i lagringspoolen.
>
>

* Antalet kolumner är lika med antalet diskar i lagringspoolen.

Mer information om lagrings utrymme och lagringspooler finns i:

* [Översikt över lagrings utrymmen](https://technet.microsoft.com/library/hh831739.aspx)
* [Windows Server Backup och lagringspooler](https://technet.microsoft.com/library/dn390929.aspx)

Mer information om metod tips för SQL Server konfiguration finns i [metod tips för prestanda för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>SQL Server-inställningar
På **SQL Server inställningar**granskar och ändrar du SQL Server namn för virtuell dator, SQL Server version, SQL Server tjänst konto och lösen ord och underhålls schema för SQL Auto-patching.

* **Prefixet SQL Server namn** används för att skapa ett namn för varje SQL Server virtuell dator. I den här självstudien använder du **SQLServer**. Mallen namnger SQL Server virtuella datorer *SQLServer-0* och *SQLServer-1*.
* **SQL Server version** är versionen av SQL Server. I den här självstudien använder **SQL Server 2014**. Du kan också välja **SQL Server 2012** eller **SQL Server 2016**.
* **SQL Server tjänst kontots användar namn** är domän konto namnet för tjänsten SQL Server. I den här självstudien använder du **sqlservice**.
* **Password** är lösen ordet för SQL Server tjänst kontot.  Använd ett komplext lösen ord. Bekräfta lösenordet.
* **Underhålls schema för automatisk uppdatering i SQL** identifierar den veckodag som Azure automatiskt uppdaterar SQL-servrarna. Skriv **söndag**för den här självstudien.
* **Start timme för automatisk uppdatering av SQL** är tiden på dagen för Azure-regionen när automatisk uppdatering börjar.

> [!NOTE]
> Uppdaterings fönstret för varje virtuell dator delas med en timme. Endast en virtuell dator har uppdaterats i taget för att förhindra avbrott i tjänster.
>
>

![SQL Server-inställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Granska inställningarna och klicka sedan på **OK**.

### <a name="summary"></a>Sammanfattning
På sidan Sammanfattning verifierar Azure inställningarna. Du kan också hämta mallen. Granska sammanfattningen. Klicka på **OK**.

### <a name="buy"></a>Köp
Det sista bladet innehåller **användnings villkor**och **sekretess policy**. Granska den här informationen. När du är redo för Azure att börja skapa de virtuella datorerna och alla andra nödvändiga resurser för tillgänglighets gruppen klickar du på **skapa**.

Azure Portal skapar resurs gruppen och alla resurser.

## <a name="monitor-deployment"></a>Övervaka distribution
Övervaka distributions förloppet från Azure Portal. En ikon som representerar distributionen fästs automatiskt på Azure Portal instrument panelen.

![Azure-instrumentpanel](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ansluta till SQL Server
De nya instanserna av SQL Server körs på virtuella datorer som har Internet-anslutna IP-adresser. Du kan vara fjärr skrivbord (RDP) direkt till varje SQL Server virtuell dator.

Följ de här stegen för RDP till en SQL Server:

1. Kontrol lera att distributionen har slutförts från Azure Portal-instrumentpanelen.
2. Klicka på **resurser**.
3. På bladet **resurser** klickar du på **SQLServer-0**, som är dator namnet på en av de virtuella datorer som kör SQL Server.
4. På bladet för **SQLServer-0**klickar du på **Anslut**. Webbläsaren frågar om du vill öppna eller spara objektet fjärr anslutning. Klicka på **öppna**.
5. **Anslutning till fjärr skrivbord** kan varna dig om att det inte går att identifiera utgivaren av den här fjärr anslutningen. Klicka på **Anslut**.
6. Windows-säkerhet efterfrågar att du anger dina autentiseringsuppgifter för att ansluta till den primära domänkontrollantens IP-adress. Klicka på **Använd ett annat konto**. För **användar namn**skriver du **contoso\DomainAdmin**. Du konfigurerade det här kontot när du anger administratörs användar namnet i mallen. Använd det komplexa lösen ord som du valde när du konfigurerade mallen.
7. **Fjärr skrivbord** kan varna dig om att fjärrdatorn inte kunde autentiseras på grund av problem med säkerhets certifikatet. Det visar namnet på säkerhetscertifikatet. Om du har följt självstudien är namnet **SQLServer-0.contoso.com**. Klicka på **Ja**.

Du är nu ansluten med RDP till den SQL Server virtuella datorn. Du kan öppna SQL Server Management Studio, ansluta till standard instansen av SQL Server och kontrol lera att tillgänglighets gruppen har kon figurer ATS.
