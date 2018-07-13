---
title: Skapa en tillgänglighetsgruppslyssnare för SQL Server i Azure virtual machines | Microsoft Docs
description: Stegvisa instruktioner för att skapa en lyssnare för en Always On-tillgänglighetsgrupp för SQL Server på Azure virtuella datorer
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
ms.openlocfilehash: 7ef26dc5fa7676ca590d56978c735bf4a195440b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698058"
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Konfigurera en belastningsutjämnare för en Always On-tillgänglighetsgrupp i Azure
Den här artikeln förklarar hur du skapar en belastningsutjämnare för en SQL Server Always On-tillgänglighetsgrupp i Azure virtuella datorer som körs med Azure Resource Manager. En tillgänglighetsgrupp kräver en belastningsutjämnare när SQL Server-instanserna är på Azure virtual machines. Belastningsutjämnaren lagrar IP-adressen för tillgänglighetsgruppens lyssnare. Om en tillgänglighetsgrupp sträcker sig över flera regioner, måste en belastningsutjämnare i varje region.

Du måste ha en SQL Server-tillgänglighetsgrupp som har distribuerats på Azure virtuella datorer som körs med Resource Manager för att slutföra den här uppgiften. Både SQL Server-datorer måste tillhöra samma tillgänglighetsuppsättning. Du kan använda den [Microsoft-mallen för](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) att automatiskt skapa tillgänglighetsgruppen i Resource Manager. Den här mallen skapar en intern belastningsutjämnare automatiskt åt dig. 

Om du föredrar, kan du [manuellt konfigurera en tillgänglighetsgrupp](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Den här artikeln kräver att din Tillgänglighetsgrupper är redan konfigurerade.  

Relaterade ämnen innefattar:

* [Konfigurera Always On-Tillgänglighetsgrupper i Azure virtuella datorer (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Genom att gå igenom den här artikeln, skapa och konfigurera en belastningsutjämnare i Azure-portalen. När processen är klar kan konfigurera du klustret för att använda IP-adressen från belastningsutjämnaren för tillgänglighetsgruppens lyssnare.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Skapa och konfigurera belastningsutjämnaren i Azure portal
I den här delen av aktiviteten, gör du följande:

1. Skapa belastningsutjämnaren i Azure-portalen och konfigurera IP-adressen.
2. Konfigurera backend-adresspoolen.
3. Skapa avsökningen. 
4. Ange regler för belastningsutjämning.

> [!NOTE]
> Om SQL Server-instanser finns i flera resursgrupper och regioner, utför varje steg två gånger, en gång i varje resursgrupp.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Steg 1: Skapa belastningsutjämnaren och konfigurera IP-adressen
Börja med att skapa belastningsutjämnaren. 

1. Öppna resursgruppen som innehåller SQL Server-datorer i Azure-portalen. 

2. I resursgruppen, klickar du på **Lägg till**.

3. Sök efter **belastningsutjämnare** och markera i sökresultaten **Load Balancer**, som publiceras av **Microsoft**.

4. På den **belastningsutjämnaren** bladet klickar du på **skapa**.

5. I den **skapa belastningsutjämnare** dialogrutan Konfigurera belastningsutjämnaren på följande sätt:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett namn som representerar belastningsutjämnaren. Till exempel **sqlLB**. |
   | **Typ** |**Interna**: de flesta implementeringar av använder en intern belastningsutjämnare, vilket gör att program i samma virtuella nätverk att ansluta till tillgänglighetsgruppen.  </br> **Externa**: gör att program kan ansluta till tillgänglighetsgrupp via en offentlig Internetanslutning. |
   | **Virtuellt nätverk** |Välj det virtuella nätverket som SQL Server-intances finns i. |
   | **Undernät** |Välj det undernät som SQL Server-instanser är i. |
   | **IP-adresstilldelning** |**Statisk** |
   | **Privat IP-adress** |Ange en tillgänglig IP-adress från undernätet. Använd den här IP-adressen när du skapar en lyssnare på klustret. I ett PowerShell-skript, senare i den här artikeln använder du den här adressen för den `$ILBIP` variabeln. |
   | **Prenumeration** |Om du har flera prenumerationer, visas det här fältet. Välj den prenumeration som du vill associera med den här resursen. Det är vanligtvis samma prenumeration som alla resurser för tillgänglighetsgruppen. |
   | **Resursgrupp** |Välj den resursgrupp som SQL Server-instanserna. |
   | **Plats** |Välj den Azure-plats som SQL Server-instanserna. |

6. Klicka på **Skapa**. 

Azure skapar belastningsutjämnaren. Belastningsutjämnaren tillhör ett visst nätverk, undernät, resursgrupp och plats. När uppgiften har slutförts i Azure kontrollerar du inställningarna för belastningsutjämnare i Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Steg 2: Konfigurera backend poolen
Azure anropar backend-adresspoolen *serverdelspool*. I det här fallet är backend poolen adresserna till de två SQL Server-instanserna i tillgänglighetsgruppen. 

1. Klicka på belastningsutjämnaren som du har skapat i din resursgrupp. 

2. På **inställningar**, klickar du på **serverdelspooler**.

3. På **serverdelspooler**, klickar du på **Lägg till** att skapa en backend-adresspool. 

4. På **Lägg till serverdelspool**under **namn**, Skriv ett namn för backend poolen.

5. Under **virtuella datorer**, klickar du på **lägga till en virtuell dator**. 

6. Under **Välj virtuella datorer**, klickar du på **Välj en tillgänglighetsuppsättning**, och sedan ange tillgänglighetsuppsättningen att SQL Server-datorer som tillhör.

7. När du har valt tillgänglighetsuppsättningen, klickar du på **väljer de virtuella datorerna**, väljer de två virtuella datorerna som är värdar för SQL Server-instanser i tillgänglighetsgruppen och klicka sedan på **Välj**. 

8. Klicka på **OK** att Stäng bladen för **Välj virtuella datorer**, och **Lägg till serverdelspool**. 

Azure uppdaterar inställningarna för backend-adresspoolen. Tillgänglighetsuppsättningen har nu en pool med två SQL Server-instanser.

### <a name="step-3-create-a-probe"></a>Steg 3: Skapa en avsökning
Avsökningen definierar hur Azure verifierar som SQL Server-instanserna för närvarande äger tillgänglighetsgruppens lyssnare. Azure-avsökningar tjänsten baserat på IP-adress på en port som du anger när du skapar avsökningen.

1. I belastningsutjämnaren **inställningar** bladet klickar du på **hälsoavsökningar**. 

2. På den **hälsoavsökningar** bladet klickar du på **Lägg till**.

3. Konfigurera avsökningen på den **Lägg till avsökning** bladet. Använd följande värden för att konfigurera avsökningen:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett namn som representerar avsökningen. Till exempel **SQLAlwaysOnEndPointProbe**. |
   | **Protokoll** |**TCP** |
   | **Port** |Du kan använda alla tillgängliga portar. Till exempel *59999*. |
   | **Intervall** |*5* |
   | **Tröskelvärde för ej felfri** |*2* |

4.  Klicka på **OK**. 

> [!NOTE]
> Se till att den port som du anger är öppen i brandväggen för både SQL Server-instanser. Båda instanserna kräver en regel för inkommande trafik för TCP-porten som du använder. Mer information finns i [Lägg till eller redigera brandväggsregel](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure skapar avsökningen och sedan använder den för att testa vilken SQL Server-instans har lyssnare för tillgänglighetsgruppen.

### <a name="step-4-set-the-load-balancing-rules"></a>Steg 4: Ange regler för belastningsutjämning
Belastningsutjämningsregler konfigurera hur belastningsutjämnaren dirigerar trafik till SQL Server-instanserna. För den här belastningsutjämnaren aktivera direkt serverreturnering eftersom endast en av de två SQL Server-instanserna äger lyssnaren tillgänglighetsgruppresursen i taget.

1. I belastningsutjämnaren **inställningar** bladet klickar du på **belastningsutjämningsregler**. 

2. På den **belastningsutjämningsregler** bladet klickar du på **Lägg till**.

3. På den **Lägg till regler för belastningsutjämning** bladet konfigurera regel för belastningsutjämning. Använd följande inställningar: 

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett namn som representerar regler för belastningsutjämning. Till exempel **SQLAlwaysOnEndPointListener**. |
   | **Protokoll** |**TCP** |
   | **Port** |*1433* |
   | **Serverdelsport** |*1433*. Det här värdet ignoreras eftersom den här regeln använder **flytande IP (direkt serverreturnering)**. |
   | **Avsökningen** |Använd namnet på avsökningen som du skapade för den här belastningsutjämnaren. |
   | **Sessionspermanens** |**Ingen** |
   | **Timeout för inaktivitet (minuter)** |*4* |
   | **Flytande IP (direkt serverreturnering)** |**Aktiverad** |

   > [!NOTE]
   > Du kan behöva rulla ned i bladet för att visa alla inställningar.
   > 

4. Klicka på **OK**. 
5. Konfigurerar belastningsutjämningsregeln för Azure. Belastningsutjämnaren har nu konfigurerats för att dirigera trafik till SQL Server-instansen som är värd för lyssnare för tillgänglighetsgruppen. 

Resursgruppen har nu en belastningsutjämnare som ansluter till både SQL Server-datorer. Belastningsutjämnaren innehåller också en IP-adress för SQL Server Always On tillgänglighetsgruppens lyssnare, så att endera datorn kan svara på begäranden för tillgänglighetsgrupperna.

> [!NOTE]
> Om din SQL Server-instanser finns i två olika områden, upprepa stegen i den andra regionen. Varje region kräver en belastningsutjämnare. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurera klustret för load balancer IP-adress
Nästa steg är att konfigurera lyssnaren på klustret och ta med lyssnaren online. Gör följande: 

1. Skapa tillgänglighetsgruppslyssnaren för redundanskluster. 

2. Ta med lyssnaren online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Steg 5: Skapa tillgänglighetsgruppslyssnaren för redundanskluster
I det här steget skapar du manuellt tillgänglighetsgruppens lyssnare i hanteraren för redundanskluster och SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Kontrollera konfigurationen för lyssnare

Om klusterresurser och beroenden är korrekt konfigurerade, bör du kunna visa lyssnaren i SQL Server Management Studio. Ange porten för lyssnare genom att göra följande:

1. Starta SQL Server Management Studio och Anslut till den primära repliken.

2. Gå till **AlwaysOn hög tillgänglighet** > **Tillgänglighetsgrupper** > **tillgänglighetsgruppens lyssnare**.  
    Du bör nu se namnet på lyssnare som du skapade i hanteraren för redundanskluster. 

3. Högerklicka på namnet på lyssnare och klicka sedan på **egenskaper**.

4. I den **Port** skriver du portnumret för tillgänglighetsgruppens lyssnare med hjälp av $EndpointPort du använde tidigare (1433 var standard), och klicka sedan på **OK**.

Nu har du en tillgänglighetsgrupp i Azure virtuella datorer i Resource Manager-läge. 

## <a name="test-the-connection-to-the-listener"></a>Testa anslutningen till lyssnaren
Testa anslutningen genom att göra följande:

1. RDP till en SQL Server-instans som är i samma virtuella nätverk, men inte äger repliken. Den här servern kan vara andra SQL Server-instans i klustret.

2. Använd **sqlcmd** verktyg för att testa anslutningen. Till exempel följande skript upprättar en **sqlcmd** anslutning till den primära repliken via lyssnaren med Windows-autentisering:
   
        sqlcmd -S <listenerName> -E

SQLCMD-anslutning ansluter automatiskt till SQL Server-instansen som är värd för den primära repliken. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Skapa en IP-adress för en ytterligare tillgänglighetsgrupp

Varje tillgänglighetsgruppen använder en separat lyssnare. Varje lyssnare har sin egen IP-adress. Använd samma belastningsutjämnare för att lagra den IP-adressen för ytterligare lyssnare. När du skapar en tillgänglighetsgrupp kan lägga till IP-adress till belastningsutjämnaren och sedan konfigurera lyssnaren.

Lägg till en IP-adress till en belastningsutjämnare med Azure portal genom att göra följande:

1. Öppna resursgruppen som innehåller belastningsutjämnaren i Azure-portalen och klicka sedan på belastningsutjämnaren. 

2. Under **inställningar**, klickar du på **Frontend IP-pool**, och klicka sedan på **Lägg till**. 

3. Under **Lägg till klientdelens IP-adress**, tilldela ett namn för klientdelen. 

4. Kontrollera att den **virtuellt nätverk** och **undernät** är samma som SQL Server-instanserna.

5. Ange IP-adressen för lyssnaren. 
   
   >[!TIP]
   >Du kan ange IP-adress till statisk och ange en adress som inte används för närvarande i undernätet. Du kan också ange IP-adressen till dynamisk och spara den nya IP-adresspoolen på klientsidan. När du gör detta på Azure portal tilldelar automatiskt en tillgänglig IP-adress till poolen. Du kan sedan öppna IP-adresspoolen på klientsidan och ändra tilldelningen till statisk. 

6. Spara IP-adressen för lyssnaren. 

7. Lägg till en hälsoavsökning med följande inställningar:

   |Inställning |Värde
   |:-----|:----
   |**Namn** |Ett namn som identifierar avsökningen.
   |**Protokoll** |TCP
   |**Port** |En oanvänd TCP-port, som måste vara tillgängliga på alla virtuella datorer. Det kan inte användas i något annat syfte. Inga två lyssnare kan använda samma avsökningsporten. 
   |**Intervall** |Tidslängd mellan avsökningsförsök. Använd standard (5).
   |**Tröskelvärde för ej felfri** |Antal på varandra följande tröskelvärden som får misslyckas innan en virtuell dator betraktas som defekt.

8. Klicka på **OK** att spara avsökningen. 

9. Skapa en regel för belastningsutjämning. Klicka på **belastningsutjämningsregler**, och klicka sedan på **Lägg till**.

10. Konfigurera nya belastningsutjämningsregeln med hjälp av följande inställningar:

   |Inställning |Värde
   |:-----|:----
   |**Namn** |Ett namn som identifierar regel för belastningsutjämning. 
   |**Frontend-IP-adress** |Välj IP-adress som du skapade. 
   |**Protokoll** |TCP
   |**Port** |Använd den port som använder SQL Server-instanserna. En standardinstans använder port 1433, såvida inte du har ändrat. 
   |**Serverdelsport** |Använd samma värde som **Port**.
   |**Serverdelspool** |Den pool som innehåller virtuella datorer med SQL Server-instanserna. 
   |**Hälsoavsökning** |Välj avsökningen som du skapade.
   |**Sessionspermanens** |Ingen
   |**Timeout för inaktivitet (minuter)** |Standard (4)
   |**Flytande IP (direkt serverreturnering)** | Enabled

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Konfigurera tillgänglighetsgruppen för att använda den nya IP-adressen

Om du vill slutföra konfigurationen av klustret, Upprepa steg som du följde när du har gjort den första tillgänglighetsgruppen. Konfigurera det vill säga den [klustret ska använda den nya IP-adressen](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

När du har lagt till en IP-adressen för lyssnaren kan du konfigurera ytterligare availability-gruppen genom att göra följande: 

1. Kontrollera att avsökningsporten för den nya IP-adressen är öppen på både SQL Server-datorer. 

2. [I Klusterhanterare, lägga till klientåtkomstpunkten](#addcap).

3. [Konfigurera IP-resurs för tillgänglighetsgruppen](#congroup).

   >[!IMPORTANT]
   >När du skapar IP-adress, kan du använda IP-adressen som du lade till belastningsutjämnaren.  

4. [Kontrollera SQL Server-tillgänglighetsgruppresursen beroende på klientåtkomstpunkten](#dependencyGroup).

5. [Kontrollera klienten åtkomst punkt resursen beroende på IP-adressen](#listname).
 
6. [Ange Klusterparametrar i PowerShell](#setparam).

När du har konfigurerat tillgänglighetsgruppen om du vill använda den nya IP-adressen kan du konfigurera anslutningen till lyssnaren. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Lägg till belastningsutjämningsregel för distribuerad tillgänglighetsgrupp

Om en tillgänglighetsgrupp deltar i en distribuerad tillgänglighetsgrupp, måste belastningsutjämnaren en ytterligare regel. Den här regeln lagrar den port som används av distribuerade tillgänglighetsgruppens lyssnare.

>[!IMPORTANT]
>Det här steget gäller endast om tillgänglighetsgruppen deltar i en [distribuerad tillgänglighetsgrupp](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Skapa en regel för inkommande på distribuerade tillgänglighetsgruppslyssnaren TCP-port på varje server som ingår i den distribuerade tillgänglighetsgruppen. I många exemplen använder dokumentation 5022. 

1. I Azure-portalen klickar du på belastningsutjämnaren och klicka på **belastningsutjämningsregler**, och klicka sedan på **+ Lägg till**. 

1. Skapa regel för belastningsutjämning med följande inställningar:

   |Inställning |Värde
   |:-----|:----
   |**Namn** |Ett namn som identifierar belastningsutjämningsregeln för den distribuerade tillgänglighetsgruppen. 
   |**Frontend-IP-adress** |Använd samma IP-adress för klientdel som tillgänglighetsgruppen.
   |**Protokoll** |TCP
   |**Port** |5022 - porten för den [distribuerade slutpunkten för tillgänglighetsgruppslyssnaren](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Kan vara alla tillgängliga portar.  
   |**Serverdelsport** | 5022 – Använd samma värde som **Port**.
   |**Serverdelspool** |Den pool som innehåller virtuella datorer med SQL Server-instanserna. 
   |**Hälsoavsökning** |Välj avsökningen som du skapade.
   |**Sessionspermanens** |Ingen
   |**Timeout för inaktivitet (minuter)** |Standard (4)
   |**Flytande IP (direkt serverreturnering)** | Enabled

Upprepa dessa steg för belastningsutjämnaren på andra tillgänglighetsgrupperna som deltar i distribuerade Tillgänglighetsgrupper.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en SQL Server Always On-tillgänglighetsgrupp på virtuella Azure-datorer i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md)
