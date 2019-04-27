---
title: Hyperledger Fabric Konsortienätverk på Azure
description: Lösningsmallen för att distribuera och konfigurera ett Hyperledger Fabric konsortienätverk
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/23/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: caleteet
manager: femila
ms.openlocfilehash: ce1afbd5499e798888e77f52d7b652e7e1f548fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690656"
---
# <a name="hyperledger-fabric-consortium-network"></a>Hyperledger Fabric konsortienätverk

Du kan använda Hyperledger Fabric consortium lösningsmallen för att distribuera och konfigurera ett Hyperledger Fabric konsortienätverk på Azure.

När du har läst den här artikeln, kommer du att:

- Få kunskaper om blockchain, Hyperledger Fabric och mer komplicerad consortium network arkitekturer
- Lär dig hur du distribuerar och konfigurerar ett Hyperledger Fabric konsortienätverk från Azure Portal

## <a name="about-blockchain"></a>Om blockchain

Om du är nybörjare på blockchain-communityn är i den här lösningsmallen en fantastisk möjlighet att lära dig om tekniken på ett enkelt och konfigurerbara sätt på Azure. Blockchain är den underliggande tekniken bakom Bitcoin; Det är dock mycket mer än bara ett verktyg för en virtuell valuta. Det är en kombination av befintlig databas, distribuerade system och kryptografiska tekniker som möjliggör säker flerparti beräkning med garantier kring oföränderlighetsprincip, verifiability, revision och återhämtning för angrepp. Olika protokoll använder olika sätt att tillhandahålla dessa attribut. [Hyperledger Fabric](https://github.com/hyperledger/fabric) är ett protokoll.

## <a name="consortium-architecture-on-azure"></a>Consortium arkitektur på Azure

Om du vill aktivera Hyperledger Fabric i Azure, finns det två primära distributionstyper som stöds. Dessa distributioner är utformade för att hantera olika topologier, baserat på önskat mål.

- **Virtuell dator, developer server** -den här distributionstypen är utformad som en utvecklingsmiljö som används för att skapa och testa lösningar som bygger på Hyperledger Fabric.
- **Flera virtuella datorer och skala ut distribution** – den här distributionstypen är avsedd för miljöer som modellera ett konsortium av olika deltagare att använda en delad miljö.

Antingen distribution är de olika byggstenarna är kärnan i Hyperledger Fabric samma.  Skillnaderna i distributionen är hur dessa komponenter skalas ut.

- **CA-noder**: En nod som kör certifikatutfärdaren som används för att generera certifikat som används för identiteter i nätverket.
- **Orderer noder**: En nod som kör kommunikationstjänsten implementera garanterad leverans, till exempel total ordning sändning eller atomiska transaktioner.
- **Peer-noder**: En nod som genomför transaktioner och underhåller tillståndet och en kopia av det distribuerade transaktionsregister.
- **CouchDB noder**: En nod som kan köra tjänsten CouchDB som kan innehålla tillstånd-databasen och ger många frågealternativ chaincode data, utöka från enkla nyckel/värde till JSON typ lagring.

### <a name="single-virtual-machine-architecture"></a>Arkitektur för virtuell dator

Som tidigare nämnts enda virtuella har datorarkitektur utformats för utvecklare att ha en små utrymmeskrav-server som används för att utveckla program. Alla behållare som visas som körs i en enda virtuell dator. Med hjälp av tjänsten skrivordning [SOLO](https://github.com/hyperledger/fabric/tree/master/orderer) för den här konfigurationen. Den här konfigurationen är *inte* en fel feltoleranta sortering tjänsten, men är utformat för att vara lätt för utvecklingsändamål.

![Enkel arkitektur för virtuell dator](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Flera arkitektur för virtuell dator

Flera VM, skalbar arkitektur är byggda med hög tillgänglighet och skalning av varje komponent i kärna. Den här arkitekturen är mycket mer lämplig för distributioner av produktion i företagsklass.

![Flera arkitektur för virtuell dator](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Komma igång

Om du vill börja, måste en Azure-prenumeration som har stöd för distribution av flera virtuella datorer och lagringskonton av standardtyp. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/).

När du har en prenumeration går du till den [Azure-portalen](https://portal.azure.com). Välj **skapa en resurs > Blockchain > Hyperledger Fabric Consortium**.

![Hyperledger Fabric enskild medlem Blockchain Marketplace-mall](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Distribution

I den **Hyperledger Fabric Consortium** mall, väljer **skapa**.

Malldistributionen vägleder dig genom konfigurationen med flera noder [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) nätverk. Distributionsflödet är uppdelad i fyra steg: Grunderna, Consortium nätverksinställningar, infrastrukturresurskonfigurationen och valfria komponenter.

### <a name="basics"></a>Grundläggande inställningar

I **grunderna**, ange värden för standard parametrar för alla distributioner. Prenumeration, resursgrupp och grundläggande virtuell dator som till exempel, egenskaper.

![Grundläggande inställningar](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Parameternamn | Beskrivning | Tillåtna värden |
|---|---|---|
**Resurs-prefix** | Namnprefixet för resurser som etablerats som en del av distributionen |6 tecken eller mindre |
**Användarnamn** | Användarnamn för administratören för var och en av de virtuella datorerna som distribueras för den här medlemmen |1 – 64 tecken |
**Autentiseringstyp** | Metod för att verifiera att den virtuella datorn |Lösenordet eller SSH offentlig nyckel|
**Lösenord (autentiseringstyp = lösenord)** |Lösenordet för administratörskontot för var och en av de virtuella datorerna som distribueras. Lösenordet måste innehålla tre av följande tecken: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken<br /><br />Även om alla virtuella datorer har ursprungligen samma lösenord, kan du ändra lösenordet efter etablering|12 – 72 tecken|
**SSH-nyckel (autentiseringstyp = offentlig SSH-nyckel)** |SSH-nyckel som används för fjärrinloggning ||
**Prenumeration** |Den prenumeration som ska distribueras ||
**Resursgrupp** |Resursgruppen som ska distribueras consortium network ||
**Plats** |Azure-region som ska distribueras den första medlemmen i ||

Välj **OK**.

### <a name="consortium-network-settings"></a>Consortium nätverksinställningar

I **nätverksinställningar**, ange indata för att skapa eller koppla en befintlig consortium network och konfigurera organisationsinställningar för din.

![Consortium nätverksinställningar](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Parameternamn | Beskrivning | Tillåtna värden |
|---|---|---|
**Nätverkskonfiguration** |Du kan välja att skapa ett nytt nätverk eller Anslut till en befintlig. Om du väljer *ansluta befintliga*, måste du ange ytterligare värden. |Nytt nätverk <br/> Ansluta till befintliga |
**HLF CA-lösenord** |Ett lösenord som används för de certifikat som genereras av de certifikatutfärdare som har skapats som en del av distributionen. Lösenordet måste innehålla tre av följande tecken: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken.<br /><br />Även om alla virtuella datorer har ursprungligen samma lösenord, kan du ändra lösenordet när du har etablerat.|1 - 25 tecken |
**Organisation installationen** |Du kan anpassa namn och certifikat för din organisation eller har standardvärden som ska användas.|Standard <br/> Avancerat |
**Inställningar för VPN-nätverk** | Etablera en VPN-tunnel gateway för att komma åt de virtuella datorerna | Ja <br/> Nej |

Välj **OK**.

### <a name="fabric-specific-settings"></a>Fabric-specifika inställningar

I **infrastrukturresurskonfigurationen**, du konfigurerar nätverkets storlek och prestanda och ange indata för tillgängligheten för nätverket. T.ex, antal orderer och peer-noder, persistence-motor som används av varje nod och VM-storleken.

![Fabric-inställningar](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Parameternamn | Beskrivning | Tillåtna värden |
|---|---|---|
**Skaltyp** |Typen av distribution av en virtuell dator med flera behållare eller flera virtuella datorer i en skalbar-modell.|Enstaka virtuell dator eller med flera virtuella datorer |
**VM-disktyp** |Typ av säkerhetskopiering av var och en av de distribuerade noderna. <br/> Mer information om tillgängliga disktyper finns [Välj en disktyp av](../../virtual-machines/windows/disks-types.md).|Standard SSD <br/> Premium SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>Distribution av flera virtuella datorer (ytterligare inställningar)

![Fabric-inställningar för flera distribueringar av virtuella datorer](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Parameternamn | Beskrivning | Tillåtna värden |
|---|---|---|
**Antalet orderer noder** |Antalet noder som beställer (organisera) transaktioner i ett block. <br />Ytterligare information om tjänsten skrivordning på Hyperledger [dokumentation](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) |1 – 4 |
**Orderer nodstorlek för virtuell dator** |VM-storleken som används för orderer noder i nätverket|Standard Bs<br />Standard Ds<br />Standard FS |
**Antalet peer-noder** | Noder som ägs av medlemmarna som utför transaktioner och underhåller tillståndet och en kopia av huvudboken.<br />Ytterligare information om tjänsten skrivordning på Hyperledger [dokumentation](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|1 – 4 |
**Noden sessionslägets beständighet** |Persistence-motor som används av peer-noder. Du kan konfigurera den här motorn per peer-nod. Se informationen nedan för flera peer-noder.|CouchDB <br />LevelDB |
**Peer nodstorlek för virtuell dator** |Storleken på virtuella datorn som används för alla noder i nätverket|Standard Bs<br />Standard Ds<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Flera peer-nodkonfiguration

Den här mallen kan du välja din persistence-motor per peer-nod. Om du har tre peer-noder du använda CouchDB på en och LevelDB på de andra två.

![Flera peer-nodkonfiguration](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Välj **OK**.

### <a name="deploy"></a>Distribuera

I **sammanfattning**, granska indata som angetts och att köra grundläggande verifiering av distributionen.

![Sammanfattning](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Läs juridisk information och sekretess licensvillkoren och välj **köp** att distribuera. Beroende på hur många virtuella datorer som håller på att etableras, tidpunkten för distributionen kan skilja sig från ett par minuter till tio minuter.

## <a name="next-steps"></a>Nästa steg

Du är nu redo att fokusera på programmet och chaincode utveckling mot nätverket Hyperledger consortium blockchain.
