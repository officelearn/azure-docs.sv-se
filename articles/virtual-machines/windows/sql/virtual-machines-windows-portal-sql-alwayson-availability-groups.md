---
title: Konfigurera hög tillgänglighet för virtuella Azure Resource Manager-datorer | Microsoft-dokument
description: Den här självstudien visar hur du skapar en alltid på tillgänglighetsgrupp med virtuella Azure-datorer i Azure Resource Manager-läge.
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
ms.openlocfilehash: d7c88e500886453fbfb53655748ccf7025ab7d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374271"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Konfigurera alltid på tillgänglighetsgrupper i Virtuella Azure-datorer automatiskt: Resurshanteraren

Den här självstudien visar hur du skapar en SQL Server-tillgänglighetsgrupp som använder virtuella Azure Resource Manager-datorer. Självstudien använder Azure-blad för att konfigurera en mall. Du kan granska standardinställningarna, skriva nödvändiga inställningar och uppdatera bladen i portalen när du går igenom den här självstudien.

Den fullständiga självstudien skapar en SQL Server-tillgänglighetsgrupp på virtuella Azure-datorer som innehåller följande element:

* Ett virtuellt nätverk som har flera undernät, inklusive ett klientdel och ett serverdelsundernät
* Två domänkontrollanter som har en Active Directory-domän
* Två virtuella datorer som kör SQL Server och distribueras till serverdelsundernätet och ansluts till Active Directory-domänen
* Ett redundanskluster för trenoder med kvorummodellen Node Majoritet
* En tillgänglighetsgrupp som har två synkrona-commit-repliker av en tillgänglighetsdatabas

Följande bild representerar den kompletta lösningen.

![Testlabbarkitektur för tillgänglighetsgrupper i Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Alla resurser i den här lösningen tillhör en enda resursgrupp.

Innan du startar den här självstudien bekräftar du följande:

* Du har redan ett Azure-konto. Om du inte har ett, [registrera dig för ett utvärderingskonto](https://azure.microsoft.com/pricing/free-trial/).
* Du vet redan hur du använder det grafiska gränssnittet för att etablera en virtuell SQL Server-dator från galleriet för virtuella datorer. Mer information finns i [Etablera en virtuell SQL Server-dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Du har redan en gedigen förståelse för tillgänglighetsgrupper. Mer information finns i [Alltid på tillgänglighetsgrupper (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> Om du är intresserad av att använda tillgänglighetsgrupper med SharePoint läser du även [Konfigurera SQL Server 2012 Always On tillgänglighetsgrupper för SharePoint 2013](/SharePoint/administration/configure-an-alwayson-availability-group).
>
>

I den här självstudien använder du Azure-portalen för att:

* Välj mallen Alltid på från portalen.
* Granska mallinställningarna och uppdatera några konfigurationsinställningar för din miljö.
* Övervaka Azure eftersom det skapar hela miljön.
* Anslut till en domänkontrollant och sedan till en server som kör SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Etablera klustret från galleriet
Azure tillhandahåller en galleriavbildning för hela lösningen. Så här hittar du mallen:

1. Logga in på Azure-portalen med ditt konto.
2. Öppna fönstret **Nytt** **i** Azure-portalen.
3. Sök efter **AlwaysOn**i fönstret **Nytt** .
   ![Sök alwayson-mall](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Leta reda på **SQL Server AlwaysOn Cluster i**sökresultaten .
   ![AlwaysOn-mall](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Välj **Resurshanteraren**på **Välj en distributionsmodell**.

### <a name="basics"></a>Grundläggande inställningar
Klicka på **Grunderna** och konfigurera följande inställningar:

* **Administratörsanvändarnamn** är ett användarkonto som har domänadministratörsbehörighet och som är medlem i rollen SQL Server sysadmin fixed server på båda instanserna av SQL Server. För den här självstudien använder du **DomainAdmin**.
* **Lösenord** är lösenordet för domänadministratörskontot. Använd ett komplext lösenord. Bekräfta lösenordet.
* **Prenumeration** är prenumerationen som Azure fakturerar för att köra alla distribuerade resurser för tillgänglighetsgruppen. Om ditt konto har flera prenumerationer kan du ange en annan prenumeration.
* **Resursgrupp** är namnet på den grupp som alla Azure-resurser som skapas av den här mallen tillhör. För den här självstudien använder du **SQL-HA-RG**. Mer information finns i [Översikt över Azure Resource Manager](../../../azure-resource-manager/management/overview.md#resource-groups).
* **Plats** är den Azure-region där självstudien skapar resurserna. Välj en Azure-region.

Följande skärmdump är ett färdigt **Basics-blad:**

![Grundläggande inställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Klicka på **OK**.

### <a name="domain-and-network-settings"></a>Domän- och nätverksinställningar
Den här Azure-gallerimallen skapar en domän- och domänkontrollant. Det skapar också ett nätverk och två undernät. Mallen kan inte skapa servrar i en befintlig domän eller ett virtuellt nätverk. Nästa steg konfigurerar domän- och nätverksinställningarna.

Granska de förinställda värdena för domän- och nätverksinställningarna på bladet **Domän- och nätverksinställningar:**

* **Skogsrotdomännamn** är domännamnet för Active Directory-domänen som är värd för klustret. För självstudiekursen använder **du contoso.com**.
* **Namn på virtuellt nätverk** är nätverksnamnet för det virtuella Azure-nätverket. För handledningen, använd **autohaVNET**.
* **Undernätsnamnet Domain Controller** är namnet på en del av det virtuella nätverket som är värd för domänkontrollanten. Använd **undernät-1**. Det här undernätet använder adressprefix **10.0.0.0/24**.
* **SQL Server-undernätsnamnet** är namnet på en del av det virtuella nätverket som är värd för servrarna som kör SQL Server och filresursvittnet. Använd **undernät-2**. Det här undernätet använder adressprefix **10.0.1.0/26**.

Mer information om virtuella nätverk i Azure finns i [Översikt över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md).  

**Domän- och nätverksinställningarna** ska se ut så här:

![Domän- och nätverksinställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Om det behövs kan du ändra dessa värden. Använd de förinställda värdena för den här självstudien.

Granska inställningarna och klicka sedan på **OK**.

### <a name="availability-group-settings"></a>Inställningar för tillgänglighetsgrupp
Granska de förinställda värdena för tillgänglighetsgruppen och lyssnaren i **gruppinställningarna**för tillgänglighet.

* **Tillgänglighetsgruppnamnet** är det klustrade resursnamnet för tillgänglighetsgruppen. För den här självstudien använder du **Contoso-ag**.
* **Tillgänglighetsgrupplyssnare namn** används av klustret och den interna belastningsutjämnaren. Klienter som ansluter till SQL Server kan använda det här namnet för att ansluta till lämplig replik av databasen. För den här självstudien använder du **Contoso-lyssnaren**.
* **Lyssnarporten för tillgänglighetsgrupper** anger TCP-porten för SQL Server-lyssnaren. För den här självstudien använder du standardporten, **1433**.

Om det behövs kan du ändra dessa värden. Använd de förinställda värdena för den här självstudien.  

![inställningar för tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Klicka på **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Storlek på virtuella datorer, lagringsinställningar
På **VM-storlek, lagringsinställningar,** välj en virtuell SQL Server-storlek och granska de andra inställningarna.

* **Sql Server virtuell datorstorlek** är storleken för både virtuella datorer som kör SQL Server. Välj en lämplig storlek på virtuella datorer för din arbetsbelastning. Om du bygger den här miljön för handledningen använder du **DS2**. För produktionsarbetsbelastningar väljer du en storlek på den virtuella datorn som kan stödja arbetsbelastningen. Många produktionsarbetsbelastningar kräver **DS4** eller större. Mallen bygger två virtuella datorer av den här storleken och installerar SQL Server på var och en. Mer information finns i [Storlekar för virtuella datorer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure installerar Enterprise Edition av SQL Server. Kostnaden beror på utgåvan och den virtuella datorns storlek. Detaljerad information om aktuella kostnader finns i [priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Storleken på den virtuella datorn för domänkontrollanten** är storleken på den virtuella datorn för domänkontrollanterna. För den här självstudien använd **D2**.
* **Filresursvittnet virtuell datorstorlek** är den virtuella datorns storlek för filresursvittnet. För den här självstudien använder du **A1**.
* **SQL Storage-konto** är namnet på det lagringskonto som innehåller SQL Server-data och operativsystemdiskar. För den här självstudien använder du **alwaysonsql01**.
* **DC Storage-konto** är namnet på lagringskontot för domänkontrollanterna. För den här självstudien använder du **alwaysondc01**.
* **SQL Server-datadiskstorlek** i TB är storleken på SQL Server-datadisken i TB. Ange ett tal från 1 till 4. För den här självstudien använder du **1**.
* **Lagringsoptimering** anger specifika lagringskonfigurationsinställningar för virtuella SQL Server-datorer baserat på arbetsbelastningstypen. Alla virtuella SQL Server-datorer i det här scenariot använder premiumlagring med Azure-diskvärdcachen inställd på skrivskyddad. Dessutom kan du optimera SQL Server-inställningarna för arbetsbelastningen genom att välja en av följande tre inställningar:

  * **Allmän arbetsbelastning** anger inga specifika konfigurationsinställningar.
  * **Transaktionsbearbetning** anger spårningsflagga 1117 och 1118.
  * **Datalageruppsättningar** spårflagga 1117 och 610.

Använd **allmän arbetsbelastning**för den här självstudien .

![Lagringsinställningar för VM-storlek](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Granska inställningarna och klicka sedan på **OK**.

#### <a name="a-note-about-storage"></a>En anteckning om lagring
Ytterligare optimeringar beror på storleken på SQL Server-datadiskarna. För varje terabyte datadisk lägger Azure till ytterligare 1 TB premiumlagring. När en server kräver 2 TB eller mer skapar mallen en lagringspool på varje virtuell SQL Server-dator. En lagringspool är en form av lagringsvirtualisering där flera skivor är konfigurerade för att ge högre kapacitet, återhämtning och prestanda.  Mallen skapar sedan ett lagringsutrymme på lagringspoolen och presenterar en enda datadisk till operativsystemet. Mallen anger den här disken som datadisk för SQL Server. Mallen ställer in lagringspoolen för SQL Server med hjälp av följande inställningar:

* Stripe-storlek är interleave-inställningen för den virtuella disken. Transaktionsarbetsbelastningar använder 64 KB. Datalagringsarbetsbelastningar använder 256 KB.
* Återhämtning är enkel (ingen återhämtning).

> [!NOTE]
> Azure premium storage är lokalt redundant och behåller tre kopior av data inom en enda region, så ytterligare återhämtning vid lagringspoolen krävs inte.
>
>

* Kolumnantalet är lika med antalet diskar i lagringspoolen.

Mer information om lagringsutrymme och lagringspooler finns i:

* [Översikt för lagringsutrymmen](https://technet.microsoft.com/library/hh831739.aspx)
* [Säkerhets- och lagringspooler för Windows Server](https://technet.microsoft.com/library/dn390929.aspx)

Mer information om metodtips för SQL Server-konfiguration finns [i Metodtips för prestanda för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>SQL Server-inställningar
På **SQL Server-inställningar**granskar och ändrar du sql server-namnprefixet för virtuella datorer, SQL Server-version, SQL Server-tjänstkonto och lösenord samt underhållsschemat för automatisk korrigering av SQL Server.

* **Sql Server Name Prefix** används för att skapa ett namn för varje virtuell SQL Server-dator. För den här självstudien använder du **sqlserver**. Mallen namnger sql server virtuella datorer *sqlserver-0* och *sqlserver-1*.
* **SQL Server-versionen** är den version av SQL Server. För den här självstudien använd **SQL Server 2014**. Du kan också välja **SQL Server 2012** eller **SQL Server 2016**.
* **Användarnamn för SQL Server-tjänstkontot** är domännamnet för SQL Server-tjänsten. För den här självstudien använder du **sqlservice**.
* **Lösenord** är lösenordet för SQL Server-tjänstkontot.  Använd ett komplext lösenord. Bekräfta lösenordet.
* **SQL Auto Patching underhållsschema** identifierar dagen i veckan som Azure automatiskt korrigeringar SQL-servrar. För den här självstudien skriver du **söndag**.
* **SQL Auto Patching underhåll starttimme** är den tid på dagen för Azure-regionen när automatisk korrigering börjar.

> [!NOTE]
> Korrigeringsfönstret för varje virtuell dator fördelas med en timme. Endast en virtuell dator korrigeras åt gången för att förhindra avbrott i tjänster.
>
>

![SQL Server-inställningar](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Granska inställningarna och klicka sedan på **OK**.

### <a name="summary"></a>Sammanfattning
På sammanfattningssidan validerar Azure inställningarna. Du kan också ladda ner mallen. Granska sammanfattningen. Klicka på **OK**.

### <a name="buy"></a>Köp
Det här sista bladet innehåller **användarvillkor**och **sekretesspolicy.** Granska den här informationen. När du är redo för Azure att börja skapa de virtuella datorerna och alla andra nödvändiga resurser för tillgänglighetsgruppen klickar du på **Skapa**.

Azure-portalen skapar resursgruppen och alla resurser.

## <a name="monitor-deployment"></a>Övervaka distributionen
Övervaka distributionsstatusen från Azure-portalen. En ikon som representerar distributionen fästs automatiskt på Azure-portalens instrumentpanel.

![Azure-instrumentpanel](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Anslut till SQL Server
De nya instanserna av SQL Server körs på virtuella datorer som har IP-adresser som är anslutna till Internet. Du kan fjärrskrivbord (RDP) direkt till varje virtuell SQL Server-dator.

Så här gör du så här om du vill rdp-till en SQL Server:

1. Kontrollera att distributionen har lyckats från Instrumentpanelen i Azure-portalen.
2. Klicka på **Resurser**.
3. Klicka på **sqlserver-0**i bladet **Resurser** , som är datornamnet på en av de virtuella datorer som kör SQL Server.
4. Klicka på **Anslut**på bladet för **sqlserver-0**. Webbläsaren frågar om du vill öppna eller spara fjärranslutningsobjektet. Klicka på **Open** (Öppna).
5. **Fjärrskrivbordsanslutning** kan varna dig om att utgivaren av den här fjärranslutningen inte kan identifieras. Klicka på **Anslut**.
6. Windows-säkerhet uppmanar dig att ange dina autentiseringsuppgifter för att ansluta till IP-adressen för den primära domänkontrollanten. Klicka på **Använd ett annat konto**. För **Användarnamn**skriver du **contoso\DomainAdmin**. Du konfigurerade det här kontot när du angav administratörsanvändarnamnet i mallen. Använd det komplexa lösenord som du valde när du konfigurerade mallen.
7. **Fjärrskrivbord** kan varna dig om att fjärrdatorn inte kunde autentiseras på grund av problem med säkerhetscertifikatet. Den visar säkerhetscertifikatnamnet. Om du har följt självstudien är namnet **sqlserver-0.contoso.com**. Klicka på **Ja**.

Du är nu ansluten med RDP till den virtuella SQL Server-datorn. Du kan öppna SQL Server Management Studio, ansluta till standardinstansen för SQL Server och kontrollera att tillgänglighetsgruppen är konfigurerad.
