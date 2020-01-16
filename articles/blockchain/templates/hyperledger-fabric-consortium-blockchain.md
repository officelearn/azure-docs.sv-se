---
title: Distribuera en lösnings mal len för huvud boks infrastrukturer i Azure
description: Så här distribuerar och konfigurerar du mallen för den här nätverks lösningen för huvud boks infrastrukturs konsortiet i Azure
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: caleteet
ms.openlocfilehash: 3e7dcd3cdcfa636c0b23ac6643bd7732e7f8ada0
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029156"
---
# <a name="hyperledger-fabric-consortium-network"></a>Nätverk för redovisningssaldo i huvud boken

Du kan använda lösnings mal len för den här mallen för att distribuera och konfigurera ett nätverk för en huvud boks infrastruktur i Azure.

> [!IMPORTANT]
> [Mikroredovisningen på Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-hyperledger-fabric) -mallen är föråldrad. Använd [huvud strukturen i Azure Kubernetes-tjänsten](hyperledger-fabric-consortium-azure-kubernetes-service.md) i stället.  

När du har läst den här artikeln, kommer du att:

- Få kunskaper om blockchain, redovisnings infrastruktur resurser och mer komplicerade konsortier nätverks arkitekturer
- Lär dig hur du distribuerar och konfigurerar ett huvud nätverk i ett huvud nätverk från Azure Portal

## <a name="about-blockchain"></a>Om blockchain

Om du är nybörjare på blockchain community är den här lösnings mal len en bra möjlighet att lära dig mer om tekniken på ett enkelt och konfigurerbart sätt i Azure. Blockchain är den underliggande tekniken bakom Bitcoin; Det är dock mycket mer än bara en aktivering för en virtuell valuta. Det är en sammansatt uppsättning av befintliga databaser, distribuerade system och kryptografiska tekniker som möjliggör säker beräkning av flera parter med garantier kring oföränderlighets, Verifiability, gransknings möjlighet och återhämtning till angrepp. Olika protokoll använder olika mekanismer för att tillhandahålla dessa attribut. [Ledger](https://github.com/hyperledger/fabric) är ett sådant protokoll.

## <a name="consortium-architecture-on-azure"></a>Konsortiets arkitektur på Azure

Det finns två primära distributions typer som stöds för att aktivera huvud strukturs infrastruktur resurser i Azure. Dessa distributioner är utformade för att hantera olika topologier, baserat på önskat mål.

- **Enskild virtuell dator, utvecklings Server** – den här distributions typen är utformad som en utvecklings miljö som används för att skapa och testa lösningar som bygger på huvud Plans infrastruktur.
- **Flera virtuella datorer, skala ut distribution** – den här distributions typen är utformad för miljöer som modeller ett konsortium av olika deltagare som utnyttjar en delad miljö.

I båda distributionerna är de bygg stenar som är kärnan i den inbyggda infrastrukturen i huvud gruppen samma.  Skillnaderna i distributionerna är hur dessa komponenter skalas ut.

- **Ca-noder**: en nod som kör certifikat utfärdare som används för att generera certifikat som används för identiteter i nätverket.
- **Beställnings noder**: en nod som kör kommunikations tjänsten och implementerar en leverans garanti, t. ex. total order sändning eller atomiska transaktioner.
- **Peer-noder**: en nod som genomför transaktioner och underhåller status och en kopia av den distribuerade redovisningen.
- **Couchdb-noder**: en nod som kan köra couchdb-tjänsten som kan lagra tillstånds databasen och skicka frågor till chaincode-data, och utöka från enkel nyckel/värde till JSON-typ lagring.

### <a name="single-virtual-machine-architecture"></a>Arkitektur för enskild virtuell dator

Som tidigare nämnts är arkitekturen för en enskild virtuell dator utformad för utvecklare att ha en server med låg utrymme som används för att utveckla program. Alla behållare som visas körs på en enda virtuell dator. Beställnings tjänsten använder [Solo](https://github.com/hyperledger/fabric/tree/master/orderer) för den här konfigurationen. Den här konfigurationen är *inte* en feltolerant sorterings tjänst, men är utformad för att vara lätt att förstå i utvecklings syfte.

![Arkitektur för enskild virtuell dator](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Arkitektur för flera virtuella datorer

Den virtuella datorn, skalbar arkitektur, är byggd med hög tillgänglighet och skalning av varje komponent i kärnan. Den här arkitekturen är mycket lämpligare för distributioner av produktions klasser.

![Arkitektur för flera virtuella datorer](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Komma igång

För att börja måste du ha en Azure-prenumeration som har stöd för distribution av flera virtuella datorer och standard lagrings konton. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

När du har en prenumeration går du till [Azure Portal](https://portal.azure.com). Välj **skapa en resurs > Blockchain > Ledger Fabric Consortium**.

![Mall för en enskild medlem blockchain Marketplace för huvud boks infrastruktur](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Distribution

I mallen för den inbyggda mallen för **huvud boken i Fabric** väljer du **skapa**.

I mallen för mallar går vi igenom hur du konfigurerar ett nätverk med flera noder i [huvud boken 1,3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) . Distributions flödet är uppdelat i fyra steg: grundläggande, inställningar för konsortiet nätverk, infrastruktur konfiguration och valfria komponenter.

### <a name="basics"></a>Grundläggande inställningar

I **grunderna**anger du värden för standard parametrar för alla distributioner. Till exempel, prenumeration, resurs grupp och grundläggande egenskaper för virtuella datorer.

![Grundläggande inställningar](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Parameternamn | Beskrivning | Tillåtna värden |
|---|---|---|
**Resource prefix** | Namn prefix för resurser som har allokerats som en del av distributionen |6 tecken eller mindre |
**Användarnamn** | Användar namnet för administratören för var och en av de virtuella datorer som har distribuerats för den här medlemmen |1-64 tecken |
**Autentiseringstyp** | Metoden för att autentisera till den virtuella datorn |Lösen ord eller offentlig SSH-nyckel|
**Lösen ord (autentiseringstyp = lösen ord)** |Lösen ordet för administratörs kontot för var och en av de virtuella datorerna som distribueras. Lösen ordet måste innehålla tre av följande Character-typer: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken<br /><br />Även om alla virtuella datorer ursprungligen har samma lösen ord, kan du ändra lösen ordet efter etableringen|12-72 tecken|
**SSH-nyckel (autentiseringstyp = Offentlig SSH-nyckel)** |Den säkra shell-nyckel som används för fjärrinloggning ||
**Prenumeration** |Prenumerationen som ska distribueras ||
**Resursgrupp** |Den resurs grupp som konsortiet-nätverket ska distribueras till ||
**Plats** |Den Azure-region som den första medlemmen ska distribueras till ||

Välj **OK**.

### <a name="consortium-network-settings"></a>Nätverks inställningar för konsortiet

I **nätverks inställningar**anger du indata för att skapa eller ansluta till ett befintligt konsortium-nätverk och konfigurera organisations inställningarna.

![Nätverks inställningar för konsortiet](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Parameternamn | Beskrivning | Tillåtna värden |
|---|---|---|
**Nätverkskonfiguration** |Du kan välja att skapa ett nytt nätverk eller ansluta ett befintligt. Om du väljer *Anslut befintliga*måste du ange ytterligare värden. |Nytt nätverk <br/> Anslut till befintlig |
**HLF CA-lösenord** |Ett lösen ord som används för de certifikat som genereras av certifikat utfärdarna som skapas som en del av distributionen. Lösen ordet måste innehålla tre av följande Character-typer: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken.<br /><br />Även om alla virtuella datorer ursprungligen har samma lösen ord, kan du ändra lösen ordet efter etableringen.|1-25 tecken |
**Organisations konfiguration** |Du kan anpassa din organisations namn och certifikat eller ha standardvärden som ska användas.|Default <br/> Advanced |
**VPN-nätverks inställningar** | Etablera en VPN-tunnel-Gateway för åtkomst till de virtuella datorerna | Ja <br/> Inga |

Välj **OK**.

### <a name="fabric-specific-settings"></a>Fabric-/regionsspecifika inställningar

I **infrastruktur konfiguration**konfigurerar du nätverks storlek och prestanda och anger indata för nätverks tillgängligheten. Till exempel, Number beställare och peer-noder, beständig motor som används av varje nod och storleken på den virtuella datorn.

![Infrastruktur inställningar](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Parameternamn | Beskrivning | Tillåtna värden |
|---|---|---|
**Skalnings typ** |Distributions typen för en enskild virtuell dator med flera behållare eller flera virtuella datorer i en skalbar modell.|Enskild virtuell dator eller flera virtuella datorer |
**Typ av virtuell dator disk** |Typ av lagring som används för att säkerhetskopiera var och en av de distribuerade noderna. <br/> Om du vill veta mer om tillgängliga disk typer går du till [Välj typ av disk](../../virtual-machines/windows/disks-types.md).|Standard SSD <br/> Premium SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>Distribution av flera virtuella datorer (ytterligare inställningar)

![Infrastruktur inställningar för flera VM-distributioner](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Parameternamn | Beskrivning | Tillåtna värden |
|---|---|---|
**Antal beställnings noder** |Antalet noder som beställer (strukturerar) transaktioner till ett block. <br />Mer information om beställnings tjänsten finns i den detaljerade [dokumentationen](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) |1 – 4 |
**Storlek på virtuell dator i beställnings nod** |Storleken på den virtuella datorn som används för beställnings noder i nätverket|Standard BS,<br />Standard DS,<br />Standard FS |
**Antal peer-noder** | Noder som ägs av konsortiet medlemmar som utför transaktioner och som upprätthåller tillstånd och en kopia av redovisningen.<br />Mer information om beställnings tjänsten finns i den detaljerade [dokumentationen](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|1 – 4 |
**Persistence för Node-tillstånd** |Den beständiga motor som används av peer-noderna. Du kan konfigurera den här motorn per peer-nod. Se informationen nedan för flera peer-noder.|CouchDB <br />LevelDB |
**Storlek på virtuell dator för peer-nod** |Storleken på den virtuella datorn som används för alla noder i nätverket|Standard BS,<br />Standard DS,<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Konfiguration av flera peer-noder

Med den här mallen kan du välja en beständig motor per peer-nod. Om du till exempel har tre peer-noder kan du använda CouchDB på en och LevelDB på de andra två.

![Konfiguration av flera peer-noder](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Välj **OK**.

### <a name="deploy"></a>Distribuera

I **Sammanfattning**granskar du de indata som angetts och kör grundläggande verifiering före distribution.

![Sammanfattning](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Granska juridiska och sekretess villkor och välj **köp** att distribuera. Beroende på hur många virtuella datorer som har allokerats kan distributions tiden variera från några minuter till flera minuter.

## <a name="next-steps"></a>Nästa steg

Du är nu redo att fokusera på program-och chaincode utveckling mot ditt blockchain-nätverk i.
