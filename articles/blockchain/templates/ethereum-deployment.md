---
title: Ethereum proof of work consortium lösningsmallen
description: Använd lösningsmallen Ethereum Proof of Work Consortium att distribuera och konfigurera ett flera medlem consortium Ethereum-nätverk
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/28/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 266e2be2775a6f9b74c714bd9112e38837bb6a6c
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098346"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Ethereum proof of work consortium lösningsmallen

Lösningsmallen Ethereum Proof of Work Consortium är utformad för att göra det enklare och snabbare att distribuera och konfigurera ett flera medlem Ethereum konsortienätverk med minimal kunskap om Azure och Ethereum.

Med en Azure Resource Manager-mall, kan varje medlem etablera sina nätverk fotavtryck, med hjälp av Microsoft Azure Compute, nätverk och lagringstjänster. Varje medlem nätverk fotavtryck består av en uppsättning noder för Utjämning av nätverksbelastning transaktion med som ett program eller en användare interagerar om du vill skicka transaktioner, en uppsättning utvinning noder till poster transaktioner och en VPN-gateway. Efter distributionen kan ansluta du dina gateways för att skapa en fullständigt konfigurerad flera medlem blockchain-nätverk.

## <a name="about-blockchain"></a>Om blockchain

För dig som är nya för blockchain-communityn, är versionen av den här lösningen en fantastisk möjlighet att lära dig om tekniken på ett enkelt och konfigurerbara sätt på Azure. För att komma igång, rekommenderar vi dock distribuera enklare fristående Ethereum nätverkets topologi med den här guidade genomgången, innan du att skapa flera medlem consortium nätverk.

### <a name="mining-node-details"></a>Nodinformation för datautvinning

Noder som utvinner data ur transaktioner är avgränsade från de noder som accepterar transaktioner för att säkerställa att de två åtgärderna inte konkurrerar om samma resurser.

Medlem consortium kan etablera upp till fem regioner som innehåller en eller flera utvinningsstrukturen noder, backas upp av en hanterad disk. En eller flera noder i regionen är konfigurerade som en Startnod för att stödja dynamisk identifiering av noderna i nätverket. Utvinning noder kommunicerar med andra utvinning noder skulle få konsensus på tillståndet för den underliggande distribuerat transaktionsregister. Det finns inget behov av ditt program vara medveten om eller kommunicera med dessa noder. Genom att fokusera på privata nätverk, är datautvinning noderna isolerade från inkommande offentliga internet-trafiken för att lägga till en sekundär skyddsnivå. Utgående trafik tillåts, men inte Ethereum identifiering port.

Alla noder har en stabil version av klienten gå Ethereum (Geth) och är konfigurerade för datautvinning noder. Om du inte har lämnat ett anpassat genesis block, Använd alla noder samma Ethereum-adress och nyckelpar som skyddas av Ethereum-kontolösenord. Ethereum-lösenfras som du angett används för att generera standardkontot (coinbase) för varje nod för datautvinning. Som utvinningsstrukturen noder utvinna, de samla in avgifter som läggs till det här kontot.

Antalet utvinningsstrukturen noder per consortium medlem beror på storleken på det önskade nätverket och mängden hash power som är dedikerad för varje medlem. Större nätverk kräver fler noder äventyras för att få en orättvis fördel. Mallen har stöd för upp till 15 utvinningsstrukturen noder per region som etablerats med hjälp av VM-skalningsuppsättningar.

### <a name="transaction-node-details"></a>Transaktionsinformation för noden

Medlem consortium har också en uppsättning noder för Utjämning av nätverksbelastning transaktion. Dessa noder kan nås från utanför det virtuella nätverket så att program kan använda dessa noder för att skicka transaktioner eller köra smarta kontrakt i blockchain-nätverket. Alla noder har en stabil version av klienten gå Ethereum (Geth) och har konfigurerats för att upprätthålla en fullständig kopia av det distribuerade transaktionsregister. Om ett anpassat genesis block inte anges, använda dessa noder samma Ethereum konto skyddas av Ethereum-kontolösenord.

Transaktionen noder är Utjämning av nätverksbelastning i en tillgänglighetsuppsättning för att bibehålla en hög tillgänglighet. Mallen har stöd för upp till fem transaktion noderna vara etablerade med hjälp av VM-skalningsuppsättningar.

### <a name="log-analytics-details"></a>Log analytics-information

Varje distribution även skapar en ny Log analytics-instans eller kan ansluta till en befintlig instans. Logganalys tillåter övervakning av olika prestandamått för alla virtuella datorer som utgör det distribuerade nätverket.

## <a name="deployment-architecture"></a>Distributionsarkitektur för

### <a name="description"></a>Beskrivning

Den här lösningsmallen kan distribuera en eller flera region-baserade multi medlem Ethereum consortium network. Det virtuella nätverket för varje region är ansluten till andra regioner i en kedja topologi med hjälp av VNET-gateways och anslutningsresurser. Det tillhandahåller även en registrator som innehåller information som krävs för alla Miner och transaktionen noder som distribueras i varje region.

### <a name="standalone-and-consortium-leader-overview"></a>Översikt över fristående och consortium ledare

![Fristående och Consortium ledare översikt](./media/ethereum-deployment/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Koppla consortium medlem översikt

![Koppla Consortium medlem översikt](./media/ethereum-deployment/member-overview.png)

## <a name="getting-started"></a>Komma igång

Den här processen kräver en Azure-prenumeration som har stöd för distribution av flera skalningsuppsättningar för virtuella datorer och hanterade diskar. Vid behov kan du skapa ett kostnadsfritt konto att börja.

När en prenumeration är skyddad, går du till Azure-portalen. Välj **+ skapa en resurs**, Marketplace (se alla), och Sök efter **Ethereum Proof of Work Consortium**.

Malldistributionen vägleder dig genom att konfigurera den första medlemmen fotavtryck i nätverket. Distributionsflödet består av fem steg: Grunderna, Operations Management Suite, distribution regioner, nätverkets storlek och prestanda, Ethereum-inställningar.

### <a name="basics"></a>Grundläggande inställningar

Under **grunderna**, ange värden för standard parametrar för alla distributioner, till exempel prenumeration, resursgrupp och grundläggande VM-egenskaper.

![Grundläggande inställningar](./media/ethereum-deployment/sample-deployment.png)

Parameternamn|Beskrivning| Tillåtna värden|Standardvärden
---|---|---|---
Skapa ett nytt nätverk eller ansluta till befintliga nätverk?|Skapa ett nytt nätverk eller Anslut till ett befintligt consortium-nätverk|Skapa ny koppling befintliga|Skapa ny
Distribuera ett nätverk som ska ingå i ett konsortium?|Ett konsortienätverk kan framtida distributioner att ansluta till det här nätverket (visas när *Skapa ny* väljs ovan)|Fristående Consortium|Fristående
Resurs-Prefix |Sträng som används som bas för namngivning av resurser (2-4 alfanumeriska tecken). Ett unikt hash-värde är tillagt för strängen för vissa resurser, medan resursspecifika information läggs.|Alfanumeriska tecken med längden 2 till 4|Ej tillämpligt
VM-användarnamn| Administratörens användarnamn för varje distribuerad virtuell dator (endast alfanumeriska tecken)|1 – 64 tecken |gethadmin
Autentiseringstyp|Metoden för att autentisera till den virtuella datorn. |Lösenordet eller SSH offentlig nyckel|Lösenord
Lösenord (autentiseringstyp = lösenord)|Lösenordet för administratörskontot för var och en av de virtuella datorerna som distribueras. Lösenordet måste innehålla 3 av följande krav: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken. <br />Även om alla virtuella datorer har ursprungligen samma lösenord, kan du ändra lösenordet när du har etablerat.|12 – 72 tecken|Ej tillämpligt
SSH-nyckel (autentiseringstyp = offentlig nyckel)|SSH-nyckel som används för fjärrinloggning.|| Ej tillämpligt
Prenumeration| Den prenumeration som ska distribueras consortium network||Ej tillämpligt
Resursgrupp| Den resursgrupp som ska distribueras consortium network.||Ej tillämpligt
Plats| Azure-regionen för resursgruppen. ||Ej tillämpligt

### <a name="operations-management-suite"></a>Operations Management Suite

Operations Management Suite (OMS) kan du konfigurera en OMS-resurs för nätverket. OMS samlar in och surface användbart mått och loggar från ditt nätverk, ger möjligheten att snabbt kontrollera nätverkets tillstånd eller felsöka problem. Gratisversionen av OMS misslyckas gradvis när kapaciteten har nåtts.

![Skapa ny OMS](./media/ethereum-deployment/new-oms.png)

Parameternamn|Beskrivning| Tillåtna värden|Standardvärden
---|---|---|---
Anslut till befintliga OMS|Skapa en ny Log Analytics-instans eller Anslut till en befintlig instans|Skapa ny koppling befintliga|Skapa ny Log Analytics-plats|Regionen där det nya Log Analytics ska distribueras (synliga om *Skapa ny* har valts)
Befintliga OMS arbetsyte-Id|Arbetsyte-ID för den befintliga instansen (synliga om *ansluta befintliga* har valts) OMS tjänstnivå|Välj prisnivå för den nya instansen. Mer information på https://azure.microsoft.com/pricing/details/log-analytics/ (synliga om *ansluta befintliga* har valts)|Kostnadsfria fristående Per nod|Kostnadsfri
Befintliga OMS primär nyckel|Den primära nyckeln som används för att ansluta till den befintliga OMS-instansen (synliga om *ansluta befintliga* har valts)

### <a name="deployment-regions"></a>Distribution av regioner

Sedan under **distribution regioner**, ange indata för **antalet region(er)** konsortienätverk och valet av Azure-regioner baserat på antalet regioner som anges. Användare kan distribuera i högst fem regioner.

![Distribution av regioner](./media/ethereum-deployment/deployment-regions.png)

Parameternamn| Beskrivning| Tillåtna värden |Standardvärden
---|---|---|---
Antal region(er)| Antal regioner för att distribuera consortium network|1, 2, 3, 4, 5| 2
Första regionen| Första regionen att distribuera consortium network|Alla tillåtna Azure-regioner| Västra USA
Andra region |Andra regionen som du distribuerar konsortienätverk (visas bara om antalet regioner som har markerats som 2)|Alla tillåtna Azure-regioner| Östra USA
Tredje region| Tredje region för att distribuera konsortienätverk (visas bara om antalet regioner som har markerats som 3)|Alla tillåtna Azure-regioner| Centrala USA
Fjärde region| Fjärde region för att distribuera konsortienätverk (visas bara om antalet regioner som har markerats som 4)|Alla tillåtna Azure-regioner| USA, östra 2
Femte region| Femte region för att distribuera konsortienätverk (visas bara om antalet regioner som har markerats som 5)|Alla tillåtna Azure-regioner| Västra USA 2

### <a name="network-size-and-performance"></a>Nätverkets storlek och prestanda

Sedan under **storlek och prestanda** ange indata för storleken på konsortienätverk. Till exempel antal och storlek för datautvinning noder och noder för transaktionen.

![Nätverkets storlek och prestanda](./media/ethereum-deployment/network-size-performance.png)

Parameternamn |Beskrivning |Tillåtna värden| Standardvärden
---|---|---|---
Antalet noder för datautvinning|Antalet utvinningsstrukturen noder som har distribuerats per region|2 - 15| 2
Lagringsprestanda för datautvinning nod|Typ av hanterad disk säkerhetskopiering varje nod distribuerade utvinningsstrukturen.|Standard- eller Premium|Standard
Utvinning nodstorlek för virtuell dator|VM-storleken som används för datautvinning noder.|Standard A, <br />Standard D <br />Standard D-v2, <br />Standard F-serien <br />Standard DS <br />and Standard FS|Standard D1v2
Antalet noder som belastningsutjämnade transaktion|Antalet noder för transaktionen att etablera som en del av nätverket.|1 - 5| 2
Lagringsprestanda för transaktionen nod|Typ av hanterad disk säkerhetskopiering var och en av noderna i distribuerade transaktioner.|Standard- eller Premium|Standard
Transaktionen nodstorlek för virtuell dator|VM-storleken som används för transaktionen noder.|Standard A, <br />Standard D <br />Standard D-v2, <br />Standard F-serien <br />Standard DS <br />and Standard FS|Standard D1v2

### <a name="ethereum-settings"></a>Ethereum-inställningar

Sedan under **Ethereum inställningar**, ange Ethereum-relaterade konfigurationsinställningar, t.ex. nätverks-ID och Ethereum-konto lösenord eller genesis blocket.

![Ethereum-inställningar](./media/ethereum-deployment/standalone-leader-deployment.png)

Parameternamn |Beskrivning |Tillåtna värden|Standardvärden
---|---|---|---
ConsortiumMember-ID|ID som är associerade med varje medlem som deltar i konsortienätverk som används för att konfigurera IP-adressutrymmen att undvika kollision. <br /><br />Medlems-ID måste vara unikt inom olika organisationer i samma nätverk. Ett unikt medlems-ID krävs även när samma organisation distribuerar till flera regioner.<br /><br />Anteckna värdet för den här parametern eftersom du kommer att behöva dela den med andra sammanbinder medlemmar.|0 - 255
Ethereum nätverks-ID|Nätverks-ID för consortium Ethereum-nätverk som ska distribueras. Varje Ethereum-nätverk har sin egen, med 1 som ID för det offentliga nätverket. Nätverksåtkomst är begränsad för datautvinning noder, rekommenderar vi ändå med hjälp av ett stort antal för att förhindra kollisioner.|5 - 999,999,999| 10101010
Anpassade genesis block|Alternativet för att automatiskt generera ett genesis block eller ange en anpassad.|Ja/nej| Nej
Kontolösenord för Ethereum (anpassad genesis block = Nej)|Administratörslösenordet som används för att skydda det Ethereum-konto som har importerats till varje nod. Lösenordet måste innehålla: 1 versal bokstav, 1 gemen bokstav och 1 siffra.|minst 12 tecken|Ej tillämpligt
Ethereum-privata nyckeln (anpassad genesis block = Nej)|Lösenfras som används för att generera den privata nyckeln för ECC som är associerade med Ethereum standardkontot som genereras. En förgenererade privat nyckel behöver inte uttryckligen också skickas.<br /><br />Beakta en lösenfras med tillräckliga slumpmässighet för att säkerställa en stark privat nyckel och utan överlappande med andra medlemmar i consortium. Lösenfrasen måste innehålla minst: 1 versal bokstav, 1 gemen bokstav och 1 siffra.<br /><br />Observera att om två medlemmar använda samma lösenfras de konton som skapas kommer att vara samma. Samma lösenfras är användbart om en enda organisation försöker distribuera i flera regioner och vill dela ett enda konto (mynt grundläggande) i alla noder.|minst 12 tecken|Ej tillämpligt
Genesis block (anpassad genesis block = Yes)|JSON-sträng som representerar anpassade genesis block. Du hittar mer information om formatet för genesis blocket här under anpassade nätverk.<br /><br />Ett Ethereum-konto skapas fortfarande när du anger en anpassad genesis-block. Överväg att ange ett prefunded Ethereum-konto i blocket genesis inte vänta tills datautvinning.|Valid JSON |Ej tillämpligt
Delad nyckel för anslutningen|En delad nyckel för anslutningen mellan VNET-gatewayer.| minst 12 tecken|Ej tillämpligt
Consortium Data-URL|Den URL som pekar mot den relevanta consortium konfigurationsdata som tillhandahålls av en annan medlem distribution. <br /><br />Den här informationen tillhandahålls av en redan anslutna medlem som har en distribution. Om du har distribuerat resten av nätverket är URL: en mall för distribution utdata med namnet CONSORTIUM-DATA.||Ej tillämpligt
VNet-Gateway för att ansluta till|Resurssökväg på VNet-gatewayen som ska anslutas.<br />Den här informationen tillhandahålls av en redan anslutna medlem som har en distribution. Om du har distribuerat resten av nätverket är URL: en i mallutdata för distribution, med namnet CONSORTIUM_MEMBER_GATEWAY_ID. Obs! Samma medlem consortium URL och VNet-Gateway Dataresurs måste användas.||Ej tillämpligt
Slutpunkten för peer-information registrator|Peer-info slutpunkt som tillhandahålls av en annan medlem distribution|Giltig första medlemmen i consortium-slutpunkt|Ej tillämpligt
Nyckel för peer-information registrator|Peer primär nyckel från en annan medlem distribution|Giltig primärnyckel för första medlemmen i consortium|Ej tillämpligt

### <a name="summary"></a>Sammanfattning

Klicka dig igenom sammanfattningen och granska indata som angetts och att köra grundläggande verifiering av distributionen.

![Sammanfattning](./media/ethereum-deployment/summary.png)

Läs juridisk information och sekretess licensvillkoren och klicka på **köp** att distribuera. Om distributionen har fler än en region eller är i ett konsortienätverk, distribuerar den här mallen före de nödvändiga VPN-gatewayerna för att stödja nätverksanslutning med andra medlemmar. Distribution av gatewayen kan ta upp till 45 till 50 minuter.

## <a name="post-deployment-sanity-checks"></a>Publicera hälsokontroller för distribution

### <a name="administrator-page"></a>Sidan för administratör

När distributionen har slutförts och alla resurser som har etablerats, går du till sidan administratör för att få en överblick över nätverket blockchain och förstånd Kontrollera distributionstillstånd för. Webbadressen till sidan är DNS-namnet på belastningsutjämnaren. Det finns i utdataavsnittet i malldistributionen med samma namn som ADMIN_SITE.

Välj den resursgrupp som har distribuerats för att hitta den. Välj **översikt**, och klicka på länken direkt under **distributioner** som visar hur många som har slutförts.

![Översikt över resurs](./media/ethereum-deployment/resource-overview.png)

Den nya skärmen visar distributionshistoriken. Välj den första distributionen (T.ex, microsoft-azure-blockchain.azure-blockchain-servi...) och leta efter den **utdata** avsnitt i den nedre delen av sidan. Webbadressen för sidan anges i mallen distribution output-parameter som ADMIN_SITE visas.

![Resource-distributioner](./media/ethereum-deployment/resource-deployments.png)

![Utdata för distribution](./media/ethereum-deployment/deployment-output.png)

Gå till sidan genom att kopiera den **webbplatsen administration** utdata och öppna den i en annan flik.

På sidan får du en översikt över den topologi som du har distribuerat genom att granska avsnittet Ethereum noden Status. Den innehåller alla nod-värdnamn och deras peer-antal senaste blocket visas. Peer-antalet för varje nod finns mellan minst en mindre än värdet *Totalt antal noder* och antalet konfigurerade maximala peer. Observera att antalet peer inte begränsar antalet noder som kan distribueras i nätverket.
Ibland visas antalet peer fluktuera och vara mindre än det (totalt antalet noder – 1). Skillnaden i antalet är inte alltid ett tecken på att noderna är skadade, eftersom förgreningar i redovisningen kan orsaka mindre ändringar i peer-antal. Slutligen kan du granska den senaste block som setts av varje nod i nätverket för att fastställa förgreningar eller LACP i systemet.

![Nodstatus](./media/ethereum-deployment/node-status.png)

Nod-status uppdateras var tionde sekund. Läs in sidan via webbläsaren eller **Reload** knappen för att uppdatera vyn.

### <a name="oms-portal"></a>OMS-portalen

Du kan hitta din OMS-portalen genom att följa länken i distributionen utdata (OMSPORTALURL) eller genom att välja OMS-resursen i distribuerade resursgruppen.

![OMS-URL](./media/ethereum-deployment/oms-url.png)

![OMS-resurs](./media/ethereum-deployment/oms-resource.png)

Övergripande nätverksstatistik visas först i portalen som en översikt.

![Översikt över OMS](./media/ethereum-deployment/oms-overview.png)

När du klickar på översikten att leda till en portal för att visa statistik per nod.

![Per nod stats](./media/ethereum-deployment/per-node-stats.png)

### <a name="accessing-nodes"></a>Åtkomst till noder

Du kan fjärransluta till de virtuella datorerna för transaktionen noder via SSH med ditt angivna admin användarnamn och lösenord/SSH-nyckel. Eftersom noden transaktion virtuella datorer inte har sina egna offentliga IP-adresser, behöver du gå igenom belastningsutjämnaren och ange portnumret. SSH-kommando för att köra för att komma åt den första noden i transaktionen visas i mallen distribution output-parameter som, **SSH_TO_FIRST_TX_NODE** (för exempeldistribution: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). Gå till ytterligare transaktionsloggar noder genom att öka portnumret med ett (till exempel den första noden i transaktionen är på port 4000).

Du måste gå igenom en av noderna i transaktionen eftersom de virtuella datorerna där utvinning noderna kör inte är externt tillgänglig. När du har en SSH-session till en transaktion nod kan installera din privata nyckel på noden för transaktionen eller ditt lösenord används för att starta en SSH-session på någon av noderna i utvinningsstrukturen.

**Obs!**

Värdnamnen kan hämtas från administratören eller Azure-portalen. Azure-portalen värdnamnen för noder finns i VM scale set (VMSS) resursen finns under **instanser**, vilket skiljer sig från den faktiska värdnamnen. Till exempel värdnamnet i Azure-portalen kan se ut **mn-asdfmv-reg1_0** men själva värdnamnet skulle vara som **mn-asdfmv-reg**.

Exempel:

Azure portal värdnamn| Själva värdnamnet
---|---
mn-ethwvu-reg1_0| mn-ethwvu-reg1000000
mn-ethwvu-reg1_1 |mn-ethwvu-reg1000001
mn-ethwvu-reg1_2 |mn-ethwvu-reg1000002

## <a name="adding-a-new-consortium-member"></a>Att lägga till en ny consortium medlem

### <a name="sharing-data"></a>Delning av data

Som den första medlemmen (eller medlem anslutna) för consortium måste du ange andra medlemmar några uppgifter så att de kan ansluta till och upprätta anslutningen. Närmare bestämt:

1. **Delade Consortium konfigurationsdata**: Det finns en uppsättning data som används för att dirigera Ethereum-anslutning mellan två medlemmar. Nödvändig information, inklusive genesis block, consortium nätverks-ID och bootnoder, skrivs till en fil på noderna transaktion av ledaren eller någon annan distribuerade medlem. Platsen för den här filen finns i mallen distribution utdataparameter med namnet **CONSORTIUM DATA**.
2. **Peer-Info slutpunkt**: Peer info registrator slutpunkten att få information för alla noder som är redan ansluten till Ethereum-nätverk från ledande eller distribution av en annan medlem. DB-butiker en uppsättning av information om varje nod som ansluten i nätverket, information, till exempel nodens värdnamn, privat IP-adress osv. Det här är mallen distribution utdataparameter med namnet **PEER_INFO_ENDPOINT**.
3. **Peer-Info primärnyckel**: Den primära nyckeln för peer-info Registratorn används för att få åtkomst till till ledare eller andra medlem Peer primära nyckel. Det här är mallen distribution utdataparameter med namnet **PEER_INFO_PRIMARY_KEY**.


4. **VNET-Gateway**: Varje medlem upprättar en anslutning till hela blockchain-nätverk via en befintlig medlem. För att ansluta virtuella nätverk, behöver du resurssökväg till VNET-Gateway för medlemmen som du ansluter. Det här är mallen distribution utdataparameter med namnet **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Delad nyckel**: En i förväg upprättade hemlighet mellan två medlemmar i konsortienätverk som upprättar en anslutning. Det här är en alfanumerisk sträng (mellan 1 och 128 tecken) som har kommit överens om utanför ramen för distributionen. (Till exempel **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Godkännande av ny medlem

Det här steget ska göras när den anslutande medlemmen har distribuerats i nätverket. Innan en medlem kan ansluta till nätverket och se transaktion trafik, måste en befintlig medlem utföra en slutgiltig konfiguration på sina VPN-Gateway för att godkänna anslutningen. Det innebär att Ethereum-noder för anslutande medlem inte kan köras förrän en anslutning har upprättats. Den här konfigurationen kan göras via PowerShell eller xPlat CLI. Ett PowerShell-modulen och xPlat CLI-skript lagras också på noden transaktion tillsammans med consortium-data. Skriptets placering är utdataparametrarna distributionen med namnet **par-GATEWAY-PS-MODULE** och **par-GATEWAY-AZURE-CLISCRIPT**respektive.

**PowerShell/CLI-installationsprogrammet**

Mer information om hur du kommer igång med Azure PowerShell-cmdletar och Azure xPlat CLI finns i Azure-dokumentationen.

Du behöver den senaste versionen av Azure-cmdlets installerat lokalt och en session öppen. Se till att logga in i sessionen med dina autentiseringsuppgifter för Azure-prenumeration.

**PowerShell: Upprätta anslutning**

Hämta PowerShell-modulen och lagra den lokalt. Platsen för PowerShell-modulen har angetts som den **par-GATEWAY-PS-MODULE** malldistributionen utdataparameter.

Om du inte redan är aktiverat använder den **Set-ExecutionPolicy** cmdlet: en för den lokala sessionen att köra en osignerad modul.

**Set-ExecutionPolicy obegränsad CurrentUser**

Nästa, logga in på din Azure-prenumeration som du har distribuerat ledare distribution med

**Login-AzureRmAccount**

Därefter importerar du modulen:

**Import-Module <filepath to downloaded file>**

Kör slutligen funktionen med lämpliga indata:

- **MyGatewayResourceId:** Resurssökväg på din Gateway. Det här är mallen distribution utdataparameter med namnet **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId:** Resursens sökväg för den anslutande medlemmen gateway. Detta tillhandahålls av sammanbinder medlemmen och utdataparameter för mall för distribution av också heter **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **Anslutningsnamn:** Ett namn för dig att identifiera den här Gateway-anslutning.
- **Delad nyckel:** I förväg upprättade hemligheten mellan de två medlemmar i konsortienätverk som vill upprätta en anslutning.

**CreateConnection** -MyGatewayResourceId <resource path of your Gateway> - OtherGatewayResourceId < resursens sökväg för den anslutande medlemmen gateway > - ConnectionName myConnection - SharedKey ”MySharedKeyAbc123”

**xPlat CLI: Upprätta anslutning**

Ladda ned Azure CLI-skript och spara den lokalt. Platsen för Azure CLI-skript har angetts i mallparametern för distribution med namnet **par-GATEWAY-AZURE-CLI-skript**.

Kör skriptet med lämpliga indata:

- **MyGatewayResourceId:** Resurssökväg på din Gateway. Det här är mallen distribution utdataparameter med namnet **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId:** Resursens sökväg för den anslutande medlemmen gateway. Detta tillhandahålls av sammanbinder medlemmen och mallparameter för distribution av deras distribution som också kallas **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **Anslutningsnamn:** Ett namn för dig att identifiera den här Gateway-anslutning.
- **Delad nyckel:** I förväg upprättade hemligheten mellan de två medlemmar i konsortienätverk som vill upprätta en anslutning.
- **Plats:** Azure-regionen där din resurs för gatewayen distribueras.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

Arkitekturen blir följande när du har konfigurerat anslutningen mellan **ledare** och **medlem** distributioner.

![Ledare och medlemmen arkitektur](./media/ethereum-deployment/leader-member.png)

## <a name="fund-new-member-account"></a>Finansiera nya medlemskonto

### <a name="generated-genesis-block"></a>Genererade genesis block

Som den första medlemmen Ethereum-konto finansieras med en biljoner ether om distributionen genererar genesis blocket (anpassad Genesis Block = Nej). Andra medlemmar har ett konto som inte redan finansieras och måste vänta juridiken Ether när deras utvinning noder börjar utvinna block. Om du vill undvika att nya medlemmar vänta tills Ether, behöver du uttryckligen finansiera sammanbinder medlemmar Ethereum-konton.

Om du vill göra det måste ansluter till medlemmar ger dig Ethereum-kontot som visas på sin administrationswebbplats. Du kan sedan använda webbplatsen administratör för att överföra Ether från ditt konto till sitt konto genom att ange det konto som anges.

### <a name="custom-genesis-block"></a>Anpassade genesis block

Om ett anpassat genesis block anges med ett visst Ethereum-konto, du kan använda MetaMask eller något annat verktyg för att överföra ether från som angetts för kontot för förgenererade Ethereum visas i admin-webbplatsen. Anvisningar om hur du använder MetaMask finns i [skapar Ethereum konto](#create-ethereum-account).

Om ett anpassat genesis block som tillhandahålls utan ett konto eller om du inte har åtkomst till någon förallokerade, behöver du vänta tills noderna utvinning börja utvinna för att generera Ether till ditt konto (mynt base). Hur snabbt pengar genereras beror på hur svårt att du anger i anpassade genesis blocket.

## <a name="create-ethereum-account"></a>Skapa Ethereum-konto

Om du vill skapa ett ytterligare konto, kan du använda en mängd olika lösningar. En sådan lösning är MetaMask, en Chrome-tillägg som innehåller en identity-valvet och anslutning till ett Ethereum-nätverk, offentlig, testning eller anpassat. MetaMask formulates en transaktion för att registrera kontot i nätverket.

Den här transaktionen, t.ex. en annan transaktion skickas till en av noderna i transaktionen och så småningom används i ett block som på bilden nedan.

![Nod för transaktion](./media/ethereum-deployment/transaction-node.png)

Om du vill installera tillägget i Chrome, går du till anpassa och styra Google Chrome (spill knapp) > fler verktyg > Tillägg > Hämta fler tillägg och Sök efter MetaMask.

![MetaMask tillägg](./media/ethereum-deployment/metamask-extension.png)

När du har installerat öppna MetaMask och skapa ett nytt valv. Som standard ska valvet anslutas till Morden Testnätverket. Ändra det här alternativet för att ansluta till nätverkets distribuerade privata consortium specifikt för belastningsutjämnaren framför transaction-noder. Hämta den exponerade Ethereum RPC slutpunkten på port 8545, med samma namn som mallutdata `ETHEREUM-RPC-ENDPOINT`, och Skriv in den i anpassade RPC enligt nedan.

![MetaMask inställningar](./media/ethereum-deployment/metamask-settings.png)

Genom att skapa valvet kan skapa du en wallet som innehåller ett konto. Om du vill skapa ytterligare konton, Välj **växel konton** och sedan den **+** knappen.

![MetaMask skapa konto](./media/ethereum-deployment/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Initiera första Ether fördelning

På sidan för administratör kan du formulera en transaktion för att överföra Ether från förallokerade kontot till ett annat Ethereum-konto. Den här Ether-överföringen är en transaktion som skickas till noden transaktion och används i ett block som på bilden nedan.

![Nod för transaktion](./media/ethereum-deployment/transaction-node-mined.png)

Kopiera adressen för Ethereum-konto som du vill överföra ether och gå tillbaka till sidan administratör via ikonen Urklipp i MetaMask wallet. Klistra in det kopierade kontot i fältet indata för att överföra 1000 ether från det förallokerade Ethereum-kontot till ditt nya konto. Klicka på **skicka** och vänta tills transaktionen till vara används i ett block.

![Nodstatus](./media/ethereum-deployment/node-status2.png)

När transaktionen genomförs i ett mined block, avspeglar överföringen av 1000 Ether med saldo i MetaMask för ditt konto.

![MetaMask överföring](./media/ethereum-deployment/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Överföring av Ether mellan konton

Nu är du redo att köra transaktioner inom nätverket privata consortium. Den enklaste transaktionen är att överföra Ether från ett konto till en annan. För att formulera sådana en transaktion, kan du använda MetaMask igen, överföra pengar från det första kontot som använde ovan för att ett andra konto.

Från **Wallet 1** i MetaMask, klickar du på Skicka. Kopiera adressen för den andra wallet som skapats i **mottagarens adress** ange fältet och mängden Ether att överföra i den **belopp** indatafält. Klicka på **skicka** och acceptera transaktionen.

![MetaMask skicka transaktion](./media/ethereum-deployment/metamask-send.png)

När transaktionen används och allokerad till ett block, återspeglas igen kontosaldon därefter. Observera att **Wallet 1**'s saldo dras mer än 15 Ether, eftersom var du tvungen att betala en avgift för datautvinning att bearbeta transaktionen.

![Wallet 1](./media/ethereum-deployment/wallet1.png)

![Wallet 2](./media/ethereum-deployment/wallet2.png)

## <a name="next-steps"></a>Nästa steg

Du är nu redo att fokusera på programmet och smarta kontrakt utveckling mot ditt privata consortium blockchain-nätverk.
