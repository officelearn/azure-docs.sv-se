---
title: Distribuera lösningsmall för Ethereum Proof-of-Authority-konsortium på Azure
description: Använda konsortielösningen Ethereum Proof-of-Authority för att distribuera och konfigurera ett Ethereum-nätverk med flera medlemmar på Azure
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387677"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Distribuera Ethereum-lösningsmall för bevis på myndighetskonsortie på Azure

Du kan använda [mallen Förhandsgranskning av Azure-lösning](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) för att distribuera, konfigurera och styra ett konsortiebevis för flera medlemmar med minimal Azure- och Ethereum-kunskap.

Lösningsmallen kan användas av varje konsortiemedlem för att etablera ett blockchain-nätverksavtryck med hjälp av Microsoft Azure-beräknings-, nätverks- och lagringstjänster. Varje konsortiemedlems nätverksavtryck består av en uppsättning belastningsbalanserade validerare som ett program eller en användare kan interagera med för att skicka Ethereum-transaktioner.

## <a name="choose-an-azure-blockchain-solution"></a>Välj en Azure Blockchain-lösning

Innan du väljer att använda lösningsmallen för Ethereum proof-of-authority-konsortium kan du jämföra ditt scenario med de vanliga användningsfallen med tillgängliga Azure Blockchain-alternativ.

Alternativ | Servicemodell | Vanligt användningsfall
-------|---------------|-----------------
Lösningsmallar | IaaS | Lösningsmallar är Azure Resource Manager-mallar som du kan använda för att etablera en fullständigt konfigurerad blockchain-nätverkstopologi. Mallarna distribuerar och konfigurerar Microsoft Azure-beräknings-, nätverks- och lagringstjänster för en viss blockchain-nätverkstyp.
[Azure Blockchain Service](../service/overview.md) | PaaS | Azure Blockchain Service Preview förenklar bildandet, hanteringen och styrningen av konsortiek blockkedjenätverk. Använd Azure Blockchain Service för lösningar som kräver PaaS, konsortiehantering eller kontrakts- och transaktionsekretess.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS och PaaS | Azure Blockchain Workbench Preview är en samling Azure-tjänster och funktioner som utformats för att hjälpa dig att skapa och distribuera blockchain-program för att dela affärsprocesser och data med andra organisationer. Använd Azure Blockchain Workbench för prototyper av en blockchain-lösning eller ett blockchain-programbevis.

## <a name="solution-architecture"></a>Lösningsarkitektur

Med ethereum-lösningsmallen kan du distribuera ett konsortienätverk med en eller flera regioner.

![distributionsarkitektur](./media/ethereum-poa-deployment/deployment-architecture.png)

Varje distribution av konsortiemedlem omfattar:

* Virtuella datorer för att köra PoA-validerare
* Azure Load Balancer för distribution av RPC-, peering- och styrnings-DApp-begäranden
* Azure Key Vault för att skydda valideraridentiteterna
* Azure Storage för värd för beständig nätverksinformation och samordna leasing
* Azure Monitor för att samla loggar och prestandastatistik
* VNet Gateway (valfritt) för att tillåta VPN-anslutningar över privata virtuella nätverk

Som standard är RPC- och peering-slutpunkterna tillgängliga via offentlig IP för att möjliggöra förenklad anslutning mellan prenumerationer och moln. För åtkomstkontroller på programnivå kan du använda [Paritys behörighetskontrakt](https://wiki.parity.io/Permissioning). Nätverk som distribueras bakom VPN, som utnyttjar VNet-gateways för anslutning mellan prenumerationer stöds. Eftersom VPN- och VNet-distributioner är mer komplexa kanske du vill börja med en offentlig IP-modell när du skapar en lösning.

Docker behållare används för tillförlitlighet och modularitet. Azure Container Registry används för att vara värd för och betjäna versionsavbildningar som en del av varje distribution. Behållaravbildningarna består av:

* Orchestrator - Genererar identiteter och styrningskontrakt. Lagrar identiteter i en identitetsbutik.
* Paritetsklient - Har utsträtt identitet från identitetsarkivet. Upptäcker och ansluter till peer-datorer.
* EthStats Agent - Samlar in lokala loggar och statistik via RPC och skickar information till Azure Monitor.
* Governance DApp - Webbgränssnitt för att interagera med styrningskontrakt.

### <a name="validator-nodes"></a>Noder för validerare

I fullmaktsprotokollet tar validatornoderna plats för traditionella gruvarbetarenoder. Varje validerare har en unik Ethereum-identitet som gör det möjligt att delta i blockskapandeprocessen. Varje konsortiemedlem kan etablera två eller flera validerarnoder i fem regioner för georedundans. Validerarnoder kommunicerar med andra validerarnoder för att komma överens om tillståndet för den underliggande distribuerade redovisningen. För att säkerställa ett rättvist deltagande i nätverket är varje konsortiemedlem förbjuden att använda fler validerare än den första medlemmen i nätverket. Om den första medlemmen till exempel distribuerar tre validerare kan varje medlem bara ha upp till tre validerare.

### <a name="identity-store"></a>Identitetsbutik

Ett identitetsarkiv distribueras i varje medlems prenumeration som säkert innehåller de genererade Ethereum-identiteterna. För varje validerare genererar orchestration-behållaren en eprigen Ethereum-nyckel och lagrar den i Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Distribuera Ethereums konsortiumnätverk

I den här genomsprånget antar vi att du skapar ett flerpartinätverk för Ethereum. Följande flöde är ett exempel på en distribution med flera parter:

1. Tre medlemmar genererar vardera ett Ethereum-konto med MetaMask
1. *Medlem A* distribuerar Ethereum PoA och tillhandahåller sin Ethereum-postadress
1. *Medlem A* tillhandahåller konsortieadressen till *medlem B* och *medlem C*
1. *Medlem B* och *medlem C* distribuera, Ethereum PoA, tillhandahålla deras Ethereum offentlig adress och *medlem A:* s konsortium URL
1. *Medlem A* röstar i *medlem B* som administratör
1. *Medlem A* och *ledamot B* röstar båda *ledamot C* som admin

I nästa avsnitt visas hur du konfigurerar den första medlemmens fotavtryck i nätverket.

### <a name="create-resource"></a>Skapa resurs

I [Azure-portalen](https://portal.azure.com)väljer du **Skapa en resurs** i det övre vänstra hörnet.

Välj **Blockchain** > **Ethereum Proof-of-Authority Consortium (förhandsversion)**.

### <a name="basics"></a>Grundläggande inställningar

Under **Basics**anger du värden för standardparametrar för alla distributioner.

![Grundläggande inställningar](./media/ethereum-poa-deployment/basic-blade.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Skapa ett nytt nätverk eller ansluta till befintligt nätverk | Du kan skapa ett nytt konsortiumnätverk eller gå med i ett befintligt konsortiumnätverk. Att ansluta till ett befintligt nätverk kräver ytterligare parametrar. | Skapa ny
E-postadress | Du får ett e-postmeddelande när distributionen är klar med information om distributionen. | En giltig e-postadress
Vm-användarnamn | Administratörsanvändarnamn för varje distribuerad virtuell dator | 1-64 alfanumeriska tecken
Autentiseringstyp | Metoden att autentisera till den virtuella datorn. | lösenord
lösenord | Lösenordet för administratörskontot för var och en av de virtuella datorer som distribueras. Alla virtuella datorer har inledningsvis samma lösenord. Du kan ändra lösenordet efter etableringen. | 12-72 tecken 
Prenumeration | Abonnemanget för att distribuera konsortienätverket |
Resursgrupp| Resursgruppen som konsortienätverket ska distribueras till. | myResourceGroup
Location | Azure-regionen för resursgrupp. | USA, västra 2

Välj **OK**.

### <a name="deployment-regions"></a>Distributionsområden

Under *Distributionsregioner*anger du antalet regioner och platser för varje. Du kan distribuera i högst fem regioner. Den första regionen ska matcha resursgruppsplatsen från avsnittet *Grunderna.* För utvecklings- eller testnätverk kan du använda en enda region per medlem. Distribuera över två eller flera regioner för hög tillgänglighet för produktion.

![distributionsregioner](./media/ethereum-poa-deployment/deployment-regions.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Antal regioner|Antal regioner som ska distribuera konsortienätverket| 2
Första regionen | Första regionen att distribuera konsortienätverket | USA, västra 2
Andra regionen | Andra regionen att distribuera konsortienätverket. Ytterligare regioner är synliga när antalet regioner är två eller fler. | USA, östra 2

Välj **OK**.

### <a name="network-size-and-performance"></a>Nätverkets storlek och prestanda

Under *Nätverksstorlek och prestanda*anger du indata för konsortienätverkets storlek. Storage-storleken för validerarenoden dikterar blockkedjans potentiella storlek. Storleken kan ändras efter distributionen.

![Nätverkets storlek och prestanda](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Antal belastningsbalanserade validerarnoder | Antalet validerarnoder som ska etableras som en del av nätverket. | 2
Lagringsprestanda för validatornod | Typ av hanterad disk för var och en av de distribuerade validerarnoderna. Mer information om priser finns i [lagringspriser](https://azure.microsoft.com/pricing/details/managed-disks/) | Standard SSD
Virtual Machine-storlek för validerare nod | Storleken på den virtuella datorn som används för validerarnoder. Mer information om prissättning finns i [priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | Standard D2 v3

Virtuell dator och lagringsnivå påverkar nätverkets prestanda.  Använd följande tabell för att välja kostnadseffektivitet:

SKU för virtuell dator|Lagringsnivå|Pris|Dataflöde|Svarstid
---|---|---|---|---
F1|Standard SSD|Låg|Låg|hög
D2_v3|Standard SSD|medel|medel|medel
F16s (på andra)|Premium SSD|hög|hög|Låg

Välj **OK**.

### <a name="ethereum-settings"></a>Ethereum-inställningar

Under *Ethereum-inställningar*anger du Ethereum-relaterade konfigurationsinställningar.

![Ethereum-inställningar](./media/ethereum-poa-deployment/ethereum-settings.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Id för konsortiemedlem | ID:et som är kopplat till varje medlem som deltar i konsortienätverket. Den används för att konfigurera IP-adressutrymmen för att undvika kollision. För ett privat nätverk bör medlems-ID vara unikt i olika organisationer i samma nätverk.  Ett unikt medlems-ID behövs även när samma organisation distribuerar till flera regioner. Notera värdet på den här parametern eftersom du måste dela den med andra anslutande medlemmar för att säkerställa att det inte finns någon kollision. Det giltiga intervallet är 0 till 255. | 0
Nätverks-ID | Nätverks-ID:et för konsortiet Ethereum-nätverk som distribueras. Varje Ethereum-nätverk har ett eget nätverks-ID, där 1 är ID för det offentliga nätverket. Det giltiga intervallet är 5 till 999 999 999 | 10101010
Admin Ethereum-adress | Ethereum-kontoadressen som används för att delta i poa-styrning. Du kan använda MetaMask för att generera en Ethereum-adress. |
Avancerade alternativ | Avancerade alternativ för Ethereum-inställningar | Aktivera
Distribuera med offentlig IP | Om Privat VNet är markerat distribueras nätverket bakom en VNet Gateway och tar bort peering-åtkomst. För privat VNet måste alla medlemmar använda en VNet Gateway för att anslutningen ska vara kompatibel. | Offentlig IP-adress
Begränsa blockgas | Nätverkets gasgräns för startblock. | 50000000
Block återförslutningsperiod (sek) | Den frekvens med vilken tomma block skapas när det inte finns några transaktioner i nätverket. En högre frekvens kommer att ha snabbare finalitet men ökade lagringskostnader. | 15
Kontrakt för transaktionsbehörighet | Bytecode för kontraktet med transaktionsbehörighet. Begränsar smart kontraktsdistribution och körning till en tillåten lista över Ethereum-konton. |

Välj **OK**.

### <a name="monitoring"></a>Övervakning

Med övervakning kan du konfigurera en loggresurs för nätverket. Övervakningsagenten samlar in och ytor användbara mått och loggar från nätverket, vilket ger möjlighet att snabbt kontrollera nätverkshälso- eller felsökningsproblem.

![Azure-övervakare](./media/ethereum-poa-deployment/azure-monitor.png)

Parameter | Beskrivning | Exempelvärde
----------|-------------|--------------
Övervakning | Möjlighet att aktivera övervakning | Aktivera
Ansluta till befintliga Azure Monitor-loggar | Möjlighet att skapa en ny Azure Monitor-logginstans eller ansluta till en befintlig instans | Skapa ny
Location | Den region där den nya instansen distribueras | USA, östra
Befintligt logganalysarbetsyte-ID (Anslut till befintliga Azure Monitor-loggar = Gå med i befintligt)|Arbetsyte-ID för den befintliga Azure Monitor-logginstansen||Ej tillämpligt
Befintlig primärnyckel för logganalys (Anslut till befintliga Azure Monitor-loggar = Gå med i befintliga)|Den primära nyckeln som används för att ansluta till den befintliga Azure Monitor-logginstansen||Ej tillämpligt

Välj **OK**.

### <a name="summary"></a>Sammanfattning

Klicka igenom sammanfattningen om du vill granska de angivna indata och köra grundläggande validering före distributionen. Innan du distribuerar kan du hämta mallen och parametrarna.

Välj **Skapa** för distribution.

Om distributionen innehåller VNet Gateways kan distributionen ta upp 45 till 50 minuter.

## <a name="deployment-output"></a>Utdata för distribution

När distributionen har slutförts kan du komma åt de nödvändiga parametrarna med hjälp av Azure-portalen.

### <a name="confirmation-email"></a>E-post med bekräftelse

Om du anger en e-postadress ([basics section)](#basics)skickas ett e-postmeddelande som innehåller distributionsinformationen och länkar till den här dokumentationen.

![e-post för distribution](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portalen

När distributionen har slutförts och alla resurser har etablerats kan du visa utdataparametrarna i resursgruppen.

1. Gå till resursgruppen i portalen.
1. Välj **Översikt > distributioner**.

    ![Översikt över resursgrupp](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Välj distributionen **microsoft-azure-blockchain.azure-blockchain-ether-....**
1. Markera avsnittet **Utdata.**

    ![Utdata för distribution](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Växande konsortiet

Om du vill utöka konsortiet måste du först ansluta det fysiska nätverket. Om du distribuerar bakom en VPN läser du avsnittet [Ansluta VNet Gateway](#connecting-vnet-gateways) konfigurera nätverksanslutningen som en del av den nya medlemsdistributionen. När distributionen är klar använder du [DApp för styrning](#governance-dapp) för att bli nätverksadministratör.

### <a name="new-member-deployment"></a>Ny medlemsdistribution

Dela följande information med den anslutande medlemmen. Informationen finns i e-postmeddelandet efter distributionen eller i utdata för portaldistributionen.

* URL för konsortiedata
* Antalet noder som du har distribuerat
* Resurs-ID för VNet Gateway (om VPN-enhet med hjälp av

Den distribuerande medlemmen bör använda samma Ethereum Proof-of-Authority-lösningsmall när de distribuerar sin nätverksnärvaro med hjälp av följande vägledning:

* Välj **Gå med befintligt**
* Välj samma antal validatornoder som resten av medlemmarna i nätverket för att säkerställa rättvis representation
* Använd samma Admin Ethereum-adress
* Använd den angivna *konsortiedataadressen* i *Ethereum-inställningarna*
* Om resten av nätverket ligger bakom ett VPN väljer du **Privat VNet** under det avancerade avsnittet

### <a name="connecting-vnet-gateways"></a>Ansluta VNet-gateways

Det här avsnittet krävs bara om du distribueras med ett privat virtuella nätverk. Du kan hoppa över det här avsnittet om du använder offentliga IP-adresser.

För ett privat nätverk är de olika medlemmarna anslutna via VNet gateway-anslutningar. Innan en medlem kan ansluta till nätverket och se transaktionstrafik måste en befintlig medlem göra en slutlig konfiguration på sin VPN-gateway för att acceptera anslutningen. Ethereum-noderna för den anslutande medlemmen körs inte förrän en anslutning har upprättats. Skapa redundanta nätverksanslutningar i konsortiet om du vill minska risken för en enskild felpunkt.

När den nya medlemmen har distribuerats måste den befintliga medlemmen slutföra dubbelriktad anslutning genom att konfigurera en VNet-gatewayanslutning till den nya medlemmen. Den befintliga medlemmen behöver:

* Den virtuella gateway ResourceID för den anslutande medlemmen. Se [distributionsutdata](#deployment-output).
* Den delade anslutningsnyckeln.

Den befintliga medlemmen måste köra följande PowerShell-skript för att slutföra anslutningen. Du kan använda Azure Cloud Shell som finns i det övre högra navigeringsfältet i portalen.

![moln skal](./media/ethereum-poa-deployment/cloud-shell.png)

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

Du kan hitta din Azure Monitor-portal antingen genom att följa länken i distributionsmeddelandet eller hitta parametern i distributionsutdata [OMS_PORTAL_URL].

Portalen visar först nätverksstatistik på hög nivå och nodöversikt.

![Övervaka kategorier](./media/ethereum-poa-deployment/monitor-categories.png)

Om du väljer **Nodöversikt** visas infrastrukturstatistik per nod.

![Nod statistik](./media/ethereum-poa-deployment/node-stats.png)

Om du väljer **Nätverksstatistik** visas Ethereum-nätverksstatistik.

![Statistik för nätverk](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Du kan fråga övervakningsloggarna för att undersöka fel eller inställningströskelavisering. Följande frågor är exempel som du kan köra i verktyget *Logga sökning:*

Listblock som har rapporterats av mer än en validatorfråga kan vara användbara för att hitta kedjeförutfärger.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Få genomsnittligt peer-antal för en angiven validerare nod i genomsnitt över 5-minuters buckets.

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

Av säkerhetsskäl nekas SSH-portåtkomsten av en säkerhetsregel för nätverksgrupp som standard. Om du vill komma åt instanserna för den virtuella datorn i PoA-nätverket måste du ändra följande säkerhet är regel för att *tillåta*.

1. Gå till avsnittet **Översikt i** den distribuerade resursgruppen i Azure-portalen.

    ![ssh översikt](./media/ethereum-poa-deployment/ssh-overview.png)

1. Välj **nätverkssäkerhetsgruppen** för den region för den virtuella dator som du vill komma åt.

    ![ssh nsg (ssh nsg)](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Välj **regeln tillåt ssh.**

    ![ssh-tillåta](./media/ethereum-poa-deployment/ssh-allow.png)

1. Ändra **åtgärd** för att **tillåta**

    ![ssh aktivera tillåt](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Välj **Spara**. Det kan ta några minuter att ändra ändringarna.

Du kan fjärransluta till de virtuella datorerna för validatornoderna via SSH med ditt medföljande administratörsanvändarnamn och lösenord/SSH-nyckel. SSH-kommandot för att komma åt den första validerarnoden visas i malldistributionsutdata. Ett exempel:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Öka portnumret med ett för att komma åt ytterligare transaktionsnoder.

Om du har distribuerat till mer än en region ändrar du kommandot till DNS-namnet eller IP-adressen för belastningsutjämnaren i den regionen. Om du vill hitta DNS-namnet eller IP-adressen för de andra regionerna hittar du resursen med namngivningskonventionen ** \* \* \* \* \*-lbpip-reg\# ** och visar dess DNS-namn och IP-adressegenskaper.

## <a name="azure-traffic-manager-load-balancing"></a>Azure Traffic Manager belastningsutjämning

Azure Traffic Manager kan bidra till att minska stilleståndstiden och förbättra svarstiden för PoA-nätverket genom att dirigera inkommande trafik över flera distributioner i olika regioner. Inbyggda hälsokontroller och automatisk omdirigering bidrar till att säkerställa hög tillgänglighet för RPC-slutpunkterna och governance DApp. Den här funktionen är användbar om du har distribuerats till flera regioner och är produktionsklar.

Använd Traffic Manager för att förbättra PoA-nätverkstillgängligheten med automatisk redundans. Du kan också använda Traffic Manager för att öka dina nätverkssvarstider genom att dirigera slutanvändare till Azure-platsen med lägsta nätverksfördröjning.

Om du bestämmer dig för att skapa en Traffic Manager-profil kan du använda DNS-namnet på profilen för att komma åt nätverket. När andra konsortiemedlemmar har lagts till i nätverket kan Traffic Manager också användas för att belastningsjämna över sina distribuerade validerare.

### <a name="creating-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

1. I [Azure-portalen](https://portal.azure.com)väljer du **Skapa en resurs** i det övre vänstra hörnet.
1. Sök efter **Traffic Manager-profil**.

    ![Sök efter Azure Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Ge profilen ett unikt namn och välj den resursgrupp som användes för PoA-distributionen.

1. Välj **Skapa** för distribution.

    ![Skapa Trafikhanteraren](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. När du har distribuerat den väljer du instansen i resursgruppen. DNS-namnet för att komma åt trafikhanteraren finns på fliken Översikt.

    ![Hitta TRAFFIC Manager DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Välj fliken **Slutpunkter** och välj knappen **Lägg till.**
1. Ge slutpunkten ett unikt namn.
1. För **resurstyp**för Mål väljer du **Offentlig IP-adress**.
1. Välj den offentliga IP-adressen för den första regionens belastningsutjämnare.

    ![Trafikhanterare för routning](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Upprepa för varje region i det distribuerade nätverket. När slutpunkterna är i **aktiverad** status läses de in automatiskt och region balanseras vid trafikhanterarens DNS-namn. Du kan nu använda det här DNS-namnet i stället för parametern [CONSORTIUM_DATA_URL] i andra steg i artikeln.

## <a name="data-api"></a>Data API

Varje konsortiummedlem är värd för nödvändig information för att andra ska kunna ansluta till nätverket. För att underlätta anslutningen är varje medlem värd för en uppsättning anslutningsinformation vid data-API-slutpunkten.

Den befintliga medlemmen tillhandahåller [CONSORTIUM_DATA_URL] före medlemmens distribution. Vid distribution hämtar en anslutande medlem information från JSON-gränssnittet vid följande slutpunkt:

`<CONSORTIUM_DATA_URL>/networkinfo`

Svaret innehåller information som är användbar för att gå med medlemmar (Genesis block, Validator Set kontrakt ABI, bootnodes) och information som är användbar för den befintliga medlemmen (validerare adresser). Du kan använda den här standardiseringen för att utöka konsortiet mellan molnleverantörer. Det här API:et returnerar ett JSON-formaterat svar med följande struktur:

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

## <a name="governance-dapp"></a>Styrning DApp

Kärnan i bevis-of-myndighet är decentraliserad styrning. Eftersom bevis på myndighet förlitar sig på en tillåten lista över nätverksmyndigheter för att hålla nätverket friskt, är det viktigt att tillhandahålla en rättvis mekanism för att göra ändringar i denna behörighetslista. Varje distribution levereras med en uppsättning smarta kontrakt och portal för styrning i kedjan av den här tillåtna listan. När en föreslagen ändring når en majoritetsomröstning av konsortiemedlemmarna antas ändringen. Genom att rösta kan nya konsensusdeltagare läggas till eller komprometterade deltagare tas bort på ett öppet sätt som uppmuntrar ett ärligt nätverk.

Styrning DApp är en uppsättning förut distribuerade [smarta kontrakt](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) och ett webbprogram som används för att styra myndigheterna i nätverket. Myndigheter delas upp i administratörsidentiteter och validerarnoder.
Administratörer har befogenhet att delegera konsensusdeltagande till en uppsättning validerarnoder. Administratörer kan också rösta andra administratörer i eller utanför nätverket.

![Styrning DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **Decentraliserad styrning:** Ändringar i nätverksmyndigheter administreras genom omröstning i kedjan av utvalda administratörer.
* **Delegering av validerare:** Myndigheter kan hantera sina validerarnoder som har ställts in i varje PoA-distribution.
* **Granskningsbar ändringshistorik:** Varje ändring registreras på blockkedjan som ger insyn och granskning.

### <a name="getting-started-with-governance"></a>Komma igång med styrning

För att utföra någon form av transaktioner via Governance DApp måste du använda en Ethereum-plånbok. Det enklaste sättet är att använda en plånbok i webbläsaren som [MetaMask;](https://metamask.io) Men eftersom dessa smarta kontrakt distribueras i nätverket kan du också automatisera dina interaktioner med governance-kontraktet.

När du har installerat MetaMask navigerar du till DApp för styrning i webbläsaren.  Du kan hitta URL:en via Azure-portalen i distributionsutdata.  Om du inte har en plånbok i webbläsaren installerad kan du inte utföra några åtgärder. Du kan dock visa administratörstillståndet.  

### <a name="becoming-an-admin"></a>Att bli administratör

Om du är den första medlemmen som distribueras i nätverket blir du automatiskt administratör och dina paritetsnoder visas som validerare. Om du ansluter till nätverket måste du bli inröstad som administratör med en majoritet (större än 50 %) den befintliga administratörsuppsättningen. Om du väljer att inte bli administratör synkroniserar och validerar du fortfarande blockkedjan. De deltar dock inte i processen för att skapa blockeringar. Om du vill starta röstningsprocessen för att bli administratör väljer du **Nominera** och anger din Ethereum-adress och ditt alias.

![Utse](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Kandidater

Om du väljer fliken **Kandidater** visas den aktuella uppsättningen kandidatadministratörer.  När en kandidat når en majoritetsomröstning av de nuvarande administratörerna, blir kandidaten befordrad till en admin.  Om du vill rösta på en kandidat markerar du raden och väljer **Rösta i**. Om du ändrar dig vid en omröstning väljer du kandidaten och väljer **Omröstning i Hävning**.

![Kandidater](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Administratörer

Fliken **Administratörer** visar den aktuella uppsättningen administratörer och ger dig möjlighet att rösta emot.  När en administratör förlorar mer än 50 % stöd tas de bort som administratör i nätverket. Alla validerarnoder som administratören äger förlorar validerarstatus och blir transaktionsnoder i nätverket. En administratör kan tas bort av flera skäl. Det är dock upp till konsortiet att komma överens om en policy i förväg.

![Administratörer](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validators

Om du väljer fliken **Validerare** visas de aktuella distribuerade paritetsnoderna för instansen och deras aktuella status (nodtyp). Varje konsortiummedlem har olika uppsättning validerare i den här listan, eftersom den här vyn representerar den aktuella distribuerade konsortiemedlemmen. Om instansen har distribuerats nyligen och du inte har lagt till dina validerare får du alternativet Lägg till **validerare**. Genom att lägga till validerare väljs automatiskt en regionalt balanserad uppsättning paritetsnoder och tilldelas dem till din validerareuppsättning. Om du har distribuerat fler noder än den tillåtna kapaciteten blir de återstående noderna transaktionsnoder i nätverket.

Adressen till varje validerare tilldelas automatiskt via [identitetsarkivet](#identity-store) i Azure.  Om en nod går ner avsäger den sig sin identitet, vilket gör att en annan nod i distributionen kan ta dess plats. Denna process säkerställer att ditt deltagande i samförstånd är mycket tillgängligt.

![Validators](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Konsortiets namn

Alla administratörer kan uppdatera konsortienamnet.  Välj kugghjulsikonen längst upp till vänster för att uppdatera konsortienamnet.

### <a name="account-menu"></a>Kontomeny

Längst upp till höger finns ditt Ethereum-kontoalias och identicon.  Om du är administratör kan du uppdatera ditt alias.

![Konto](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Ethereum utveckling<a id="tutorials"></a>

Om du vill kompilera, distribuera och testa smarta kontrakt kan du överväga för Ethereum-utveckling:
* [Tryffelsvit](https://www.trufflesuite.com/docs/truffle/overview) - Kundbaserad Ethereum-utvecklingsmiljö
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) - Webbläsarbaserad och lokal Ethereum utvecklingsmiljö

### <a name="compile-deploy-and-execute-smart-contract"></a>Kompilera, distribuera och utföra smarta kontrakt

I följande exempel skapar du ett enkelt smart kontrakt. Du använder Tryffel för att kompilera och distribuera det smarta kontraktet till ditt blockchain-nätverk. När du har distribuerat anropar du en smart kontraktsfunktion via en transaktion.

#### <a name="prerequisites"></a>Krav

* Installera [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python behövs för Tryffel och Web3. Välj installationsalternativet om du vill inkludera Python i sökvägen.
* Installera Tryffel v5.0.5 `npm install -g truffle@v5.0.5`. Tryffel kräver flera verktyg som ska installeras, inklusive [Node.js](https://nodejs.org), [Git](https://git-scm.com/). Mer information finns i [Tryffeldokumentation](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Skapa tryffelprojekt

Innan du kan kompilera och distribuera ett smart kontrakt måste du skapa ett tryffelprojekt.

1. Öppna en kommandotolk eller ett skal.
1. Skapa en mapp med namnet `HelloWorld`.
1. Ändra katalogen `HelloWorld` till den nya mappen.
1. Initiera ett nytt tryffelprojekt med `truffle init`kommandot .

    ![Skapa ett nytt tryffelprojekt](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Lägga till ett smart kontrakt

Skapa dina smarta **contracts** kontrakt i kontraktsunderkatalogen för ditt Tryffelprojekt.

1. Skapa en fil `postBox.sol` i namnet i **kontraktunderkatalogen** för tryffelprojektet.
1. Lägg till följande soliditetskod **i postBox.sol**.

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

### <a name="deploy-smart-contract-using-truffle"></a>Distribuera smart kontrakt med Tryffel

Tryffelprojekt innehåller en konfigurationsfil för information om blockchain-nätverksanslutning. Ändra konfigurationsfilen så att den innehåller anslutningsinformationen för nätverket.

> [!WARNING]
> Skicka aldrig din e-nyckel till Ethereum via nätverket. Kontrollera att varje transaktion signeras lokalt först och att den signerade transaktionen skickas över nätverket.

1. Du behöver den mnemonic frasen för [Ethereum admin-konto som används när du distribuerar ditt blockchain-nätverk](#ethereum-settings). Om du använde MetaMask för att skapa kontot kan du hämta mnemonic från MetaMask. Välj ikonen för administratörskonto längst upp till höger i MetaMask-tillägget och välj **Inställningar > säkerhet & Sekretess > Visa seed-ord**.
1. Ersätt innehållet `truffle-config.js` i tryffelprojektet med följande innehåll. Ersätt platshållarslutpunkten och mnemonic-värdena.

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

1. Eftersom vi använder Tryffel HD Wallet-leverantören installerar du modulen `npm install truffle-hdwallet-provider --save`i projektet med kommandot .

Tryffel använder migreringsskript för att distribuera smarta kontrakt till ett blockchain-nätverk. Du behöver ett migreringsskript för att distribuera ditt nya smarta kontrakt.

1. Lägg till en ny migrering för att distribuera det nya kontraktet. Skapa `2_deploy_contracts.js` fil i underkatalogen **för migreringar** i Tryffelprojektet.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Distribuera till PoA-nätverket med kommandot Tryffel migrera. Kör i kommandotolken i tryffelprojektkatalogen:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Anropa en smart kontraktsfunktion

Nu när ditt smarta kontrakt har distribuerats kan du skicka en transaktion för att anropa en funktion.

1. Skapa en ny fil med namnet `sendtransaction.js`.
1. Lägg till följande innehåll för att **skickatransaction.js**.

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

1. Kör skriptet med kommandot Tryffelkörning.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Kör skript till anropsfunktion via transaktion](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Stöd för WebAssembly (WASM)

WebAssembly-stöd är redan aktiverat för dig i nyligen distribuerade PoA-nätverk. Det möjliggör smart kontrakt utveckling på alla språk som transpiles till Web-Assembly (Rust, C, C + +). Mer information finns i: [Paritetsöversikt över WebAssembly](https://wiki.parity.io/WebAssembly-Home) och [Självstudiekurs från Parity Tech](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Jag märker att det finns många transaktioner på nätet som jag inte skickade. Var kommer de här ifrån?

Det är osäkert att låsa upp den [personliga API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). Bots lyssna efter olåst Ethereum konton och försöka dränera medel. Boten förutsätter att dessa konton innehåller real-eter och försöker vara den första att suga balansen. Aktivera inte det personliga API:et i nätverket. I stället pre-sign transaktionerna antingen manuellt med hjälp av en plånbok som MetaMask eller programmässigt.

### <a name="how-to-ssh-onto-a-vm"></a>Hur SSH på en virtuell dator?

SSH-porten är inte exponerad av säkerhetsskäl. Följ [den här guiden för att aktivera SSH-porten](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Hur konfigurerar jag en revisionsmedlem eller transaktionsnod?

Transaktionsnoder är en uppsättning paritetsklienter som är peered med nätverket men inte deltar i konsensus. Dessa noder kan fortfarande användas för att skicka Ethereum-transaktioner och läsa tillståndet för smarta kontrakt. Denna mekanism arbetar för att tillhandahålla granskningsbarhet till icke-myndighetskonsortiemedlemmar i nätverket. För att uppnå detta, följ stegen i [Växande konsortiet](#growing-the-consortium).

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Varför tar MetaMask-transaktioner lång tid?

För att säkerställa att transaktioner tas emot i rätt ordning kommer varje Ethereum-transaktion med en stegvis nonce. Om du har använt ett konto i MetaMask i ett annat nätverk måste du återställa nonce-värdet. Klicka på inställningsikonen (trestaplar), Inställningar, Återställ konto. Transaktionshistoriken rensas och nu kan du skicka transaktionen igen.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Måste jag ange gasavgift i MetaMask?

Eter tjänar inte ett syfte i bevis-of-authority konsortium. Därför finns det ingen anledning att ange gasavgift när du skickar transaktioner i MetaMask.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Vad ska jag göra om min distribution misslyckas på grund av att Azure OMS inte har etablerats?

Övervakning är en valfri funktion. I vissa sällsynta fall där distributionen misslyckas på grund av oförmåga att etablera Azure Monitor-resursen kan du distribuera om utan Azure Monitor.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Är offentliga IP-distributioner kompatibla med privata nätverksdistributioner?

Nej. Peering kräver tvåvägskommunikation så att hela nätverket måste vara offentligt eller privat.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Vad är den förväntade transaktionsgenomströmningen för Proof-of-Authority?

Transaktionsdataflödet kommer att vara starkt beroende av vilka typer av transaktioner och nätverkstopologin. Med enkla transaktioner har vi benchmarkat i genomsnitt 400 transaktioner per sekund med ett nätverk som distribueras över flera regioner.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Hur prenumererar jag på smarta kontraktshändelser?

Ethereum Proof-of-Authority stöder nu webbuttag.  Kontrollera distributionsutgången för att hitta webbadressen och porten för webbuttaget.

## <a name="next-steps"></a>Nästa steg

Mer Azure Blockchain-lösningar finns i [Azure Blockchain-dokumentationen](https://docs.microsoft.com/azure/blockchain/).
