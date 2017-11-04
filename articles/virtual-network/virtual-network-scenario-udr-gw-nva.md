---
title: "Hybridanslutning med 2-nivåprogram | Microsoft Docs"
description: "Lär dig att distribuera virtuella installationer och UDR att skapa en programmiljö med flera nivåer i Azure"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: jdial
ms.openlocfilehash: 544ba6484b23da425d53594622122b1e18b92359
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="virtual-appliance-scenario"></a>Virtuella utrustningsscenario
Ett vanligt scenario bland större Azure kunden är behovet av att tillhandahålla ett två skikt program exponerad mot Internet, samtidigt som åtkomst till den bakre nivån från ett lokalt datacenter. Det här dokumentet vägleder dig genom ett scenario med användaren användardefinierade vägar (UDR), en VPN-Gateway och virtuella nätverksenheter för att distribuera en tvålagers-miljö som uppfyller följande krav:

* Webbprogrammet måste vara tillgänglig via det offentliga Internet.
* Webbservern som värd för programmet måste kunna komma åt en backend-programserver.
* All trafik från Internet till webbprogrammet måste gå via en virtuell brandväggsinstallation. Den här virtuella installations ska användas för Internet-trafiken.
* All trafik till programservern måste gå via en virtuell brandväggsinstallation. Den här virtuella installations ska användas för åtkomst till slutet serverdelen och åtkomst som kommer från det lokala nätverket via en VPN-Gateway.
* Administratörer måste kunna hantera virtuella brandväggsenheter från sina lokala datorer, med hjälp av en tredje brandväggen virtuell installation som uteslutande används för hanteringsändamål.

Det här är ett standard DMZ-scenario med en DMZ och ett skyddat nätverk. Detta scenario kan konstrueras i Azure med hjälp av NSG: er, virtuella installationer av brandväggen eller en kombination av båda. Tabellen nedan visar några av för- och nackdelar mellan NSG: er och virtuella installationer i brandväggen.

|  | Tekniker | Nackdelar |
| --- | --- | --- |
| NSG |Utan kostnad. <br/>Integrerat i Azure RBAC. <br/>Regler kan skapas i ARM-mallar. |Komplexitet kan variera i större miljöer. |
| Brandvägg |Fullständig kontroll över dataplan. <br/>Central hantering via brandväggen-konsolen. |Kostnaden för brandväggsinstallation. <br/>Inte integrerat med Azure RBAC. |

Lösningen nedan använder virtuella installationer i brandväggen för att implementera en DMZ/skyddad scenariot.

## <a name="considerations"></a>Överväganden
Du kan distribuera miljön som beskrivs ovan i Azure med hjälp av olika funktioner som är tillgängliga i dag, enligt följande.

* **Virtuella nätverk (VNet)**. Ett virtuellt Azure-nätverk fungerar på liknande sätt som ett lokalt nätverk och kan segmenterat till ett eller flera undernät för trafikisolering och avgränsning av säkerhetsskäl.
* **Virtuell installation**. Flera partners erbjuda virtuella installationer i Azure Marketplace som kan användas för de tre brandväggar som beskrivs ovan. 
* **Användardefinierade vägar (UDR)**. Routningstabeller kan innehålla udr: er som används av Azure-nätverk för att styra flödet av paket i ett virtuellt nätverk. Dessa routningstabeller kan tillämpas på undernät. En av de senaste funktionerna i Azure är möjligheten att använda en routningstabell för GatewaySubnet möjligheten att vidarebefordra all trafik som kommer till Azure-VNet från en hybridanslutning för en virtuell installation.
* **IP-vidarebefordring**. Standard Azure nätverk motorn vidarebefordra paket till virtuella nätverkskort (NIC) endast om den paket IP-adressen matchar NIC IP-adress. Om en UDR definierar att ett paket måste skickas till en viss virtuell installation, skulle därför Azure nätverk motorn släppa paketets. För att säkerställa paketet levereras till en virtuell dator (i det här fallet en virtuell installation) som inte är den faktiska destinationen för paketet, måste du aktivera IP-vidarebefordring för den virtuella installationen.
* **Nätverkssäkerhetsgrupper (NSG: er)**. Exemplet nedan gör inte användning av NSG: er, men du kan använda NSG: er som tillämpas på undernät och/eller nätverkskort i den här lösningen för att ytterligare filtrera trafik till och från dessa undernät och nätverkskort.

![IPv6-anslutning](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

I det här exemplet finns det en prenumeration som innehåller följande:

* 2 resursgrupper inte visas i diagrammet. 
  * **ONPREMRG**. Innehåller alla resurser som är nödvändiga för att simulera ett lokalt nätverk.
  * **AZURERG**. Innehåller alla nödvändiga resurser för Azure virtual network-miljön. 
* Ett VNet med namnet **onpremvnet** används för att efterlikna ett lokalt datacenter segmenterat enligt nedan.
  * **onpremsn1**. Undernät som innehåller en virtuell dator (VM) som kör Ubuntu att efterlikna en lokal server.
  * **onpremsn2**. Undernät som innehåller en virtuell dator som kör Ubuntu att efterlikna en lokal dator som används av en administratör.
* Det finns en virtuell brandväggsinstallation med namnet **OPFW** på **onpremvnet** används för att upprätthålla en tunnel till **azurevnet**.
* Ett VNet med namnet **azurevnet** segmenterat enligt nedan.
  * **azsn1**. Extern brandvägg undernät som används endast för extern brandvägg. Alla Internet-trafiken kommer via det här undernätet. Det här undernätet innehåller bara ett nätverkskort som är kopplad till extern brandvägg.
  * **azsn2**. Klientdelens undernät som värd för en virtuell dator som körs som en server som ska kunna nås från Internet.
  * **azsn3**. Backend-undernät som värd för en virtuell dator som kör en backend-programserver som ska användas av frontend-webbserver.
  * **azsn4**. Management-undernät som används enbart för att ge åtkomst till alla virtuella brandväggsenheter. Det här undernätet innehåller bara ett nätverkskort för varje virtuell brandväggsinstallation som används i lösningen.
  * **GatewaySubnet**. Hybridrapportering i Azure-anslutningen undernät som krävs för ExpressRoute- och VPN-Gateway för att tillhandahålla anslutningsbarhet mellan virtuella Azure-nätverk och andra nätverk. 
* Det finns 3 brandväggen virtuella installationer i den **azurevnet** nätverk. 
  * **AZF1**. Extern brandvägg exponeras för det offentliga Internet med hjälp av en offentlig IP-adressresurs i Azure. Du måste se till att du har en mall från Marketplace eller direkt från din enhet leverantör, som tillhandahåller en 3-NIC virtuell installation.
  * **AZF2**. Intern brandvägg som används för att kontrollera trafik mellan **azsn2** och **azsn3**. Det här är en virtuell 3-NIC-installation.
  * **AZF3**. Hantering av brandvägg tillgängliga för administratörer från det lokala datacentrat och är ansluten till ett undernät för hantering som används för att hantera alla brandväggsenheter. Du kan hitta 2-NIC virtuell installation mallar i Marketplace eller be en direkt från leverantören av enheten.

## <a name="user-defined-routing-udr"></a>Användardefinierad routning (UDR)
Varje undernät i Azure kan länkas till en UDR tabell som används för att definiera hur trafik initieras i att dirigeras undernät. Om inga udr: er definieras använder Azure standardvägar för att tillåta trafik från ett undernät till en annan. För att bättre förstå udr: er, besök [vad är användardefinierade vägar och IP-vidarebefordran](virtual-networks-udr-overview.md).

Om du vill se till att kommunikationen sker via rätt brandväggsinstallation baserat på senaste kravet ovan, måste du skapa följande vägtabell som innehåller udr: er i **azurevnet**.

### <a name="azgwudr"></a>azgwudr
I det här scenariot endast trafik som flödar från lokal till Azure används för att hantera brandväggarna genom att ansluta till **AZF3**, och att trafik måste gå igenom den interna brandväggen **AZF2**. Därför bara en väg är nödvändigt i det **GatewaySubnet** enligt nedan.

| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Tillåter lokal trafik till hantering av brandvägg **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Tillåter trafik till backend-undernät som är värd för programserver via **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Tillåter att andra trafik dirigeras via **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Tillåter trafik till **azsn2** till flödet, från app-servern till webbserver via **AZF2** |

Du måste också skapa routningstabeller för undernät i **onpremvnet** att efterlikna lokala datacenter.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Tillåter trafik till **onpremsn2** via **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Tillåter trafik till undernätet för säkerhetskopieras i Azure via **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Tillåter trafik till **onpremsn1** via **OPFW** |

## <a name="ip-forwarding"></a>IP-vidarebefordran
UDR och IP-vidarebefordran är funktioner som kan användas tillsammans för att tillåta virtuella installationer som används för att kontrollera trafikflödet i ett Azure VNet.  En virtuell installation är helt enkelt en VM som kör ett program som används för att hantera nätverkstrafik på något sätt, som en brandvägg eller en NAT-enhet.

Den här virtuella installations-VM:en måste kunna ta emot inkommande trafik som inte är adresserad till den. För att låta en VM ta emot trafik som är adresserad till andra mål, behöver du aktivera IP-vidarebefordran för VM:en. Det är en Azure-inställning, inte en inställning i gästoperativsystemet. Din virtuella installation måste fortfarande köra vissa program för att hantera inkommande trafik och vidarebefordra korrekt.

Läs mer om IP-vidarebefordran [vad är användardefinierade vägar och IP-vidarebefordran](virtual-networks-udr-overview.md).

Ett exempel anta att du har följande inställningar i ett Azure-vnet:

* Undernät **onpremsn1** innehåller en virtuell dator med namnet **onpremvm1**.
* Undernät **onpremsn2** innehåller en virtuell dator med namnet **onpremvm2**.
* En virtuell installation med namnet **OPFW** är ansluten till **onpremsn1** och **onpremsn2**.
* En användardefinierad väg som är kopplad till **onpremsn1** anger att all trafik till **onpremsn2** måste skickas till **OPFW**.

AT som detta pekar, om **onpremvm1** försöker upprätta en anslutning med **onpremvm2**, används UDR och trafik skickas till **OPFW** som nexthop. Tänk på att inte ändras faktiska paketets mål, det är fortfarande **onpremvm2** är målet. 

Utan IP-vidarebefordran aktiverad för **OPFW**, Azure virtuella nätverk logiken släpper paket, eftersom det bara tillåter att paket som skickas till en virtuell dator om den Virtuella datorns IP-adress som är mål för paketet.

Med IP-vidarebefordring vidarebefordrar virtuella Azure-nätverket logiken paketen till OPFW, utan att ändra dess ursprungliga måladressen. **OPFW** måste hantera paketen och ta reda på vad du gör med dem.

För scenariot ovan för att fungera, måste du aktivera IP-vidarebefordring på nätverkskort för **OPFW**, **AZF1**, **AZF2**, och **AZF3** som används för routning (alla NIC förutom de som är kopplad till undernätet för management). 

## <a name="firewall-rules"></a>Brandväggsregler
Enligt beskrivningen ovan, garanterar IP-vidarebefordran endast paket skickas till virtuella installationer. Din enhet måste fortfarande avgöra vad du gör med dessa paket. I scenariot ovan behöver du skapa följande regler i dina installationer:

### <a name="opfw"></a>OPFW
OPFW representerar en lokal enhet som innehåller följande regler:

* **Väg**: All trafik till 10.0.0.0/16 (**azurevnet**) måste skickas via tunnel **ONPREMAZURE**.
* **Principen**: alla dubbelriktad trafik mellan **port2** och **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 representerar en Azure virtuell installation som innehåller följande regler:

* **Principen**: alla dubbelriktad trafik mellan **port1** och **port2**.

### <a name="azf2"></a>AZF2
AZF2 representerar en Azure virtuell installation som innehåller följande regler:

* **Väg**: All trafik till 10.0.0.0/16 (**onpremvnet**) måste skickas till Azure gateway IP-adress (d.v.s. 10.0.0.1) via **port1**.
* **Principen**: alla dubbelriktad trafik mellan **port1** och **port2**.

## <a name="network-security-groups-nsgs"></a>Nätverkssäkerhetsgrupper (NSG: er)
I det här scenariot används inte NSG: er. Du kan dock använda NSG: er för varje undernät som begränsa inkommande och utgående trafik. Du kan exempelvis använda följande NSG-regler till externa FW undernätet.

**Inkommande**

* Tillåt alla TCP-trafik från Internet till port 80 på en virtuell dator i undernätet.
* Neka all annan trafik från Internet.

**Utgående**

* Neka all trafik till Internet.

## <a name="high-level-steps"></a>Hög nivå steg
Följ hög nivå stegen nedan om du vill distribuera det här scenariot.

1. Logga in på Azure-prenumerationen.
2. Om du vill distribuera ett virtuellt nätverk för att efterlikna lokalt nätverk tillhandahåller de resurser som ingår i **ONPREMRG**.
3. Tillhandahåller de resurser som ingår i **AZURERG**.
4. Etablera tunneln från **onpremvnet** till **azurevnet**.
5. När alla resurser etableras, logga in på **onpremvm2** och pinga 10.0.3.101 för att testa anslutningen mellan **onpremsn2** och **azsn3**.

