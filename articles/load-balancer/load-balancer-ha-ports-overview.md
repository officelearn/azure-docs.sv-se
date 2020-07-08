---
title: Översikt över portar med hög tillgänglighet i Azure
titleSuffix: Azure Load Balancer
description: Lär dig mer om belastnings utjämning för hög tillgänglighets portar på en intern belastningsutjämnare.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: da776c679d5ded6f53cd0d224828fffee058d37d
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85920621"
---
# <a name="high-availability-ports-overview"></a>Översikt över portar med hög tillgänglighet

Med Azure Standard Load Balancer kan du belastningsutjämna TCP-och UDP-flöden på alla portar samtidigt när du använder en intern belastningsutjämnare. 

En belastnings Utjämnings regel för belastnings utjämning med hög tillgänglighet (HA) är en variant av en belastnings Utjämnings regel som kon figurer ATS på ett internt Standard Load Balancer. Du kan förenkla användningen av en belastningsutjämnare genom att tillhandahålla en enskild regel för att belastningsutjämna alla TCP-och UDP-flöden som kommer till alla portar i en intern Standard Load Balancer. Belastnings Utjämnings beslutet görs per flöde. Den här åtgärden baseras på följande fem-tuple-anslutning: Källans IP-adress, källport, mål-IP-adress, målport och protokoll

Belastnings Utjämnings reglerna för HA-portar hjälper dig med kritiska scenarier, till exempel hög tillgänglighet och skalning för virtuella nätverks installationer (NVA) i virtuella nätverk. Funktionen kan också hjälpa när ett stort antal portar måste vara belastningsutjämnade. 

Belastnings Utjämnings reglerna för HA-portar konfigureras när du ställer in frontend-och backend-portarna på **0** och protokollet till **alla**. Den interna belastnings Utjämnings resursen balanserar sedan alla TCP-och UDP-flöden, oavsett port nummer

## <a name="why-use-ha-ports"></a>Varför ska man använda HA-portar?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Virtuella nätverksinstallationer

Du kan använda NVA för att skydda din Azure-arbetsbelastning från flera typer av säkerhetshot. När du använder NVA i dessa scenarier måste de vara pålitliga och hög tillgängliga och de måste skalas upp för efter frågan.

Du kan enkelt uppnå dessa mål genom att lägga till NVA-instanser till backend-poolen för den interna belastningsutjämnaren och konfigurera en belastnings Utjämnings regel för HA-portar.

För NVA HA-scenarier erbjuder HA-portar följande fördelar:
- Ge snabba redundans till felfria instanser med hälso avsökningar per instans
- Se till att högre prestanda med skalbar till *n*-aktiva instanser
- Ange *n*-aktiva och aktiva-passiva scenarier
- Eliminera behovet av komplexa lösningar, till exempel Apache ZooKeeper noder för övervakning av enheter

I följande diagram presenteras en nav-och-eker-distribution av virtuella nätverk. Ekrarna tvingar fram tunnel trafik till det virtuella hubb nätverket och via NVA innan det betrodda utrymmet lämnas. NVA ligger bakom ett internt Standard Load Balancer med konfiguration av HA-portar. All trafik kan bearbetas och vidarebefordras därefter. När en belastnings Utjämnings regel för belastnings utjämning har kon figurer ATS som visas i följande diagram tillhandahåller belastnings Utjämnings regeln för belastnings utjämning också flödes symmetri för ingress

<a node="diagram"></a>
![Diagram över nav-och-eker-virtuellt nätverk med NVA distribuerat i HA-läge](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Om du använder NVA, bekräftar du med deras leverantörer hur du bäst använder HA-portar och för att lära dig vilka scenarier som stöds.

### <a name="load-balancing-large-numbers-of-ports"></a>Belastnings utjämning av ett stort antal portar

Du kan också använda HA-portar för program som kräver belastnings utjämning för ett stort antal portar. Du kan förenkla dessa scenarier genom att använda en intern [standard Load Balancer](load-balancer-standard-overview.md) med ha-portar. En enskild regel för belastnings utjämning ersätter flera enskilda regler för belastnings utjämning, en för varje port.

## <a name="region-availability"></a>Regional tillgänglighet

Funktionen HA portar är tillgänglig i alla globala Azure-regioner.

## <a name="supported-configurations"></a>Konfigurationer som stöds

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>En icke-flytande IP-konfiguration med en icke-flytande IP-adress (ej direkt Server) för en intern Standard Load Balancer

Den här konfigurationen är en konfiguration för grundläggande HA-portar. Du kan konfigurera en belastnings Utjämnings regel för en belastnings utjämning på en enda klient dels-IP-adress genom att göra följande:
1. När du konfigurerar Standard Load Balancer markerar du kryss rutan **ha portar** i Load Balancer regel konfigurationen.
2. För **flytande IP-adress**väljer du **inaktive rad**.

Den här konfigurationen tillåter inte någon annan belastnings Utjämnings regel konfiguration på den aktuella belastnings Utjämnings resursen. Det innebär också att ingen annan intern belastnings Utjämnings resurs konfiguration för den angivna uppsättningen backend-instanser tillåts.

Du kan dock konfigurera en offentlig Standard Load Balancer för Server dels instanserna förutom den här regeln för HA-portar.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>En enda, flytande IP (Direct Server Return) HA-port konfiguration för en intern Standard Load Balancer

Du kan konfigurera belastningsutjämnaren på samma sätt genom att använda en belastnings Utjämnings regel med **ha-port** med en enda klient del genom att ställa in den **flytande IP-adressen** på **aktive rad**. 

Genom att använda den här konfigurationen kan du lägga till mer flytande IP-regler för belastnings utjämning och/eller en offentlig belastningsutjämnare. Du kan dock inte använda en icke-flytande IP, HA belastnings Utjämnings konfiguration med belastnings utjämning överst i den här konfigurationen.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Flera HA-port-konfigurationer på ett internt Standard Load Balancer

Om ditt scenario kräver att du konfigurerar fler än en HA-port-klient för samma backend-pool kan du göra följande: 
- Konfigurera fler än en privat IP-adress för klient delen för en enskild intern Standard Load Balancer resurs.
- Konfigurera flera regler för belastnings utjämning, där varje regel har en enda unik IP-adress för klient delen vald.
- Välj alternativet **ha portar** och ställ sedan in **flytande IP** till **aktive rad** för alla regler för belastnings utjämning.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>En intern belastningsutjämnare med HA-portar och en offentlig belastningsutjämnare på samma backend-instans

Du kan konfigurera *en* offentlig standard Load Balancer resurs för Server dels resurserna, tillsammans med en enda intern standard load BALANCER med ha-portar.

## <a name="limitations"></a>Begränsningar

- Belastnings Utjämnings regler för HA-portar är bara tillgängliga för interna Standard Load Balancer.
- Att kombinera en belastnings Utjämnings regel för belastnings utjämning och en icke-HA-belastnings Utjämnings regel som pekar på samma ipconfiguration (en eller flera), stöds inte om inte båda har flytande IP-aktiverade.
- Befintliga IP-fragment vidarebefordras av HA port belastnings Utjämnings regler till samma mål som det första paketet.  IP-fragmentering av UDP-eller TCP-paket stöds inte.
- Flödes symmetrin (främst för NVA-scenarier) stöds med backend-instansen och ett enda nätverkskort (och enskild IP-konfiguration) endast när det används som det visas i diagrammet ovan och med belastnings Utjämnings regler för HA-portar. Den finns inte i något annat scenario. Det innebär att två eller flera Load Balancer resurser och deras respektive regler gör oberoende beslut och inte samordnade. Se beskrivningen och diagrammet för [virtuella nätverks enheter](#nva). När du använder flera nätverkskort eller mellan NVA mellan en offentlig och intern Load Balancer, är flödets symmetri inte tillgängligt.  Du kanske kan kringgå detta genom att källan NAT'ing ingångs flödet till IP-enheten för att tillåta svar på samma NVA.  Vi rekommenderar dock starkt att du använder ett enda nätverkskort och använder referens arkitekturen som visas i diagrammet ovan.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar HA-portar för din ILB via portal (tutorial-Load-Balancer-standard-Internal-Portal. mdl # Create-a-Load-Balancer-Rule), [PowerShell](load-balancer-get-started-ilb-arm-ps.md#create-the-configuration-rules-probe-and-load-balancer), [CLI](load-balancer-get-started-ilb-arm-cli.md#create-the-load-balancer-rule)eller [templates](load-balancer-get-started-ilb-arm-template.md).
- [Läs mer om Standard Load Balancer](load-balancer-standard-overview.md)
