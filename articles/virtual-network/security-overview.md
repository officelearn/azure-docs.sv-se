---
title: "Översikt över nätverkssäkerhet för Azure | Microsoft Docs"
description: "Läs mer om säkerhetsalternativ för att styra flödet av nätverkstrafik mellan Azure-resurser."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.openlocfilehash: 98559cbb0acab91c4b2c30c6d0129e955eef85f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="network-security"></a>Nätverkssäkerhet

Du kan begränsa nätverkstrafiken till resurser i ett virtuellt nätverk med hjälp av en nätverkssäkerhetsgrupp. En nätverkssäkerhetsgrupp innehåller en lista över säkerhetsregler som tillåter eller nekar inkommande eller utgående nätverkstrafik baserat på käll- eller mål-IP-adress, port och protokoll. 

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Varje nätverksgränssnitt har ingen, eller en, associerad nätverkssäkerhetsgrupp. Varje nätverksgränssnitt finns i ett [virtuellt nätverks](virtual-networks-overview.md) undernät. Varje undernät kan också ha ingen, eller en, associerad nätverkssäkerhetsgrupp. 

När säkerhetsregler tillämpas för ett undernät tillämpas de för alla resurser i undernätet. Förutom nätverksgränssnitt kan du ha instanser av andra Azure-tjänster, till exempel HDInsight, VM-skalningsuppsättningar och tillämpningstjänstmiljöer, distribuerade i undernätet.

Så här tillämpas nätverkssäkerhetsgrupper när en nätverkssäkerhetsgrupp är associerad med både ett nätverksgränssnitt och det undernät som nätverksgränssnittet finns i:

- **Inkommande trafik**: Den nätverkssäkerhetsgrupp som är associerad med det undernät som nätverksgränssnittet finns i utvärderas först. All trafik som tillåts via den nätverkssäkerhetsgrupp som är associerad med undernätet utvärderas sedan av den nätverkssäkerhetsgrupp som är associerad med nätverksgränssnittet. Du kan till exempel kräva åtkomst för inkommande trafik till en virtuell dator via port 80 från Internet. Om du associerar en nätverkssäkerhetsgrupp med både nätverksgränssnittet och det undernät som nätverksgränssnittet finns i, måste nätverkssäkerhetsgruppen som är associerad med undernätet och nätverksgränssnittet tillåta port 80. Om du bara tillåter port 80 via den nätverkssäkerhetsgrupp som är associerad med undernätet eller det nätverksgränssnitt som undernätet finns i misslyckas kommunikationen på grund av standardsäkerhetsreglerna. Se [standardsäkerhetsregler](#default-security-rules) för mer information. Om du endast tillämpar en nätverkssäkerhetsgrupp för antingen undernätet eller nätverksgränssnittet och nätverkssäkerhetsgruppen innehåller en regel som tillåter inkommande trafik på port 80, lyckas kommunikationen. 
- **Utgående trafik**: Den nätverkssäkerhetsgrupp som är associerad med nätverksgränssnittet utvärderas först. All trafik som tillåts via den nätverkssäkerhetsgrupp som är associerad med nätverksgränssnittet utvärderas sedan av den nätverkssäkerhetsgrupp som är associerad med undernätet.

Du kanske inte alltid är medveten om när nätverkssäkerhetsgrupper tillämpas för både ett nätverksgränssnitt och ett undernät. Du kan enkelt granska vilka regler som tillämpas för ett nätverksgränssnitt genom att visa [gällande säkerhetsregler](virtual-network-manage-nsg-arm-portal.md) för ett nätverksgränssnitt. Du kan också använda funktionen [Kontrollera IP-flöde](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i Azure Network Watcher för att ta reda på om kommunikation tillåts till eller från ett nätverksgränssnitt. Verktyget visar om kommunikation tillåts, och om nätverkssäkerhetsregeln tillåter eller nekar trafik.
 
> [!NOTE]
> Nätverkssäkerhetsgrupper är associerade med undernät eller med virtuella datorer och molntjänster som distribuerats enligt den klassiska distributionsmodellen, inte med nätverksgränssnitt i Resource Manager-distributionsmodellen. Läs mer i avsnittet [om Azures distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Samma nätverkssäkerhetsgrupp kan användas för så många enskilda nätverksgränssnitt och undernät du vill.

## <a name="security-rules"></a>Säkerhetsregler

En nätverkssäkerhetsgrupp kan innehålla noll regler, eller så många regler du vill, inom Azure-prenumerationens [gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Varje regel anger följande egenskaper:

|Egenskap  |Förklaring  |
|---------|---------|
|Namn|Ett unikt namn inom nätverkssäkerhetsgruppen.|
|Prioritet | Ett tal mellan 100 och 4096. Regler bearbetas i prioritetsordning. Låga tal bearbetas före höga tal eftersom låga tal har högre prioritet. När trafiken matchar en regel avbryts bearbetningen. Det innebär att regler som har lägre prioritet (högre tal) och samma attribut som regler med högre prioritet inte bearbetas.|
|Källa eller mål| Valfria IP-adresser (eller en enskild IP-adress), CIDR-block (till exempel 10.0.0.0/24), tjänsttagg eller programsäkerhetsgrupp. Läs mer om [tjänsttaggar](#service-tags) och [programsäkerhetsgrupper](#application-security-groups). Du kan begränsa antalet säkerhetsregler du skapar genom att ange ett intervall, en tjänsttagg eller en programsäkerhetsgrupp. Möjligheten att ange flera enskilda IP-adresser och intervall (du kan inte ange flera tjänsttaggar eller programgrupper) i en regel finns som förhandsversion och kallas förhöjda säkerhetsregler. Förhöjda säkerhetsregler kan bara skapas i nätverkssäkerhetsgrupper som skapats genom Resource Manager-distributionsmodellen. Du kan inte ange flera IP-adresser och IP-adressintervall i nätverkssäkerhetsgrupper som skapats via den klassiska distributionsmodellen.|
|Protokoll     | TCP, UDP eller Any (Alla), vilket innefattar TCP, UDP och ICMP. Du kan inte ange ICMP separat, så om du vill använda ICMP, måste du använda Any (Alla). |
|Riktning| Om regeln gäller för inkommande eller utgående trafik.|
|Portintervall     |Du kan ange en enskild port eller ett portintervall. Du kan till exempel ange 80 eller 10000–10005. Om du anger intervall behöver du inte skapa lika många säkerhetsregler. Möjligheten att ange flera enskilda portar och portintervall i en regel finns som förhandsversion och kallas förhöjda säkerhetsregler. Läs viktig information i [Förhandsversionsfunktioner](#preview-features) innan du använder förhöjda säkerhetsregler. Förhöjda säkerhetsregler kan bara skapas i nätverkssäkerhetsgrupper som skapats genom Resource Manager-distributionsmodellen. Du kan inte ange flera portar eller portintervall i samma säkerhetsregel i nätverkssäkerhetsgrupper som skapats med den klassiska distributionsmodellen.   |
|Åtgärd     | Tillåt eller neka        |

**Överväganden**

- **Värdnodens virtuella IP-adress**: Grundläggande infrastrukturtjänster som DHCP, DNS och hälsoövervakning tillhandahålls via de virtualiserade värd-IP-adresserna 168.63.129.16 och 169.254.169.254. De här offentliga IP-adresserna tillhör Microsoft och kommer att vara de enda virtualiserade IP-adresserna som används i alla regioner för det här ändamålet. Den här adressen mappar till den fysiska adressen för serverdatorn (värdnoden) som är värd för den virtuella datorn. Värdnoden agerar som ett DHCP-relä, rekursiv DNS-matchare och avsökningskälla för belastningsutjämnaren, hälsoavsökningen och datorhälsoavsökningen. Kommunikation till de här IP-adresserna är inte ett angrepp. Om du blockerar trafik till eller från dessa IP-adresser kanske den virtuella datorn inte fungerar korrekt.
- **Licensiering (nyckelhanteringstjänsten):** Windows-avbildningar som kör på de virtuella datorerna ska vara licensierade. Licensieringen kontrolleras genom att en begäran skickas till nyckelhanteringstjänstens värdservrar som hanterar sådana frågor. Begäran är en utgående begäran via port 1688.
- **Virtuella datorer i belastningsutjämnade pooler**: Källporten och adressintervallet som används kommer från den ursprungliga datorn, inte belastningsutjämnaren. Målporten och måladressutrymmet kommer från måldatorn, inte belastningsutjämnaren.
- **Azure-tjänstinstanser**: Instanser av flera Azure-tjänster, till exempel HDInsight, tillämpningstjänstmiljöer och VM-skalningsuppsättningar distribueras i undernät för virtuella nätverk. Ta reda på portkraven för varje tjänst innan du tillämpar en nätverkssäkerhetsgrupp för det undernät som resursen är distribuerad i. Om du nekar åtkomst till portar som krävs för tjänsten kommer tjänsten inte att fungera korrekt. 

Säkerhetsregler är tillståndskänsliga. Om du till exempel anger en utgående säkerhetsregel till en adress via port 80, behöver du inte ange en inkommande säkerhetsregel för svar på utgående trafik. Du behöver bara ange en inkommande säkerhetsregel om kommunikationen initieras externt. Även det motsatta gäller. Om inkommande trafik tillåts via en port, behöver du inte ange en utgående säkerhetsregel för svar på trafik via porten. Mer information om begränsningar när du skapar säkerhetsregler finns i avsnittet om [Azure-gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Förhöjda säkerhetsregler

Förhöjda regler förenklar säkerhetsdefinitionen för virtuella nätverk så att du kan definiera större och mer komplexa nätverkssäkerhetsprinciper med färre regler. Du kan kombinera flera portar, flera explicita IP-adresser, tjänsttaggar och programsäkerhetsgrupper i en enkel säkerhetsregel som är lätt att förstå. Använd förhöjda regler i fälten för källa, mål och port för en regel. När du skapar en regel kan du ange flera explicita IP-adresser, CIDR-intervall och portar. För att förenkla underhållet av din säkerhetsregeldefinition kan du kombinera förhöjda säkerhetsregler med tjänsttaggar eller programsäkerhetsgrupper. 

Förhöjda säkerhetsregler är tillgängliga som förhandsversion. Mer information om begränsningar när du skapar förhöjda säkerhetsregler finns i avsnittet om [Azure-gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Funktioner i förhandsversionen har inte samma grad av tillgänglighet och tillförlitlighet som funktioner i en allmän version. Funktionerna är bara tillgängliga i följande regioner: östra USA, västra USA, västra USA 2, västra centrala USA, östra Australien, sydöstra Australien och södra Storbritannien.

## <a name="default-security-rules"></a>Standardsäkerhetsregler

Om en nätverkssäkerhetsgrupp inte är associerad med ett undernät eller ett nätverksgränssnitt tillåts all trafik som inkommer till, eller utgår från, undernätet eller nätverksgränssnittet. Följande standardregler skapas direkt i nätverkssäkerhetsgruppen av Azure när en nätverkssäkerhetsgrupp har skapats:

### <a name="inbound"></a>Inkommande

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioritet|Källa|Källportar|Mål|Målportar|Protokoll|Åtkomst|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Alla|Tillåt|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioritet|Källa|Källportar|Mål|Målportar|Protokoll|Åtkomst|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Alla|Tillåt|

#### <a name="denyallinbound"></a>DenyAllInbound

|Prioritet|Källa|Källportar|Mål|Målportar|Protokoll|Åtkomst|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Alla|Neka|

### <a name="outbound"></a>Utgående

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Åtkomst |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Alla | Tillåt |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Åtkomst |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Alla | Tillåt |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Åtkomst |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Alla | Neka |

I kolumnerna **Källa** och **Mål** är *VirtualNetwork*, *AzureLoadBalancer*, och *Internet* så kallade [tjänsttaggar](#tags), inte IP-adresser. **Alla** i protokollkolumnen omfattar TCP, UDP och ICMP. När du skapar en regel kan du ange TCP, UDP eller Alla, men du kan inte ange bara ICMP. Om du har en regel som kräver ICMP måste du därför välja *Alla* för protokoll. *0.0.0.0/0* i kolumnerna **Källa** och **Mål** representerar alla adresser.
 
Du kan inte ta bort standardreglerna, men du kan åsidosätta dem genom att skapa regler med högre prioritet.

## <a name="service-tags"></a>Tjänsttaggar

 En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa egna tjänsttaggar, och du kan inte heller ange vilka IP-adresser som ska finnas i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Du kan använda följande tjänsttaggar när du definierar säkerhetsregler. Namnen skiljer sig lite mellan olika [Azure-distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (*Resource Manager) (**VIRTUAL_NETWORK** för klassisk): Den här taggen innehåller adressutrymmet för det virtuella nätverket (alla CIDR-intervall som har definierats för det virtuella nätverket), alla anslutna lokala adressutrymmen och [peer-kopplade](virtual-network-peering-overview.md) virtuella nätverk eller virtuella nätverk som anslutits till en [virtuell nätverksgateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** för klassisk): Den här taggen anger belastningsutjämnaren för Azures infrastruktur. Taggen översätts till en [IP-adress för Azure-datacentret](https://www.microsoft.com/download/details.aspx?id=41653) som Azures hälsoavsökning kommer från. Du kan åsidosätta den här regeln om du inte använder Azures belastningsutjämnare.
* **Internet** (Resource Manager) (**INTERNET** för klassisk): Den här taggen anger IP-adressutrymmet som är utanför det virtuella nätverket och som kan nås av det offentliga Internet. Adressintervallet omfattar det [offentliga IP-adressutrymmet som ägs av Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureTrafficManager** (endast Resource Manager): Den här taggen anger IP-adressutrymmet för tjänsten Azure Traffic Manager.
* **Storage** (endast Resource Manager): Den här taggen anger IP-adressutrymmet för tjänsten Azure Storage. Om du anger *Storage* som värde tillåts eller nekas trafik till lagringen. Om du bara vill tillåta åtkomst till lagring i en viss [region](https://azure.microsoft.com/regions) anger du regionen. Om du till exempel bara vill tillåta åtkomst till Azure Storage i regionen östra USA kan du ange *Storage.EastUS* som tjänsttagg. Ytterligare regionala tjänsttaggar som är tillgängliga: Storage.AustraliaEast, Storage.AustraliaSoutheast, Storage.EastUS, Storage.UKSouth, Storage.WestCentralUS, Storage.WestUS och Storage.WestUS2. Taggen representerar tjänsten, men inte specifika instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure Storage, men inte ett specifikt Azure Storage-konto.
* **SQL** (endast Resource Manager): Den här taggen anger adressprefix för tjänsterna Azure SQL Database och Azure SQL Data Warehouse. Du kan bara ange vissa regioner för den här tjänsttaggen. Om du till exempel vill tillåta åtkomst endast till Azure SQL Database i regionen östra USA anger du *Sql.EastUS* som tjänsttagg. Du kan inte ange endast Sql för alla Azure-regioner. Du måste ange regioner var för sig. Andra tillgängliga regionala tjänsttaggar är: Sql.AustraliaEast, Sql.AustraliaSoutheast, Sql.EastUS, Sql.UKSouth, Sql.WestCentralUS, Sql.WestUS och Sql.WestUS2. Taggen representerar tjänsten, men inte specifika instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure SQL Database, men inte en specifik Azure SQL-databas.

> [!WARNING]
> Tjänsttaggar för AzureTrafficManager, Storage och Sql är tillgängliga som förhandsversion. Funktioner i förhandsversionen har inte samma grad av tillgänglighet och tillförlitlighet som funktioner i en allmän version. Tjänsttaggarna är bara tillgängliga i följande regioner: östra USA, västra USA, västra USA 2, västra centrala USA, östra Australien, sydöstra Australien och södra Storbritannien.

> [!NOTE]
> Om du implementerar en tjänstslutpunkt för ett virtuellt nätverk för en viss tjänst, till exempel Azure Storage eller Azure SQL Database, lägger Azure till en väg till ett undernät för virtuellt nätverk för tjänsten. Vägens adressprefix är samma adressprefix, eller CIDR-intervall, som motsvarande tjänsttagg.

## <a name="application-security-groups"></a>Programsäkerhetsgrupper

Med programsäkerhetsgrupper kan du konfigurera nätverkssäkerhet som ett naturligt tillägg till ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på dessa grupper. Funktionen gör att du kan återanvända din säkerhetsprincip i stor skala utan manuellt underhåll av explicita IP-adresser. Plattformen hanterar komplexiteten med explicita IP-adresser och flera regeluppsättningar så att du kan fokusera på affärslogik.

Du kan ange en programsäkerhetsgrupp som källa och mål i en säkerhetsregel. När din säkerhetsprincip har definierats kan du skapa virtuella datorer och tilldela nätverksgränssnitt i den virtuella datorn till en programsäkerhetsgrupp. Principen tillämpas baserat på programsäkerhetsgruppens medlemskap i varje nätverksgränssnitt inom en virtuell dator. I följande exempel visas hur du kan använda en programsäkerhetsgrupp för alla webbservrar i din prenumeration:

1. Skapa en programsäkerhetsgrupp som heter *WebServers*.
2. Skapa en nätverkssäkerhetsgrupp som heter *MyNSG*.
3. Skapa en inkommande säkerhetsregel i nätverkssäkerhetsgruppen, ange tjänsttaggen *Internet* som källadress och programservergruppen *WebServers* som måladress och tillåt portarna 80 och 443.
4. Distribuera en virtuell dator som kör ett webbserverprogram. Ange att nätverksgränssnittet i den virtuella datorn ska vara medlem i programsäkerhetsgruppen *WebServers*. Port 80 och 443 är sedan tillåtna för den virtuella datorn. Portarna är också tillåtna för eventuella webbservrar som du skapar senare och gör till medlemmar i programsäkerhetsgruppen *WebServers*. 

Om du skapar andra regler, anger andra programsäkerhetsgrupper som mål, tillämpas inte dessa regler för webbservrarna i föregående exempel. Regler som anger en programsäkerhetsgrupp tillämpas endast för nätverksgränssnitt som är medlemmar i programsäkerhetsgruppen. Programsäkerhetsgrupper i kombination med förhöjda säkerhetsregler och tjänsttaggar gör det möjligt att skapa ett minimalt antal nätverkssäkerhetsgrupper för att hantera nätverkssäkerheten i din prenumeration.
 
Mer information om begränsningar när du skapar programsäkerhetsgrupper och hur du anger dem finns i avsnittet om [Azure-gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Programsäkerhetsgrupper är tillgängliga som förhandsversion. Innan du kan använda programsäkerhetsgrupper måste du registrera dig för att använda dem genom att slutföra steg 1 till 5 i [Skapa en nätverkssäkerhetsgrupp med programsäkerhetsgrupper](create-network-security-group-preview.md#powershell) och du bör också läsa viktig information i [Förhandsfunktioner](#preview-features). I förhandsversionen är programsäkerhetsgrupper begränsade till det virtuella nätverkets omfång. Virtuella nätverk som är peer-kopplade med korsreferenser till programsäkerhetsgrupper i en nätverkssäkerhetsgrupp tillämpas inte. 

Funktioner i förhandsversionen har inte samma grad av tillgänglighet och tillförlitlighet som funktioner i en allmän version. Innan du kan använda programsäkerhetsgrupper måste du registrera dig för att använda dem. Funktionerna är endast tillgängliga i följande regioner: västra centrala USA.

## <a name="next-steps"></a>Nästa steg

* Slutför självstudiekursen [Skapa en nätverkssäkerhetsgrupp](virtual-networks-create-nsg-arm-pportal.md)
* Slutför självstudiekursen [Skapa en nätverkssäkerhetsgrupp med programsäkerhetsgrupper](create-network-security-group-preview.md)
