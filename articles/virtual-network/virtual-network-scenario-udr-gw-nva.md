---
title: Hybridanslutning med nivå 2-program | Microsoft Docs
description: Lär dig hur du distribuerar virtuella installationer och UDR att skapa en programmiljö med flera nivåer i Azure
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
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575567"
---
# <a name="virtual-appliance-scenario"></a>Virtuell installation scenario
Ett vanligt scenario bland större Azure-kund är behovet av att tillhandahålla ett program för två nivåer som är exponerade för Internet, samtidigt som åtkomst till backend-nivån från ett lokalt datacenter. Det här dokumentet vägleder dig genom ett scenario med användardefinierade vägar (UDR), en VPN-Gateway och virtuella nätverksenheter för att distribuera en tvålagers-miljö som uppfyller följande krav:

* Webbprogram måste kunna nås från det offentliga Internet.
* Webbservern som värd för programmet måste kunna få åtkomst till en backend-programserver.
* All trafik från Internet till webbprogrammet måste gå via en virtuell brandväggsinstallation. Den här virtuella installations ska användas för Internet-trafiken.
* All trafik till programservern måste gå via en virtuell brandväggsinstallation. Den här virtuella installations ska användas för tillgång till slut backend-servern, och kommer från det lokala nätverket via en VPN-Gateway.
* Administratörer måste kunna hantera brandväggen virtuella installationer från sina lokala datorer, med hjälp av en tredje brandväggen virtuell installation som uteslutande används för hanteringsändamål.

Det här är ett perimeternätverk (även knowns som DMZ) scenariot med en DMZ och ett skyddat nätverk. Sådant scenario kan skapas i Azure med hjälp av NSG: er, brandvägg virtuella installationer eller en kombination av båda. Tabellen nedan visar några av för- och nackdelar mellan NSG: er och brandväggen virtuella installationer.

|  | Experter | Nackdelar |
| --- | --- | --- |
| NSG |Utan kostnad. <br/>Integreras i Azure RBAC. <br/>Regler kan skapas i Azure Resource Manager-mallar. |Komplexiteten kan variera i större miljöer. |
| Brandvägg |Fullständig kontroll över dataplanet. <br/>Central hantering via brandväggen-konsolen. |Kostnaden för brandväggsinstallation. <br/>Inte integrerat med Azure RBAC. |

Lösningen nedan använder virtuella installationer i brandväggen för att implementera ett perimeternätverk (DMZ) / skyddade scenariot.

## <a name="considerations"></a>Överväganden
Du kan distribuera den miljö som beskrivs ovan i Azure med hjälp av olika funktioner som är tillgängliga idag, enligt följande.

* **Virtuellt nätverk (VNet)**. Ett virtuellt Azure nätverk fungerar på liknande sätt till lokala nätverk och kan segmenteras i en eller flera undernät för att tillhandahålla trafikisolering och inkapsling av problem.
* **Virtuell installation**. Flera partner ger virtuella installationer i Azure Marketplace som kan användas för tre brandväggar som beskrivs ovan. 
* **Användardefinierade vägar (UDR)**. Routningstabeller kan innehålla udr: er som används av Azure-nätverk för att styra flödet av paket i ett virtuellt nätverk. Dessa routningstabeller kan tillämpas på undernät. En av de senaste funktionerna i Azure är möjligheten att använda en routningstabell till GatewaySubnet, ger möjligheten att vidarebefordra all trafik som kommer till Azure VNet från en hybridanslutning till en virtuell installation.
* **IP-vidarebefordring**. Som standard Azure networking motorn vidarebefordra paket till virtuella nätverkskort (NIC) endast om målets IP-adress för paketet matchar NIC IP-adress. Om en UDR definierar att ett paket måste skickas till en viss virtuell installation, skulle därför Azure networking motorn släppa paketets. För att säkerställa att paketet skickas till en virtuell dator (i det här fallet en virtuell installation) som inte är det faktiska målet för paketet, måste du aktivera IP-vidarebefordran för den virtuella installationen.
* **Nätverkssäkerhetsgrupper (NSG)**. Exemplet nedan gör inte användning av NSG: er, men du kan använda NSG: er som tillämpas på undernät och/eller nätverkskort i den här lösningen för att ytterligare filtrera trafik in och ut från dessa undernät och nätverkskort.

![IPv6-anslutning](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

I det här exemplet finns det en prenumeration som innehåller följande:

* 2 resursgrupper, inte visas i diagrammet. 
  * **ONPREMRG**. Innehåller alla resurser som behövs för att simulera ett lokalt nätverk.
  * **AZURERG**. Innehåller alla resurser som krävs för Azure virtual network-miljön. 
* Ett virtuellt nätverk med namnet **onpremvnet** används för att efterlikna en lokala datacenter uppdelat enligt nedan.
  * **onpremsn1**. Undernät som innehåller en virtuell dator (VM) som kör Ubuntu att efterlikna en lokal server.
  * **onpremsn2**. Undernät som innehåller en virtuell dator som kör Ubuntu att efterlikna en lokal dator som används av en administratör.
* Det finns en virtuell brandväggsinstallation med namnet **OPFW** på **onpremvnet** används för att upprätthålla en tunnel till **azurevnet**.
* Ett virtuellt nätverk med namnet **azurevnet** uppdelat enligt nedan.
  * **azsn1**. Extern brandvägg undernät används exklusivt för extern brandvägg. All Internettrafik kommer via det här undernätet. Det här undernätet innehåller endast ett nätverkskort som är länkad till extern brandvägg.
  * **azsn2**. Klientdelens undernät som är värd för en virtuell dator som körs som en webbserver som ska kunna nås från Internet.
  * **azsn3**. Backend-undernät som är värd för en virtuell dator med en backend-programserver som ska användas av frontend-webbserver.
  * **azsn4**. Hanteringsundernätet används uteslutande för att ge åtkomst till alla brandväggen virtuella installationer. Det här undernätet innehåller endast ett nätverkskort för varje virtuell brandväggsinstallation som används i lösningen.
  * **GatewaySubnet**. Azure hybrid-anslutning undernätet krävs för ExpressRoute och VPN-Gateway för anslutningar mellan virtuella Azure-nätverk och andra nätverk. 
* Det finns 3 brandväggen virtuella installationer i den **azurevnet** nätverk. 
  * **AZF1**. Extern brandvägg som är exponerade för det offentliga Internet med hjälp av en offentlig IP-adressresurs i Azure. Du måste se till att du har en mall från Marketplace, eller direkt från din leverantören av enheten, som tillhandahåller en 3-NIC-installation.
  * **AZF2**. Intern brandvägg som används för att kontrollera trafiken mellan **azsn2** och **azsn3**. Det är också en 3-NIC-installation.
  * **AZF3**. Management-brandväggen är tillgängliga för administratörer från det lokala datacentret och ansluten till ett hanteringsundernät som används för att hantera alla brandväggsinstallationer. Du kan hitta 2-NIC virtuell installation mallar i Marketplace eller begära en direkt från leverantören av enheten.

## <a name="user-defined-routing-udr"></a>Användardefinierad routning (UDR)
Varje undernät i Azure kan länkas till en UDR-tabell som används för att definiera hur trafiken initieras i att undernätet dirigeras. Om inga udr: er har definierats använder Azure standardvägar för att tillåta trafik från ett undernät till en annan. För att bättre förstå udr: er, besök [vad är användardefinierade vägar och IP-vidarebefordring](virtual-networks-udr-overview.md).

Om du vill se till att kommunikationen sker via rätt brandväggsinstallation, baserat på senaste kravet ovan, måste du skapa följande routningstabellen som innehåller udr: er i **azurevnet**.

### <a name="azgwudr"></a>azgwudr
I det här scenariot endast trafik som flödar från en lokal plats till Azure används för att hantera andra genom att ansluta till **AZF3**, och att trafik måste gå igenom den interna brandväggen **AZF2**. Därför bara en väg är nödvändigt i den **GatewaySubnet** enligt nedan.

| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Tillåter lokal trafik nå management brandväggen **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Tillåter trafik till backend-undernät som är värd för programserver via **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Gör att all trafik dirigeras via **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Mål | Nästa hopp | Förklaring |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Tillåter trafik till **azsn2** till flow från appservern till webbservern via **AZF2** |

Du måste också skapa routningstabeller för undernäten i **onpremvnet** att efterlikna datacenter på plats.

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
UDR och IP-vidarebefordran är funktioner som du kan använda tillsammans att virtuella enheter som används för att kontrollera trafikflödet i ett virtuellt Azure nätverk.  En virtuell installation är helt enkelt en VM som kör ett program som används för att hantera nätverkstrafik på något sätt, som en brandvägg eller en NAT-enhet.

Den här virtuella installations-VM:en måste kunna ta emot inkommande trafik som inte är adresserad till den. För att låta en VM ta emot trafik som är adresserad till andra mål, behöver du aktivera IP-vidarebefordran för VM:en. Det är en Azure-inställning, inte en inställning i gästoperativsystemet. Din virtuella installation måste fortfarande köra någon typ av program för att hantera inkommande trafik och dirigerar den på rätt sätt.

Läs mer om IP-vidarebefordran [vad är användardefinierade vägar och IP-vidarebefordring](virtual-networks-udr-overview.md).

Till exempel anta att du har följande inställningar i ett Azure vnet:

* Undernät **onpremsn1** innehåller en virtuell dator med namnet **onpremvm1**.
* Undernät **onpremsn2** innehåller en virtuell dator med namnet **onpremvm2**.
* En virtuell installation som heter **OPFW** är ansluten till **onpremsn1** och **onpremsn2**.
* En användardefinierad väg som är länkad till **onpremsn1** anger att all trafik till **onpremsn2** måste skickas till **OPFW**.

AT som detta peka, om **onpremvm1** försöker upprätta en anslutning med **onpremvm2**, den användardefinierade vägen används och trafik skickas till **OPFW** som nästa hopp. Tänk på att inte ändras faktiska paketets mål, det är fortfarande **onpremvm2** är målet. 

Utan IP-vidarebefordring aktiverad för **OPFW**, Azure virtuella nätverk logiken ignorerar paketen, eftersom det tillåter bara paket som ska skickas till en virtuell dator om den Virtuella datorns IP-adress är målet för paketet.

Med IP-vidarebefordran vidarebefordrar Azure-nätverk logiken paketen till OPFW, utan att ändra dess ursprungliga måladress. **OPFW** måste hantera paketen och avgöra vad du gör med dem.

För scenariot ovan för att fungera, måste du aktivera IP-vidarebefordran på nätverkskort för **OPFW**, **AZF1**, **AZF2**, och **AZF3** som används för routning (alla nätverkskort förutom de som är länkad till hanteringsundernätet). 

## <a name="firewall-rules"></a>Brandväggsregler
Enligt beskrivningen ovan, säkerställer IP-vidarebefordran endast paket skickas till de virtuella installationer. Din installation måste fortfarande avgöra vad du gör med dessa paket. I scenariot ovan behöver du skapa följande regler i dina installationer:

### <a name="opfw"></a>OPFW
OPFW representerar en lokal enhet som innehåller följande regler:

* **väg**: All trafik till 10.0.0.0/16 (**azurevnet**) måste skickas via tunnel **ONPREMAZURE**.
* **Principen**: Tillåt all dubbelriktad trafik mellan **port2** och **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 representerar en Azure virtuell installation som innehåller följande regler:

* **Principen**: Tillåt all dubbelriktad trafik mellan **port1** och **port2**.

### <a name="azf2"></a>AZF2
AZF2 representerar en Azure virtuell installation som innehåller följande regler:

* **väg**: All trafik till 10.0.0.0/16 (**onpremvnet**) måste skickas till Azure-gatewayen IP-adress (t.ex. 10.0.0.1) via **port1**.
* **Principen**: Tillåt all dubbelriktad trafik mellan **port1** och **port2**.

## <a name="network-security-groups-nsgs"></a>Nätverkssäkerhetsgrupper (NSG)
I det här scenariot används inte NSG: er. Du kan dock använda Nätverkssäkerhetsgrupper till varje undernät att begränsa inkommande och utgående trafik. Du kan exempelvis använda följande NSG-reglerna för det externa VB-undernätet.

**inkommande**

* Tillåt alla TCP-trafik från Internet till port 80 på alla virtuella datorer i undernätet.
* Neka all annan trafik från Internet.

**Utgående**

* Neka all trafik till Internet.

## <a name="high-level-steps"></a>Hög steg
Följ de viktigaste stegen nedan om du vill distribuera det här scenariot.

1. Logga in på din Azure-prenumeration.
2. Om du vill distribuera ett virtuellt nätverk för att efterlikna det lokala nätverket etablera resurser som är en del av **ONPREMRG**.
3. Etablera resurser som är en del av **AZURERG**.
4. Etablera tunneln från **onpremvnet** till **azurevnet**.
5. När alla resurser etableras, logga in på **onpremvm2** och pinga 10.0.3.101 för att testa anslutningen mellan **onpremsn2** och **azsn3**.

