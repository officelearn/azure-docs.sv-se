---
title: "Konfigurera hög tillgänglighet för virtuella datorer i Azure Resource Manager | Microsoft Docs"
description: "Den här kursen visar hur du skapar en Always On-tillgänglighetsgrupp med virtuella Azure-datorer i Azure Resource Manager-läge."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: d430febee23081b26eee0a68d4beb43228549f52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Konfigurera Always On-Tillgänglighetsgrupper i Azure Virtual Machines automatiskt: Resource Manager

Den här kursen visar hur du skapar en SQL Server-tillgänglighetsgrupp som använder Azure Resource Manager virtuella datorer. Självstudierna använder Azure blad för att konfigurera en mall. Du kan granska standardinställningarna, ange nödvändiga inställningar och uppdatera blad i portalen som du går igenom den här kursen.

Fullständig kursen skapar en tillgänglighetsgrupp för SQL Server på Azure Virtual Machines som innehåller följande element:

* Ett virtuellt nätverk som har flera undernät, inklusive en frontend och backend-undernät
* Två domänkontrollanter som har en Active Directory-domän
* Två virtuella datorer som kör SQL Server distribueras till backend-undernät och är anslutna till Active Directory-domän
* Ett redundanskluster med tre noder med kvorummodellen Nodmajoritet
* En tillgänglighetsgrupp som har två replikerna med synkront genomförande av en tillgänglighetsdatabas

Följande bild visar den kompletta lösningen.

![Test lab-arkitektur för Tillgänglighetsgrupper i Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Alla resurser i den här lösningen tillhör en enskild resursgrupp.

Innan du börjar den här kursen kan du kontrollera följande:

* Du har redan ett Azure-konto. Om du inte har någon [registrera dig för ett utvärderingskonto](http://azure.microsoft.com/pricing/free-trial/).
* Du vet redan hur du använder det grafiska Användargränssnittet för att etablera en virtuell dator med SQL Server från galleriet för virtuella datorer. Mer information finns i [etablera en virtuell dator i SQL Server på Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Du har redan en god förståelse av Tillgänglighetsgrupper. Mer information finns i [Always On-Tillgänglighetsgrupper (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Om du vill använda Tillgänglighetsgrupper med SharePoint finns även [Konfigurera SQL Server 2012 Always On-Tillgänglighetsgrupper för SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).
>
>

I de här självstudierna Använd Azure portal:

* Välj mallen alltid på portalen.
* Granska mallinställningarna och uppdatera några konfigurationsinställningar för din miljö.
* Övervaka Azure som skapas av hela miljön.
* Anslut till en domänkontrollant och sedan till en server som kör SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Etablera klustret från galleriet
Azure tillhandahåller en bild i galleriet för hela lösningen. Leta upp mallen:

1. Logga in på Azure-portalen med ditt konto.
2. I Azure-portalen klickar du på **+ ny** att öppna den **ny** bladet.
3. På den **ny** bladet, söka efter **AlwaysOn**.
   ![Hitta AlwaysOn-mall](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Leta upp i sökresultaten **SQL Server AlwaysOn-kluster**.
   ![AlwaysOn-mall](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. På **Välj en distributionsmodell**, Välj **Resource Manager**.

### <a name="basics"></a>Grundläggande inställningar
Klicka på **grunderna** och konfigurera följande inställningar:

* **Administratörsanvändarnamn** är ett användarkonto som har administratörsbehörighet för domänen och är medlem i den fasta serverrollen SQL Server skulle sysadmin på båda instanser av SQL Server. Den här kursen använder **DomainAdmin**.
* **Lösenordet** är lösenordet för administratörskontot för domänen. Använd ett komplext lösenord. Bekräfta lösenordet.
* **Prenumerationen** är prenumerationen som Azure fakturerar att köra alla distribuerade resurser för tillgänglighetsgruppen. Om ditt konto har flera prenumerationer, kan du ange en annan prenumeration.
* **Resursgruppen** är namnet på gruppen som alla Azure-resurser som har skapats med den här mallen tillhör. Den här kursen använder **SQL-hög tillgänglighet-RG**. Mer information finns i [Översikt över Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Plats** är Azure-regionen där kursen skapar resurser. Välj en Azure-region.

Följande skärmbild är en slutförd **grunderna** bladet:

![Grundläggande inställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Klicka på **OK**.

### <a name="domain-and-network-settings"></a>Domän-och nätverksinställningar
Den här mallen för Azure-galleriet skapar en domän och domänkontrollanter. Det skapar också ett nätverk och två undernät. Mallen kan inte skapa servrar i en befintlig domän eller ett virtuellt nätverk. Nästa steg konfigurerar inställningar för domän- och nätverksanslutning.

På den **domän-och nätverksinställningarna** bladet granska de förinställda värdena för domänen och inställningar:

* **Namn för skogsrotsdomänen** är domännamnet för Active Directory-domänen som är värd för klustret. Självstudierna använder **contoso.com**.
* **Virtuella nätverksnamnet** är nätverksnamnet för det virtuella Azure-nätverket. Självstudierna använder **autohaVNET**.
* **Namnet på domänkontrollanten undernät** är namnet på en del av det virtuella nätverket som är värd för domänkontrollanten. Använd **undernät 1**. Det här undernätet använder adressprefixet **10.0.0.0/24**.
* **SQL Server-undernätsnamn** är namnet på en del av det virtuella nätverket som är värd för de servrar som kör SQL Server och den filresurs vittne. Använd **undernät 2**. Det här undernätet använder adressprefixet **10.0.1.0/26**.

Mer information om virtuella nätverk i Azure finns [översikt över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md).  

Den **domän-och nätverksinställningarna** bör se ut som följande skärmbild:

![Domän-och nätverksinställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Du kan ändra dessa värden om det behövs. Använd de förinställda värdena för den här självstudiekursen.

Granska inställningarna och klicka sedan på **OK**.

### <a name="availability-group-settings"></a>Inställningarna för tillgänglighet
På **tillgänglighet gruppinställningar**, granska de förinställda värdena för tillgänglighetsgruppen och lyssnaren.

* **Tillgänglighetsgruppens namn** är klustrade resursnamnet för tillgänglighetsgruppen. Den här kursen använder **Contoso ag**.
* **Tillgänglighetsgruppens lyssnare namn** används av klustret och den interna belastningsutjämnaren. Klienter som ansluter till SQL Server kan använda det här namnet för att ansluta till lämplig replik av databasen. Den här kursen använder **Contoso-lyssnaren**.
* **Porten för tillgänglighetsgruppens lyssnare** anger TCP-port för SQL Server-lyssnare. För den här kursen använder standardporten, **1433**.

Du kan ändra dessa värden om det behövs. Använd de förinställda värdena för den här självstudiekursen.  

![Inställningarna för tillgänglighet](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Klicka på **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Storlek på virtuell dator, inställningar för lagring
På **VM-storlek, Lagringsinställningar**, Välj en SQL Server-storlek för virtuell dator och granska de andra inställningarna.

* **Storlek för virtuell dator i SQL Server** storlek för både virtuella datorer som kör SQL Server. Välj en lämplig virtuell datorstorlek för din arbetsbelastning. Om du skapar den här miljön under kursen, använda **DS2**. Välj en storlek på virtuell dator som har stöd för arbetsbördan för produktionsarbetsbelastningar. Många produktionsarbetsbelastningar kräver **DS4** eller större. Mallen skapar två virtuella datorer av den här storleken och installerar SQL Server på var och en. Mer information finns i [storlekar för virtuella datorer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure installerar Enterprise-utgåvan av SQL Server. Kostnaden beror på utgåvan och storleken på virtuella datorn. Detaljerad information om aktuella kostnaderna finns [virtuella datorer priser](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Domain controller virtuella datorstorleken** är storleken på virtuella datorn för domänkontrollanter. För den här självstudiekursen **D2**.
* **Dela vittne virtuella filstorlek** är storleken på virtuella datorn för filresursvittnet. Den här kursen använder **A1**.
* **SQL Storage-konto** är namnet på det lagringskonto som innehåller SQL Server-data och operativsystemet diskar. Den här kursen använder **alwaysonsql01**.
* **DC-lagringskonto** är namnet på lagringskontot för domänkontrollanter. Den här kursen använder **alwaysondc01**.
* **SQL Server-data diskstorlek** TB är storleken på disken för SQL Server-data i TB. Ange ett tal från 1 till 4. Den här kursen använder **1**.
* **Lagringsoptimering** anger särskilda lagrings-konfigurationsinställningar för SQL Server virtuella datorer baserat på arbetsbelastning. Alla SQL Server-datorer i det här scenariot kan du använda premium-lagring med Azure värden diskcache anges skrivskyddade. Dessutom kan du optimera SQL Server-inställningar för arbetsbelastning genom att välja ett av dessa tre inställningar:

  * **Allmän arbetsbelastning** anger inga specifika konfigurationsinställningar.
  * **Transaktionell bearbetning** anger spåra flaggan 1117 och 1118.
  * **Datalagring** anger spåra flaggan 1117 och 610.

Den här kursen använder **allmänna arbetsbelastning**.

![Lagringsinställningarna för VM-storlek](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Granska inställningarna och klicka sedan på **OK**.

#### <a name="a-note-about-storage"></a>En anteckning om lagring
Ytterligare optimeringar beror på storleken på diskar för SQL Server-data. Lägger till ytterligare 1 TB premium-lagring för varje terabyte datadisk, Azure. När en server kräver 2 TB eller mer, skapas en lagringspool på varje virtuell dator med SQL Server. En lagringspool är en form av lagringsvirtualisering där flera skivor är konfigurerad för att ge högre kapacitet, återhämtning och prestanda.  Mallen sedan skapas ett lagringsutrymme i lagringspoolen och visar en enkel datadisk för operativsystemet. Mallen anger den här disken datadisken för SQL Server. Mallen justerar lagringspoolen för SQL Server med hjälp av följande inställningar:

* Stripe-storlek är överlagring för den virtuella disken. Transaktionell arbetsbelastningar använder 64 KB. Arbetsbelastningar i informationslager använda 256 KB.
* Återhämtning är enkel (ingen återhämtning).

> [!NOTE]
> Azure premium-lagring är lokalt redundant och behålls tre kopior av data inom en enskild region så att ytterligare återhämtning vid lagringspoolen inte krävs.
>
>

* Kolumnantal är lika med antalet diskar i lagringspoolen.

För ytterligare information om lagringsutrymme och lagringspooler, se:

* [Översikt för lagringsutrymmen](http://technet.microsoft.com/library/hh831739.aspx)
* [Windows Serverbackup och lagringspooler](http://technet.microsoft.com/library/dn390929.aspx)

Läs mer om bästa praxis för SQL Server configuration [prestandarelaterade Metodtips för SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>SQL Server-inställningar
På **SQL Server-inställningar**, granska och ändra namnprefixet för virtuell dator för SQL Server, SQL Server-version, SQL Server-tjänstkontot och lösenord och automatisk uppdatering underhållsschema för SQL.

* **SQL Server-namnet Prefix** används för att skapa ett namn för varje virtuell dator med SQL Server. Den här kursen använder **sqlserver**. Mallen trots att de virtuella datorerna för SQL Server *sqlserver-0* och *sqlserver-1*.
* **SQL Server-version** är versionen av SQL Server. För den här självstudiekursen **SQL Server 2014**. Du kan också välja **SQL Server 2012** eller **SQL Server 2016**.
* **Användarnamn för SQL Server-tjänsten** är domänkontonamnet för SQL Server-tjänsten. Den här kursen använder **sqlservice**.
* **Lösenordet** är lösenordet för SQL Server-tjänstkontot.  Använd ett komplext lösenord. Bekräfta lösenordet.
* **SQL automatisk uppdatering underhållsschema** identifierar veckodag att Azure automatiskt korrigeringsfiler SQL-servrar. Den här kursen skriver **söndag**.
* **Starttimme för underhåll SQL automatisk uppdatering** är tid på dagen för Azure-regionen när automatisk korrigering börjar.

> [!NOTE]
> Uppdateringsperiod för varje virtuell dator ut en timme. Endast en virtuell dator korrigeras åt gången för att förhindra avbrott i tjänsterna.
>
>

![SQL Server-inställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Granska inställningarna och klicka sedan på **OK**.

### <a name="summary"></a>Sammanfattning
På sidan Översikt verifierar inställningarna i Azure. Du kan också hämta mallen. Granska sammanfattningen. Klicka på **OK**.

### <a name="buy"></a>Köp
Det här sista bladet innehåller **användningsvillkoren**, och **sekretesspolicy**. Gå igenom informationen. När du är redo för Azure att börja skapa de virtuella datorerna och alla andra nödvändiga resurser för tillgänglighetsgruppen, klickar du på **skapa**.

Azure-portalen skapar resursgruppen och alla resurser.

## <a name="monitor-deployment"></a>Övervakaren distribution
Övervaka förloppet för distributionen från Azure-portalen. En ikon som representerar distributionen är automatiskt fäst på instrumentpanelen för Azure-portalen.

![Instrumentpanelen för Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ansluta till SQLServer
Nya instanser av SQL Server körs på virtuella datorer som har internet-ansluten IP-adresser. Du kan fjärrskrivbord (RDP) direkt till varje virtuell dator med SQL Server.

Följ dessa steg till RDP till en SQL Server:

1. Verifiera att distributionen har slutförts på Azure portal instrumentpanel.
2. Klicka på **resurser**.
3. I den **resurser** bladet, klickar du på **sqlserver-0**, vilket är namnet på en av de virtuella datorer som kör SQL Server.
4. På bladet för **sqlserver-0**, klickar du på **Anslut**. Webbläsaren frågar om du vill öppna eller spara objektet fjärranslutning. Klicka på **öppna**.
5. **Anslutning till fjärrskrivbord** kan varna dig att utgivaren av den här fjärranslutningen inte kan identifieras. Klicka på **Anslut**.
6. Windows-säkerhet uppmanas du att ange dina autentiseringsuppgifter för att ansluta till IP-adressen för den primära domänkontrollanten. Klicka på **använder ett annat konto**. För **användarnamn**, typen **contoso\DomainAdmin**. Du har konfigurerat det här kontot när du ställer in administratören användarnamn i mallen. Använd komplexa lösenord som du valde när du konfigurerade mallen.
7. **Fjärrskrivbord** kan varna dig fjärrdatorn inte kunde autentiseras på grund av problem med säkerhetscertifikatet. Den visar namnet på säkerhet. Om du har följt kursen namnet är **sqlserver 0.contoso.com**. Klicka på **Ja**.

Du är nu ansluten med RDP till den virtuella datorn för SQL Server. Du kan öppna SQL Server Management Studio, ansluta till standardinstansen av SQL Server och kontrollera att tillgänglighetsgruppen har konfigurerats.
