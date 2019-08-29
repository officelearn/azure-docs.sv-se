---
title: Skapa en lyssnare för en SQL Server tillgänglighets grupp på virtuella Azure-datorer | Microsoft Docs
description: Stegvisa instruktioner för att skapa en lyssnare för en Always on-tillgänglighets grupp för SQL Server på virtuella Azure-datorer
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
ms.openlocfilehash: c9c8379787619608421256120139f07c8dbd8d14
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102245"
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Konfigurera en belastningsutjämnare för tillgänglighets gruppen Always on i Azure
Den här artikeln beskriver hur du skapar en belastningsutjämnare för en SQL Server Always on-tillgänglighetsgrupper på virtuella Azure-datorer som körs med Azure Resource Manager. En tillgänglighets grupp kräver en belastningsutjämnare när SQL Server instanserna finns på virtuella Azure-datorer. Belastningsutjämnaren lagrar IP-adressen för tillgänglighets gruppens lyssnare. Om en tillgänglighets grupp sträcker sig över flera regioner behöver varje region en belastningsutjämnare.

För att slutföra den här uppgiften måste du ha en SQL Server tillgänglighets grupp distribuerad på virtuella Azure-datorer som kör med Resource Manager. Både SQL Server virtuella datorer måste tillhöra samma tillgänglighets uppsättning. Du kan använda [Microsoft-mallen](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) för att automatiskt skapa tillgänglighets gruppen i Resource Manager. Den här mallen skapar automatiskt en intern belastningsutjämnare åt dig. 

Om du vill kan du [Konfigurera en tillgänglighets grupp manuellt](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Den här artikeln kräver att dina tillgänglighets grupper redan har kon figurer ATS.  

Relaterade ämnen är:

* [Konfigurera Always on Availability groups i Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Genom att gå igenom den här artikeln skapar du och konfigurerar en belastningsutjämnare i Azure Portal. När processen har slutförts konfigurerar du klustret så att det använder IP-adressen från belastningsutjämnaren för tillgänglighets gruppens lyssnare.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Skapa och konfigurera belastningsutjämnaren i Azure Portal
I den här delen av uppgiften gör du följande:

1. Skapa belastningsutjämnaren i Azure Portal och konfigurera IP-adressen.
2. Konfigurera backend-poolen.
3. Skapa avsökningen. 
4. Ange regler för belastnings utjämning.

> [!NOTE]
> Om SQL Server instanserna finns i flera resurs grupper och regioner utför du varje steg två gånger, en gång i varje resurs grupp.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Steg 1: Skapa belastningsutjämnaren och konfigurera IP-adressen
Skapa först belastningsutjämnaren. 

1. Öppna den resurs grupp som innehåller de SQL Server virtuella datorerna i Azure Portal. 

2. I resurs gruppen klickar du på **Lägg till**.

3. Sök efter **belastningsutjämnare** och välj **Load Balancer**i Sök resultaten, som publiceras av **Microsoft**.

4. Klicka på **skapa**på bladet **Load Balancer** .

5. I dialog rutan **skapa belastnings utjämning** konfigurerar du belastningsutjämnaren enligt följande:

   | Inställning | Value |
   | --- | --- |
   | **Namn** |Ett text namn som representerar belastningsutjämnaren. Till exempel **sqlLB**. |
   | **Typ** |**Internt**: De flesta implementeringar använder en intern belastningsutjämnare som gör det möjligt för program i samma virtuella nätverk att ansluta till tillgänglighets gruppen.  </br> **Externt**: Tillåter att program ansluter till tillgänglighets gruppen via en offentlig Internet anslutning. |
   | **Virtuellt nätverk** |Välj det virtuella nätverk som SQL Servers instanserna finns i. |
   | **Undernät** |Välj det undernät som de SQL Server instanserna finns i. |
   | **Tilldelning av IP-adress** |**Oföränderlig** |
   | **Privat IP-adress** |Ange en tillgänglig IP-adress från under nätet. Använd den här IP-adressen när du skapar en lyssnare i klustret. I ett PowerShell-skript längre fram i den här artikeln använder du `$ILBIP` den här adressen för variabeln. |
   | **Prenumeration** |Om du har flera prenumerationer kan det här fältet visas. Välj den prenumeration som du vill koppla till den här resursen. Det är normalt samma prenumeration som alla resurser för tillgänglighets gruppen. |
   | **Resursgrupp** |Välj den resurs grupp som SQL Server instanserna finns i. |
   | **Location** |Välj den Azure-plats som SQL Server instanserna finns i. |

6. Klicka på **Skapa**. 

Azure skapar belastningsutjämnaren. Belastningsutjämnaren tillhör ett särskilt nätverk, undernät, resurs grupp och plats. När Azure har slutfört aktiviteten kontrollerar du inställningarna för belastningsutjämnare i Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Steg 2: Konfigurera backend-poolen
Azure anropar *backend-adresspoolen*för backend-adresspoolen. I det här fallet är backend-poolen adresserna för de två SQL Server instanserna i tillgänglighets gruppen. 

1. I resurs gruppen klickar du på den belastningsutjämnare som du har skapat. 

2. I **Inställningar**klickar du på **backend-pooler**.

3. På **backend-pooler**klickar du på **Lägg till** för att skapa en backend-adresspool. 

4. I **Lägg till backend-pool**, under **namn**, anger du ett namn för backend-poolen.

5. Under **virtuella datorer**klickar du på **Lägg till en virtuell dator**. 

6. Under **Välj virtuella datorer**klickar du på **Välj en tillgänglighets uppsättning**och anger sedan den tillgänglighets uppsättning som SQL Server virtuella datorerna tillhör.

7. När du har valt tillgänglighets uppsättningen klickar du på **Välj virtuella datorer**, väljer de två virtuella datorer som är värdar för de SQL Server instanserna i tillgänglighets gruppen och klickar sedan på **Välj**. 

8. Klicka på **OK** för att stänga bladen för **Välj virtuella datorer**och **Lägg till en backend-pool**. 

Azure uppdaterar inställningarna för backend-adresspoolen. Nu har din tillgänglighets uppsättning en pool med två SQL Server instanser.

### <a name="step-3-create-a-probe"></a>Steg 3: Skapa en avsökning
Avsökningen definierar hur Azure verifierar vilken av de SQL Server instanser som för närvarande äger tillgänglighets gruppens lyssnare. Azure avsöker tjänsten baserat på IP-adressen på en port som du anger när du skapar avsökningen.

1. Klicka på **hälso**avsökningar på bladet **Inställningar** för belastnings utjämning. 

2. På bladet **hälso** avsökningar klickar du på **Lägg till**.

3. Konfigurera avsökningen på bladet **Lägg till sökning** . Använd följande värden för att konfigurera avsökningen:

   | Inställning | Value |
   | --- | --- |
   | **Namn** |Ett text namn som representerar avsökningen. Till exempel **SQLAlwaysOnEndPointProbe**. |
   | **Protokoll** |**TCP** |
   | **Port** |Du kan använda valfri tillgänglig port. Till exempel *59999*. |
   | **Intervall** |*5* |
   | **Tröskelvärde för ej felfri** |*2* |

4.  Klicka på **OK**. 

> [!NOTE]
> Kontrol lera att den port du anger är öppen i brand väggen för båda SQL Server instanser. Båda instanserna kräver en regel för inkommande trafik för TCP-porten som du använder. Mer information finns i [Lägg till eller redigera brand Väggs regel](https://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure skapar avsökningen och använder den för att testa vilken SQL Server instans som har lyssnaren för tillgänglighets gruppen.

### <a name="step-4-set-the-load-balancing-rules"></a>Steg 4: Ange regler för belastnings utjämning
Reglerna för belastnings utjämning anger hur belastningsutjämnaren dirigerar trafik till SQL Server instanser. För den här belastningsutjämnaren aktiverar du direkt Server retur eftersom endast en av de två SQL Server instanserna äger tillgänglighets gruppens lyssnar resurs i taget.

1. Klicka på **belastnings Utjämnings regler**på bladet **Inställningar** för belastningsutjämnare. 

2. Klicka på **Lägg till**på bladet **belastnings Utjämnings regler** .

3. Konfigurera belastnings Utjämnings regeln på bladet **Lägg till belastnings Utjämnings regler** . Använd följande inställningar: 

   | Inställning | Value |
   | --- | --- |
   | **Namn** |Ett text namn som representerar belastnings Utjämnings reglerna. Till exempel **SQLAlwaysOnEndPointListener**. |
   | **Protokoll** |**TCP** |
   | **Port** |*1433* |
   | **Backend-port** |*1433*. Värdet ignoreras eftersom den här regeln använder **flytande IP (direkt Server retur)** . |
   | **Provtagning** |Använd namnet på avsökningen som du skapade för den här belastningsutjämnaren. |
   | **Beständig session** |**Alternativet** |
   | **Tids gräns för inaktivitet (minuter)** |*4* |
   | **Flytande IP (direkt Server retur)** |**Aktiverad** |

   > [!NOTE]
   > Du kan behöva bläddra nedåt i bladet för att visa alla inställningar.
   > 

4. Klicka på **OK**. 
5. Azure konfigurerar belastnings Utjämnings regeln. Nu konfigureras belastningsutjämnaren för att dirigera trafik till den SQL Server-instans som är värd för lyssnaren för tillgänglighets gruppen. 

I det här läget har resurs gruppen en belastningsutjämnare som ansluter till båda SQL Server datorer. Belastningsutjämnaren innehåller också en IP-adress för SQL Server Always on-tillgänglighetsgrupper, så att antingen datorn kan svara på begär Anden för tillgänglighets grupper.

> [!NOTE]
> Om SQL Server instanserna finns i två olika regioner upprepar du stegen i den andra regionen. Varje region kräver en belastningsutjämnare. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurera klustret till att använda belastnings utjämningens IP-adress
Nästa steg är att konfigurera lyssnaren på klustret och ta med lyssnaren online. Gör följande: 

1. Skapa tillgänglighets gruppens lyssnare i redundansklustret. 

2. Ta med lyssnaren online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Steg 5: Skapa tillgänglighets gruppens lyssnare i redundansklustret
I det här steget skapar du tillgänglighets gruppens lyssnare manuellt i Klusterhanteraren för växling vid fel och SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Verifiera konfigurationen av lyssnaren

Om kluster resurserna och beroendena är korrekt konfigurerade bör du kunna visa lyssnaren i SQL Server Management Studio. Gör så här för att ange lyssnings porten:

1. Starta SQL Server Management Studio och Anslut sedan till den primära repliken.

2. Gå till tillgänglighets**grupps lyssnare**för tillgänglighets**grupper** > med **hög tillgänglighet** > för AlwaysOn.  
    Du bör nu se det lyssnar namn som du skapade i Klusterhanteraren för växling vid fel. 

3. Högerklicka på namnet på lyssnaren och klicka sedan på **Egenskaper**.

4. I rutan **port** anger du Port numret för tillgänglighets gruppens lyssnare genom att använda $EndpointPort som du använde tidigare (1433 var standard) och klickar sedan på **OK**.

Nu har du en tillgänglighets grupp i Azure virtuella datorer som körs i Resource Manager-läge. 

## <a name="test-the-connection-to-the-listener"></a>Testa anslutningen till lyssnaren
Testa anslutningen genom att göra följande:

1. RDP till en SQL Server-instans som finns i samma virtuella nätverk, men som inte äger repliken. Den här servern kan vara den andra SQL Server-instansen i klustret.

2. Använd **SQLCMD** -verktyget för att testa anslutningen. Följande skript upprättar till exempel en **SQLCMD** -anslutning till den primära repliken via lyssnaren med Windows-autentisering:
   
        sqlcmd -S <listenerName> -E

SQLCMD-anslutningen ansluts automatiskt till den SQL Server-instans som är värd för den primära repliken. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Skapa en IP-adress för en extra tillgänglighets grupp

Varje tillgänglighets grupp använder en separat lyssnare. Varje lyssnare har sin egen IP-adress. Använd samma belastningsutjämnare för att lagra IP-adressen för ytterligare lyssnare. När du har skapat en tillgänglighets grupp lägger du till IP-adressen i belastningsutjämnaren och konfigurerar sedan lyssnaren.

Om du vill lägga till en IP-adress till en belastningsutjämnare med Azure Portal gör du följande:

1. Öppna resurs gruppen som innehåller belastningsutjämnaren i Azure Portal och klicka sedan på belastningsutjämnaren. 

2. Under **Inställningar**klickar du på **klient delens IP-pool**och sedan på **Lägg till**. 

3. Under **Lägg till IP-adress för klient**del tilldelar du ett namn för klient delen. 

4. Kontrol lera att det **virtuella nätverket** och **under nätet** är desamma som SQL Server-instanserna.

5. Ange IP-adressen för lyssnaren. 
   
   >[!TIP]
   >Du kan ange IP-adressen som statisk och ange en adress som inte används i under nätet. Alternativt kan du ange IP-adressen till dynamisk och spara den nya frontend-IP-poolen. När du gör det tilldelar Azure Portal automatiskt en tillgänglig IP-adress till poolen. Sedan kan du öppna IP-adresspoolen på klient sidan och ändra tilldelningen till statisk. 

6. Spara IP-adressen för lyssnaren. 

7. Lägg till en hälso avsökning med hjälp av följande inställningar:

   |Inställning |Value
   |:-----|:----
   |**Namn** |Ett namn som identifierar avsökningen.
   |**Protokoll** |TCP
   |**Port** |En oanvänd TCP-port som måste vara tillgänglig på alla virtuella datorer. Det kan inte användas i något annat syfte. Två lyssnare kan inte använda samma avsöknings port. 
   |**Intervall** |Tiden mellan avsöknings försök. Använd standardvärdet (5).
   |**Tröskelvärde för ej felfri** |Det antal tröskelvärden i följd som ska gå innan en virtuell dator betraktas som ohälsosam.

8. Spara avsökningen genom att klicka på **OK** . 

9. Skapa en belastnings Utjämnings regel. Klicka på **belastnings Utjämnings regler**och sedan på **Lägg till**.

10. Konfigurera den nya belastnings Utjämnings regeln med följande inställningar:

    |Inställning |Value
    |:-----|:----
    |**Namn** |Ett namn som identifierar belastnings Utjämnings regeln. 
    |**IP-adress för klient del** |Välj den IP-adress som du skapade. 
    |**Protokoll** |TCP
    |**Port** |Använd porten som SQL Server instanser använder. En standard instans använder port 1433, om du inte har ändrat den. 
    |**Backend-port** |Använd samma värde som **port**.
    |**Backend-pool** |Poolen som innehåller de virtuella datorerna med SQL Server instanser. 
    |**Hälso avsökning** |Välj den avsökning som du har skapat.
    |**Beständig session** |Inga
    |**Tids gräns för inaktivitet (minuter)** |Standard (4)
    |**Flytande IP (direkt Server retur)** | Aktiverad

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Konfigurera tillgänglighets gruppen så att den använder den nya IP-adressen

Slutför konfigurationen av klustret genom att upprepa de steg som du följde när du gjorde den första tillgänglighets gruppen. Det vill säga konfigurera [klustret så att det använder den nya IP-adressen](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

När du har lagt till en IP-adress för lyssnaren konfigurerar du den ytterligare tillgänglighets gruppen genom att göra följande: 

1. Kontrol lera att avsöknings porten för den nya IP-adressen är öppen på båda SQL Server virtuella datorerna. 

2. [Lägg till klient åtkomst punkten i kluster hanteraren](#addcap).

3. [Konfigurera IP-resursen för tillgänglighets gruppen](#congroup).

   >[!IMPORTANT]
   >När du skapar IP-adressen använder du den IP-adress som du lade till i belastningsutjämnaren.  

4. [Gör SQL Server tillgänglighets grupps resurs beroende av klient åtkomst punkten](#dependencyGroup).

5. [Gör klient åtkomst punkt resursen beroende av IP-adressen](#listname).
 
6. [Ange kluster parametrarna i PowerShell](#setparam).

När du har konfigurerat tillgänglighets gruppen till att använda den nya IP-adressen konfigurerar du anslutningen till lyssnaren. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Lägg till belastnings Utjämnings regel för distribuerad tillgänglighets grupp

Om en tillgänglighets grupp deltar i en distribuerad tillgänglighets grupp behöver belastningsutjämnaren en ytterligare regel. Den här regeln lagrar den port som används av den distribuerade tillgänglighets gruppens lyssnare.

>[!IMPORTANT]
>Det här steget gäller bara om tillgänglighets gruppen ingår i en [distribuerad tillgänglighets grupp](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. På varje server som ingår i den distribuerade tillgänglighets gruppen skapar du en regel för inkommande trafik på den distribuerade tillgänglighets gruppens lyssnare TCP-port. I många exempel använder dokumentationen 5022. 

1. Klicka på belastningsutjämnaren i Azure Portal och klicka sedan på **belastnings Utjämnings regler**. Klicka sedan på **+ Lägg till**. 

1. Skapa belastnings Utjämnings regeln med följande inställningar:

   |Inställning |Value
   |:-----|:----
   |**Namn** |Ett namn som identifierar belastnings Utjämnings regeln för den distribuerade tillgänglighets gruppen. 
   |**IP-adress för klient del** |Använd samma IP-adress för klient delen som tillgänglighets gruppen.
   |**Protokoll** |TCP
   |**Port** |5022 – porten för [slut punkten](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups)för den distribuerade tillgänglighets gruppen.</br> Kan vara vilken tillgänglig port som helst.  
   |**Backend-port** | 5022 – Använd samma värde som **port**.
   |**Backend-pool** |Poolen som innehåller de virtuella datorerna med SQL Server instanser. 
   |**Hälso avsökning** |Välj den avsökning som du har skapat.
   |**Beständig session** |Inga
   |**Tids gräns för inaktivitet (minuter)** |Standard (4)
   |**Flytande IP (direkt Server retur)** | Aktiverad

Upprepa de här stegen för belastningsutjämnaren för de andra tillgänglighets grupper som deltar i de distribuerade tillgänglighets grupperna.

Om du begränsar åtkomsten med en Azure-nätverks säkerhets grupp kontrollerar du att Tillåt-reglerna omfattar Server dels SQL Server VM IP-adresser och de flytande IP-adresserna för AG-lyssnaren och kluster Core-IP-adressen, om tillämpligt.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en SQL Server Always on-tillgänglighetsgrupper på virtuella Azure-datorer i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md)
