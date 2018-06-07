---
title: Ethereum bevis fungerar consortium lösningsmall
description: Med mallen Etherereum bevis av arbete Consortium lösning kan du distribuera och konfigurera flera medlem consortium Ethereum nätverket
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 0ea9bd2c7d23aa227e62858983e7170b771751da
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655471"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Ethereum bevis fungerar consortium lösningsmall

Ethereum bevis av arbete Consortium lösning mallen är avsedd att göra det enklare och snabbare att distribuera och konfigurera flera medlem consortium Ethereum nätverket med minimal kunskap för Azure och Ethereum.

En handfull användarindata och en enda musklick distribution via Azure portal, kan varje medlem etablera sina nätverk storleken, med hjälp av Microsoft Azure Compute, nätverk och lagringstjänster över hela världen. Storleken för varje medlem-nätverk består av en uppsättning belastningsutjämnade transaktion noder med som ett program eller en användare kan samverka för att skicka transaktioner, en uppsättning poster transaktioner utvinningsmodellen noder och en VPN-gateway. Ett efterföljande anslutning steg ansluter gateways för att skapa ett helt konfigurerade flera medlem blockchain nätverk.

## <a name="about-blockchain"></a>Om blockchain

För de som är nya för blockchain community, är versionen av den här lösningen en bra möjlighet att lära dig om tekniken på ett enkelt och konfigurerbara sätt på Azure. Om du vill komma igång, rekommenderar vi dock distribuera enklare fristående Ethereum nätverkets topologi med den här guidad genomgång innan skapa flera medlem consortium nätverk.

### <a name="mining-node-details"></a>Utvinningsmodellen noden information

Noder som min transaktioner separeras från de noder som accepterar transaktioner för att säkerställa att de två åtgärderna inte konkurrerar om samma resurser.

Medlem consortium kan etablera upp till fem regioner som innehåller en eller flera utvinningsmodellen noder, backas upp av en hanterad disk. En eller flera noder i region har konfigurerats som en Startnod för att stödja dynamiska identifieringsmöjligheten för noderna i nätverket. Utvinning noder kommunicerar med andra utvinningsmodellen noder för att gå till konsensus för underliggande distribuerade redovisning tillstånd. Det behövs ingen för att programmet ska vara medveten om eller kommunicera med dessa noder. Genom att fokusera på privata nätverk, isoleras datautvinning noder från inkommande offentliga internet-trafik till en sekundär skyddslager. Utgående trafik tillåts, men inte till Ethereum identifiering port.

Alla noder har en stabil version av klienten gå Ethereum (Geth) och har konfigurerats för att vara utvinningsmodellen noder. Om du inte tillhandahöll ett anpassat genesis block, Använd alla noder samma Ethereum adress och nyckelpar som skyddas av Ethereum kontolösenord. Ethereum lösenfrasen som du angav används för att generera standardkontot (coinbase) för varje nod i utvinningsmodellen. Som utvinningsmodellen noder, min, de samlar in avgifter som läggs till i det här kontot.

Antalet utvinningsmodellen noder per consortium medlem beror på storleken på nätverket som önskas och mängden hash power dedikerad för varje medlem. Ju större nätverk, fler noder som behöver för att få en otillbörlig fördel äventyras. Mallen har stöd för upp till 15 utvinningsmodellen noder per region som etablerats med hjälp av virtuella datorer.

### <a name="transaction-node-details"></a>Transaktionsinformation för nod

Medlem consortium har också en uppsättning noder för Utjämning av nätverksbelastning transaktion. Dessa noder kan nås från utanför det virtuella nätverket så att program kan använda dessa noder för att skicka transaktioner eller köra smart kontrakt i blockchain-nätverket. Alla noder har en stabil version av klienten gå Ethereum (Geth) och har konfigurerats för att upprätthålla en fullständig kopia av den distribuerade redovisningen. Om en anpassad genesis block inte tillhandahålls, använda dessa noder samma Ethereum konto skyddas av Ethereum kontolösenord.

Transaktionen noder är Utjämning av nätverksbelastning i en tillgänglighetsuppsättning för att bibehålla en hög tillgänglighet. Mallen har stöd för upp till fem transaktion noder etablerats med hjälp av virtuella datorer.

### <a name="log-analytics-details"></a>Log analytics-information

Varje distribution även skapar en ny logganalys-instans eller kan ansluta till en befintlig instans. Detta tillåter övervakning av olika prestandamått för varje virtuell dator som utgör det distribuerade nätverket.

## <a name="deployment-architecture"></a>Arkitektur för distribution

### <a name="description"></a>Beskrivning

Den här lösningen mallen kan distribuera en eller flera region-baserade multi medlem Ethereum consortium nätverk. Det virtuella nätverket för varje region är ansluten till andra region i en kedja med gateways för virtuella nätverk och anslutning resurser. Det tillhandahåller också ett register som innehåller information som krävs för alla Miner och transaktionen noder som distribuerats i varje region.

### <a name="standalone-and-consortium-leader-overview"></a>Översikt över fristående och consortium ledande

![Fristående och Consortium ledande översikt](./media/ethereum-deployment-guide/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Koppla consortium medlem översikt

![Koppla Consortium medlem översikt](./media/ethereum-deployment-guide/member-overview.png)

## <a name="getting-started"></a>Komma igång

Den här processen kräver en Azure-prenumeration som har stöd för distribution av flera skalningsuppsättningar i virtuella datorer och hanterade diskar. Vid behov kan du skapa ett kostnadsfritt Azure-konto ska börja.

När en prenumeration är skyddad, gå till Azure-portalen. Välj **+ skapa en resurs**, Marketplace (Visa alla), och Sök efter **Ethereum bevis av arbete Consortium**.

Malldistributionen vägleder dig genom konfigurationen av den första medlemmen storleken i nätverket. Distributionsflödet är indelat i fem steg: grundläggande, Operations Management Suite, distribution regioner, nätverkets storlek och prestanda, Ethereum inställningar.

### <a name="basics"></a>Grundläggande inställningar

Under **grunderna**, ange värden för standard parametrar för en distribution som prenumerationen, resursgruppen och egenskaper för grundläggande virtuella datorer.

![Grundläggande inställningar](./media/ethereum-deployment-guide/sample-deployment.png)

Parameternamn|Beskrivning| Tillåtna värden|Standardvärden
---|---|---|---
Skapa ett nytt nätverk eller ansluta till befintliga nätverk?|Skapa ett nytt nätverk eller ansluta till en befintlig consortium-nätverk|Skapa ny anslutning till befintliga|Skapa ny
Distribuera ett nätverk som ska ingå i en consortium?|En consortium nätverk tillåter framtida distributioner att ansluta till det här nätverket (visas när *Skapa nytt* ovan valda)|Fristående Consortium|Fristående
Resurs-Prefix |Sträng som används som bas för namngivning av resurser (2-4 alfanumeriska tecken). Ett unikt hash-värde är till strängen för vissa resurser, före medan resurs-specifik information har lagts till.|Alfanumeriska tecken med längden 2-4|Ej tillämpligt
Användarnamn för VM| Administratörsanvändarnamnet för varje distribuerad virtuell dator (endast alfanumeriska tecken)|1 – 64 tecken |gethadmin
Autentiseringstyp|Metoden som används för att autentisera till den virtuella datorn. |Lösenord eller SSH offentlig nyckel|Lösenord
Lösenord (autentiseringstyp = lösenord)|Lösenordet för administratörskontot för var och en av de virtuella datorerna som distribueras. Lösenordet måste innehålla 3 av följande krav: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken. <br />Alla virtuella datorer som ursprungligen har samma lösenord, kan du ändra lösenordet när du har etablerat.|12 - 72 tecken|Ej tillämpligt
SSH-nyckel (autentiseringstyp = offentlig nyckel)|Secure shell-nyckel som används för fjärrinloggning.|| Ej tillämpligt
Prenumeration| Den prenumeration som du distribuerar consortium-nätverk||Ej tillämpligt
Resursgrupp| Resursgrupp som du distribuerar consortium-nätverk.||Ej tillämpligt
Plats| Azure-regionen för resursgruppen. ||Ej tillämpligt



### <a name="operations-management-suite"></a>Operations Management Suite

Bladet Operations Management Suite (OMS) kan du konfigurera en OMS-resurs i nätverket. Samlar in OMS och ytan användbara mätvärden och loggar från nätverket, vilket ger möjlighet att snabbt kontrollera nätverkets tillstånd eller debug utfärdar. Gratisversionen av OMS misslyckas avslutas när kapacitet har uppnåtts.

![Skapa ny OMS](./media/ethereum-deployment-guide/new-oms.png)

Parameternamn|Beskrivning| Tillåtna värden|Standardvärden
---|---|---|---
Ansluta till befintliga OMS|Skapa en ny logganalys-instans eller Anslut till en befintlig instans|Skapa ny anslutning till befintliga|Skapa ny Log Analytics-plats|Den region där den nya logganalys ska distribueras (synliga om *Skapa nytt* har valts)
Befintliga OMS arbetsyte-Id|Arbetsyte-ID för den befintliga instansen (synliga om *ansluta till befintliga* väljs) OMS-tjänstnivå|Välj prisnivå för den nya instansen. Mer information på https://azure.microsoft.com/pricing/details/log-analytics/ (synliga om *ansluta till befintliga* har valts)|Ledigt fristående Per nod|Kostnadsfri
Befintlig OMS primärnyckel|Den primära nyckeln som används för att ansluta till den befintliga instansen av OMS (synliga om *ansluta till befintliga* har valts)

### <a name="deployment-regions"></a>Distribution av regioner

Sedan under **distribution regioner**, ange indata för **antalet region(erna)** att distribuera consortium nätverks- och val av Azure-regioner baserat på antalet områden som anges. Användaren kan distribueras i högst fem regioner.

![Distribution av regioner](./media/ethereum-deployment-guide/deployment-regions.png)

Parameternamn| Beskrivning| Tillåtna värden |Standardvärden
---|---|---|---
Antal region(erna)| Antalet regioner som du distribuerar consortium-nätverk|1, 2, 3, 4, 5| 2
Första region| Första region du distribuerar consortium-nätverk|Alla tillåtna Azure-regioner| Västra USA
Andra region |Andra region du distribuerar consortium nätverk (visas bara om antalet områden har valts som 2)|Alla tillåtna Azure-regioner| Östra USA
Tredje region| Tredje region du distribuerar consortium nätverk (visas bara om antalet områden har valts som 3)|Alla tillåtna Azure-regioner| Centrala USA
Fjärde region| Fjärde region du distribuerar consortium nätverk (visas bara om antalet områden har valts som 4)|Alla tillåtna Azure-regioner| Östra USA 2
Femte region| Femte region du distribuerar consortium nätverk (visas bara om antalet områden har valts som 5)|Alla tillåtna Azure-regioner| Västra USA 2

### <a name="network-size-and-performance"></a>Nätverkets storlek och prestanda

Sedan under **storlek och prestanda** ange indata för storleken på consortium-nätverk, till exempel antalet och storleken på utvinningsmodellen noder och transaktionen noder.

![Nätverkets storlek och prestanda](./media/ethereum-deployment-guide/network-size-performance.png)

Parameternamn |Beskrivning |Tillåtna värden| Standardvärden
---|---|---|---
Antalet noder för datautvinning|Antalet noder för datautvinning distribueras per region|2 - 15| 2
Lagringsprestanda för datautvinning nod|Typ av hanterade disklagring varje nod distribuerade utvinningsmodellen.|Standard- eller Premium|Standard
Utvinning nodstorlek för virtuell dator|Den virtuella datorstorleken som används för datautvinning noder.|Standard A <br />Standard D <br />Standard D-v2 <br />Standard F-serien <br />Standard DS <br />och Standard FS|Standard D1v2
Antalet belastningsutjämnade transaktion noder|Antalet transaktion noder att tillhandahålla som en del av nätverket.|1 - 5| 2
Lagringsprestanda för transaktionen nod|Typ av hanterade disklagring varje nod för distribuerad transaktion.|Standard- eller Premium|Standard
Transaktionen nodstorlek för virtuell dator|Den virtuella datorstorleken som används för transaktionen noder.|Standard A <br />Standard D <br />Standard D-v2 <br />Standard F-serien <br />Standard DS <br />och Standard FS|Standard D1v2

### <a name="ethereum-settings"></a>Ethereum inställningar

Sedan under **Ethereum inställningar**, ange Ethereum konfigurationsinställningar som rör, t.ex. nätverks-ID och Ethereum konto lösenord eller genesis blocket.

![Ethereum inställningar](./media/ethereum-deployment-guide/standalone-leader-deployment.png)

Parameternamn |Beskrivning |Tillåtna värden|Standardvärden
---|---|---|---
ConsortiumMember-ID|Det ID som är associerade med varje medlem i consortium nätverket används för att konfigurera IP-adressutrymmen för att undvika kollisionen. <br /><br />Medlems-ID måste vara unikt inom olika organisationer i samma nätverk. Ett unikt medlems-ID krävs även om samma organisation distribuerar till flera regioner.<br /><br />Anteckna värdet för den här parametern eftersom behöver du dela den med andra anslutande medlemmar.|0 - 255
Ethereum nätverks-ID|Nätverks-ID för consortium Ethereum nätverket som ska distribueras. Varje Ethereum nätverk har sin egen nätverks-ID med 1 som ID för ett offentligt nätverk. Åtkomst till nätverket är begränsad för datautvinning noder, rekommenderar vi ändå med hjälp av ett stort antal för att förhindra kollisioner.|5 - 999,999,999| 10101010
Anpassade genesis block|Alternativet för att automatiskt generera ett genesis block eller ange en anpassad.|Ja/nej| Nej
Kontolösenord för Ethereum (anpassad genesis block = Nej)|Administratörslösenordet som används för att skydda kontot Ethereum importeras till varje nod. Lösenordet måste innehålla följande: 1 versal bokstav, 1 gemen bokstav och 1 siffra.|12 eller flera tecken|Ej tillämpligt
Ethereum privata nyckel lösenfras (anpassad genesis block = Nej)|Lösenfras som används för att generera ECC privata nyckeln som associeras med standardkontot för Ethereum som genereras. En förgenererade privat nyckel behöver inte överföras explicit.<br /><br />Beakta en lösenfras med tillräckligt slumpmässighet så att en stark privat nyckel och utan överlappande med andra medlemmar i consortium. Lösenfrasen måste innehålla minst följande: 1 versal bokstav, 1 gemen bokstav och 1 siffra.<br /><br />Observera om två medlemmar använder samma lösenfras de konton som skapas är samma. Samma lösenfras är användbart om en enda organisation försöker distribuera över regioner och vill dela ett enda konto (mynt base) på alla noder.|12 eller flera tecken|Ej tillämpligt
Genesis block (anpassad genesis block = Yes)|JSON-sträng som representerar anpassade genesis block. Du hittar mer information om formatet för genesis blocket här under anpassade nätverk.<br /><br />Ett Ethereum konto skapas fortfarande när en anpassad genesis-block. Överväg att ange ett prefunded Ethereum konto i blocket genesis inte vänta på utvinningsmodellen.|Giltig JSON |Ej tillämpligt
Delad nyckel för anslutningen|En delad nyckel för anslutningen mellan virtuella nätverk gateways.| 12 eller flera tecken|Ej tillämpligt
Webbadressen för Consortium Data|URL: en pekar på relevanta consortium konfigurationsdata som tillhandahålls av en annan medlem distribution. <br /><br />Den här informationen kommer från en redan ansluten medlem som har en distribution. Om du har distribuerat resten av nätverket är URL: en mall för distribution utdata med namnet CONSORTIUM-DATA.||Ej tillämpligt
Gateway för virtuellt nätverk att ansluta till|Resursens sökväg för Gateway för virtuellt nätverk som ska anslutas.<br />Den här informationen kommer från en redan ansluten medlem som har en distribution. Om du har distribuerat resten av nätverket är URL: en i mallutdata för distribution, med namnet CONSORTIUM_MEMBER_GATEWAY_ID. Obs: Samma medlem consortium URL och VNet Gateway Dataresurs måste användas.||Ej tillämpligt
Slutpunkten för peer-information register|Peer-info slutpunkt som tillhandahålls av en annan medlem distribution|Ogiltig slutpunkt av första medlemmen i consortium|Ej tillämpligt
Nyckeln för peer-information register|Peer-primära nyckel som tillhandahålls av en annan medlem distribution|Giltig primärnyckel för första medlemmen i consortium|Ej tillämpligt

### <a name="summary"></a>Sammanfattning

Klicka dig igenom sammanfattning bladet för att granska indata som angetts och att köra grundläggande verifiering av distributionen.

![Sammanfattning](./media/ethereum-deployment-guide/summary.png)

Läs igenom licensvillkoren för juridisk information och sekretess och klickar på **inköp** att distribuera. Om distributionen har mer än en region eller är för ett consortium nätverk, distribuerar mallen före de nödvändiga VPN-gatewayerna för att stödja nätverksanslutning med andra medlemmar. Distribution av gatewayen kan ta upp till 45 till 50 minuter.

## <a name="post-deployment-sanity-checks"></a>Efter distributionen hälsokontroller

### <a name="administrator-page"></a>Administratören sida

När distributionen har slutförts och alla resurser som har etablerats, går du till sidan administratör för att få en överblick över nätverket blockchain och förstånd Kontrollera distributionstillståndet. URL till sidan är DNS-namnet på belastningsutjämnaren. Det finns i utdataavsnittet i malldistribution med samma namn som ADMIN_SITE.

Välj den resursgrupp som har distribuerats för att hitta den. Markera **översikt**, och klicka på länken direkt under **distributioner** som visar hur många som har slutförts.

![Översikt över Resource group](./media/ethereum-deployment-guide/resource-overview.png)

Den nya skärmen visar distributionshistoriken. Välj den första distribution resursen (till exempel microsoft-azure-blockchain.azure-blockchain-servi...) och leta efter den **utdata** avsnitt i den nedre delen av sidan. URL till sidan som anges i mallen distribution output-parameter som ADMIN_SITE visas.

![Resurs-distributioner](./media/ethereum-deployment-guide/resource-deployments.png)

![Distribution av utdata](./media/ethereum-deployment-guide/deployment-output.png)

Gå till sidan genom att kopiera den **webbplatsen administration** utdata och öppna den i en annan flik.

På administratörssidan får du en översikt över topologi som du har distribuerat genom att granska avsnittet Ethereum noden Status. Det innehåller alla nod värdnamn, antal peer och den senaste block visas. Peer-antalet för varje nod är mellan minst en mindre än den *totala antalet noder* och antalet konfigurerade maximala peer-datorn. Observera att antalet peer inte begränsar antalet noder som kan distribueras i nätverket.
Ibland kan visas antalet peer variera och vara mindre än det (totalt antalet noder - 1). Skillnaden i beräkningen är inte alltid ett att noderna är felaktig, eftersom fildelar i redovisningen kan orsaka mindre ändringar i peer-antal tecken. Slutligen kan du granska den senaste block som setts av varje nod i nätverket för att fastställa fildelar eller LACP i systemet.

![Nod status](./media/ethereum-deployment-guide/node-status.png)

Nod status uppdateras var 10: e sekund. Läsa in sidan via webbläsaren eller **ladda** om du vill uppdatera vyn.

### <a name="oms-portal"></a>OMS-portalen

Du kan hitta OMS-portalen genom att följa länken i distributionen utdata (OMSPORTALURL) eller genom att välja OMS-resurs i din distribuerade resursgrupp.

![OMS-URL](./media/ethereum-deployment-guide/oms-url.png)

![OMS-resurs](./media/ethereum-deployment-guide/oms-resource.png)

Övergripande nätverksstatistik visas först i portalen som en översikt.

![OMS-översikt](./media/ethereum-deployment-guide/oms-overview.png)

Klicka på översikten att leda till en portal och visa statistik per nod.

![Statistik för per nod](./media/ethereum-deployment-guide/per-node-stats.png)

### <a name="accessing-nodes"></a>Åtkomst till noder

Du kan fjärransluta till virtuella datorer för transaktionen noder via SSH med din angivna admin användarnamnet och lösenordet/SSH-nyckel. Eftersom noden transaktion virtuella datorer inte har sina egna offentliga IP-adresser, behöver du gå igenom belastningsutjämnaren och ange portnumret. SSH-kommandot ska köras för att få åtkomst till den första noden i transaktion som anges i mallen distribution output-parameter som **SSH_TO_FIRST_TX_NODE** (för distributionen av exempel: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). För att få ytterligare transaktionsloggar noder kan öka portnumret med ett (till exempel den första noden i transaktion är på port 4000).

Eftersom de virtuella datorerna som utvinningsmodellen noder kör inte är externt tillgänglig, som du behöver gå igenom en av noderna transaktion. När du har en SSH-session till en transaktion nod kan installera din privata nyckel på noden transaktion eller ditt lösenord används för att starta en SSH-session till någon av noderna i utvinningsmodellen.

**Obs!**

Värdnamnen kan hämtas från administratören eller den Azure-portalen. I Azure-portalen värdnamn noder finns i resursen virtuella scale set (VMSS) visas under **instanser**, vilket skiljer sig från den faktiska värdnamnen. Till exempel värdnamnet i Azure-portalen kan se ut så **mn-asdfmv-reg1_0** men det faktiska värdnamnet som **mn-asdfmv-reg**.

Exempel:

Azure portal värdnamn| Själva värdnamnet
---|---
mn-ethwvu-reg1_0| mn-ethwvu-reg1000000
mn-ethwvu-reg1_1 |mn-ethwvu-reg1000001
mn-ethwvu-reg1_2 |mn-ethwvu-reg1000002

## <a name="adding-a-new-consortium-member"></a>Lägga till en ny consortium medlem

### <a name="sharing-data"></a>Dela data

Som den första medlemmen (eller medlem anslutna) hos consortium måste du ange andra medlemmar några uppgifter så att de kan ansluta och upprätta anslutningen. Närmare bestämt:

1. **Delade Consortium konfigurationsdata**: det finns en uppsättning data som används för att dirigera Ethereum-anslutning mellan två medlemmar. Nödvändig information, inklusive genesis block, consortium nätverks-ID och bootnoder, skrivs till en fil på transaction-noder i ledande eller någon annan distribuerade medlem. Platsen för den här filen finns i mallen distribution output-parameter med namnet **CONSORTIUM DATA**.
2. **Peer-Info slutpunkt**: den Peer info registrator slutpunkten för att få information för alla noder som är redan ansluten till nätverket Ethereum från ledande eller distribution av en annan medlem. DB-lagrar en uppsättning information om varje nod ansluten i nätverket, information, till exempel nodens värdnamn, privat IP-adress osv. Detta är mallen distribution output-parameter med namnet **PEER_INFO_ENDPOINT**.
3. **Peer-Info primärnyckel**: den Peer info registrator primärnyckeln används kan få åtkomst till styr eller andra medlem Peer info primärnyckel. Detta är mallen distribution output-parameter med namnet **PEER_INFO_PRIMARY_KEY**.


4. **VNET Gateway**: varje medlem upprättar en anslutning till hela blockchain nätverk via en befintlig medlem. För att ansluta virtuella nätverk, behöver du resursens sökväg till Gateway för virtuellt nätverk för medlemmen som du ansluter. Detta är mallen distribution output-parameter med namnet **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Delad nyckel**: A före upprätta hemliga mellan två network-medlemmar consortium som upprättar en anslutning. Detta är en alfanumerisk sträng (mellan 1 och 128 tecken) som har kommit överens om utanför ramen för distributionen. (Till exempel **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Godkännande av ny medlem

Det här steget ska göras när den anslutande medlemmen har distribuerats i nätverket. Innan en medlem kan ansluta till nätverk och se transaktion trafik, måste en befintlig medlem utföra en slutlig konfiguration på sina VPN-Gateway för att godkänna anslutningen. Det innebär att Ethereum noderna på den anslutande medlemmen inte kan köras förrän en anslutning har upprättats. Den här konfigurationen kan utföras via PowerShell eller xPlat CLI. Ett PowerShell-modulen och xPlat CLI-skript lagras också på noden transaktion tillsammans med consortium data. Skriptets placering är utdataparametrarna distribution med namnet **par-GATEWAY-PS-MODULE** och **par-GATEWAY-AZURE-CLISCRIPT**respektive.

**Installationsprogrammet för PowerShell/CLI**

Mer information om hur du kommer igång med Azure PowerShell-cmdlets och Azure xPlat CLI finns i dokumentationen för Azure.

Du behöver den senaste versionen av Azure-cmdlets installeras lokalt och en session öppen. Se till att logga in i sessionen med dina autentiseringsuppgifter för Azure-prenumeration.

**PowerShell: Upprätta anslutning**

Ladda ned PowerShell-modulen och lagras lokalt. Platsen för PowerShell-modulen har angetts som den **par-GATEWAY-PS-MODULE** malldistribution utdataparameter.

Om du inte redan är aktiverat använder den **Set-ExecutionPolicy** cmdlet att köra en osignerad modul lokala session.

**Set-ExecutionPolicy obegränsad CurrentUser**

Nästa inloggning i Azure-prenumerationen som du har distribuerat ledande distribution med

**Login-AzureRmAccount**

Därefter importerar du modulen:

**Import-Module <filepath to downloaded file>**

Slutligen kör funktionen med lämpliga indata:

- **MyGatewayResourceId** : resurssökvägen för din Gateway. Detta är mallen distribution output-parameter med namnet **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : resursens sökväg för den anslutande medlemmen gateway. Detta tillhandahålls av anslutande medlemmen och Utdataparametern mall för distribution av heter **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : ett namn för att identifiera den här Gateway-anslutningen.
- **Delad nyckel** : den i förväg upprättade hemligheten mellan de två medlemmar i nätverket consortium som upprättar en anslutning.

**CreateConnection** -MyGatewayResourceId <resource path of your Gateway> - OtherGatewayResourceId < resursens sökväg för den anslutande medlemmen gateway > - ConnectionName myConnection - SharedKey ”MySharedKeyAbc123”

**xPlat CLI: upprätta anslutning**

Hämta Azure CLI-skript och lagras lokalt. Platsen för Azure CLI-skript har angetts i mallparametern för distribution med namnet **par-GATEWAY-AZURE-CLI-skriptet**.

Kör skriptet med lämpliga indata:

- **MyGatewayResourceId** : resurssökvägen för din Gateway. Detta är mallen distribution output-parameter med namnet **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : resursens sökväg för den anslutande medlemmen gateway. Detta tillhandahålls av anslutande medlem och är mallparameter för distribution av deras distribution som också kallas **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : ett namn för att identifiera den här Gateway-anslutningen.
- **Delad nyckel** : den i förväg upprättade hemligheten mellan de två medlemmar i nätverket consortium som upprättar en anslutning.
- **Plats** : Azure-region där din gateway-resurs har distribuerats.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

Arkitekturen är följande när du har konfigurerat anslutningen mellan **ledande** och **medlem** distributioner.

![Ledande och medlemmen arkitektur](./media/ethereum-deployment-guide/leader-member.png)

## <a name="fund-new-member-account"></a>Finansiering nya medlemskonto

### <a name="generated-genesis-block"></a>Genererade genesis block

Som den första medlemmen Ethereum-konto är grundval med en biljoner ether om distributionen genererar genesis blocket (anpassad Genesis Block = Nej). Andra medlemmar har ett konto som inte är fördefinierade grundval och måste vänta på att lagra Ether när utvinningsmodellen noder börjar min block. Om du vill undvika nya medlemmar vänta Ether, behöver du uttryckligen finansiering anslutande medlemmar Ethereum konton.

Om du vill göra det, måste koppla medlemmar ger dig Ethereum kontot som visas på deras admin-webbplats. Du kan sedan använda webbplatsen admin för att överföra Ether från ditt konto till sitt konto genom att ange det konto som anges.

### <a name="custom-genesis-block"></a>Anpassade genesis block

Om en anpassad genesis block anges med ett visst Ethereum konto, du kan använda MetaMask eller ett annat verktyg för att överföra ether från som angetts för kontot för förgenererade Ethereum visas i admin-webbplatsen. Instruktioner om hur du använder MetaMask finns [konto för att skapa Ethereum](#create-ethereum-account).

Om en anpassad genesis block tillhandahålls utan ett konto eller du har inte åtkomst till alla förallokerade konton, måste vänta tills utvinningsmodellen noderna börjar min för att generera Ether till ditt konto (mynt base). Hur snabbt medel genereras beror på nivån problem som du anger i anpassade genesis blocket.

## <a name="create-ethereum-account"></a>Skapa Ethereum konto

Du kan använda en mängd olika lösningar för att skapa en ytterligare konto. En sådan lösning är MetaMask, en Chrome-tillägg som innehåller en identity-valvet och anslutning till en Ethereum nätverk, offentliga, test eller anpassad. MetaMask formulates en transaktion för att registrera kontot i nätverket.

Den här transaktionen som en annan transaktion skickas till en av noderna transaktion och slutligen minor i ett block som på bilden nedan.

![Transaktionen nod](./media/ethereum-deployment-guide/transaction-node.png)

Om du vill installera tillägget i Chrome, gå till anpassa och styra Google Chrome (spillknappen) > fler verktyg > Tillägg > få fler tillägg och Sök efter MetaMask.

![MetaMask tillägg](./media/ethereum-deployment-guide/metamask-extension.png)

När du har installerat öppna MetaMask och skapa ett nytt valv. Som standard ansluts valvet till Morden Testnätverket. Du måste ändra den här för att ansluta till nätverkets distribuerade privata consortium, särskilt till belastningsutjämnaren framför transaction-noder. Mallutdata, hämta exponerade Ethereum RPC slutpunkten på port 8545, med samma namn som `ETHEREUM-RPC-ENDPOINT`, och i det anpassade RPC enligt nedan.

![MetaMask inställningar](./media/ethereum-deployment-guide/metamask-settings.png)

Genom att skapa valvet kan skapa du en ksprogramvara som innehåller ett konto. Om du vill skapa ytterligare konton **växel konton** och sedan den **+** knappen.

![MetaMask skapa konto](./media/ethereum-deployment-guide/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Initiera första Ether fördelning

Via sidan administratör formulerar du en transaktion för att överföra Ether från förallokerade kontot till ett annat Ethereum konto. Den här Ether överföringen är en transaktion som skickas till noden transaktion och minor i ett block som på bilden nedan.

![Transaktionen nod](./media/ethereum-deployment-guide/transaction-node-mined.png)

Kopiera adressen för kontot Ethereum som du vill överföra ether och gå tillbaka till sidan administratören via ikonen Urklipp i MetaMask ksprogramvara. Klistra in det kopierade kontot i inmatningsfält överför 1000 ether från kontot förallokerade Ethereum till ditt nya konto. Klicka på **skicka** och vänta tills transaktionen minor i ett block.

![Nod status](./media/ethereum-deployment-guide/node-status2.png)

När det genomförs transaktionen till ett mined block, kommer att användas överföringen av 1000 Ether kontosaldo i MetaMask för ditt konto.

![MetaMask överföring](./media/ethereum-deployment-guide/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Överföring av Ether mellan konton

Nu är du redo att utföra transaktioner inom nätverket privata consortium. Den enklaste transaktionen är att överföra Ether från ett konto till en annan. För att formulera sådan transaktion, kan du använda MetaMask igen Överför pengar från det första kontot som ett andra konto ovan.

Från **Ksprogramvara 1** i MetaMask, klickar du på Skicka. Kopiera adressen till den andra ksprogramvara som skapats i **mottagarens adress** ange fältet och mängden Ether att överföra i den **belopp** inmatningsfält. Klicka på **skicka** och acceptera transaktionen.

![MetaMask skicka transaktion](./media/ethereum-deployment-guide/metamask-send.png)

När transaktionen minor och verkställs i ett block, återspeglas igen kontosaldon därefter. Tänk **Ksprogramvara 1**'s saldo dras mer än 15 Ether eftersom var du tvungen att betala en avgift för datautvinning att bearbeta transaktionen.

![Ksprogramvara 1](./media/ethereum-deployment-guide/wallet1.png)

![Ksprogramvara 2](./media/ethereum-deployment-guide/wallet2.png)

## <a name="next-steps"></a>Nästa steg

Du är nu redo att fokusera på programmet och smarta kontraktet utveckling mot nätverket blockchain privata consortium.
