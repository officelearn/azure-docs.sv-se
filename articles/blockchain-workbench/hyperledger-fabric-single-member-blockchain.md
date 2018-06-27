---
title: Hyperledger Fabric Consortium
description: Med mallen Hyperledger Fabric Consortium lösning kan du distribuera och konfigurera en enda medlem nätverk
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 28561f5f94044d19cfd07e99d7f7a736ec470cf1
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960363"
---
# <a name="hyperledger-fabric-single-member-network"></a>Hyperledger Fabric medlem nätverk

Du kan använda mallen Hyperledger Fabric Consortium lösning för att distribuera och konfigurera ett Hyperledger Fabric medlem (med flera noder) nätverk.

När du har läst den här artikeln, kommer du att:

- Hämta kunskaper om blockchain, Hyperledger Infrastrukturresurser och mer komplicerad consortium nätverksarkitekturer
- Lär dig att distribuera och konfigurera en enskild medlem Hyperledger Fabric consortium nätverk från Azure-portalen

## <a name="about-blockchain"></a>Om blockchain

Om du har använt blockchain community, är det en bra möjlighet att lära dig om tekniken på ett enkelt och konfigurerbara sätt på Azure. Blockchain är den underliggande tekniken bakom Bitcoin; dock är det mycket mer än bara ett sätt för en virtuell valuta. Det är en kombination av befintlig databas, distribuerade system och kryptografiska teknik som möjliggör säker flerparti beräkning med garantier runt immutability, verifiability, revision och återhämtning för angrepp. Olika protokoll använder olika metoder för dessa attribut. [Hyperledger Fabric](https://github.com/hyperledger/fabric) är ett protokoll.

## <a name="consortium-architecture-on-azure"></a>Arkitektur för Consortium på Azure

Den här mallen distribuerar en topologi för att testa och simulera produktion för användare i en enskild organisation (medlem). Den här distributionen består av ett nätverk med flera noder i en enda region snart ska expanderas till flera regioner.

Nätverket består av tre typer av noder:

1. **Medlemmen noden**: en nod som kör tjänsten Fabric medlemskap som registrerar och hanterar network-medlemmar. Den här noden klustras så småningom för skalbarhet och hög tillgänglighet, men en enda medlem nod kommer att användas i den här övningen.
2. **Orderer noder**: en nod som kör kommunikationstjänsten implementera leverans säkerhet, till exempel totalt ordning broadcast- eller atomiska transaktioner.
3. **Peer-noder**: en nod som genomför transaktioner och bibehåller tillståndet och en kopia av den distribuerade redovisningen.

## <a name="getting-started"></a>Komma igång

Om du vill börja, behöver du en Azure-prenumeration som har stöd för distribution av flera virtuella datorer och standard storage-konton. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

Som standard stöder de flesta prenumerationstyper en liten distributionstopologi utan att behöva öka kvoten. Minsta möjliga distributionen för en medlem behöver:

- 5 virtuella datorer (5 kärnor)
- 1 VNet
- 1 belastningsutjämnare
- 1 offentlig IP-adress

När du har en prenumeration går du till den [Azure-portalen](https://portal.azure.com). Välj **+** väljer **Blockchain**, och välj **Hyperledger Fabric enskild medlem Blockchain**.

![Hyperledger Fabric enskild medlem Blockchain Marketplace-mall](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Distribution

Om du vill starta, Välj den **Hyperledger Fabric enskild medlem Blockchain** och på **skapa**. Då öppnas den **grunderna** bladet i guiden.

Malldistributionen vägleder dig genom att konfigurera nätverket med flera noder. Distributionsflödet är uppdelat i tre steg: grundläggande, nätverkskonfigurationen och Fabric-konfiguration.

### <a name="basics"></a>Grundläggande inställningar

Under den **grunderna** bladet ange värden för standard parametrar för en distribution som prenumerationen, resursgruppen och egenskaper för grundläggande virtuella datorer.

![Grundläggande inställningar](./media/hyperledger-fabric-single-member-blockchain/basics.png)

Parameternamn| Beskrivning| Tillåtna värden|Standardvärde
---|---|---|---
**Resurs-prefix**| En sträng som används som bas för namngivning av distribuerade resurser.|6 tecken eller mindre|Ej tillämpligt
**Användarnamn för VM**| Användarnamn för administratören för var och en av de virtuella datorerna som distribueras för den här medlemmen.|1 – 64 tecken|azureuser
**Autentiseringstyp**| Metoden som används för att autentisera till den virtuella datorn.|Lösenord eller SSH offentlig nyckel|Lösenord
**Lösenord (autentiseringstyp = lösenord)**|Lösenordet för administratörskontot för var och en av de virtuella datorerna som distribueras. Lösenordet måste innehålla 3 av följande: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken.<br /><br />Alla virtuella datorer som ursprungligen har samma lösenord, kan du ändra lösenordet när du har etablerat.|12 - 72 tecken|Ej tillämpligt
**SSH-nyckel (autentiseringstyp = offentlig nyckel)**|Secure shell-nyckel som används för fjärrinloggning.||Ej tillämpligt
**Begränsa åtkomst efter IP-adress**|Inställning för att fastställa typ om slutpunkten för klientåtkomst är begränsad eller inte.|Ja/nej| Nej
**Tillåtna IP-adressen eller undernätet (begränsa åtkomst efter IP-adress = Yes)**|IP-adress eller en uppsättning IP-adresser som tillåts att komma åt slutpunkten för klienten när åtkomstkontroll är aktiverat.||Ej tillämpligt
**Prenumeration** |Prenumerationen som ska distribueras.
**Resursgrupp** |Resursgrupp som du distribuerar consortium-nätverk.||Ej tillämpligt
**Plats** |Azure-region som du vill distribuera den första medlemmen ** s nätverk storleken.

### <a name="network-size-and-performance"></a>Nätverkets storlek och prestanda

Sedan under **storlek och prestanda,** ange indata för storleken på nätverkets consortium, till exempel antalet medlemskap, orderer och peer-noder. Välj alternativ för infrastruktur och storlek på virtuell dator.

![Nätverkets storlek och prestanda](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

Parameternamn| Beskrivning| Tillåtna värden|Standardvärde
---|---|---|---
**Antalet noder för medlemskap**|Antalet noder som kör tjänsten medlemskap. För ytterligare information om tjänsten medlemskap, titta på säkerhets- och Medlemskapstjänster under Hyperledger [dokumentationen](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf).<br /><br />Det här värdet är för närvarande begränsad till 1 nod, men vi planerar att stödja skalbar via klustring i nästa version.|1| 1
**Antalet Orderer noder** |Antalet noder som beställer (ordna) transaktioner i block.--> detta instruktionen är mångordig och förvirrande. Mer information om tjänsten onlinebeställning finns i Hyperledger [dokumentationen](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html).<br /><br />Det här värdet är för närvarande begränsad till 1 nod. |1 |1
**Antalet peer-noder**| Noder som ägs av medlemmarna som utför transaktioner och upprätthålla tillstånd och en kopia av.<br /><br />Mer information om tjänsten onlinebeställning finns i Hyperledger [dokumentationen](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|3| 3 - 9
**Lagringsprestanda**|Typ av lagring säkerhetskopiering varje distribuerad nod. Mer information om lagring finns [introduktion till Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) och [Premiumlagring](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|Standard- eller Premium|Standard
**Storlek på virtuell dator** |Storleken på virtuella datorn används för alla noder i nätverket|Standard A<br />Standard D<br />Standard D-v2<br />Standard F-serien<br />Standard DS<br />och Standard FS|Standard D1_v2

### <a name="fabric-specific-settings"></a>Fabric-specifika inställningar

Slutligen under **Infrastrukturinställningarna**, ange Infrastrukturresurser-relaterade konfigurationsinställningar.

![Infrastrukturinställningarna](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

Parameternamn| Beskrivning| Tillåtna värden|Standardvärde
---|---|---|---
**Bootstrap användarnamn**| Den inledande behöriga användare som ska registreras med medlemstjänster i distribuerade nätverk.|9 tecken|Admin
**Bootstrap användarlösenord för Infrastrukturresurser Certifikatutfärdare**|Administratörslösenordet som används för att skydda kontot Fabric CA importeras till noden medlemskap.<br /><br />Lösenordet måste innehålla en versal, en gemen bokstav och en siffra.|12 eller flera tecken|Ej tillämpligt

### <a name="deploy"></a>Distribuera

I **sammanfattning**, granska indata som angetts och att köra grundläggande verifiering av distributionen.

![Sammanfattning](./media/hyperledger-fabric-single-member-blockchain/summary.png)

Läs igenom licensvillkoren för juridisk information och sekretess och klickar på **inköp** att distribuera. Beroende på antalet virtuella datorer som håller på att etableras, tidpunkten för distribution kan skilja sig från några minuter till flera minuter.

### <a name="accessing-nodes"></a>Åtkomst till noder

När distributionen är klar, en **översikt** visas.

![Distributioner](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

Om skärmen inte visas automatiskt (kanske eftersom du har flyttat runt hanteringsportalen när distributionen körs) bör du alltid hittar den på fliken resursgrupper i det vänstra navigeringsfältet. Klicka på namnet för resursgruppen som du angav i steg 1 för att gå till den **översikt** sidan.

Översikten visar alla resurser som har distribuerats av mallen lösning. Du kan utforska dem när du vill, men i det här fönstret kan du också öppna den _utdataparametrar_ genereras av mallen. Dessa utdataparametrar får du användbar information när du ansluter till nätverket Hyperledger Fabric.

För att komma åt utdataparametrarna först klicka på den **distributioner** fliken i resursgrupp-bladet. Distributionshistoriken visas.

![Distributionshistorik](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

Klicka på den första distributionen i listan för att visa informationen från historiken distribution.

![Distributionsinformation](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

Skärmen information visas en sammanfattning av distributionen, följt av tre användbara utdataparametrar:

- Den _API-SLUTPUNKT_ kan användas när du distribuerar ett program i nätverket.
- Den _PREFIX_ , även kallat _distribution prefixet_ , unikt identifierar dina resurser och din distribution. Den används när du använder de kommandoradsbaserade verktyg.
- Den _SSH till första VM_ ger du före monterade ssh-kommando med rätt parametrar som krävs för att ansluta till den första virtuella datorn i nätverket; när det gäller Hyperledger Fabric blir Fabric-CA-nod.

Du kan fjärransluta till virtuella datorer för varje nod via SSH med din angivna admin användarnamnet och lösenordet/SSH-nyckel. Eftersom noden virtuella datorer inte har sina egna offentliga IP-adresser, behöver du gå igenom belastningsutjämnaren och ange portnumret. SSH-kommandot för att få åtkomst till den första noden i transaktionen är den tredje mallutdata ** SSH till första VM (för distributionen av exempel: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`). För att få ytterligare transaktionsloggar noder kan öka portnumret med ett (till exempel den första noden i transaktionen finns på port 3000, andra på 3001 är tredje på 3002, etc.).

## <a name="next-steps"></a>Nästa steg

Du är nu redo att fokusera på programmet och chaincode utveckling mot nätverket Hyperledger consortium blockchain.
