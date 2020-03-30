---
title: Konfigurera tillgänglighetsgrupplyssnare & belastningsutjämnare (Azure-portal)
description: Steg-för-steg-instruktioner för att skapa en lyssnare för en alltid på tillgänglighetsgrupp för SQL Server i virtuella Azure-datorer
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: aefd7a55090da7f55404d6f551ab61268582ff5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096331"
---
# <a name="configure-a-load-balancer-for-an-availability-group-on-azure-sql-server-vms"></a>Konfigurera en belastningsutjämnare för en tillgänglighetsgrupp på virtuella Azure SQL Server-datorer
I den här artikeln beskrivs hur du skapar en belastningsutjämnare för en SQL Server Always On-tillgänglighetsgrupp i virtuella Azure-datorer som körs med Azure Resource Manager. En tillgänglighetsgrupp kräver en belastningsutjämnare när SQL Server-instanserna finns på virtuella Azure-datorer. Belastningsutjämnaren lagrar IP-adressen för tillgänglighetsgruppens lyssnare. Om en tillgänglighetsgrupp sträcker sig över flera regioner behöver varje region en belastningsutjämnare.

För att slutföra den här uppgiften måste du ha en SQL Server-tillgänglighetsgrupp distribuerad på virtuella Azure-datorer som körs med Resource Manager. Båda virtuella SQL Server-datorerna måste tillhöra samma tillgänglighetsuppsättning. Du kan använda [Microsoft-mallen](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) för att automatiskt skapa tillgänglighetsgruppen i Resource Manager. Den här mallen skapar automatiskt en intern belastningsutjämnare åt dig. 

Om du vill kan du [konfigurera en tillgänglighetsgrupp manuellt](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Den här artikeln kräver att dina tillgänglighetsgrupper redan är konfigurerade.  

Relaterade ämnen inkluderar:

* [Konfigurera alltid på tillgänglighetsgrupper i Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Genom att gå igenom den här artikeln skapar och konfigurerar du en belastningsutjämnare i Azure-portalen. När processen är klar konfigurerar du klustret så att IP-adressen används från belastningsutjämnaren för tillgänglighetsgrupplyssnaren.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Skapa och konfigurera belastningsutjämnaren i Azure-portalen
Gör följande i den här delen av uppgiften:

1. Skapa belastningsutjämnaren i Azure-portalen och konfigurera IP-adressen.
2. Konfigurera backend-poolen.
3. Skapa sonden. 
4. Ställ in reglerna för belastningsutjämning.

> [!NOTE]
> Om SQL Server-instanserna finns i flera resursgrupper och regioner utför du varje steg två gånger, en gång i varje resursgrupp.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Steg 1: Skapa belastningsutjämnaren och konfigurera IP-adressen
Skapa först belastningsutjämnaren. 

1. Öppna resursgruppen som innehåller virtuella SQL Server-datorer i Azure-portalen. 

2. Klicka på Lägg **till**i resursgruppen .

3. Sök efter **belastningsutjämnare** och välj sedan **Belastningsutjämnare**i sökresultaten , som publiceras av **Microsoft**.

4. Klicka på **Skapa**på bladet **Belastningsutjämnare** .

5. Konfigurera belastningsutjämnaren i dialogrutan **Skapa belastningsutjämnare** på följande sätt:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett textnamn som representerar belastningsutjämnaren. Till exempel **sqlLB**. |
   | **Typ** |**Internt**: De flesta implementeringar använder en intern belastningsutjämnare, vilket gör att program inom samma virtuella nätverk kan ansluta till tillgänglighetsgruppen.  </br> **Extern**: Tillåter program att ansluta till tillgänglighetsgruppen via en offentlig Internetanslutning. |
   | **Virtuellt nätverk** |Välj det virtuella nätverk som SQL Server-instanserna finns i. |
   | **Undernät** |Markera det undernät som SQL Server-instanserna finns i. |
   | **TILLDELNING AV IP-adress** |**Statisk** |
   | **Privat IP-adress** |Ange en tillgänglig IP-adress från undernätet. Använd den här IP-adressen när du skapar en lyssnare i klustret. I ett PowerShell-skript, senare i den `$ILBIP` här artikeln, använder du den här adressen för variabeln. |
   | **Prenumeration** |Om du har flera prenumerationer kan det här fältet visas. Välj den prenumeration som du vill associera med den här resursen. Det är normalt samma prenumeration som alla resurser för tillgänglighetsgruppen. |
   | **Resursgrupp** |Markera den resursgrupp som SQL Server-instanserna finns i. |
   | **Location** |Välj den Azure-plats som SQL Server-instanserna finns i. |

6. Klicka på **Skapa**. 

Azure skapar belastningsutjämnaren. Belastningsutjämnaren tillhör ett visst nätverk, undernät, en resursgrupp och en plats. När Azure har slutfört uppgiften verifierar du belastningsutjämningsinställningarna i Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Steg 2: Konfigurera backend-poolen
Azure anropar *backend-adresspoolens backend-pool*. I det här fallet är backend-poolen adresserna för de två SQL Server-instanserna i din tillgänglighetsgrupp. 

1. Klicka på den belastningsutjämnare som du skapade i resursgruppen. 

2. Klicka på **Backend-pooler i** **Inställningar**.

3. I **Backend-pooler**klickar du på **Lägg till** för att skapa en backend-adresspool. 

4. Skriv ett namn för backend-poolen under **Namn**i Poolen Lägg **till backend.**

5. Klicka på **Lägg till en virtuell dator**under Virtuella **datorer.** 

6. Klicka på Välj **en tillgänglighetsuppsättning**under **Välj virtuella datorer**och ange sedan den tillgänglighetsuppsättning som virtuella SQL Server-datorerna tillhör.

7. När du har valt tillgänglighetsuppsättningen klickar du på **Välj de virtuella datorerna,** väljer de två virtuella datorer som är värdar för SQL Server-instanserna i tillgänglighetsgruppen och klickar sedan på **Välj**. 

8. Klicka på **OK** om du vill stänga bladen för **Välj virtuella datorer**och Lägga till **serverdapool**. 

Azure uppdaterar inställningarna för backend-adresspoolen. Nu har tillgänglighetsuppsättningen en pool med två SQL Server-instanser.

### <a name="step-3-create-a-probe"></a>Steg 3: Skapa en sond
Avsökningen definierar hur Azure verifierar vilken av SQL Server-instanserna som för närvarande äger tillgänglighetsgruppavlyssnaren. Azure avsöker tjänsten baserat på IP-adressen på en port som du definierar när du skapar avsökningen.

1. Klicka på Hälsoavsökningar på bladet **Inställningar för belastningsutjämnare** . **Health probes** 

2. Klicka på **Lägg till**på bladet **Hälsa avsökningar** .

3. Konfigurera avsökningen på **Bladet Lägg till sond.** Använd följande värden för att konfigurera avsökningen:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett textnamn som representerar avsökningen. Till exempel **SQLAlwaysOnEndPointProbe**. |
   | **Protokollet** |**TCP** |
   | **Port** |Du kan använda vilken tillgänglig port som helst. Till exempel *59999*. |
   | **Intervall** |*5* |
   | **Felfritt tröskelvärde** |*2* |

4.  Klicka på **OK**. 

> [!NOTE]
> Kontrollera att porten du anger är öppen i brandväggen för båda SQL Server-instanserna. Båda instanserna kräver en inkommande regel för TCP-porten som du använder. Mer information finns i [Lägga till eller redigera brandväggsregel](https://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure skapar avsökningen och använder den sedan för att testa vilken SQL Server-instans som har lyssnaren för tillgänglighetsgruppen.

### <a name="step-4-set-the-load-balancing-rules"></a>Steg 4: Ställ in reglerna för belastningsutjämning
Belastningsutjämningsreglerna konfigurerar hur belastningsutjämnaren dirigerar trafik till SQL Server-instanserna. För den här belastningsutjämnaren aktiverar du direkt serverretur eftersom endast en av de två SQL Server-instanserna äger tillgänglighetsgruppens lyssnarresurs åt gången.

1. Klicka på **Belastningsutjämningsregler**på bladet Inställningar för belastningsutjämnare . **Settings** 

2. Klicka på **Lägg till**på bladet **Belastningsutjämningsregler** .

3. Konfigurera belastningsutjämningsregeln på bladet **Lägg till belastningsutjämningsregler.** Använd följande inställningar: 

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett textnamn som representerar belastningsutjämningsreglerna. **SqlAlwaysOnEndPointListener**. |
   | **Protokollet** |**TCP** |
   | **Port** |*1433* |
   | **Backend-port** |*1433*. Det här värdet ignoreras eftersom den här regeln använder **flytande IP (direkt serverretur)**. |
   | **Avsökning** |Använd namnet på den avsökning som du skapade för den här belastningsutjämnaren. |
   | **Sessionspermanens** |**Inget** |
   | **Tidsgränsen för inaktiv tid (minuter)** |*4* |
   | **Flytande IP (direkt serverretur)** |**Enabled** |

   > [!NOTE]
   > Du kanske måste rulla nedåt i bladet för att visa alla inställningar.
   > 

4. Klicka på **OK**. 
5. Azure konfigurerar belastningsutjämningsregeln. Nu är belastningsutjämnaren konfigurerad för att dirigera trafik till SQL Server-instansen som är värd för lyssnaren för tillgänglighetsgruppen. 

Nu har resursgruppen en belastningsutjämnare som ansluter till båda SQL Server-datorerna. Belastningsutjämnaren innehåller också en IP-adress för SQL Server Always On availability group listener, så att båda datorerna kan svara på begäranden för tillgänglighetsgrupperna.

> [!NOTE]
> Om SQL Server-instanserna finns i två separata regioner upprepar du stegen i den andra regionen. Varje region kräver en belastningsutjämnare. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurera klustret så att den använder IP-adressen för belastningsutjämnaren
Nästa steg är att konfigurera lyssnaren i klustret och ansluta lyssnaren. Gör följande: 

1. Skapa tillgänglighetsgruppavlyssnaren i redundansklustret. 

2. Ta med lyssnaren online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Steg 5: Skapa tillgänglighetsgruppavlyssnare i redundansklustret
I det här steget skapar du manuellt tillgänglighetsgruppavlyssnaren i Redundansklusterhanteraren och SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Verifiera lyssnarens konfiguration

Om klusterresurserna och beroendena är korrekt konfigurerade bör du kunna visa lyssnaren i SQL Server Management Studio. Så här ställer du in lyssnarporten:

1. Starta SQL Server Management Studio och anslut sedan till den primära repliken.

2. Gå till**Availability Group Listeners** **AlwaysOn Tillgänglighetsgrupper** > **Availability Groups** > med hög tillgänglighet.  
    Du bör nu se lyssnarnamnet som du skapade i Redundansklusterhanteraren. 

3. Högerklicka på lyssnarens namn och klicka sedan på **Egenskaper**.

4. I rutan **Port** anger du portnumret för tillgänglighetsgruppens lyssnare med hjälp av $EndpointPort du använde tidigare (1433 var standard) och klickar sedan på **OK**.

Du har nu en tillgänglighetsgrupp i virtuella Azure-datorer som körs i Resource Manager-läge. 

## <a name="test-the-connection-to-the-listener"></a>Testa anslutningen till lyssnaren
Testa anslutningen genom att göra följande:

1. RDP till en SQL Server-instans som finns i samma virtuella nätverk, men som inte äger repliken. Den här servern kan vara den andra SQL Server-instansen i klustret.

2. Använd **sqlcmd-verktyget** för att testa anslutningen. Följande skript upprättar till exempel en **sqlcmd-anslutning** till den primära repliken via lyssnaren med Windows-autentisering:
   
        sqlcmd -S <listenerName> -E

SQLCMD-anslutningen ansluter automatiskt till SQL Server-instansen som är värd för den primära repliken. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Skapa en IP-adress för en ytterligare tillgänglighetsgrupp

Varje tillgänglighetsgrupp använder en separat lyssnare. Varje lyssnare har sin egen IP-adress. Använd samma belastningsutjämnare för att lagra IP-adressen för ytterligare lyssnare. När du har skapat en tillgänglighetsgrupp lägger du till IP-adressen i belastningsutjämnaren och konfigurerar sedan lyssnaren.

Så här lägger du till en IP-adress i en belastningsutjämnare med Azure-portalen:

1. Öppna resursgruppen som innehåller belastningsutjämnaren i Azure-portalen och klicka sedan på belastningsutjämnaren. 

2. Klicka på **Frontend IP-pool**under **INSTÄLLNINGAR**och klicka sedan på **Lägg till**. 

3. Under **Lägg till IP-adress för klientdelen**tilldelar du ett namn för klientdelen. 

4. Kontrollera att det **virtuella nätverket** och **undernätet** är desamma som SQL Server-instanserna.

5. Ange IP-adressen för lyssnaren. 
   
   >[!TIP]
   >Du kan ange IP-adressen till statisk och skriva en adress som för närvarande inte används i undernätet. Alternativt kan du ställa in IP-adressen till dynamisk och spara den nya frontend IP-poolen. När du gör det tilldelar Azure-portalen automatiskt en tillgänglig IP-adress till poolen. Du kan sedan öppna frontend IP-poolen igen och ändra tilldelningen till statisk. 

6. Spara IP-adressen för lyssnaren. 

7. Lägg till en hälsoavsökning med hjälp av följande inställningar:

   |Inställning |Värde
   |:-----|:----
   |**Namn** |Ett namn för att identifiera sonden.
   |**Protokollet** |TCP
   |**Port** |En oanvänd TCP-port som måste vara tillgänglig på alla virtuella datorer. Det kan inte användas för något annat ändamål. Inga två lyssnare kan använda samma sondport. 
   |**Intervall** |Tiden mellan avsökningsförsöken. Använd standard (5).
   |**Felfritt tröskelvärde** |Antalet på varandra följande tröskelvärden som ska misslyckas innan en virtuell dator anses vara felfritt.

8. Spara avsökningen genom att klicka på **OK.** 

9. Skapa en belastningsutjämningsregel. Klicka på **Regler för belastningsutjämning**och sedan på **Lägg till**.

10. Konfigurera den nya belastningsutjämningsregeln med hjälp av följande inställningar:

    |Inställning |Värde
    |:-----|:----
    |**Namn** |Ett namn för att identifiera belastningsutjämningsregeln. 
    |**IP-adress för klientdel** |Välj den IP-adress som du skapade. 
    |**Protokollet** |TCP
    |**Port** |Använd porten som SQL Server-instanserna använder. En standardinstans använder port 1433, om du inte har ändrat den. 
    |**Backend-port** |Använd samma värde som **Port**.
    |**Backend pool** |Poolen som innehåller de virtuella datorerna med SQL Server-instanserna. 
    |**Hälsosond** |Välj den avsökning som du skapade.
    |**Sessionspermanens** |Inget
    |**Tidsgränsen för inaktiv tid (minuter)** |Standard (4)
    |**Flytande IP (direkt serverretur)** | Enabled

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Konfigurera tillgänglighetsgruppen så att den nya IP-adressen används

Om du vill konfigurera klustret igen upprepar du stegen som du följde när du skapade den första tillgänglighetsgruppen. Det vill än konfigurera [klustret så att det nya IP-adressen används](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

När du har lagt till en IP-adress för lyssnaren konfigurerar du gruppen ytterligare tillgänglighet genom att göra följande: 

1. Kontrollera att avsökningsporten för den nya IP-adressen är öppen på båda virtuella SQL Server-datorerna. 

2. [Lägg till klientåtkomstpunkten i Klusterhanteraren](#addcap).

3. [Konfigurera IP-resursen för tillgänglighetsgruppen](#congroup).

   >[!IMPORTANT]
   >När du skapar IP-adressen använder du IP-adressen som du har lagt till i belastningsutjämnaren.  

4. [Gör SQL Server-tillgänglighetsgruppresursen beroende av klientåtkomstpunkten](#dependencyGroup).

5. [Gör klientåtkomstpunktens resurs beroende av IP-adressen](#listname).
 
6. [Ange klusterparametrar i PowerShell](#setparam).

När du har konfigurerat tillgänglighetsgruppen så att den nya IP-adressen används konfigurerar du anslutningen till lyssnaren. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Lägga till belastningsutjämningsregel för gruppen för distribuerad tillgänglighet

Om en tillgänglighetsgrupp deltar i en grupp för distribuerad tillgänglighet behöver belastningsutjämnaren ytterligare en regel. Den här regeln lagrar porten som används av gruppen för distribuerad tillgänglighet.

>[!IMPORTANT]
>Det här steget gäller endast om tillgänglighetsgruppen deltar i en [grupp för distribuerad tillgänglighet](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Skapa en inkommande regel för TCP-porten för den distribuerade tillgänglighetsgruppen på varje server som deltar i gruppen för distribuerad tillgänglighet. I många exempel används 5022 i dokumentationen. 

1. Klicka på belastningsutjämnaren i Azure-portalen och klicka sedan på **Utjämningsregler**för belastning och klicka sedan på **+Lägg till**. 

1. Skapa belastningsutjämningsregeln med följande inställningar:

   |Inställning |Värde
   |:-----|:----
   |**Namn** |Ett namn för att identifiera belastningsutjämningsregeln för gruppen för distribuerad tillgänglighet. 
   |**IP-adress för klientdel** |Använd samma ip-adress för klientdel som tillgänglighetsgruppen.
   |**Protokollet** |TCP
   |**Port** |5022 - Porten för [slutpunktslyssnaren för den distribuerade tillgänglighetsgruppen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Kan vara vilken port som helst.  
   |**Backend-port** | 5022 - Använd samma värde som **Port**.
   |**Backend pool** |Poolen som innehåller de virtuella datorerna med SQL Server-instanserna. 
   |**Hälsosond** |Välj den avsökning som du skapade.
   |**Sessionspermanens** |Inget
   |**Tidsgränsen för inaktiv tid (minuter)** |Standard (4)
   |**Flytande IP (direkt serverretur)** | Enabled

Upprepa dessa steg för belastningsutjämnaren för andra tillgänglighetsgrupper som deltar i de distribuerade tillgänglighetsgrupperna.

Om du begränsar åtkomsten med en Azure Network Security Group kontrollerar du att tillåt-reglerna inkluderar SQL Server-IP-adresserna för serverdelen och den flytande IP-adresserna för belastningsutjämnaden för AG-lyssnaren och klustrets kärn-IP-adress, om tillämpligt.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en SQL Server Always On-tillgänglighetsgrupp på virtuella Azure-datorer i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md)
