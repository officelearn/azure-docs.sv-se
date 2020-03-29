---
title: Hybridanslutning med tvåstegsapplikation | Microsoft-dokument
description: Lär dig hur du distribuerar virtuella installationer och UDR för att skapa en programmiljö på flera nivåer i Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 1bdc485dfb352144e8a8d0fb75965cbb78288e2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64575567"
---
# <a name="virtual-appliance-scenario"></a>Scenario för virtuell installation
Ett vanligt scenario bland större Azure-kunder är behovet av att tillhandahålla ett tvådelat program som exponeras för Internet, samtidigt som åtkomst till backnivån från ett lokalt datacenter tillåts. Det här dokumentet hjälper dig att gå igenom ett scenario med hjälp av ANVÄNDARDEFINIERAde vägar (UDR), en VPN-gateway och virtuella nätverksinstallationer för att distribuera en miljö på två nivåer som uppfyller följande krav:

* Webbapplikation får endast vara tillgängligt från det offentliga Internet.
* Webbserver som är värd för programmet måste kunna komma åt en server för serverdelsprogram.
* All trafik från Internet till webbprogrammet måste gå igenom en virtuell brandväggsinstallation. Den här virtuella installationen kommer endast att användas för Internet-trafik.
* All trafik som går till programservern måste gå igenom en virtuell brandvägg. Den här virtuella installationen kommer att användas för åtkomst till serverdelsservern och åtkomst som kommer in från det lokala nätverket via en VPN-gateway.
* Administratörer måste kunna hantera brandväggen virtuella apparater från sina lokala datorer, med hjälp av en tredje brandvägg virtuell installation som används uteslutande för hantering.

Detta är ett standard perimeternätverk (även känt som DMZ) scenario med en DMZ och ett skyddat nätverk. Ett sådant scenario kan konstrueras i Azure med hjälp av NSGs, virtuella brandväggsinstallationer eller en kombination av båda. Tabellen nedan visar några av för-och nackdelar mellan NSGs och brandvägg virtuella apparater.

|  | Fördelar | Nackdelar |
| --- | --- | --- |
| NSG |Ingen kostnad. <br/>Integrerad i Azure RBAC. <br/>Regler kan skapas i Azure Resource Manager-mallar. |Komplexiteten kan variera i större miljöer. |
| Brandvägg |Full kontroll över dataplanet. <br/>Central hantering via brandväggskonsol. |Kostnad för brandväggsapparat. <br/>Inte integrerat med Azure RBAC. |

Lösningen nedan använder virtuella brandväggsinstallationer för att implementera ett perimeternätverk (DMZ)/skyddat nätverksscenario.

## <a name="considerations"></a>Överväganden
Du kan distribuera miljön som beskrivs ovan i Azure med hjälp av olika funktioner som är tillgängliga idag, enligt följande.

* **Virtuellt nätverk (VNet)**. Ett Azure-nätverk fungerar på liknande sätt som ett lokalt nätverk och kan segmenteras i ett eller flera undernät för att tillhandahålla trafikisolering och separation av problem.
* **Virtuell apparat**. Flera partner tillhandahåller virtuella enheter på Azure Marketplace som kan användas för de tre brandväggar som beskrivs ovan. 
* **Användardefinierade vägar (UDR)**. Vägtabeller kan innehålla UDR:er som används av Azure-nätverk för att styra flödet av paket i ett virtuella nätverk. Dessa flödestabeller kan tillämpas på undernät. En av de senaste funktionerna i Azure är möjligheten att tillämpa en vägtabell på GatewaySubnet, vilket ger möjlighet att vidarebefordra all trafik som kommer till Azure VNet från en hybridanslutning till en virtuell installation.
* **IP-vidarebefordran**. Som standard vidarebefordrar Azure-nätverksmotorn paket till virtuella nätverkskort (NIC) endast om IP-adressen för paketmål matchar IP-adressen för nätverkskortet. Om en UDR definierar att ett paket måste skickas till en viss virtuell installation, skulle Azure-nätverksmotorn släppa det paketet. För att säkerställa att paketet levereras till en virtuell dator (i det här fallet en virtuell installation) som inte är det faktiska målet för paketet, måste du aktivera IP-vidarebefordran för den virtuella installationen.
* **Nätverkssäkerhetsgrupper (NSG)**. Exemplet nedan använder inte NSG: er, men du kan använda NSG som tillämpas på undernäten och/eller nätverkskorten i den här lösningen för att ytterligare filtrera trafiken till och från dessa undernät och nätverkskort.

![IPv6-anslutning](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

I det här exemplet finns det en prenumeration som innehåller följande:

* 2 resursgrupper som inte visas i diagrammet. 
  * **ONPREMRG**. Innehåller alla resurser som behövs för att simulera ett lokalt nätverk.
  * **AZURERG**. Innehåller alla resurser som behövs för den virtuella Azure-nätverksmiljön. 
* Ett virtuella nätverk med namnet **onpremvnet** som används för att efterlikna ett lokalt datacenter som är segmenterat enligt nedan.
  * **onpremsn1**. Undernät som innehåller en virtuell dator (VM) som kör Ubuntu för att efterlikna en lokal server.
  * **onpremsn2**. Undernät som innehåller en virtuell dator som kör Ubuntu för att efterlikna en lokal dator som används av en administratör.
* Det finns en virtuell brandvägg som heter **OPFW** **på onpremvnet** som används för att underhålla en tunnel till **azurevnet**.
* Ett virtuella nätverk med namnet **azurevnet** segmenterat enligt listan nedan.
  * **azsn1**. Externt brandväggsundernät som endast används för den externa brandväggen. All Internettrafik kommer in genom detta undernät. Det här undernätet innehåller bara ett nätverkskort som är kopplat till den externa brandväggen.
  * **azsn2**. Front end-undernät som är värd för en virtuell dator som körs som en webbserver som kommer att nås från Internet.
  * **azsn3**. Serverdelsundernät som är värd för en virtuell dator som kör en serverdelsprogramserver som kommer att nås av klientservern.
  * **azsn4**. Hanteringsundernät som endast används för att ge hanteringsåtkomst till alla virtuella brandväggsinstallationer. Det här undernätet innehåller bara ett nätverkskort för varje virtuell brandväggsinstallation som används i lösningen.
  * **GatewaySubnet**. Azure hybridanslutningsundernät krävs för ExpressRoute och VPN Gateway för att tillhandahålla anslutning mellan Virtuella Azure-nätverk och andra nätverk. 
* Det finns 3 virtuella brandväggsinstallationer i **azurevnet-nätverket.** 
  * **AZF1**. Extern brandvägg som exponeras för det offentliga Internet med hjälp av en offentlig IP-adressresurs i Azure. Du måste se till att du har en mall från Marketplace, eller direkt från din apparatleverantör, som innehåller en virtuell 3-NIC-apparat.
  * **AZF2**. Intern brandvägg som används för att styra trafiken mellan **azsn2** och **azsn3**. Detta är också en 3-NIC virtuell apparat.
  * **AZF3**. Hanteringsbrandvägg som är tillgänglig för administratörer från det lokala datacentret och som är ansluten till ett hanteringsundernät som används för att hantera alla brandväggsinstallationer. Du kan hitta mallar för virtuella 2 NIC-enheter på Marketplace eller begära en direkt från din apparatleverantör.

## <a name="user-defined-routing-udr"></a>Användardefinierad routning (UDR)
Varje undernät i Azure kan länkas till en UDR-tabell som används för att definiera hur trafik som initieras i det undernätet dirigeras. Om inga UDR-enheter har definierats använder Azure standardvägar för att tillåta trafik att flöda från ett undernät till ett annat. Om du vill bättre förstå UDR:er besöker du [Vilka är användardefinierade vägar och IP-vidarebefordran](virtual-networks-udr-overview.md).

För att säkerställa att kommunikation sker via rätt brandväggsinstallation, baserat på det senaste kravet ovan, måste du skapa följande vägtabell som innehåller UDR:er i **azurevnet**.

### <a name="azgwudr"></a>azgwudr (
I det här fallet kommer den enda trafiken som flödar från lokalt till Azure att användas för att hantera brandväggarna genom att ansluta till **AZF3**och att trafiken måste gå igenom den interna brandväggen, **AZF2**. Därför är endast en väg nödvändig i **GatewaySubnet** som visas nedan.

| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Tillåter lokal trafik för att nå hanteringsbrandväggen **AZF3** |

### <a name="azsn2udr"></a>azsn2udr (
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Tillåter trafik till serverdelsundernätet som är värd för programservern via **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Gör att all annan trafik kan dirigeras via **AZF1** |

### <a name="azsn3udr"></a>azsn3udr (
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Tillåter trafik till **azsn2** att flöda från appserver till webbservern via **AZF2** |

Du måste också skapa flödestabeller för undernäten i **onpremvnet** för att efterlikna det lokala datacentret.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Tillåter trafik till **onpremsn2** via **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Tillåter trafik till det säkerhetskopierade undernätet i Azure via **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Tillåter trafik till **onpremsn1** via **OPFW** |

## <a name="ip-forwarding"></a>IP-vidarebefordran
UDR- och IP-vidarebefordran är funktioner som du kan använda i kombination för att tillåta virtuella installationer som ska användas för att styra trafikflödet i ett Azure-virtuellt nätverk.  En virtuell installation är helt enkelt en VM som kör ett program som används för att hantera nätverkstrafik på något sätt, som en brandvägg eller en NAT-enhet.

Den här virtuella installations-VM:en måste kunna ta emot inkommande trafik som inte är adresserad till den. För att låta en VM ta emot trafik som är adresserad till andra mål, behöver du aktivera IP-vidarebefordran för VM:en. Det är en Azure-inställning, inte en inställning i gästoperativsystemet. Den virtuella installationen måste fortfarande köra någon typ av program för att hantera inkommande trafik och dirigera den på rätt sätt.

Mer information om IP-vidarebefordran finns i [Vilka är användardefinierade rutter och IP-vidarebefordran](virtual-networks-udr-overview.md).

Anta att du har följande inställningar i ett Azure-nätverk:

* Undernät **påpremsn1** innehåller en virtuell dator med namnet **onpremvm1**.
* Undernät **onpremsn2** innehåller en virtuell dator med namnet **onpremvm2**.
* En virtuell apparat med namnet **OPFW** är ansluten till **onpremsn1** och **onpremsn2**.
* En användardefinierad väg som är länkad till **onpremsn1** anger att all trafik till **onpremsn2** måste skickas till **OPFW**.

Vid denna punkt, om **onpremvm1** försöker upprätta en anslutning med **onpremvm2,** UDR kommer att användas och trafiken kommer att skickas till **OPFW** som nästa hopp. Tänk på att det faktiska paketmålet inte ändras, det står fortfarande **att onpremvm2** är målet. 

Utan IP-vidarebefordran aktiverad för **OPFW**släpper Azure virtuell nätverkslogik paketen, eftersom det bara tillåter att paket skickas till en virtuell dator om den virtuella datorns IP-adress är målet för paketet.

Med IP-vidarebefordran vidarebefordrar Azure-logiken för virtuella nätverk till OPFW, utan att ändra dess ursprungliga måladress. **OPFW** måste hantera paketen och bestämma vad de ska göra med dem.

För att scenariot ovan ska fungera måste du aktivera IP-vidarebefordran på nätverkskorten för **OPFW,** **AZF1,** **AZF2**och **AZF3** som används för routning (alla nätverkskort utom de som är kopplade till hanteringsundernätet). 

## <a name="firewall-rules"></a>Brandväggsregler
Som beskrivits ovan säkerställer IP-vidarebefordran endast att paket skickas till de virtuella apparaterna. Din apparat måste fortfarande bestämma vad du ska göra med dessa paket. I scenariot ovan måste du skapa följande regler i dina apparater:

### <a name="opfw"></a>OPFW (PÅ VÄG)
OPFW representerar en lokal enhet som innehåller följande regler:

* **Rutt**: All trafik till 10.0.0.0/16 **(azurevnet)** måste skickas via tunnel **ONPREMAZURE**.
* **Policy**: Tillåt all dubbelriktad trafik mellan **port2** och **ONPREMAZURE**.

### <a name="azf1"></a>AZF1 (PÅ ANDRA)
AZF1 representerar en virtuell Azure-installation som innehåller följande regler:

* **Princip**: Tillåt all dubbelriktad trafik mellan **port1** och **port2**.

### <a name="azf2"></a>AZF2 (PÅ ANDRA)
AZF2 representerar en virtuell Azure-installation som innehåller följande regler:

* **Rutt**: All trafik till 10.0.0.0/16 **(onpremvnet)** måste skickas till AZURE gateway IP-adressen (dvs. 10.0.0.1) via **port1**.
* **Princip**: Tillåt all dubbelriktad trafik mellan **port1** och **port2**.

## <a name="network-security-groups-nsgs"></a>Nätverkssäkerhetsgrupper (NSG)
I det här fallet används inte NSG:er. Du kan dock använda NSG:er på varje undernät för att begränsa inkommande och utgående trafik. Du kan till exempel tillämpa följande NSG-regler på det externa FW-undernätet.

**Inkommande**

* Tillåt all TCP-trafik från Internet till port 80 på valfri virtuell dator i undernätet.
* Neka all annan trafik från Internet.

**Utgående**

* Neka all trafik till Internet.

## <a name="high-level-steps"></a>Steg på hög nivå
Om du vill distribuera det här scenariot följer du stegen på hög nivå nedan.

1. Logga in på din Azure-prenumeration.
2. Om du vill distribuera ett virtuella nätverk för att efterlikna det lokala nätverket etablerar du de resurser som ingår i **ONPREMRG**.
3. Etablera de resurser som ingår i **AZURERG**.
4. Etablera tunneln från **onpremvnet** till **azurevnet**.
5. När alla resurser har etablerats loggar du in **på onpremvm2** och pingar 10.0.3.101 för att testa anslutningen mellan **onpremsn2** och **azsn3**.

