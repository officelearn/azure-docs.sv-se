---
title: Distribuera Ethereum proof-of-Authority – Solution Template på Azure
description: Använd Ethereum-lösningen för att distribuera och konfigurera ett Ethereum-nätverk med flera medlemmar i Azure
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: devx-track-js
ms.openlocfilehash: 5bbfca4d890440574ee6717ca910969226fc781a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987073"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Distribuera Ethereum proof-of-Authority – Solution Template på Azure

Du kan använda [Ethereum-mallen för förhands granskning av Azure-lösningar](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) för att distribuera, konfigurera och styra ett Ethereum-nätverk med flera medlemmar med minimala kunskaper om Azure och Ethereum.

Lösnings mal len kan användas av varje konsortiums medlem för att etablera ett blockchain nätverk med hjälp av Microsoft Azure beräknings-, nätverks-och lagrings tjänster. Varje medlem i konsortiets nätverk består av en uppsättning belastningsutjämnade validator-noder som ett program eller en användare kan interagera med för att skicka Ethereum-transaktioner.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Välj en Azure blockchain-lösning

Innan du väljer att använda Ethereum proof-of-Authority-, jämför ditt scenario med vanliga användnings fall av tillgängliga alternativ för Azure blockchain.

> [!IMPORTANT]
> Överväg att använda [Azure blockchain-tjänsten](../service/overview.md) i stället för Ethereum på Azures lösnings mall. Azure blockchain-tjänsten är en hanterad Azure-tjänst som stöds. Paritets Ethereum övergick till community driven utveckling och underhåll. Mer information finns i [överföra paritets Ethereum till OPENETHEREUM Dao](https://www.parity.io/parity-ethereum-openethereum-dao/).

Alternativ | Tjänst modell | Vanligt användnings fall
-------|---------------|-----------------
Lösningsmallar | IaaS | Solution templates är Azure Resource Manager mallar som du kan använda för att etablera en helt konfigurerad blockchain-nätverkstopologi. Mallarna distribuerar och konfigurerar Microsoft Azure beräknings-, nätverks-och lagrings tjänster för en specifik nätverks typ för blockchain. Solution templates tillhandahålls utan något service nivå avtal. Använd [sidan Microsoft Q&en fråga](/answers/topics/azure-blockchain-workbench.html) för support.
[Azure Blockchain Service](../service/overview.md) | PaaS | För hands versionen av Azure blockchain service fören klar investeringen, hanteringen och styrningen av konsortiet blockchain-nätverk. Använd Azure blockchain-tjänsten för lösningar som kräver PaaS, konsortiets hantering eller kontrakt och transaktions sekretess.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS och PaaS | Azure blockchain Workbench Preview är en samling Azure-tjänster och-funktioner som är utformade för att hjälpa dig att skapa och distribuera blockchain-program för att dela affärs processer och data med andra organisationer. Använd Azure blockchain Workbench för prototyp av en blockchain-lösning eller ett koncept för blockchain-program. Azure Blockchain Workbench tillhandahålls utan serviceavtal. Använd [sidan Microsoft Q&en fråga](/answers/topics/azure-blockchain-workbench.html) för support.

## <a name="solution-architecture"></a>Lösningsarkitekturen

Med hjälp av mallen för Ethereum-lösningar kan du distribuera en enda eller flera regioner-baserade Ethereum-nätverk med flera medlemmar.

![distributions arkitektur](./media/ethereum-poa-deployment/deployment-architecture.png)

Varje distribution av konsortiet ingår:

* Virtual Machines för att köra PoA-verifierarna
* Azure Load Balancer för distribution av RPC-, peering-och styrnings DApp-begäranden
* Azure Key Vault för att skydda verifieraren identiteter
* Azure Storage för att vara värd för beständig nätverksinformation och koordinera leasing
* Azure Monitor för agg regering av loggar och prestanda statistik
* VNet Gateway (valfritt) för att tillåta VPN-anslutningar över privata virtuella nätverk

Som standard är RPC-och peering-slutpunkterna tillgängliga över offentlig IP för att möjliggöra förenklad anslutning mellan prenumerationer och moln. För åtkomst på program nivå kan du använda Paritetens godkännande avtal. Nätverk som distribueras bakom VPN, som utnyttjar VNet-gatewayer för anslutning mellan prenumerationer stöds. Eftersom VPN-och VNet-distributioner är mer komplexa kan du behöva börja med en offentlig IP-modell vid prototyp av en lösning.

Docker-behållare används för tillförlitlighet och modulärhet. Azure Container Registry används för att vara värd för och hantera versions avbildningar som en del av varje distribution. Behållar avbildningarna består av:

* Orchestrator – genererar identiteter och styrnings avtal. Lagrar identiteter i ett identitets lager.
* Paritets klient – lånar identitet från identitets lagret. Identifierar och ansluter till peer-datorer.
* EthStats-agent – samlar in lokala loggar och statistik via RPC och push-överför information till Azure Monitor.
* Styrning DApp – webb gränssnitt för att interagera med styrnings avtal.

### <a name="validator-nodes"></a>Validator-noder

I proof-of-Authority-protokollet tar verifierade noder platsen för traditionella Miner-noder. Varje verifierare har en unik Ethereum-identitet som gör det möjligt att delta i processen för att skapa block. Varje konsortiums medlem kan etablera två eller fler verifierade noder i fem regioner för GEO-redundans. Validator-noder kommunicerar med andra validator-noder för att komma till enighet om status för den underliggande distribuerade redovisningen. För att säkerställa en rättvis medverkan i nätverket, tillåts varje konsortiums medlem inte att använda fler verifierare än den första medlemmen i nätverket. Om den första medlemmen till exempel distribuerar tre verifierare får varje medlem bara ha upp till tre verifierare.

### <a name="identity-store"></a>Identitets Arkiv

Ett identitets lager distribueras i varje medlems prenumeration som har genererat Ethereum-identiteter på ett säkert sätt. För varje verifierare genererar Orchestration-behållaren en privat Ethereum-nyckel och lagrar den i Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Distribuera Ethereum Consortium-nätverk

I den här genom gången antar vi att du skapar ett Ethereum konsortium-nätverk med flera parter. Följande flöde är ett exempel på en distribution med flera parter:

1. Tre medlemmar varje generera ett Ethereum-konto med MetaMask
1. *Medlem A* distribuerar Ethereum POA, vilket ger sin Ethereum offentliga adress
1. *Medlem A* innehåller konsortiets URL till *medlem B* och *medlem C*
1. *Medlem B* och *medlem C* -distribution, Ethereum POA, som tillhandahåller sin Ethereum offentliga adress och *medlem A*: s konsortiums-URL
1. *Medlem A* röster i *medlem B* som administratör
1. *Medlem A* och *medlem B* både röst *medlem C* som administratör

I nästa avsnitt visas hur du konfigurerar den första medlemmens storlek i nätverket.

### <a name="create-resource"></a>Skapa resurs

I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs** i det övre vänstra hörnet.

Välj **blockchain**  >  **Ethereum proof-of-Authority Consortium (för hands version)**.

### <a name="basics"></a>Grunder

Under **grunderna** anger du värden för standard parametrar för alla distributioner.

![Grunder](./media/ethereum-poa-deployment/basic-blade.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Skapa ett nytt nätverk eller Anslut till ett befintligt nätverk | Du kan skapa ett nytt konsortiums nätverk eller ansluta till ett befintligt konsortiums nätverk. För att kunna ansluta till ett befintligt nätverk krävs ytterligare parametrar. | Skapa ny
E-postadress | Du får ett e-postmeddelande när distributionen är klar med information om distributionen. | En giltig e-postadress
Användar namn för virtuell dator | Administratörs användar namn för varje distribuerad virtuell dator | 1-64 alfanumeriska tecken
Autentiseringstyp | Metoden för att autentisera till den virtuella datorn. | Lösenord
Lösenord | Lösen ordet för administratörs kontot för var och en av de virtuella datorerna som distribueras. Alla virtuella datorer har till början samma lösen ord. Du kan ändra lösen ordet efter etableringen. | 12-72 tecken 
Prenumeration | Den prenumeration som används för att distribuera konsortiets nätverk |
Resursgrupp| Den resurs grupp som används för att distribuera konsortial nätverket. | myResourceGroup
Plats | Azure-regionen för resurs gruppen. | USA, västra 2

Välj **OK**.

### <a name="deployment-regions"></a>Distributions regioner

Under *distributions regioner* anger du antalet regioner och platser för var och en. Du kan distribuera högst fem regioner. Den första regionen ska överensstämma med avsnittet för resurs gruppens plats från *grunderna* . För utvecklings-eller test nätverk kan du använda en enda region per medlem. För produktion kan du distribuera över två eller flera regioner för hög tillgänglighet.

![distributions regioner](./media/ethereum-poa-deployment/deployment-regions.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Antal regioner|Antal regioner för att distribuera konsortiets nätverk| 2
Första regionen | Första regionen för att distribuera konsortiet nätverk | USA, västra 2
Andra regionen | Andra regionen för att distribuera konsortiets nätverk. Ytterligare regioner visas när antalet regioner är två eller fler. | USA, östra 2

Välj **OK**.

### <a name="network-size-and-performance"></a>Nätverks storlek och prestanda

Under *nätverks storlek och prestanda* anger du indata för storleken på konsortiets nätverk. Lagrings storleken för validator-noden styr den potentiella storleken på blockchain. Storleken kan ändras efter distributionen.

![Nätverks storlek och prestanda](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Antal noder för belastningsutjämnad verifiering | Antalet verifierade noder som ska etableras som en del av nätverket. | 2
Lagrings prestanda för validator-nod | Den typ av hanterad disk som ska användas för var och en av de distribuerade validator-noderna. Mer information om priser finns i [Storage-priser](https://azure.microsoft.com/pricing/details/managed-disks/) | Standard SSD
Virtuell dator storlek för validator-nod | Den virtuella dator storleken som används för verifierade noder. Mer information om priser finns i [priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | Standard D2 v3

Den virtuella datorn och lagrings nivån påverkar nätverks prestanda.  Använd följande tabell för att få hjälp att välja kostnads effektivitet:

SKU för virtuell dator|Lagrings nivå|Pris|Dataflöde|Svarstid
---|---|---|---|---
F1|Standard SSD|börjar|börjar|hög
D2_v3|Standard SSD|medel|medel|medel
F16-enheter|Premium SSD|hög|hög|börjar

Välj **OK**.

### <a name="ethereum-settings"></a>Ethereum-inställningar

Under *Inställningar för Ethereum* anger du Ethereum konfigurations inställningar.

![Ethereum-inställningar](./media/ethereum-poa-deployment/ethereum-settings.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Konsortiums medlems-ID | Det ID som är kopplat till varje medlem som ingår i konsortiets nätverk. Den används för att konfigurera IP-adressutrymme för att undvika kollision. För ett privat nätverk ska medlems-ID: t vara unikt i olika organisationer i samma nätverk.  Ett unikt medlems-ID krävs även om samma organisation distribuerar till flera regioner. Anteckna värdet för den här parametern eftersom du måste dela den med andra medlemmar för att se till att det inte finns någon kollision. Giltigt intervall är 0 till 255. | 0
Nätverks-ID | Nätverks-ID för konsortiet Ethereum-nätverk som ska distribueras. Varje Ethereum nätverk har sitt eget nätverks-ID, med 1 som ID för det offentliga nätverket. Giltigt intervall är 5 till 999 999 999 | 10101010
Admin Ethereum-adress | Ethereum-konto adress som används för att delta i PoA-styrning. Du kan använda MetaMask för att generera en Ethereum-adress. |
Avancerade alternativ | Avancerade alternativ för Ethereum-inställningar | Aktivera
Distribuera med hjälp av offentlig IP | Om du väljer privat VNet, distribueras nätverket bakom en VNet-gateway och tar bort peering-åtkomst. För privata virtuella nätverk måste alla medlemmar använda en VNet-Gateway för att anslutningen ska vara kompatibel. | Offentlig IP-adress
Block gas gräns | Nätverkets start Blocks gräns. | 50000000
Blockera omförslutning av period (SEK) | Den frekvens med vilken tomma block skapas när det inte finns några transaktioner i nätverket. En högre frekvens kommer att ha snabbare slut för and öka lagrings kostnaderna. | 15
Transaktions godkännande kontrakt | Bytekod för transaktions behörighets avtal. Begränsar distribution och körning av smarta kontrakt till en lista över tillåtna Ethereum-konton. |

Välj **OK**.

### <a name="monitoring"></a>Övervakning

Med övervakning kan du konfigurera en logg resurs för nätverket. Övervaknings agenten samlar in och samlar in användbara mått och loggar från nätverket och ger möjlighet att snabbt kontrol lera nätverks hälsan eller fel söknings problem.

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Övervakning | Alternativ för att aktivera övervakning | Aktivera
Ansluta till befintliga Azure Monitor loggar | Alternativ för att skapa en ny Azure Monitor loggar instans eller ansluta till en befintlig instans | Skapa ny
Plats | Den region där den nya instansen distribueras | East US
Befintligt logganalys-arbetsyte-ID (Anslut till befintliga Azure Monitor loggar = koppling befintlig)|Arbetsyte-ID för den befintliga Azure Monitor loggar instansen||NA
Befintlig Log Analytics primär nyckel (Anslut till befintliga Azure Monitor loggar = koppling befintlig)|Den primära nyckel som används för att ansluta till den befintliga Azure Monitor loggar instansen||NA

Välj **OK**.

### <a name="summary"></a>Sammanfattning

Klicka dig igenom sammanfattningen för att granska de angivna indata och köra grundläggande verifiering före distribution. Innan du distribuerar kan du hämta mallen och parametrarna.

Välj **skapa** för att distribuera.

Om distributionen inkluderar VNet-gatewayer kan distributionen ta 45 till 50 minuter.

## <a name="deployment-output"></a>Distributions utdata

När distributionen har slutförts kan du komma åt de parametrar som krävs med hjälp av Azure Portal.

### <a name="confirmation-email"></a>Bekräfta e-post

Om du anger en e-postadress ([avsnittet grundläggande](#basics)) skickas ett e-postmeddelande som innehåller distributions information och länkar till den här dokumentationen.

![e-postdistribution](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portalen

När distributionen har slutförts och alla resurser har etablerats kan du Visa utdataparametrar i resurs gruppen.

1. Gå till resurs gruppen i portalen.
1. Välj **översikt > distributioner**.

    ![Översikt över resurs grupp](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Välj den **Microsoft-Azure-blockchain. Azure-blockchain-eter-...-** distributionen.
1. Välj avsnittet **utdata** .

    ![Distributions utdata](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Växande konsortiet

Om du vill expandera ditt konsortium måste du först ansluta det fysiska nätverket. Om du distribuerar bakom en VPN-anslutning kan du läsa avsnittet [ansluta VNet Gateway](#connecting-vnet-gateways) konfigurera nätverks anslutningen som en del av den nya medlems distributionen. När distributionen är klar använder du [styrnings DApp](#governance-dapp) för att bli nätverks administratör.

### <a name="new-member-deployment"></a>Ny medlems distribution

Dela följande information med den anslutna medlemmen. Informationen finns i e-postmeddelandet efter distributionen eller i portalen.

* Data-URL för konsortiet
* Antalet noder som du har distribuerat
* Resurs-ID för VNet-Gateway (om du använder VPN)

Den distribuerade medlemmen bör använda samma Ethereum-mall för certifikat utfärdare som används för att distribuera nätverks närvaron med hjälp av följande rikt linjer:

* Välj **Anslut befintlig**
* Välj samma antal validator-noder som resten av medlemmarna i nätverket för att säkerställa en rättvis representation
* Använd samma administratörs Ethereum-adress
* Använd den angivna *konsortiet data-URL: en* i *Ethereum-inställningarna*
* Om resten av nätverket ligger bakom en VPN väljer du **privat VNet** under avsnittet Avancerat

### <a name="connecting-vnet-gateways"></a>Ansluta VNet-gatewayer

Det här avsnittet krävs endast om du har distribuerat med ett privat VNet. Du kan hoppa över det här avsnittet om du använder offentliga IP-adresser.

För ett privat nätverk är de olika medlemmarna anslutna via VNet Gateway-anslutningar. Innan en medlem kan ansluta till nätverket och se transaktions trafik, måste en befintlig medlem utföra en slutgiltig konfiguration på sin VPN-gateway för att godkänna anslutningen. Ethereum-noderna i den anslutna medlemmen körs inte förrän en anslutning har upprättats. Du kan minska risken för en enskild felpunkt genom att skapa redundanta nätverks anslutningar i konsortiet.

När den nya medlemmen har distribuerats måste den befintliga medlemmen slutföra den dubbelriktade anslutningen genom att konfigurera en VNet Gateway-anslutning till den nya medlemmen. Befintliga medlems behov:

* VNet Gateway-ResourceID för den anslutna medlemmen. Se [distributions utdata](#deployment-output).
* Den delade anslutnings nyckeln.

Den befintliga medlemmen måste köra följande PowerShell-skript för att slutföra anslutningen. Du kan använda Azure Cloud Shell som finns i det övre högra navigerings fältet i portalen.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>Tjänstövervakning

Du kan hitta din Azure Monitor Portal genom att följa länken i e-postadressen för distribution eller hitta parametern i distributions resultatet [OMS_PORTAL_URL].

Portalen visar först nätverks statistik och node-översikt på hög nivå.

![Övervaka kategorier](./media/ethereum-poa-deployment/monitor-categories.png)

Om du väljer **node-översikt**  visas infrastruktur statistik per nod.

![Nods statistik](./media/ethereum-poa-deployment/node-stats.png)

Om du väljer **nätverks** statistik visas Ethereum nätverks statistik.

![Nätverks statistik](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Du kan fråga övervaknings loggarna om du vill undersöka felen eller varningar om tröskeln för konfigurationen. Följande frågor är exempel som du kan köra i *loggs öknings* verktyget:

List block som har rapporter ATS av fler än en verifierings fråga kan vara användbara för att hitta kedje delar.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Få ett genomsnittligt antal peer-datorer för en angiven validator-nod i genomsnitt över 5 minuters buckets.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>SSH-åtkomst

Av säkerhets skäl nekas åtkomst till SSH-porten av en säkerhets regel för nätverks grupper som standard. Om du vill komma åt de virtuella dator instanserna i PoA-nätverket måste du ändra följande regel för att *tillåta*.

1. Gå till **översikts** avsnittet i den distribuerade resurs gruppen i Azure Portal.

    ![Översikt över SSH](./media/ethereum-poa-deployment/ssh-overview.png)

1. Välj **nätverks säkerhets gruppen** för regionen för den virtuella dator som du vill komma åt.

    ![SSH-NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Välj regeln **Allow-SSH** .

    ![Skärm dum par visar ett översikts fönster med alternativet SSH-Allow markerat.](./media/ethereum-poa-deployment/ssh-allow.png)

1. Ändra **åtgärd** att **tillåta**

    ![Tillåt att SSH aktive ras](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Välj **Spara**. Det kan ta några minuter att göra ändringar.

Du kan fjärrans luta till de virtuella datorerna för validator-noder via SSH med ditt tillhandahållna administratörs användar namn och lösen ord/SSH-nyckel. SSH-kommandot för att komma åt den första validator-noden visas i mallens distributions data. Ett exempel:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Öka port numret med ett om du vill gå till ytterligare Transaction Nodes.

Om du har distribuerat till fler än en region ändrar du kommandot till belastnings utjämningens DNS-namn eller IP-adress i den regionen. Om du vill hitta DNS-namn eller IP-adress för de andra regionerna hittar du resursen med namngivnings konventionen **\* \* \* \* \* – lbpip \# -REG** och visar dess egenskaper för DNS-namn och IP-adress.

## <a name="azure-traffic-manager-load-balancing"></a>Belastnings utjämning i Azure Traffic Manager

Azure Traffic Manager kan hjälpa till att minska stillestånds tiden och förbättra svars tiden för PoA-nätverket genom att dirigera inkommande trafik över flera distributioner i olika regioner. Inbyggda hälso kontroller och automatisk omdirigering säkerställer hög tillgänglighet för RPC-slutpunkterna och styrnings DApp. Den här funktionen är användbar om du har distribuerat till flera regioner och är redo för produktion.

Använd Traffic Manager för att förbättra PoA nätverks tillgänglighet med automatisk redundans. Du kan också använda Traffic Manager för att öka nätverkets svars tid genom att dirigera slutanvändare till Azure-platsen med lägsta nätverks fördröjning.

Om du väljer att skapa en Traffic Manager-profil kan du använda DNS-namnet på profilen för att komma åt nätverket. När andra medlemmar i konsortiet har lagts till i nätverket kan Traffic Manager också användas för att belastningsutjämna över sina distribuerade verifierare.

### <a name="creating-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs** i det övre vänstra hörnet.
1. Sök efter **Traffic Manager profil**.

    ![Sök efter Azure-Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Ge profilen ett unikt namn och välj den resurs grupp som användes för PoA-distributionen.

1. Välj **skapa** för att distribuera.

    ![Skapa Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. När du har distribuerat väljer du instansen i resurs gruppen. DNS-namnet som du kan använda för att komma åt Traffic Manager finns på fliken Översikt.

    ![Hitta Traffic Manager DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Välj fliken **slut punkter** och välj knappen **Lägg till** .
1. Ge slut punkten ett unikt namn.
1. Välj **offentlig IP-adress** för **mål resurs typ**.
1. Välj den offentliga IP-adressen för den första regionens belastningsutjämnare.

    ![Routning Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Upprepa för varje region i det distribuerade nätverket. När slut punkterna har statusen **aktive rad** läses de in automatiskt och regions balansen sker i Traffic Managers DNS-namn. Du kan nu använda DNS-namnet i stället för parametern [CONSORTIUM_DATA_URL] i andra steg i artikeln.

## <a name="data-api"></a>Data-API

Varje konsortiums medlem är värd för den information som behövs för att andra ska kunna ansluta till nätverket. För att under lätta anslutningen är varje medlem en uppsättning anslutnings information på data API-slutpunkten.

Den befintliga medlemmen innehåller [CONSORTIUM_DATA_URL] innan medlemmens distribution. Vid distributionen hämtar en medlem som ansluter information från JSON-gränssnittet vid följande slut punkt:

`<CONSORTIUM_DATA_URL>/networkinfo`

Svaret innehåller information som är användbar för att ansluta till medlemmar (Genesis-block, validator set kontrakts-ABI, bootnodes) och information som är användbar för den befintliga medlemmen (verifierare adresser). Du kan använda den här standardiseringen för att utöka konsortiet över moln leverantörer. Detta API returnerar ett JSON-formaterat svar med följande struktur:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>Styrnings DApp

När det gäller certifikat beviset är decentraliserad styrning. Eftersom bevis på auktoritet förlitar sig på en tillåten lista över nätverks certifikat för att hålla nätverket felfritt, är det viktigt att tillhandahålla en rättvis mekanism för att göra ändringar i behörighets listan. Varje distribution levereras med en uppsättning smarta kontrakt och portaler för styrning av den här listan över tillåtna användare. När en föreslagen ändring når en majoritets röst av konsortiets medlemmar, är ändringen i sitt beslut. Röstning gör det möjligt att lägga till eller komprometterade deltagare som kan tas bort på ett transparent sätt som uppmuntrar ett Honest nätverk.

Styrnings DApp är en uppsättning fördistribuerade [smarta kontrakt](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) och ett webb program som används för att styra myndigheterna i nätverket. Utfärdarna är indelade i admin identiteter och validator-noder.
Administratörer har möjlighet att delegera konsensus deltagande till en uppsättning validator-noder. Administratörer kan också rösta andra administratörer i eller ut ur nätverket.

![Styrnings DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **Decentraliserad styrning:** Ändringar i nätverks myndigheterna administreras genom en röst markering genom att välja administratörer.
* **Delegering av verifierare:** Myndigheter kan hantera sina verifierade noder som ställs in i varje PoA-distribution.
* **Gransknings bar ändrings historik:** Varje ändring registreras på blockchain och ger insyn och gransknings möjlighet.

### <a name="getting-started-with-governance"></a>Komma igång med styrning

Om du vill utföra någon typ av transaktioner via styrnings DApp måste du använda en Ethereum plån bok. Den enklaste metoden är att använda en plån bok i en webbläsare, till exempel [MetaMask](https://metamask.io); men eftersom dessa smarta kontrakt har distribuerats i nätverket kan du också automatisera dina interaktioner till styrnings kontraktet.

När du har installerat MetaMask navigerar du till styrnings-DApp i webbläsaren.  Du kan hitta URL: en via Azure Portal i distributionens utdata.  Om du inte har en plån bok som är installerad i webbläsaren kan du inte utföra några åtgärder. Du kan dock Visa administratörs tillstånd.  

### <a name="becoming-an-admin"></a>Bli administratör

Om du är den första medlem som distribueras i nätverket blir du automatiskt en administratör och dina paritets-noder visas som verifierare. Om du ansluter till nätverket måste du ha röstat in som administratör med en majoritet (större än 50%) av den befintliga administratörs uppsättningen. Om du väljer att inte bli administratör synkroniserar noderna fortfarande och validerar blockchain. de deltar dock inte i processen för att skapa block. Starta röstnings processen och bli administratör genom att välja **nominera** och ange din Ethereum-adress och alias.

![Utse](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Beds

Om du väljer fliken **kandidater** visas den aktuella uppsättningen av kandidat administratörer.  När en kandidat når majoritets omröstning av de aktuella administratörerna, kommer kandidat gruppen att uppgraderas till en administratör.  Om du vill rösta på en kandidat markerar du raden och väljer **rösta i**. Om du ändrar dig för en röst väljer du kandidaten och väljer **återkalla röst**.

![Beds](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Administratörer

Fliken **Administratörer** visar den aktuella uppsättningen administratörer och ger dig möjlighet att rösta mot.  När en administratör har förlorat mer än 50% stöd tas de bort som administratör i nätverket. Alla validerade noder som administratören äger förlorar verifierings status och blir transaktions noder i nätverket. En administratör kan tas bort av valfritt antal orsaker. Det är dock upp till konsortiet att enas om en princip i förväg.

![Administratörer](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Verifierare

Om du väljer fliken **verifierare** visas de aktuella distribuerade paritets noderna för instansen och deras aktuella status (nodtyp). Varje konsortiums medlem har en annan uppsättning verifierare i listan, eftersom den här vyn representerar den aktuella distribuerade konsortiet medlem. Om instansen nyligen distribueras och du inte har lagt till dina verifierare får du möjlighet att **lägga till verifierare**. Genom att lägga till validerare väljs automatiskt en regionalt bal anse rad uppsättning paritets noder och tilldelar dem till din verifierings uppsättning. Om du har distribuerat fler noder än den tillåtna kapaciteten blir de återstående noderna Transaction-noder i nätverket.

Adressen för varje verifierare tilldelas automatiskt via [identitets arkivet](#identity-store) i Azure.  Om en nod kraschar, överlåter den sin identitet, vilket gör att en annan nod i distributionen kan ta sin plats. Den här processen säkerställer att ditt samförstånds bidrag är högt tillgängligt.

![Verifierare](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Namn på konsortium

Alla administratörer kan uppdatera namnet på konsortiet.  Välj kugg hjuls ikonen längst upp till vänster för att uppdatera namnet på konsortiet.

### <a name="account-menu"></a>Menyn konto

Längst upp till höger är ditt Ethereum-konto-alias och Identicon.  Om du är administratör har du möjlighet att uppdatera ditt alias.

![Konto](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Ethereum-utveckling<a id="tutorials"></a>

För att kunna kompilera, distribuera och testa smarta kontrakt finns här några alternativ som du kan tänka på för Ethereum-utveckling:
* [Truffle Suite](https://www.trufflesuite.com/docs/truffle/overview) – klient-baserad Ethereum utvecklings miljö
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) – webbläsarbaserad och lokal Ethereum utvecklings miljö

### <a name="compile-deploy-and-execute-smart-contract"></a>Kompilera, distribuera och köra ett smart kontrakt

I följande exempel skapar du ett enkelt Smart kontrakt. Du använder Truffle för att kompilera och distribuera det smarta kontraktet till ditt blockchain-nätverk. När du har distribuerat anropar du en smart kontrakts funktion via en transaktion.

#### <a name="prerequisites"></a>Förutsättningar

* Installera [python-2.7.15](https://www.python.org/downloads/release/python-2715/). Python krävs för Truffle och web3. Välj alternativet Installera för att inkludera python i sökvägen.
* Installera Truffle v-5.0.5 `npm install -g truffle@v5.0.5` . Truffle kräver att flera verktyg installeras, inklusive [Node.js](https://nodejs.org), [git](https://git-scm.com/). Mer information finns i [Truffle-dokumentationen](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Skapa Truffle-projekt

Innan du kan kompilera och distribuera ett smart kontrakt måste du skapa ett Truffle-projekt.

1. Öppna en kommando tolk eller ett gränssnitt.
1. Skapa en mapp med namnet `HelloWorld`.
1. Ändra katalogen till den nya `HelloWorld` mappen.
1. Initiera ett nytt Truffle-projekt med hjälp av kommandot `truffle init` .

    ![Skapa ett nytt Truffle-projekt](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Lägg till ett smart kontrakt

Skapa dina smarta kontrakt i under katalogen **kontrakt** i Truffle-projektet.

1. Skapa en fil med namnet `postBox.sol` i under katalogen **kontrakt** i Truffle-projektet.
1. Lägg till följande halvledar kod i **postBox. sol**.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Distribuera Smart kontrakt med Truffle

Truffle-projekt innehåller en konfigurations fil för blockchain-nätverks anslutnings information. Ändra konfigurations filen så att den innehåller anslutnings informationen för nätverket.

> [!WARNING]
> Skicka aldrig din privata Ethereum-nyckel över nätverket. Se till att varje transaktion signeras lokalt först och att den signerade transaktionen skickas över nätverket.

1. Du behöver en LED texts fras för det [Ethereum administratörs konto som används för att distribuera blockchain-nätverket](#ethereum-settings). Om du använde MetaMask för att skapa kontot kan du hämta det från MetaMask. Välj ikonen administratörs konto längst upp till höger i MetaMask-tillägget och välj **inställningar > säkerhets & sekretess > Visa Seed-ord**.
1. Ersätt innehållet i `truffle-config.js` Truffle-projektet med följande innehåll. Ersätt plats hållarens slut punkt och instruktioner.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Eftersom vi använder Truffle HD-providern för plån boks tjänster installerar du modulen i projektet med hjälp av kommandot `npm install truffle-hdwallet-provider --save` .

Truffle använder migrations skript för att distribuera smarta kontrakt till ett blockchain-nätverk. Du behöver ett migreringsarkiv för att distribuera ditt nya smarta kontrakt.

1. Lägg till en ny migrering för att distribuera det nya kontraktet. Skapa fil `2_deploy_contracts.js` i under katalogen **migreringar** i Truffle-projektet.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Distribuera till PoA-nätverket med kommandot Truffle Migrate. Kör följande i kommando tolken i Truffle-projekt katalogen:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Anropa en smart kontrakts funktion

Nu när ditt smarta kontrakt har distribuerats kan du skicka en transaktion för att anropa en funktion.

1. Skapa en ny fil med namnet i Truffle-projekt katalogen `sendtransaction.js` .
1. Lägg till följande innehåll i **sendtransaction.js**.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Kör skriptet med kommandot Truffle EXECUTE.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Kör skript för att anropa funktionen via transaktion](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>WASM-stöd (WebAssembly)

WebAssembly support har redan Aktiver ATS för dig i nyligen distribuerade PoA-nätverk. Det gör det möjligt att utveckla Smart-kontrakt på alla språk som instaplar i Web-Assembly (Rust, C, C++). Mer information finns i [självstudier från paritets teknik](https://github.com/paritytech/pwasm-tutorial).

## <a name="faq"></a>Vanliga frågor

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Jag märker att det finns många transaktioner i nätverket som jag inte har skickat. Var kommer de att komma från?

Det är inte säkert att låsa upp det [personliga API: et](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). Robotar lyssnar efter olåsta Ethereum-konton och försöker tömma penning medlen. Roboten förutsätter att dessa konton innehåller verklig och försöker vara det första för att Siphon saldot. Aktivera inte det personliga API: t i nätverket. I stället försignera transaktionerna manuellt med en plån boks gilla som MetaMask eller program mässigt.

### <a name="how-to-ssh-onto-a-vm"></a>Hur ska SSH på en virtuell dator?

SSH-porten exponeras inte av säkerhets skäl. Följ [den här guiden för att aktivera SSH-porten](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Hur gör jag för att skapar du en gransknings medlems-eller Transaction-noder?

Transaction Nodes är en uppsättning paritets klienter som peer-kopplas till nätverket men inte deltar i samförstånd. Dessa noder kan fortfarande användas för att skicka Ethereum-transaktioner och läsa status för smart kontrakt. Den här mekanismen fungerar för att tillhandahålla gransknings behörighet till medlemmar som inte tillhör en myndighet i nätverket. Följ stegen i utveckla [konsortiet](#growing-the-consortium)för att uppnå detta.

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Varför tar MetaMask-transaktioner längre tid?

För att säkerställa att transaktioner tas emot i rätt ordning, levereras varje Ethereum-transaktion med en stegvis ökning av nonce. Om du har använt ett konto i MetaMask i ett annat nätverk måste du återställa värdet för nonce. Klicka på inställnings ikonen (tre staplar), inställningar, Återställ konto. Transaktions historiken tas bort och nu kan du skicka transaktionen igen.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Måste jag ange en gas avgift i MetaMask?

Eter är inte ett syfte i proof-of-Authority-konsortiet. Därför behöver du inte ange någon avgift för naturgas när du skickar transaktioner i MetaMask.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Vad ska jag göra om min distribution Miss lyckas på grund av att det inte går att etablera Azure OMS?

Övervakning är en valfri funktion. I vissa sällsynta fall där distributionen Miss lyckas på grund av att det inte går att etablera Azure Monitor resurs kan du distribuera om utan Azure Monitor.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Är offentliga IP-distributioner kompatibla med privata nätverks distributioner?

Nej. Peering kräver kommunikation på två sätt så att hela nätverket antingen måste vara offentligt eller privat.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Vad är det förväntade transaktions flödet för proof-of-Authority?

Transaktions data flödet är mycket beroende av typerna av transaktioner och nätverk sto pol Ogin. Med enkla transaktioner har vi förvaltat ett genomsnitt på 400 transaktioner per sekund med ett nätverk som distribuerats över flera regioner.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Hur gör jag för att prenumerera på smarta kontrakt händelser?

Ethereum proof-of-Authority stöder nu webb-Sockets.  Kontrol lera dina distributions utdata för att hitta webbsocket-URL: en och porten.

## <a name="support-and-feedback"></a>Support och feedback

För Azure blockchain News går du till [Azure blockchain-bloggen](https://azure.microsoft.com/blog/topics/blockchain/) för att hålla dig uppdaterad om blockchain service-erbjudanden och information från Azures teknik team för blockchain.

För att ge feedback på produkter eller för att begära nya funktioner, post eller rösta för en idé via [Azure feedback-forumet för blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Community-support

Engagera med Microsoft-tekniker och Azure blockchain community-experter.

* [Sidan Microsoft Q&en fråga](/answers/topics/azure-blockchain-workbench.html). Teknisk support för blockchain-mallar är begränsad till distributions problem.
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure blockchain-lösningar finns i [dokumentationen för Azure blockchain](../index.yml).
