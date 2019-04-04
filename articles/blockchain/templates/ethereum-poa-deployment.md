---
title: Ethereum Proof-of-Authority Consortium - Azure
description: Använda Ethereum Proof-of-Authority Consortium-lösning för att distribuera och konfigurera ett flera medlem consortium Ethereum-nätverk
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 8/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: 5a693fef2f77471f799bec46f149ff19d6edca80
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905927"
---
# <a name="ethereum-proof-of-authority-consortium"></a>Ethereum proof-of-authority consortium

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
[Den här lösningen](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) har utformats för att göra det enklare att distribuera, konfigurera och reglerar ett flera medlem Proof-of-authority Ethereum konsortienätverk med minimal kunskap om Azure och Ethereum.

Med en handfull användarindata och en enda musklick distribution via Azure-portalen, kan varje medlem etablera ett nätverk fotavtryck, med hjälp av Microsoft Azure Compute, nätverk och lagringstjänster över hela världen. Varje medlem nätverk fotavtryck består av en uppsättning noder för Utjämning av nätverksbelastning verifieraren med som ett program eller en användare kan interagera om du vill skicka Ethereum transaktioner.

## <a name="concepts"></a>Begrepp

### <a name="terminology"></a>Terminologi

-   **Konsensus** -av synkronisera data i distribuerade nätverket via block validering och skapa.

-   **Consortium medlem** – en enhet som deltar i konsensus i Blockchain-nätverket.

-   **Administratören** – en Ethereum-konto som används för att hantera deltagandet för en viss consortium medlem.

-   **Verifieraren** – en dator som är associerade med ett Ethereum-konto som deltar i konsensus uppdrag av en administratör.

### <a name="proof-of-authority"></a>Proof-of-authority

För dig som är nya för blockchain-communityn, är versionen av den här lösningen en fantastisk möjlighet att lära dig om tekniken på ett enkelt och konfigurerbara sätt på Azure. Proof of work är en mekanism för Sybil motstånd som utnyttjar beräkning kostnader för att reglera själva nätverket och Tillåt fair deltagande. Detta fungerar bra i anonym, öppna blockchain nätverk där tävling för kryptovalutor främjar säkerhet i nätverket. I privat/consortium nätverk har den underliggande Ether men inget värde. Ett alternativt protokoll proof-of-authority, är mer lämpliga för tillåtna nätverk där alla konsensus-deltagare är kända och välkända. Utan att du behöver för utvinning är Proof-of-authority effektivare utan att försämra Byzantine feltolerans.

### <a name="consortium-governance"></a>Consortium styrning

Eftersom proof-of-authority bygger på en lista över nätverket myndigheter att nätverket är felfritt tillåtna, är det viktigt att tillhandahålla en rättvis mekanism för att göra ändringar i den här behörighetslistan. Varje distribution levereras med en uppsättning smarta kontrakt och -portalen för styrning i kedjan av den här tillåts. När en ändring av föreslagna når en majoritet av consortium medlemmar, ändringen är branschrekommendationer när det gäller. På så sätt kan nya konsensus-deltagare ska vara läggs till eller komprometterats deltagare som ska tas bort på ett transparent sätt som uppmuntrar till en ärlig nätverk.

### <a name="admin-account"></a>Administratörskonto

Under distributionen av proof-of-authority-noder, får du en fråga för en administratör Ethereum-adress. Du kan använda flera olika sätt för att generera och skydda den här Ethereum-konto. När den här adressen läggs till som en utfärdare i nätverket, kan du använda det här kontot för att delta i styrning. Den här administratörskonto används också för att delegera konsensus deltagande verifieraren noder som skapas som en del av den här distributionen. Eftersom endast den offentliga Ethereum-adressen används har varje administratör möjlighet att skydda sina privata nycklar på ett sätt som följer sin önskade säkerhetsmodell.

### <a name="validator-node"></a>Verifieraren nod

I proof-of-authority-protokollet ta verifieraren noder för traditionella miner noder. Varje systemhälsoverifierare har en unik Ethereum-identitet som läggs till en smart-kontraktet behörighetslistan. När en verifieraren finns på den här listan, kan den ingå i blocket skapandeprocessen. Mer information om den här processen finns paritets dokumentation på [utfärdare runda konsensus](https://wiki.parity.io/Aura). Varje consortium medlem kan etablera minst två noder i verifieraren i fem regioner, för geo-redundans. Verifieraren noder kommunicerar med andra verifieraren noder skulle få konsensus på tillståndet för den underliggande distribuerat transaktionsregister.
För att säkerställa en rättvis deltagande i nätverket, varje consortium medlem är förbjudna att använda flera systemhälsoverifierare än den första medlemmen i nätverket (om den första medlemmen distribuerar tre systemhälsoverifierare, varje medlem kan bara ha upp till tre systemhälsoverifierare).

### <a name="identity-store"></a>Identitetslagret

Eftersom varje medlem har flera verifieraren noder som körs parallellt och varje nod måste ha en tillåtna identitet, är det viktigt att systemhälsoverifierare på ett säkert sätt kan hämta en unik identitet som är aktiv i nätverket. Vi har skapat en Identity-Store som distribueras i varje medlems-prenumeration som innehåller de genererade Ethereum-identiteterna på ett säkert sätt för att underlätta detta finns. Vid distribution, orchestration-behållaren Generera en Ethereum privat nyckel för varje verifieraren och lagra den i Azure Key Vault. Innan noden paritet startar inhämtar den första ett lån på ett oanvända identitet för att kontrollera identiteten inte är hämtas av en annan nod. Identiteten har angetts för klienten som ger den behörighet att skapa block. Om den värdbaserade Virtuellt uppstår ett avbrott, kommer identity lånet att publiceras, så att en ersättning nod att återuppta sin identitet i framtiden.

### <a name="bootnode-registrar"></a>Bootnode registrator

Om du vill aktivera enkel anslutning varje medlem ska vara värd för en uppsättning anslutningsinformationen i [data API-slutpunkt](#data-api). Dessa data innehåller en lista över bootnodes som tillhandahålls som peering-noder för den anslutande medlemmen. Som en del av dessa data API kan hålla vi bootnode listan uppdaterad

### <a name="bring-your-own-operator"></a>Ta med din egen operator

Ofta vill delta i nätverket styrning medlem consortium men vill inte använda och underhålla infrastrukturen. Till skillnad från traditionella system har du en operator över nätverket fungerar mot decentraliserad modell för blockchain-system. I stället för att anlita en centraliserad mellanhand att driva ett nätverk, kan varje medlem i consortium delegera infrastrukturhantering till operatorn de önskar. På så sätt kan en hybridmodell där varje medlem kan välja att använda sin egen infrastruktur eller delegera åtgärden till en annan partner. Delegerad åtgärd arbetsflödet fungerar på följande sätt:

1.  **Consortium medlem** genererar en Ethereum-adress (innehåller privata nyckeln)

2.  **Consortium medlem** tillhandahåller offentlig Ethereum-adress till **Operator**

3.  **Operatorn** distribuerar och konfigurerar PoA verifieraren noderna med hjälp av vår Azure Resource Manager-lösning

4.  **Operatorn** ger RPC och hanteringsslutpunkt till **Consortium medlem**

5.  **Consortium medlem** använder sin privata nyckel för att logga en begäran som tar emot verifieraren noderna **operatorn** har distribuerat för att delta för deras räkning

### <a name="azure-monitor"></a>Azure Monitor

Den här lösningen levereras också med Azure Monitor för att spåra noden statistik. Detta ger insyn i den underliggande blockchain att spåra block generation statistik för programutvecklare. Nätverksadministratörer kan använda Azure Monitor för att snabbt identifiera och förhindra avbrott i nätverket via infrastruktur statistik och frågningsbart loggar. Mer information finns i [tjänsten övervakning](#service-monitoring).

### <a name="deployment-architecture"></a>Distributionsarkitektur för

#### <a name="description"></a>Beskrivning

Den här lösningen kan distribuera en enda eller flera regioner baserat flera medlem Ethereum consortium network. Som standard är RPC- och peering-slutpunkter tillgängliga via offentlig IP-adress genom att aktivera förenklad anslutning över prenumerationer och moln. Vi rekommenderar att utnyttja [paritets ge behörighet till kontrakt](https://wiki.parity.io/Permissioning) nivå åtkomstkontroller för programmet. Vi har också stöd för nätverk som distribuerats bakom VPN, som utnyttjar VNet-gateways för anslutning mellan prenumerationer. Dessa distributioner är mer komplexa, så rekommenderar vi att du börjar med den offentliga IP-modellen först.

#### <a name="consortium-member-overview"></a>Consortium medlem översikt

Varje consortium medlem distributionen omfattar:

-   Virtuella datorer för att köra PoA systemhälsoverifierare

-   Azure Load Balancer för att distribuera RPC, peering och styrning DApp begäranden

-   Azure Key Vault för att skydda identiteter för systemhälsoverifierare

-   Azure Storage för som är värd för beständiga nätverksinformation och samordna leasing

-   Azure Monitor för att sammanställa loggar och prestandastatistik

-   VNet-Gateway (valfritt) för att tillåta VPN-anslutningar över privata virtuella nätverk

![Distributionsarkitektur för](./media/ethereum-poa-deployment/deployment-architecture.png)

Vi använda Docker-behållare för tillförlitlighet och modularitet. Vi använder Azure Container Registry för att vara värd för och tillhandahålla version bilder som en del av varje distribution. Behållaravbildningarna består av:

-   Orchestrator

    -   Körs en gång under distributionen

    -   Genererar identiteter och styrning kontrakt

    -   Butiker identiteter i Identity Store

-   Paritet klienten

    -   Lån identiteten från identitet Store

    -   Identifierar och ansluter till peer-datorer

-   EthStats Agent

    -   Samlar in lokala loggar och statistik via RPC och push-meddelanden till Azure Monitor

-   Styrning DApp

    -   Webbgränssnitt för att interagera med styrning kontrakt

## <a name="how-to-guides"></a>Instruktionsguider
### <a name="governance-dapp"></a>Styrning DApp

I centrum för proof-of-authority är decentraliserad styrning. Styrning DApp är en uppsättning redan distribuerat [smarta kontrakt](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) och ett webbprogram som används för att styra myndigheterna i nätverket.
Myndigheter har delats upp i Admin-identiteter och verifieraren noder.
Administratörer har möjlighet att delegera konsensus deltagande till en uppsättning verifieraren noder. Administratörer kan också rösta andra administratörer i eller utanför nätverket.

![styrning dapp](./media/ethereum-poa-deployment/governance-dapp.png)

-   **Decentraliserad styrning -** ändringar i nätverket myndigheter hanteras via i kedjan rösta av väljer administratörer.

-   **Verifieraren delegering -** myndigheter kan hantera sina verifieraren noder som ställs in i varje PoA-distribution.

-   **Granskningsbara ändringshistorik -** varje ändring registreras på den blockkedja som tillhandahåller transparens och revision.

#### <a name="getting-started-with-governance"></a>Komma igång med styrning
För att utföra alla typer av transaktioner via styrning DApp, måste du använda en Ethereum-wallet.  Den enklaste metoden är att använda en i webbläsaren wallet som [MetaMask](https://metamask.io), men eftersom dessa smarta kontrakt som distribueras i nätverket kan du även automatisera dina interaktioner på styrning avtalet.

När du har installerat MetaMask, navigerar du till styrning DApp i webbläsaren.  Du kan hitta URL: en i bekräftelsemeddelandet distribution eller via Azure-portalen i utdata för distribution.  Om du inte har en i webbläsaren wallet installerad du kommer inte att kunna utföra några åtgärder. men du kan fortfarande kan läsa tillståndet administratör.  

#### <a name="becoming-an-admin"></a>Bli en administratör
Om du är den första medlemmen som distribuerats i nätverket, sedan du blir automatiskt en administratör och paritet noderna visas som systemhälsoverifierare.  Om du ska ansluta till nätverket, måste du hämta röstade en administratör med majoritet (större än 50%) av den befintliga uppsättningen av administratören.  Om du väljer att inte bli administratör sedan dina noder fortfarande synkronisera och validera blockchain; de kommer dock inte deltar i block skapandeprocessen. Starta röstande processen för att bli administratör, klicka på __Nominate__ och ange ditt Ethereum-adress och alias.

![Nominera](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Kandidater
Att välja den __kandidater__ fliken visar den aktuella uppsättningen kandidat administratörer.  När en kandidat når en majoritet av aktuella administratörer, ska kandidaten få befordras till en administratör.  För att rösta på en kandidat, Välj raden och klicka på ”röst” högst upp.  Om du ändrar dig på en röst, kan du välja kandidaten och klicka på ”Rescind röst”.

![Kandidater](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Administratörer
Den __administratörer__ fliken visas den aktuella uppsättningen av administratörer och ger dig möjlighet att rösta mot.  När en administratör förlorar mer än 50%-support, kommer de tas bort som administratör på nätverket.  Verifieraren noder som äger den här administratören kommer att förlora verifieraren status och bli transaktion noder i nätverket.  En administratör kan tas bort av olika skäl. men är det upp till consortium att komma överens om en princip i förväg.

![Administratörer](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Systemhälsoverifierare
Att välja den __systemhälsoverifierare__ fliken på den vänstra menyn visar aktuella distribuerade paritet noderna för den här instansen och deras aktuella status (nodtyp).  Varje consortium medlem har en annan uppsättning systemhälsoverifierare på den här listan, eftersom den här vyn visar den aktuella distribuerade consortium medlemmen.  Om det är en nyligen distribuerade instans och du inte har lagt till din systemhälsoverifierare, visas alternativet att lägga till systemhälsoverifierare.  Om du väljer detta automatiskt väljer en regionalt belastningsutjämnad uppsättning paritet noder och tilldela dem till din verifieraren uppsättning.  Om du har distribuerat fler noder än den tillåtna kapaciteten blir de återstående noderna transaktion noder i nätverket.

Adressen för varje verifieraren tilldelas automatiskt den [identitetsarkiv](#identity-store) i Azure.  Om en nod stängs av, kommer den ifrån sig sin identitet, vilket gör att en annan nod i distributionen ta dess plats.  Detta garanterar att ditt deltagande konsensus har hög tillgänglighet.

![Systemhälsoverifierare](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Consortium namn
En administratör kan uppdatera Consortium-namnet som visas överst på sidan.  Välj kugghjulsikonen i det övre vänstra hörnet att uppdatera namnet Consortium.

#### <a name="account-menu"></a>Kontomeny
I det övre högerkant hörnet är dina Ethereum-kontoalias och identicon.  Om du är administratör har du möjlighet att uppdatera ditt alias.

![Konto](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Distribuera Ethereum Proof-of-Authority

Här är ett exempel på en flerparti distributionsflödet:

1.  Tre medlemmar generera ett Ethereum-konto med hjälp av MetaMask

2.  *Medlem A* distribuerar Ethereum PoA, att tillhandahålla sina offentlig Ethereum-adress

3.  *Medlem A* ger consortium-URL: en till *medlem B* och *medlem C*

4.  *Medlem B* och *medlem C* distribuera Ethereum PoA, att tillhandahålla sina offentliga Ethereum-adressen och *medlem A*'s consortium URL

5.  *Medlem A* rösterna inom *medlem B* som administratör

6.  *Medlem A* och *medlem B* båda röst *medlem C* som administratör

Den här processen kräver en Azure-prenumeration som har stöd för distribution av flera virtuella datorer och hanterade diskar. Om det behövs [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) att börja.

När en prenumeration är skyddad, går du till Azure-portalen. Välj ”+”, Marketplace (”Visa alla”), och Sök efter Ethereum PoA Consortium.

Följande avsnitt vägleder dig genom att konfigurera den första medlemmen fotavtryck i nätverket. Distributionsflödet består av fem steg: Grunderna, distribution regioner, nätverkets storlek och prestanda, Ethereum-inställningar, Azure Monitor.

#### <a name="basics"></a>Grundläggande inställningar

Under **grunderna**, ange värden för standard parametrar för alla distributioner, till exempel prenumeration, resursgrupp och grundläggande VM-egenskaper.

En detaljerad beskrivning av varje parameter visas nedan:

Parameternamn|Beskrivning|Tillåtna värden|Standardvärden
---|---|---|---
Skapa ett nytt nätverk eller ansluta till befintliga nätverk?|Skapa ett nytt nätverk eller Anslut till ett befintligt consortium-nätverk|Skapa ny koppling befintliga|Skapa ny
E-postadress (valfritt)|Du får ett e-postmeddelande när distributionen är klar med information om din distribution.|Giltig e-postadress|Ej tillämpligt
VM-användarnamn|Administratörens användarnamn för varje distribuerad virtuell dator (endast alfanumeriska tecken)|1 – 64 tecken|Ej tillämpligt
Autentiseringstyp|Metoden för att autentisera till den virtuella datorn.|Lösenordet eller SSH offentlig nyckel|Lösenord
Lösenord (autentiseringstyp = lösenord)|Lösenordet för administratörskontot för var och en av de virtuella datorerna som distribueras.  Lösenordet måste innehålla 3 av följande: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken. Även om alla virtuella datorer har ursprungligen samma lösenord, kan du ändra lösenordet när du har etablerat.|12 – 72 tecken|Ej tillämpligt
SSH-nyckel (autentiseringstyp = offentlig nyckel)|SSH-nyckel som används för fjärrinloggning.||Ej tillämpligt
Prenumeration|Den prenumeration som ska distribueras consortium network||Ej tillämpligt
Resursgrupp|Den resursgrupp som ska distribueras consortium network.||Ej tillämpligt
Plats|Azure-regionen för resursgruppen.||Ej tillämpligt

En exempeldistribution visas nedan: ![grundläggande bladet](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>Distribution av regioner

Ange indata för antal region(er) konsortienätverk och valet av Azure-regioner baserat på antalet regioner som anges under distributionen regioner. Användare kan distribuera i högst 5 regioner. Vi rekommenderar att du väljer den första regionen så att de matchar resursgruppens plats från avsnittet grunderna. En enda region per medlem rekommenderas för utveckling eller testning nätverk. Vi rekommenderar för produktion, distribution över två eller fler regioner för hög tillgänglighet.

En detaljerad beskrivning av varje parameter visas nedan:

  Parameternamn|Beskrivning|Tillåtna värden|Standardvärden
  ---|---|---|---
  Antal region(er)|Antal regioner för att distribuera consortium network|1, 2, 3, 4, 5|1
  Första regionen|Första regionen att distribuera consortium network|Alla tillåtna Azure-regioner|Ej tillämpligt
  Andra region|Andra regionen som du distribuerar konsortienätverk (visas bara om antalet regioner som har markerats som 2)|Alla tillåtna Azure-regioner|Ej tillämpligt
  Tredje region|Tredje region för att distribuera konsortienätverk (visas bara om antalet regioner som har markerats som 3)|Alla tillåtna Azure-regioner|Ej tillämpligt
  Fjärde region|Fjärde region för att distribuera konsortienätverk (visas bara om antalet regioner som har markerats som 4)|Alla tillåtna Azure-regioner|Ej tillämpligt
  Femte region|Femte region för att distribuera konsortienätverk (visas bara om antalet regioner som har markerats som 5)|Alla tillåtna Azure-regioner|Ej tillämpligt

En exempeldistribution visas nedan: ![distribution regioner](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Nätverkets storlek och prestanda

Ange indata för storleken på consortium-nätverket, till exempel antal och storlek för systemhälsoverifierarens noder under ”nätverkets storlek och prestanda”.
Verifieraren storage nodstorlek bestämmer potentiella storleken på blockchain. Detta kan ändras efter distributionen.

En detaljerad beskrivning av varje parameter visas nedan:

  Parameternamn|Beskrivning|Tillåtna värden|Standardvärden
  ---|---|---|---
  Antalet belastningsutjämnade verifieraren noder|Antalet noder som verifieraren kan etablera som en del av nätverket|2-15|2
  Verifieraren noden lagringsprestanda|Typ av hanterad disk säkerhetskopiering varje distribuerade verifieraren nod.|Standard SSD- eller Premium|Standard SSD
  Verifieraren nodstorlek för virtuell dator|VM-storleken som används för systemhälsoverifierarens noder.|Standard A Standard D, Standard D-v2, Standard F-serien, Standard DS och Standard FS|Standard D1 v2

[Prisinformation om Lagring](https://azure.microsoft.com/pricing/details/managed-disks/)

[Prisinformation för virtuell dator](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

Virtuell dator och lagringsnivå påverkar nätverkets prestanda.  Vi rekommenderar följande SKU: er baserat på önskad kostnadseffektivitet:

  SKU för virtuell dator|Lagringsnivå|Pris|Dataflöde|Svarstid
  ---|---|---|---|---
  F1|Standard SSD|Låg|Låg|Hög
  D2_v3|Standard SSD|medel|medel|medel
  F16s|Premium SSD|Hög|Hög|Låg

En exempeldistribution visas nedan: ![storlek och prestanda](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ethereum-inställningar

Ange därefter Ethereum-relaterade konfigurationsinställningar, t.ex. nätverks-ID och Ethereum-konto lösenord eller genesis blocket under Ethereum-inställningar.

En detaljerad beskrivning av varje parameter visas nedan:

  Parameternamn|Beskrivning|Tillåtna värden|Standardvärden
  ---|---|---|---
Consortium medlems-ID|ID som är associerade med varje medlem som deltar i konsortienätverk som används för att konfigurera IP-adressutrymmen att undvika kollision. När det gäller ett privat nätverk måste medlems-ID vara unikt inom olika organisationer i samma nätverk.  Ett unikt medlems-ID krävs även när samma organisation distribuerar till flera regioner. Anteckna värdet för den här parametern eftersom du behöver dela den med andra sammanbinder medlemmar att se till att det finns inga kollision.|0-255|Ej tillämpligt
Nätverks-ID|Nätverks-ID för consortium Ethereum-nätverk som ska distribueras.  Varje Ethereum-nätverk har sin egen, med 1 som ID för det offentliga nätverket.|5 - 999,999,999|10101010
Administratören Ethereum-adress|Ethereum kontoadress som används för att delta i PoA styrning.  Vi rekommenderar att du använder MetaMask för att generera en Ethereum-adress.|42 alfanumeriska tecken som börjar med 0 x|Ej tillämpligt
Avancerade alternativ|Avancerade alternativ för Ethereum-inställningar|Aktivera eller inaktivera|Inaktivera
Offentlig IP-adress (avancerade alternativ = Enable)|Distribuerar nätverket bakom en VNet-Gateway och tar bort peering åtkomst. Om det här alternativet väljs, måste alla medlemmar använda en VNet-Gateway för anslutningen för att vara kompatibel.|Privat virtuellt nätverk för offentlig IP-adress|Offentlig IP-adress
Block Gas Limit (Advanced Options = Enable)|Startar block gas gränsen på nätverket|Numerisk|50,000,00
Reseal Blockeringsperiod (sek)|Frekvensen som tomt block skapas när det finns inga transaktioner i nätverket. Tätare har snabbare avveckling överföring men ökade lagringskostnader.|Numerisk|15
Transaktionen behörighet kontraktet (avancerade alternativ = Enable)|ByteCode för transaktionen ge behörighet till kontraktet. Begränsar smarta kontrakt distribution och körning till en lista med tillåtna av Ethereum-konton.|Kontraktet bytecode|Ej tillämpligt

En exempeldistribution visas nedan: ![ethereum-inställningar](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>Övervakning

Bladet övervakning kan du konfigurera en resurs med Azure Monitor-loggar för nätverket. Övervakningsagenten samlar in och surface användbart mått och loggar från ditt nätverk, ger möjligheten att snabbt kontrollera nätverkets tillstånd eller felsöka problem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Parameternamn|Beskrivning|Tillåtna värden|Standardvärden
  ---|---|---|---
Övervakning|Möjlighet att aktivera övervakning|Aktivera eller inaktivera|Aktivera
Ansluta till befintliga Azure Monitor-loggar|Skapa en ny instans av Azure Monitor-loggar eller Anslut till en befintlig instans|Skapa en ny eller ansluta till befintliga|Skapa ny
Övervaka plats (Anslut till befintliga Azure Monitor-loggar = Skapa ny)|Den region där den nya Azure Monitor loggar instansen kommer att distribueras|Alla Azure Monitor loggar regioner|Ej tillämpligt
Befintliga log analytics arbetsyte-ID (Anslut till befintliga Azure Monitor-loggar = ansluta befintliga)|Arbetsyte-ID för den befintliga Azure-övervakaren loggar instans||Ej tillämpligt
Primärnyckeln för befintliga log analytics (Anslut till befintliga Azure Monitor-loggar = ansluta befintliga)|Den primära nyckeln som används för att ansluta till den befintliga instansen av Azure Monitor-loggar||Ej tillämpligt


En exempeldistribution visas nedan: ![med azure monitor](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Sammanfattning

Klicka dig igenom sammanfattningsbladet och granska indata som angetts och att köra grundläggande verifiering av distributionen. Innan du distribuerar kan du ladda ned mall och parametrar.

Läs juridisk information och sekretess licensvillkoren och klicka på Köp om du distribuerar. Om distributionen innehåller VNet-gatewayer kan kommer distributionen ta 45 50 minuter.

#### <a name="post-deployment"></a>Efter distributionen

##### <a name="deployment-output"></a>Utdata för distribution

När distributionen är klar kan du komma åt de nödvändiga parametrarna via e-postbekräftelsen eller via Azure portal. I de här parametrarna hittar du:

-   Ethereum RPC-slutpunkt

-   Styrning instrumentpanelens URL-adress

-   Azure Monitor-URL

-   Webbadressen för data

-   VNet-Gateway resurs-ID (valfritt)

##### <a name="confirmation-email"></a>E-postbekräftelsen

Om du anger en e-postadress ([grunderna avsnittet](#basics)), ett e-postmeddelande skickas till e-postadress med distributionsinformation för utdata.

![distribution av e-post](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portalen

När distributionen har slutförts och alla resurser som har etablerats kan du visa utdataparametrarna i resursgruppen.

1.  Leta reda på resursgruppen i portalen

2.  Gå till *distributioner*

3.  Välj den översta distributionen med samma namn som din resursgrupp

4.  Välj *utdata*

### <a name="growing-the-consortium"></a>Växande consortium

Om du vill expandera din consortium, måste du först ansluta det fysiska nätverket.
Med hjälp av den offentliga IP-baserad distributionen är den här första steget sömlös. Om du distribuerar bakom en VPN-anslutning, se avsnittet [ansluter VNet-Gateway](#connecting-vnet-gateways) göra nätverksanslutning som en del av den nya medlemmen-distributionen.  När distributionen har slutförts används den [styrning DApp](#governance-dapp) att bli ett nätverk som administratör.

#### <a name="new-member-deployment"></a>Ny medlem distribution

1.  Dela följande information med den anslutande medlemmen. Den här informationen kan hittas i din efter distributionen e-post eller portaldistribuering utdata.

    -  Consortium Data-Url

    -  Antalet noder som du har distribuerat

    -  VNet-Gateway resurs-ID (om du använder VPN)

2.  Distribuera medlemmen ska använda den [samma lösning](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) vid distribution av sin nätverksanvändning med Tänk på följande:

    -  Välj *ansluta befintliga*

    -  Välj samma antal verifieraren noder som resten av medlemmarna i nätverket för att se till att fair representation

    -  Använd samma Ethereum-adress som angavs i föregående steg

    -  Skicka in den angivna *Consortium Data Url* på den *Ethereum inställningar* fliken

    -  Om resten av nätverket är bakom en VPN-anslutning väljer *privata VNet* under avsnittet avancerade

#### <a name="connecting-vnet-gateways"></a>Ansluta VNet-gatewayer

Du kan ignorera det här steget om du har distribuerat med hjälp av standardinställningarna för offentlig IP-adress. När det gäller ett privat nätverk är i olika medlemmar anslutna via VNet-gateway-anslutningar. Innan en medlem kan ansluta till nätverket och se transaktion trafik, måste en befintlig medlem göra en slutgiltig konfiguration på sina VPN-gatewayen för att godkänna anslutningen. Det innebär att Ethereum-noder för medlemmen som sammanbinder inte köras förrän en anslutning har upprättats. Vi rekommenderar att du skapar överflödiga nätverksanslutningar (nät) till consortium att minska risken för en enskild felpunkt.

Efter den nya medlemmen distribuerar utföra befintliga medlemmen dubbelriktad anslutning genom att konfigurera en VNet-gateway-anslutning till den nya medlemmen. För att uppnå detta behöver befintlig medlem:

1.  VNet-gateway ResourceID medlemmens anslutning (se distribution utdata)

2.  Nyckeln för delad anslutning

Befintlig medlem måste köra följande PowerShell-skript för att slutföra anslutningen. Vi rekommenderar att du använder Azure Cloud Shell finns i det övre högra navigeringsfältet i portalen.

![cloudshell](./media/ethereum-poa-deployment/cloud-shell.png)

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

### <a name="service-monitoring"></a>Tjänstövervakning

Du kan hitta din Azure Monitor-portal, antingen genom att följa länken i e-post för distribution eller hitta parametern i distributionen utdata \[OMS\_PORTAL\_URL\].

Portalen visar övergripande statistik och noden översikt över först.

![övervaka kategorier](./media/ethereum-poa-deployment/monitor-categories.png)

Att välja **noden översikt** omdirigerar dig till en portal för att visa statistik för per nod-infrastruktur.

![noden stats](./media/ethereum-poa-deployment/node-stats.png)

Att välja **nätverk Stats** ombeds du att visa Ethereum nätverksstatistik.

![statistik för nätverk](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Exempelfrågor för Kusto

Är en uppsättning frågningsbart loggarna bakom instrumentpanelerna. Du kan använda de här loggarna för att anpassa instrumentpanelerna, Undersök fel eller konfigurera tröskelvärde för aviseringar. Nedan hittar du en uppsättning exempel på frågor som kan vara kördes i verktyget Log Search:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Visar en lista över block som har rapporterats av mer än en systemhälsoverifierare. Användbart för att hitta kedja förgreningar.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Hämta peer-Genomsnittligt antal för en angiven verifieraren nod i genomsnitt över 5 minuter buckets.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>SSH-åtkomst

Av säkerhetsskäl nekas SSH-portåtkomst av en regel för säkerhet som standard. För att komma åt de virtuella datorinstanserna i PoA nätverk, måste du ändra den här regeln till \"Tillåt\"

1.  Starta i översiktsavsnittet för distribuerade resursgruppen från Azure-portalen.

    ![SSH översikt](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Välj Nätverkssäkerhetsgrupp för regionen för den virtuella datorn som du vill komma åt

    ![SSH nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Välj den \"Tillåt-ssh\" regel

    ![SSH-Tillåt](./media/ethereum-poa-deployment/ssh-allow.png)

4.  Ändra \"åtgärd\" att tillåta

    ![SSH Aktivera Tillåt](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  Klicka på \"spara\" (ändringar kan ta några minuter att tillämpa)

Du kan nu fjärransluta till de virtuella datorerna för systemhälsoverifierarens noder via SSH med ditt angivna admin användarnamn och lösenord/SSH-nyckel.
SSH-kommando för att köra för att komma åt den första noden i verifieraren visas i output-mallparameter för distribution enligt följande ”: SSH\_till\_första\_VL\_NODEN\_område 1” (för exempeldistribution: ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com). Gå till ytterligare transaktionsloggar noder genom att öka portnumret med ett (till exempel den första noden i transaktionen är på port 4000).

Om du har distribuerat till flera regioner kan du ändra kommandot ovan till DNS-namn eller IP-adressen för belastningsutjämnaren i den regionen. Hitta resursen med namngivningskonventionen för att hitta den DNS-namn eller IP-adressen för de andra regionerna, \* \* \* \* \*- lbpip reg\#, och visa dess DNS-namn och IP-egenskaper.

### <a name="azure-traffic-manager-load-balancing"></a>Azure Traffic Manager för belastningsutjämning

Med Azure Traffic Manager kan hjälpa att minska stopptiden och förbättra svarstiden för PoA nätverket genom att dirigera inkommande trafik mellan flera distributioner i olika regioner. Inbyggda hälsokontroller och automatisk omdirigering säkerställer hög tillgänglighet för RPC-slutpunkter och styrning DApp. Den här funktionen är användbart om du har distribuerat till flera regioner och är klara för produktion.

Med Traffic Manager kan du:

-   Förbättra PoA nätverkets tillgänglighet med automatisk redundans.

-   Öka dina nätverk svarstider av Routning slutanvändare till Azure-plats med låg nätverksfördröjning.

Om du vill skapa en Traffic Manager-profil kan du kan använda DNS-namnet på profilen för att ansluta till nätverket. När andra consortium medlemmar har lagts till i nätverket, Traffic Manager kan också användas för belastningsutjämning över sina distribuerade systemhälsoverifierare.

#### <a name="creating-a-traffic-manager-profile"></a>Skapar en Traffic Manager-profil

Sök efter och välj \"Traffic Manager-profil\" när du klickar på den \"skapa en resurs\" knappen i Azure-portalen.

![Sök efter azure traffic manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

Ge profilen ett unikt namn och välj den resursgrupp som skapades under distributionen av PoA. Distribuera genom att klicka på ”Skapa”.

![Skapa traffic manager](./media/ethereum-poa-deployment/traffic-manager-create.png)

När den har distribuerats väljer du instansen i resursgruppen. DNS-namn till traffic manager finns i Översikt-fliken

![Leta upp traffic manager DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Välj fliken slutpunkter och klicka på knappen Lägg till. Ge slutpunkten ett unikt namn. Ändra resource måltypen till offentliga IP-adressen. Välj sedan den offentliga IP-adressen för den första regionen\'s belastningsutjämnare.

![Routning traffic manager](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Upprepa för varje region i distribuerade nätverk. När slutpunkterna är i den \"aktiverat\" status, de kommer att läsa in och region balanserade på DNS-namnet på traffic manager. Du kan nu använda den här DNS-namnet i stället för den \[CONSORTIUM\_DATA\_URL\] parameter i andra steg i dokumentet.

### <a name="data-api"></a>Data API

Varje consortium medlem är värd för nödvändig information för att andra kan ansluta till nätverket. Den befintliga medlemmen ger [CONSORTIUM_DATA_URL] innan den medlemmen distribution. Vid distribution, att slå samman medlemmar hämta information från JSON-gränssnittet på följande slutpunkt:

`<CONSORTIUM_DATA_URL>/networkinfo`

Svaret innehåller information som är användbar för att ansluta till medlemmar (Genesis block, verifieraren inställd kontrakt ABI, bootnodes) och användbar information till den befintliga medlemmen (verifieraren adresser). Vi rekommenderar användning av den här standardisering att utöka consortium över molnleverantörer. Den här API: et returnerar ett JSON-formaterade svar med följande struktur:
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
## <a name="tutorials"></a>Självstudier

### <a name="programmatically-interacting-with-a-smart-contract"></a>Interagera programmässigt med ett smarta kontrakt

> [!WARNING]
> Skicka aldrig din privata nyckel Ethereum över nätverket! Kontrollera att varje transaktion signeras lokalt först och signerade transaktionen skickas över nätverket.

I följande exempel använder vi *ethereumjs wallet* att generera en Ethereum-adress *ethereumjs-tx* att logga in lokalt, och *web3* att skicka raw transaktionen till den Ethereum RPC-slutpunkt.

Vi använder den här enkla Hello-World smarta kontrakt för det här exemplet:

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text;
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

Det här exemplet förutsätter att kontraktet har redan distribuerats. Du kan använda *solc* och *web3* för att distribuera ett kontrakt programmässigt. Installera först följande nodmodulerna:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Det här nodeJS-skriptet utför följande:

-   Skapa en rå transaktion: postMsg

-   Logga transaktionen med den genererade privat nyckeln

-   Skicka signerad transaktionen till Ethereum-nätverk

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

// TODO Replace with your RPC endpoint
var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));

// Get the current nonce of the account
web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00',
     gasLimit: '0x2FAF080',
     to: address,
     value: '0x00',
     data: data
   }
   var tx = new ethereumjs(rawTx);

   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="deploy-smart-contract-with-truffle"></a>Distribuera smarta kontrakt med Truffle

-   Installera nödvändiga bibliotek

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   I truffle.js, lägger du till följande kod för att låsa upp kontot MetaMask och konfigurera noden PoA som posten pekar genom att tillhandahålla mnemoteknisk frasen (MetaMask / inställningar / avslöja Seed ord)

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var rpc_endpoint = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    poa: {
      provider: new HDWalletProvider(mnemonic, rpc_endpoint),
      network_id: 3,
      gasPrice : 0
    }
  }
};

```

-   Distribuera till PoA nätverk

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Felsöka smarta kontrakt med Truffle

Truffle har en lokal utveckla nätverk som är tillgänglig för felsökning smarta kontrakt. Du hittar den fullständiga självstudien [här](https://truffleframework.com/tutorials/debugging-a-smart-contract).

### <a name="webassembly-wasm-support"></a>Stöd för WebAssembly (WASM)

WebAssembly support har redan aktiverats för dig i den nyligen distribuerade PoA nätverk. Det möjliggör smarta kontrakt utveckling på valfritt språk som transpiles till webb-sammansättningen (Rust, C, C++). Se länkarna nedan för mer information

-   Paritet översikt över WebAssembly- <https://wiki.parity.io/WebAssembly-Home>

-   Självstudien från paritet Tech- <https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>Referens

### <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Jag Observera att det finns många transaktioner i nätverket som jag fungerade\'t skicka. Var kommer dessa från?

Det är säkert att låsa upp den [personliga API](https://web3js.readthedocs.io/en/1.0/web3-eth-personal.html). Robotar lyssna efter upplåst Ethereum-konton och försök att tömma pengar. Roboten förutsätter dessa konton innehåller verkliga ether och försöker var först med att siphon balans. Aktivera inte personliga API: et i nätverket. I stället Logga transaktioner antingen manuellt med hjälp av en wallet som MetaMask eller programmässigt som beskrivs i avsnittet före [interagera programmässigt med ett smarta kontrakt](#programmatically-interacting-with-a-smart-contract).

#### <a name="how-to-ssh-onto-a-vm"></a>Hur SSH till en virtuell dator?

SSH-porten exponeras inte av säkerhetsskäl. Följ [den här handboken för att aktivera SSH-porten](#ssh-access).

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Hur konfigurerar jag en granskning medlem eller transaktionen noder?

Transaktionen noder är en uppsättning paritet klienter som är peer-kopplade med nätverket men inte deltar i konsensus. Dessa noder kan användas för att skicka in Ethereum-transaktioner och läsa smarta kontrakt-tillstånd.
Detta fungerar även som en mekanism för att tillhandahålla revision för icke-authority consortium medlemmar i nätverket. För att uppnå detta bara följa steg 2 växer Consortium.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Varför är MetaMask transaktioner tar lång tid?

För att säkerställa transaktioner tas emot i rätt ordning, kommer varje Ethereum-transaktion med en stegvis ökande nonce. Om du har använt ett konto i MetaMask i ett annat nätverk, måste du återställa det tillfälligt. Klicka på ikonen för inställningar (3-staplar), inställningar, Återställ konto. Transaktionshistoriken kommer att tas bort och du kan nu skicka om transaktionen.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Behöver jag ange gas avgift i MetaMask?

Ether har inte ett syfte i proof-of-authority consortium. Det är därför behöver inte ange gas avgift när du skickar in transaktioner i MetaMask.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Vad ska jag göra om distributionen misslyckas på grund av det gick inte att etablera Azure OMS?

Övervakning är en valfri funktion. I sällsynta fall där distributionen misslyckas på grund av oförmåga att har etablera Azure Monitor-resurs som du kan distribuera om utan Azure Monitor.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Är den offentliga IP-distributioner som är kompatibla med privat nätverksdistributioner?

Nej, peering krävs dubbelriktad kommunikation så att hela nätverket måste vara antingen offentliga eller privata.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Vad är den förväntade transaktionsdataflöde för Proof-of-Authority?

Transaktionen dataflödet blir mycket beroende på vilka typer av transaktioner och nätverkets topologi.  Med hjälp av enkla transaktioner, har vi jämfört genomsnitt 400 transaktioner per sekund med ett nätverk som distribueras över flera regioner.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Hur jag prenumererar på smarta kontrakt händelser?

Ethereum Proof-of-Authority har nu stöd för web sockets.  Kontrollera distributionen e-post eller distribution av utdata för att hitta den uttag Webb-URL och portnummer.

## <a name="next-steps"></a>Nästa steg

Kom igång med hjälp av den [Ethereum Proof-of-Authority Consortium](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) lösning.
