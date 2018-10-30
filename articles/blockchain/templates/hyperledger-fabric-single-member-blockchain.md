---
title: Konsortium för Hyperledger Fabric
description: Använd lösningsmallen Hyperledger Fabric Consortium att distribuera och konfigurera ett enda medlem nätverk
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/29/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: c08557156848d4e7fcf0b1adbe6c8faa4ee00c82
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231380"
---
# <a name="hyperledger-fabric-single-member-network"></a>Hyperledger Fabric medlem nätverk

Du kan använda lösningsmallen Hyperledger Fabric Consortium för att distribuera och konfigurera ett Hyperledger Fabric medlem (med flera noder) nätverk.

När du har läst den här artikeln, kommer du att:

- Få kunskaper om blockchain, Hyperledger Fabric och mer komplicerad consortium network arkitekturer
- Lär dig hur du distribuerar och konfigurerar en medlem Hyperledger Fabric konsortienätverk från Azure Portal

## <a name="about-blockchain"></a>Om blockchain

Om du är nybörjare på blockchain-communityn är i den här lösningsmallen en fantastisk möjlighet att lära dig om tekniken på ett enkelt och konfigurerbara sätt på Azure. Blockchain är den underliggande tekniken bakom Bitcoin; Det är dock mycket mer än bara ett verktyg för en virtuell valuta. Det är en kombination av befintlig databas, distribuerade system och kryptografiska tekniker som möjliggör säker flerparti beräkning med garantier kring oföränderlighetsprincip, verifiability, revision och återhämtning för angrepp. Olika protokoll använder olika sätt att tillhandahålla dessa attribut. [Hyperledger Fabric](https://github.com/hyperledger/fabric) är ett protokoll.

## <a name="consortium-architecture-on-azure"></a>Consortium arkitektur på Azure

Den här mallen distribuerar en topologi för att testa och simulera produktion för användare inom en organisation (enskild medlem). Den här distributionen består av ett nätverk med flera noder i en region, så snart som ska expanderas till flera regioner.

Nätverket består av tre typer av noder:

1. **Medlem noden**: en nod som kör tjänsten Fabric medlemskap som registrerar och hanterar medlemmar i nätverket. Den här noden kan grupperas för skalbarhet och hög tillgänglighet. Men i den här övningen, en enda medlem nod används.
2. **Orderer noder**: en nod som kör kommunikationstjänsten implementera garanterad leverans som totalt order broadcast eller atomiska transaktioner.
3. **Peer-noder**: en nod som genomför transaktioner och underhåller tillståndet och en kopia av det distribuerade transaktionsregister.

## <a name="getting-started"></a>Komma igång

Om du vill börja, måste en Azure-prenumeration som har stöd för distribution av flera virtuella datorer och lagringskonton av standardtyp. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/).

De flesta typer av prenumerationer stöder en liten distributionstopologi utan att behöva öka kvoten som standard. Minsta möjliga distributionen för en medlem måste:

- 5 virtuella datorer (5 kärnor)
- 1 virtuellt nätverk
- 1 belastningsutjämnare
- 1 offentlig IP-adress

När du har en prenumeration går du till den [Azure-portalen](https://portal.azure.com). Välj **+** väljer **Blockchain**, och välj **Hyperledger Fabric enskild medlem Blockchain**.

![Hyperledger Fabric enskild medlem Blockchain Marketplace-mall](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Distribution

För att starta, markerar den **Hyperledger Fabric enskild medlem Blockchain** och klicka på **skapa** att öppna den **grunderna** bladet i guiden.

Malldistributionen vägleder dig genom att konfigurera nätverk på flera noder. Distributionsflödet är uppdelad i tre steg: grundläggande konfiguration och infrastrukturresurskonfigurationen.

### <a name="basics"></a>Grundläggande inställningar

I den **grunderna** bladet ange värden för standard parametrar för alla distributioner. Prenumeration, resursgrupp och grundläggande virtuell dator som till exempel, egenskaper.

![Grundläggande inställningar](./media/hyperledger-fabric-single-member-blockchain/basics.png)

Parameternamn| Beskrivning| Tillåtna värden|Standardvärde
---|---|---|---
**Resurs-prefix**| En sträng som används som bas för namngivning av distribuerade resurser.|6 tecken eller mindre|Ej tillämpligt
**VM-användarnamn**| Användarnamn för administratören för var och en av de virtuella datorerna som distribueras för den här medlemmen.|1 – 64 tecken|azureuser
**Autentiseringstyp**| Metoden för att autentisera till den virtuella datorn.|Lösenordet eller SSH offentlig nyckel|Lösenord
**Lösenord (autentiseringstyp = lösenord)**|Lösenordet för administratörskontot för var och en av de virtuella datorerna som distribueras. Lösenordet måste innehålla tre av följande tecken: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken.<br /><br />Även om alla virtuella datorer har ursprungligen samma lösenord, kan du ändra lösenordet när du har etablerat.|12 – 72 tecken|Ej tillämpligt
**SSH-nyckel (autentiseringstyp = offentlig nyckel)**|SSH-nyckel som används för fjärrinloggning.||Ej tillämpligt
**Begränsa åtkomst efter IP-adress**|Inställningen för att fastställa typ om slutpunkten för klientåtkomst är begränsad eller inte.|Ja/nej| Nej
**Tillåtna IP-adress eller undernät (begränsa åtkomst efter IP-adress = Yes)**|IP-adress eller en uppsättning IP-adresser som har åtkomstbehörighet till klient-slutpunkt när åtkomstkontroll är aktiverat.||Ej tillämpligt
**Prenumeration** |Den prenumeration som ska distribueras.
**Resursgrupp** |Den resursgrupp som ska distribueras consortium network.||Ej tillämpligt
**Plats** |Azure-region som ska distribueras den första medlemmen ** s nätverk fotavtryck.

### <a name="network-size-and-performance"></a>Nätverkets storlek och prestanda

I **storlek och prestanda,** ange indata för storleken på konsortienätverk. Till exempel hur många medlemskap, orderer och peer-noder. Välj infrastrukturalternativ för och storlek på virtuell dator.

![Nätverkets storlek och prestanda](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

Parameternamn| Beskrivning| Tillåtna värden|Standardvärde
---|---|---|---
**Antalet noder för medlemskap**|Antalet noder som kör tjänsten medlemskap. Mer information om tjänsten medlemskap titta på säkerhet och Medlemskapstjänster under Hyperledger [dokumentation](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf).<br /><br />Det här värdet är för närvarande begränsade till 1 nod, men vi planerar att stödja skalbar via klustring i nästa version.|1| 1
**Antalet Orderer noder** |Antalet noder som beställer (organisera) transaktioner i block.--> den här instruktionen är mångordig och förvirrande. Ytterligare information om tjänsten skrivordning på Hyperledger [dokumentation](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html).<br /><br />Det här värdet är för närvarande begränsad till 1 nod. |1 |1
**Antalet Peer-noder**| Noder som ägs av medlemmarna som utför transaktioner och underhåller tillståndet och en kopia av huvudboken.<br /><br />Ytterligare information om tjänsten skrivordning på Hyperledger [dokumentation](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|3| 3 – 9
**Lagringsprestanda**|Typ av säkerhetskopiering av var och en av de distribuerade noderna. Läs mer om storage [introduktion till Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) och [Premiumlagring](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|Standard- eller Premium|Standard
**Storlek på virtuell dator** |Storleken på virtuella datorn som används för alla noder i nätverket|Standard A<br />Standard D<br />Standard D-v2<br />Standard F-serien<br />Standard DS<br />och Standard FS|Standard D1_v2

### <a name="fabric-specific-settings"></a>Fabric-specifika inställningar

Slutligen går **Infrastrukturinställningarna**, ange Infrastrukturresurser-relaterade konfigurationsinställningar.

![Fabric-inställningar](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

Parameternamn| Beskrivning| Tillåtna värden|Standardvärde
---|---|---|---
**Bootstrap användarnamn**| Den inledande behöriga användare som ska registreras med tjänsten medlem i det distribuerade nätverket.|9 eller färre tecken|Admin
**Bootstrap användarlösenord för Fabric CA**|Administratörslösenordet som används för att skydda den Fabric CA-konto som har importerats till noden medlemskap.<br /><br />Lösenordet måste innehålla en versal, en gemen bokstav och en siffra.|minst 12 tecken|Ej tillämpligt

### <a name="deploy"></a>Distribuera

I **sammanfattning**, granska indata som angetts och att köra grundläggande verifiering av distributionen.

![Sammanfattning](./media/hyperledger-fabric-single-member-blockchain/summary.png)

Läs juridisk information och sekretess licensvillkoren och klicka på **köp** att distribuera. Beroende på hur många virtuella datorer som håller på att etableras, tidpunkten för distributionen kan skilja sig från ett par minuter till tio minuter.

### <a name="accessing-nodes"></a>Åtkomst till noder

När distributionen är klar, en **översikt** visas.

![Distributioner](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

Om skärmen inte visas automatiskt (kanske eftersom du har flyttat runt hanteringsportalen vid distributionen kördes), du kan alltid hitta den på fliken resursgrupper i vänster navigeringsfält. Klicka på namnet för resursgruppen som du angav i steg 1 för att gå till den **översikt** sidan.

Översikten visar alla resurser som har distribuerats av lösningsmallen. Du kan utforska dem när du vill, men från den här skärmen kan du också öppna den _utdataparametrar_ genereras av mallen. Dessa utdataparametrar får du användbar information när du ansluter till nätverket Hyperledger Fabric.

För att komma åt utdataparametrarna, först klicka på den **distributioner** flik i resursgrupp-bladet. Distributionshistoriken visas.

![Distributionshistorik](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

Från Distributionshistoriken, klickar du på den första distributionen i listan för att visa informationen.

![Distributionsinformation](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

Informationsskärmen visar en sammanfattning av distributionen, följt av tre användbara utdataparametrar:

- Den _API-SLUTPUNKT_ kan användas när du distribuerar ett program i nätverket.
- Den _PREFIX_ , kallas även _distribution prefix_ , unikt identifierar dina resurser och din distribution. Den används när du använder de kommandoradsbaserade verktyg.
- Den _SSH till första VM_ ger dig förväg monterade ssh-kommando med alla rätt parametrar som krävs för att ansluta till den första virtuella datorn i nätverket. För Hyperledger Fabric blir det Fabric-CA-nod.

Du kan fjärransluta till de virtuella datorerna för varje nod via SSH med ditt angivna admin användarnamn och lösenord/SSH-nyckel. Eftersom noden virtuella datorer inte har sina egna offentliga IP-adresser, behöver du gå igenom belastningsutjämnaren och ange portnumret. SSH-kommando för att få åtkomst till den första noden i transaktionen är den tredje mallutdata ** SSH till första VM (för exempeldistribution: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`). Gå till ytterligare transaktionsloggar noder genom att öka portnumret med ett (till exempel den första noden i transaktionen är på port 3000, andra på 3001 är tredje på 3002, osv.).

## <a name="next-steps"></a>Nästa steg

Du är nu redo att fokusera på programmet och chaincode utveckling mot nätverket Hyperledger consortium blockchain.
