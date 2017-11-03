---
title: "Översikt över Azure belastningsutjämnare | Microsoft Docs"
description: "Översikt över Azure belastningsutjämnare funktioner, arkitektur och implementering. Lär dig hur belastningsutjämnaren fungerar och utnyttja i molnet."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 0f313dc0-f007-4cee-b2b9-f542357925a3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ecf1fc38d2b9fd54fe5b00db616224a0848179fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-load-balancer-overview"></a>Översikt över belastningsutjämnaren i Azure

Azure Load Balancer levererar hög tillgänglighet och nätverksprestanda till dina program. Det är en belastningsutjämnare för lager 4 (TCP, UDP) som distribuerar inkommande trafik mellan felfri instanser av tjänster som definierats i en belastningsutjämnad uppsättning.

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure belastningsutjämnare kan konfigureras för att:

* Läsa in Utjämna inkommande Internet-trafiken till virtuella datorer. Den här konfigurationen kallas [mot Internet för belastningsutjämning](load-balancer-internet-overview.md).
* Läs in Utjämna trafiken mellan virtuella datorer i ett virtuellt nätverk, mellan virtuella datorer i molntjänster eller mellan lokala datorer och virtuella datorer i ett virtuellt nätverk mellan platser. Den här konfigurationen kallas [intern belastningsutjämning](load-balancer-internal-overview.md).
* Vidarebefordra externa trafik till en specifik virtuell dator.

Alla resurser i molnet måste en offentlig IP-adress ska kunna nås från Internet. Moln-infrastruktur i Azure använder icke-dirigerbara IP-adresser för dess resurser. Azure använder network address translation (NAT) med offentliga IP-adresser för att kommunicera med Internet.

## <a name="load-balancer-features"></a>Läsa in belastningsutjämning funktioner

* Hash-baserad distribution

    Azure belastningsutjämnare använder en algoritm för hash-baserad distribution. Som standard används en 5-tuppel-hash som består av käll-IP, källport, mål-IP, målport och protokolltyp för att mappa trafik till tillgängliga servrar. Det ger endast varaktighet *inom* en transportsession. Paket i samma TCP eller UDP-session dirigeras till samma instans bakom Utjämning av nätverksbelastning-slutpunkten. När klienten stänger och öppnar en ny anslutningen eller startar en ny session från samma käll-IP, ändras källport. Detta kan orsaka trafik att gå till en annan slutpunkt i olika datacenter.

    Mer information finns i [belastningsdistributionsläget nätverksbelastningsutjämning](load-balancer-distribution-mode.md). Följande bild visar den hash-baserad distributionen:

    ![Hash-baserad distribution](./media/load-balancer-overview/load-balancer-distribution.png)

    Bild - Hash-baserad distribution

* Vidarebefordrade portar

    Azure belastningsutjämnare ger dig kontroll över hur inkommande kommunikation hanteras. Detta meddelande innehåller trafik som initieras från Internet-värdar, virtuella datorer i andra molntjänster eller virtuella nätverk. Den här kontrollen representeras av en slutpunkt (kallas även en slutpunkt för indata).

    En slutpunkt för indata lyssnar på en offentlig port och vidarebefordrar trafik till en intern port. Du kan mappa samma portar för en intern eller extern slutpunkt eller Använd en annan port för dem. Du kan till exempel ha en server som är konfigurerad för att lyssna på port 81 när offentlig slutpunkt mappningen är port 80. Skapandet av en offentlig slutpunkt utlöser skapandet av en load belastningsutjämnaren instans.

    När de skapas med hjälp av Azure portal, skapar portalen automatiskt slutpunkter till den virtuella datorn för Remote Desktop Protocol (RDP) och remote trafiken i Windows PowerShell-sessionen. Du kan använda dessa slutpunkter för att fjärradministrera den virtuella datorn via Internet.

* Automatisk omkonfiguration

    Azure belastningsutjämnare konfigurerar omedelbart om sig själv när du skalar delar upp eller ned. Till exempel händer omkonfigureringen om du ökar instanser för web/worker-roller i en molntjänst eller när du lägger till ytterligare virtuella datorer i samma belastningsutjämnad uppsättning.

* Tjänstövervakning

    Azure belastningsutjämnare kan avsöka hälsotillståndet för de olika serverinstanserna. När en avsökning inte svarar, stannar belastningsutjämnaren skickar nya anslutningar till feltillstånd instanser. Befintliga anslutningar påverkas inte.

    Tre typer av avsökningar stöds:

    + **Gästen agent avsökningen (på plattform som en tjänst endast virtuella datorer):** belastningsutjämnaren använder gästagenten på den virtuella datorn. Gästagenten lyssnar och svarar med ett HTTP-200 OK svar endast när instansen är i tillståndet redo (d.v.s. instansen inte är i ett tillstånd som upptagen återvinning eller stoppar). Om agenten inte svarar med en HTTP-200 OK, markerar instansen som inte svarar belastningsutjämnaren och stoppar skickar trafik till den instansen. Belastningsutjämnaren fortsätter att pinga instansen. Om gästagenten svarar med en HTTP-200, skickar belastningsutjämnaren trafik till instansen igen. När du använder en webbroll webbplats koden vanligtvis körs i w3wp.exe som inte övervakas av Azure fabric eller gäst-agenten. Detta innebär att fel i w3wp.exe (t.ex. HTTP 500-svar) inte kommer att rapporteras till gästagenten och belastningsutjämnaren märker inte för att ta den instansen utanför rotation.
    + **Anpassade HTTP-avsökningen:** den här avsökningen åsidosätter standard (gästagenten) avsökning. Du kan använda den för att skapa egna anpassade logik för att fastställa hälsotillståndet för instansen. Belastningsutjämnaren ska regelbundet söka slutpunkten (var 15 sekunder, som standard). Instansen anses vara i rotation om den svarar med en ACK TCP eller HTTP 200 inom tidsgränsen (standard 31 sekunder). Detta är användbart för att implementera din egen logik för att ta bort instanser från belastningsutjämnarens rotation. Du kan till exempel konfigurera instans för att returnera en icke-200 status om instansen är över 90% CPU. Web-roller som använder w3wp.exe du också få automatisk övervakning av din webbplats, eftersom fel i koden webbplats returstatus en icke-200 till avsökningen.
    + **Anpassade TCP-avsökning:** den här avsökningen förlitar sig om lyckade TCP-session till en definierad avsökningsport.

    Mer information finns i [LoadBalancerProbe schemat](https://msdn.microsoft.com/library/azure/jj151530.aspx).

* Källan NAT

    All utgående trafik till Internet från din tjänst kommer genomgår källa NAT (SNAT) genom att använda samma VIP-adressen som den inkommande trafiken. SNAT ger viktiga fördelar:

    + Enkel uppgradering och katastrofåterställning återställning för tjänster, kan eftersom VIP dynamiskt kan mappas till en annan instans av tjänsten.
    + Det underlättar åtkomstkontrollistan (ACL) för åtkomstkontrollhantering. ACL: er som uttrycks i VIP ändras inte services skala upp, ner eller hämta omdistribueras.

    Belastningsutjämningskonfigurationen stöder fullständig kon NAT för UDP. Fullständig kon NAT är en typ av NAT-enhet där porten som tillåter inkommande anslutningar från alla externa värdar (som svar på en begäran om utgående).

    För varje ny utgående anslutning som initierar en virtuell dator, har också en utgående port allokerats av belastningsutjämnaren. Den externa värden ser trafik med en virtuell IP-VIP-allokerad port. För scenarier som kräver ett stort antal utgående anslutningar, rekommenderas att använda [offentlig IP på instansnivå](../virtual-network/virtual-networks-instance-level-public-ip.md) adresser, så att de virtuella datorerna ha en dedikerad utgående IP-adress för SNAT. Detta minskar risken för port resursuttömning.

    Se [utgående anslutningar](load-balancer-outbound-connections.md) mer information om det här avsnittet.

### <a name="support-for-multiple-load-balanced-ip-addresses-for-virtual-machines"></a>Stöd för flera belastningsutjämnad IP-adresser för virtuella datorer
Du kan tilldela fler än en belastningsutjämnad offentliga IP-adressen till en uppsättning virtuella datorer. Med den här möjligheten du kan vara värd för flera SSL-webbplatser och/eller flera SQL Server AlwaysOn Availability Group-lyssnare på samma uppsättning virtuella datorer. Mer information finns i [flera VIP: er per Molntjänsten](load-balancer-multivip.md).

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="limitations"></a>Begränsningar

Läs in Belastningsutjämnarens serverdelspooler kan innehålla alla VM SKU förutom grundläggande nivån.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Internetriktade belastningsutjämnare](load-balancer-internet-overview.md)

- Lär dig mer om [översikt över interna belastningsutjämnare](load-balancer-internal-overview.md)

- Skapa en [Internetriktade belastningsutjämnare](load-balancer-get-started-internet-portal.md)

- Lär dig mer om den andra nyckeln [nätverk](../networking/networking-overview.md) Azure

