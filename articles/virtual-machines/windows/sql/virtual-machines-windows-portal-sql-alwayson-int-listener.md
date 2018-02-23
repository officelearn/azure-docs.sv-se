---
title: "Skapa en SQL Server tillgänglighetsgruppens lyssnare i virtuella Azure-datorer | Microsoft Docs"
description: "Stegvisa instruktioner för att skapa en lyssnare för en Always On-tillgänglighetsgrupp för SQL Server på virtuella Azure-datorer"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.openlocfilehash: 0399f9ef969098216e080140a67f81725b670115
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Konfigurera en belastningsutjämnare för en tillgänglighetsgrupp alltid på i Azure
Den här artikeln förklarar hur du skapar en belastningsutjämnare för en SQL Server Always On-tillgänglighetsgrupp i virtuella Azure-datorer som körs med Azure Resource Manager. En tillgänglighetsgrupp kräver en belastningsutjämnare när SQL Server-instanser finns på virtuella Azure-datorer. Belastningsutjämnaren lagrar IP-adressen för tillgänglighetsgruppens lyssnare. Om en tillgänglighetsgrupp sträcker sig över flera regioner, måste en belastningsutjämnare i varje region.

Du måste ha en SQL Server-tillgänglighetsgrupp som distribueras på virtuella Azure-datorer som körs med Resource Manager för att slutföra den här uppgiften. Både SQL Server-datorer måste tillhöra samma tillgänglighetsuppsättning. Du kan använda den [Microsoft mall](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) att automatiskt skapa tillgänglighetsgruppen i Resource Manager. Den här mallen skapas automatiskt en intern belastningsutjämnare. 

Om du vill kan du [manuellt konfigurera en tillgänglighetsgrupp](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Den här artikeln kräver att din Tillgänglighetsgrupper har redan konfigurerats.  

Närliggande ämnen innefattar:

* [Konfigurera alltid på Tillgänglighetsgrupper i Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Genom att gå via den här artikeln, skapa och konfigurera en belastningsutjämnare i Azure-portalen. När processen är klar kan konfigurera du klustret för att använda IP-adress från belastningsutjämnaren för tillgänglighetsgruppens lyssnare.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Skapa och konfigurera belastningsutjämnaren i Azure-portalen
I den här delen av aktiviteten, gör du följande:

1. Skapa belastningsutjämnaren i Azure-portalen och konfigurera IP-adressen.
2. Konfigurera backend-adresspoolen.
3. Skapa avsökningen. 
4. Ange regler för belastningsutjämning.

> [!NOTE]
> Om SQL Server-instanser i flera resursgrupper och regioner, utför varje steg två gånger, en gång i varje resursgrupp.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Steg 1: Skapa belastningsutjämnaren och konfigurera IP-adress
Först skapa belastningsutjämnaren. 

1. Öppna resursgruppen som innehåller de virtuella datorerna för SQL Server i Azure-portalen. 

2. I resursgrupp, klickar du på **Lägg till**.

3. Sök efter **belastningsutjämnare** och markera i sökresultaten **belastningsutjämnare**, som publiceras av **Microsoft**.

4. På den **belastningsutjämnaren** bladet, klickar du på **skapa**.

5. I den **skapa belastningsutjämnaren** dialogrutan Konfigurera belastningsutjämnaren på följande sätt:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett namn som representerar belastningsutjämnaren. Till exempel **sqlLB**. |
   | **Typ** |**Internt**: de flesta implementeringar av använder en intern belastningsutjämnare, vilket gör att program i samma virtuella nätverk att ansluta till tillgänglighetsgruppen.  </br> **Externa**: gör att program kan ansluta till tillgänglighetsgruppen via en offentlig Internetanslutning. |
   | **Virtuellt nätverk** |Välj det virtuella nätverket som SQL Server-intances finns i. |
   | **Undernät** |Välj det undernät som SQL Server-instanser finns i. |
   | IP-adresstilldelning |**Statisk** |
   | **Privat IP-adress** |Ange en tillgänglig IP-adress från undernätet. Använd följande IP-adress när du skapar en lyssnare på klustret. I ett PowerShell-skript, senare i den här artikeln använder den här adressen för den `$ILBIP` variabeln. |
   | **Prenumeration** |Om du har flera prenumerationer, visas det här fältet. Välj den prenumeration som du vill associera med den här resursen. Det är vanligtvis samma prenumeration som alla resurser för tillgänglighetsgruppen. |
   | **Resursgrupp** |Välj den resursgrupp som SQL Server-instanser finns i. |
   | **Plats** |Välj den Azure-plats som SQL Server-instanser. |

6. Klicka på **Skapa**. 

Azure skapar belastningsutjämnaren. Belastningsutjämnaren hör till ett visst nätverk, undernät, resursgrupp och plats. Kontrollera inställningarna för belastningsutjämnaren i Azure när uppgiften har slutförts i Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Steg 2: Konfigurera backend-adresspool
Azure anropar backend-adresspool *serverdelspool*. I det här fallet är poolen backend-adresserna för de två SQL Server-instanserna i tillgänglighetsgruppen. 

1. Klicka på belastningsutjämnaren som du skapat i din resursgrupp. 

2. På **inställningar**, klickar du på **serverdelspooler**.

3. På **serverdelspooler**, klickar du på **Lägg till** att skapa en backend-adresspool. 

4. På **lägga till serverdelspoolen**under **namn**, Skriv ett namn för backend-poolen.

5. Under **virtuella datorer**, klickar du på **lägga till en virtuell dator**. 

6. Under **Välj virtuella datorer**, klickar du på **Välj en tillgänglighetsuppsättning**, och sedan ange tillgänglighetsuppsättning att SQL Server-datorer som tillhör.

7. När du har valt tillgänglighetsuppsättningen, klickar du på **Välj de virtuella datorerna**, Välj två virtuella datorer som är värdar för SQL Server-instanser i tillgänglighetsgruppen och klicka sedan på **Välj**. 

8. Klicka på **OK** att stänga bladen för **Välj virtuella datorer**, och **lägga till serverdelspoolen**. 

Azure uppdaterar inställningarna för backend-adresspool. Din tillgänglighetsuppsättning har nu en pool med två SQL Server-instanser.

### <a name="step-3-create-a-probe"></a>Steg 3: Skapa en avsökning
Avsökningen definierar hur Azure verifierar som SQL Server-instanser för närvarande äger tillgänglighetsgruppens lyssnare. Azure-avsökningar tjänsten baserat på IP-adress på en port som du anger när du skapar avsökningen.

1. I belastningsutjämnaren **inställningar** bladet, klickar du på **hälsoavsökning**. 

2. På den **hälsoavsökning** bladet, klickar du på **Lägg till**.

3. Konfigurera avsökningen på den **Lägg till avsökning** bladet. Använd följande värden för att konfigurera avsökningen:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett namn som representerar avsökningen. Till exempel **SQLAlwaysOnEndPointProbe**. |
   | **Protokoll** |**TCP** |
   | **Port** |Du kan använda alla tillgängliga portar. Till exempel *59999*. |
   | **Intervall** |*5* |
   | **Tröskelvärde för ohälsosamt värde** |*2* |

4.  Klicka på **OK**. 

> [!NOTE]
> Kontrollera att den port som du anger är öppen i brandväggen för både SQL Server-instanser. Båda instanser kräver en inkommande regel för TCP-porten som du använder. Mer information finns i [Lägg till eller redigera brandväggsregel](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure skapar avsökningen och sedan används för att testa vilka SQL Server-instansen har lyssnaren för tillgänglighetsgruppen.

### <a name="step-4-set-the-load-balancing-rules"></a>Steg 4: Ange regler för belastningsutjämning
Belastningsutjämningsregler konfigurera hur belastningsutjämnaren dirigerar trafik till SQL Server-instanser. För den här belastningsutjämnaren aktivera direkt serverreturnering eftersom endast en av två SQL Server-instanser äger lyssnaren tillgänglighetsgruppsresursen i taget.

1. I belastningsutjämnaren **inställningar** bladet, klickar du på **belastningsutjämningsregler**. 

2. På den **belastningsutjämningsregler** bladet, klickar du på **Lägg till**.

3. På den **Lägg till regler för belastningsutjämning** bladet konfigurera regel för belastningsutjämning. Använd följande inställningar: 

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett namn som representerar regler för belastningsutjämning. Till exempel **SQLAlwaysOnEndPointListener**. |
   | **Protokoll** |**TCP** |
   | **Port** |*1433* |
   | **Backend-Port** |*1433*. Det här värdet ignoreras eftersom den här regeln använder **flytande IP (direkt serverreturnering)**. |
   | **Probe** |Använd namnet på den avsökningen som du skapade för denna belastningsutjämning. |
   | **Persistence för session** |Ingen |
   | Tidsgränsen för inaktivitet (minuter) |*4* |
   | **Flytande IP (direkt serverreturnering)** |aktiverad |

   > [!NOTE]
   > Du kanske måste rulla nedåt bladet för att visa alla inställningar.
   > 

4. Klicka på **OK**. 
5. Azure konfigurerar regel för belastningsutjämning. Belastningsutjämnaren har nu konfigurerats för att dirigera trafik till SQL Server-instansen som är värd för lyssnaren för tillgänglighetsgruppen. 

Resursgruppen har nu en belastningsutjämnare som ansluter till både SQL Server-datorer. Belastningsutjämnaren innehåller också en IP-adress för SQL Server alltid på tillgänglighetsgruppens lyssnare, så att antingen datorn kan svara på begäranden om tillgänglighetsgrupperna.

> [!NOTE]
> Om SQL Server-instanser i två olika områden, upprepa stegen i regionen som helst. Varje region kräver en belastningsutjämnare. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurera klustret för att använda IP-adressen för belastningsutjämnaren
Nästa steg är att konfigurera lyssnaren på klustret och ta lyssnaren online. Gör följande: 

1. Skapa tillgänglighetsgruppslyssnaren för redundanskluster. 

2. Sätta lyssnaren online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Steg 5: Skapa tillgänglighetsgruppens lyssnare för redundanskluster
I det här steget kan skapa du manuellt tillgänglighetsgruppens lyssnare i hanteraren för redundanskluster och SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Kontrollera konfigurationen för lyssnare

Om klusterresurser och beroenden är korrekt konfigurerade, bör du kunna visa lyssnaren i SQL Server Management Studio. Ange porten för lyssnare genom att göra följande:

1. Starta SQL Server Management Studio och Anslut till den primära repliken.

2. Gå till **AlwaysOn hög tillgänglighet** > **Tillgänglighetsgrupper** > **tillgänglighetsgruppens lyssnare**.  
    Du bör nu se lyssnare namnet som du skapade i hanteraren för redundanskluster. 

3. Högerklicka på grupplyssnarens namn och klicka sedan på **egenskaper**.

4. I den **Port** anger du portnumret för tillgänglighetsgruppens lyssnare med hjälp av $EndpointPort du använde tidigare (1433 var standard), och klicka sedan på **OK**.

Nu har du en tillgänglighetsgrupp i Azure virtuella datorer som körs i läget Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Testa anslutningen till lyssnaren
Testa anslutningen genom att göra följande:

1. RDP till en SQL Server-instans som är i samma virtuella nätverk, men inte äger repliken. Den här servern kan vara andra SQL Server-instansen i klustret.

2. Använd **sqlcmd** verktyg för att testa anslutningen. Till exempel följande skript upprättar en **sqlcmd** anslutning till den primära repliken via lyssnaren med Windows-autentisering:
   
        sqlcmd -S <listenerName> -E

SQLCMD-anslutningen ansluter automatiskt till SQL Server-instansen som är värd för den primära repliken. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Skapa en IP-adress för en ytterligare tillgänglighetsgrupp

Varje tillgänglighetsgruppen använder en separat lyssnare. Varje lyssnare har sin egen IP-adress. Använda samma belastningsutjämnare ska innehålla IP-adressen för ytterligare lyssnare. När du har skapat en tillgänglighetsgrupp lägga till IP-adress till belastningsutjämnaren och sedan konfigurera lyssnaren.

Om du vill lägga till en IP-adress till en belastningsutjämnare med Azure-portalen, gör du följande:

1. Öppna resursgruppen som innehåller belastningsutjämnaren i Azure-portalen och klicka sedan på belastningsutjämnaren. 

2. Under **inställningar**, klickar du på **Frontend IP-pool**, och klicka sedan på **Lägg till**. 

3. Under **lägga till IP-adress för klientdel**, tilldela ett namn för klientdelen. 

4. Kontrollera att den **för virtuella nätverk** och **undernät** är samma som SQL Server-instanser.

5. Ange IP-adressen för lyssnaren. 
   
   >[!TIP]
   >Du kan ange IP-adress till statisk och ange en adress som inte används för närvarande i undernätet. Du kan också ange IP-adressen till dynamisk och spara den nya frontend IP-adresspoolen. När du gör det Azure portal tilldelar automatiskt en tillgänglig IP-adress till poolen. Du kan sedan öppna den frontend IP-adresspoolen och ändra tilldelningen till statisk. 

6. Spara IP-adressen för lyssnaren. 

7. Lägg till en hälsoavsökningen med följande inställningar:

   |Inställning |Värde
   |:-----|:----
   |**Namn** |Ett namn som identifierar avsökningen.
   |**Protokoll** |TCP
   |**Port** |En inte används TCP-port som måste finnas tillgängligt på alla virtuella datorer. Den kan inte användas för andra ändamål. Inga två lyssnare kan använda samma avsökningsport. 
   |**Intervall** |Tidslängd mellan avsökningsförsök. Använd standardvärdet (5).
   |**Tröskelvärde för ohälsosamt värde** |Antal på varandra följande tröskelvärden som får misslyckas innan en virtuell dator anses vara felaktig.

8. Klicka på **OK** att spara avsökningen. 

9. Skapa en regel för belastningsutjämning. Klicka på **belastningsutjämningsregler**, och klicka sedan på **Lägg till**.

10. Konfigurera nya belastningsutjämningsregeln med hjälp av följande inställningar:

   |Inställning |Värde
   |:-----|:----
   |**Namn** |Ett namn som identifierar regel för belastningsutjämning. 
   |**Frontend-IP-adress** |Välj IP-adress som du skapade. 
   |**Protokoll** |TCP
   |**Port** |Använd den port som använder SQL Server-instanser. En standardinstans använder port 1433, om du har ändrat den. 
   |**Backend-port** |Använd samma värde som **Port**.
   |**Serverdelspool** |Den pool som innehåller de virtuella datorerna med SQL Server-instanser. 
   |Hälsoavsökningen |Välj avsökningen som du skapade.
   |**Persistence för session** |Ingen
   |Tidsgränsen för inaktivitet (minuter) |Standard (4)
   |**Flytande IP (direkt serverreturnering)** | Enabled

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Konfigurera tillgänglighetsgruppen för att använda den nya IP-adressen

Upprepa steg som du har följt när du har gjort den första tillgänglighetsgruppen för att konfigurera klustret. Det vill säga konfigurerar den [klustret ska använda den nya IP-adressen](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

När du har lagt till en IP-adress för lyssnaren kan du konfigurera ytterligare tillgänglighetsgruppen genom att göra följande: 

1. Kontrollera att avsökningsport för den nya IP-adressen är öppen på både SQL Server-datorer. 

2. [I Klusterhanteraren, lägga till klientåtkomstpunkten](#addcap).

3. [Konfigurera IP-resurs för tillgänglighetsgruppen](#congroup).

   >[!IMPORTANT]
   >När du skapar IP-adressen måste använda IP-adressen som du lagt till belastningsutjämnaren.  

4. [Gör tillgänglighetsgruppresursen för SQL Server som är beroende av klientåtkomstpunkten](#dependencyGroup).

5. [Kontrollera klienten åtkomst punkt resursen beroende av IP-adressen](#listname).
 
6. [Ange Klusterparametrar i PowerShell](#setparam).

När du har konfigurerat tillgänglighetsgruppen för att använda den nya IP-adressen kan du konfigurera anslutningen till lyssnaren. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Lägg till belastningsutjämningsregel för distribuerad tillgänglighetsgrupp

Om en tillgänglighetsgrupp deltar i en distribuerad tillgänglighetsgrupp, måste belastningsutjämnaren en ytterligare regel. Den här regeln lagrar den port som används av distribuerade tillgänglighetsgruppens lyssnare.

>[!IMPORTANT]
>Det här steget gäller endast om tillgänglighetsgruppen deltar i en [distribuerad tillgänglighetsgrupp](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Skapa en regel för inkommande trafik på distribuerade tillgänglighetsgruppens lyssnare TCP-port på varje server som ingår i den distribuerade tillgänglighetsgruppen. I många exemplen använder dokumentationen 5022. 

1. I Azure-portalen klickar du på belastningsutjämnaren och klicka på **belastningsutjämningsregler**, och klicka sedan på **+ Lägg till**. 

1. Skapa regel för belastningsutjämning med följande inställningar:

   |Inställning |Värde
   |:-----|:----
   |**Namn** |Ett namn som identifierar belastningsutjämningsregeln för den distribuerade tillgänglighetsgruppen. 
   |**Frontend-IP-adress** |Använd samma IP-adress för klientdel som tillgänglighetsgruppen.
   |**Protokoll** |TCP
   |**Port** |5022 - porten för den [distribuerade slutpunkten för tillgänglighetsgruppslyssnaren](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Kan vara en ledig port.  
   |**Backend-port** | 5022 - Använd samma värde som **Port**.
   |**Serverdelspool** |Den pool som innehåller de virtuella datorerna med SQL Server-instanser. 
   |Hälsoavsökningen |Välj avsökningen som du skapade.
   |**Persistence för session** |Ingen
   |Tidsgränsen för inaktivitet (minuter) |Standard (4)
   |**Flytande IP (direkt serverreturnering)** | Enabled

Upprepa dessa steg för belastningsutjämnaren på andra Tillgänglighetsgrupper som deltar i distribuerade Tillgänglighetsgrupper.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en SQL Server Always On availability group på virtuella Azure-datorer i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md)
