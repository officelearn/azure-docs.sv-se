---
title: Distribuera Ethereum proof-of-Authority – Solution Template på Azure
description: Använd Ethereum-lösningen för att distribuera och konfigurera ett Ethereum-nätverk med flera medlemmar i Azure
ms.date: 04/08/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: c3e449c1d6ebaf7c6cb2c35dc9f91d55f569447a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326177"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Distribuera Ethereum proof-of-Authority – Solution Template på Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Ethereum-mallen för proof-of-Authority-myndigheter](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) är utformad för att göra det enklare att distribuera, konfigurera och styra ett Ethereum-nätverk med flera medlemmar med minimal Azure-och Ethereum-kunskap.

Med en fåtal med indata från användaren och en enkel klicknings distribution via Azure Portal kan varje medlem etablera ett nätverk med hjälp av Microsoft Azure beräknings-, nätverks-och lagrings tjänster världen över. Varje medlems nätverks storlek består av en uppsättning belastningsutjämnade validator-noder som ett program eller en användare kan använda för att skicka Ethereum transaktioner.

## <a name="concepts"></a>Begrepp

### <a name="terminology"></a>Terminologi

-   **Konsensus** – syftet med synkroniseringen av data i det distribuerade nätverket genom validering och skapande av block.

-   **Konsortiet medlem** – en enhet som deltar i samförstånd i blockchain-nätverket.

-   **Admin** – ett Ethereum-konto som används för att hantera deltagande för en specifik konsortiums medlem.

-   **Validator** – en dator som är kopplad till ett Ethereum-konto som deltar i samförstånd för en administratörs räkning.

### <a name="proof-of-authority"></a>Proof-of-Authority

För de som är nya för blockchain-communityn är lanseringen av den här lösningen en bra möjlighet att lära sig mer om tekniken på ett enkelt och konfigurerbart sätt i Azure. Proof-of-Work är en Sybil mekanism som utnyttjar beräknings kostnader för att själv reglera nätverket och tillåta rättvis medverkan. Detta fungerar bra i anonyma öppna blockchain-nätverk där konkurrensen för kryptovalutor höjer säkerheten i nätverket. I privata/konsortier-nätverk har den bakomliggande etern dock inget värde. Ett alternativt protokoll, bevis-från-myndighet, är lämpligare för tillåtna nätverk där alla konsensus deltagare är kända och välkända. Utan att behöva utvinnas behöver du mer effektiv bevisning från auktoritet samtidigt som du behåller Byzantine fel tolerans.

### <a name="consortium-governance"></a>Konsortiets styrning

Eftersom bevis på auktoritet förlitar sig på en tillåten lista över nätverks certifikat för att hålla nätverket felfritt, är det viktigt att tillhandahålla en rättvis mekanism för att göra ändringar i behörighets listan. Varje distribution levereras med en uppsättning smarta kontrakt och portaler för styrning av den här listan över tillåtna användare. När en föreslagen ändring når en majoritets röst av konsortiets medlemmar, är ändringen i sitt beslut. På så sätt kan nya konsensus deltagare läggas till eller komprometterade deltagare tas bort på ett transparent sätt som uppmuntrar ett Honest-nätverk.

### <a name="admin-account"></a>Administratörs konto

Under distributionen av noderna för proof-of-Authority uppmanas du att ange en admin Ethereum-adress. Du kan använda flera olika metoder för att skapa och skydda det här Ethereum-kontot. När den här adressen har lagts till som en auktoritet i nätverket kan du använda det här kontot för att delta i styrning. Det här administratörs kontot kommer också att användas för att delegera konsensus deltagande till de validerade noder som skapas som en del av den här distributionen. Eftersom endast den offentliga Ethereum-adressen används, har varje administratör flexibiliteten att skydda sina privata nycklar på ett sätt som följer den önskade säkerhets modellen.

### <a name="validator-node"></a>Validator-nod

I proof-of-Authority-protokollet tar verifierade noder platsen för traditionella Miner-noder. Varje verifierare har en unik Ethereum-identitet som läggs till i en lista med Smart-kontrakt behörighet. När en verifierare finns i listan kan den delta i processen för att skapa block. Mer information om den här processen finns i paritets dokumentationen om [auktoritets rundan](https://wiki.parity.io/Aura). Varje konsortiums medlem kan etablera två eller fler verifierade noder i fem regioner för GEO-redundans. Validator-noder kommunicerar med andra validator-noder för att komma till enighet om status för den underliggande distribuerade redovisningen.
För att säkerställa ett rättvist deltagande i nätverket, är varje medlem av konsortiet förbjuden från att använda fler verifierare än den första medlemmen i nätverket (om den första medlemmen distribuerar tre verifierare får varje medlem bara ha upp till tre verifierare).

### <a name="identity-store"></a>Identitets Arkiv

Eftersom varje medlem kommer att ha flera verifierare som körs samtidigt och varje nod måste ha en tillåten identitet, är det viktigt att verifierarna kan få en unik Active identitet i nätverket på ett säkert sätt. För att göra det enklare har vi skapat ett identitets lager som distribueras i varje medlems prenumeration som på ett säkert sätt har genererade Ethereum-identiteter. Vid distributionen genererar Orchestration-behållaren en privat Ethereum-nyckel för varje verifierare och lagrar den i Azure Key Vault. Innan paritets-noden startar får den först ett lån på en oanvänd identitet för att se till att identiteten inte hämtas av en annan nod. Identiteten tillhandahålls till klienten som ger den behörighet att börja skapa block. Om den värdbaserade virtuella datorn upplever ett avbrott frigörs identitets lånet, så att en ersättande nod kan återuppta sin identitet i framtiden.

### <a name="bootnode-registrar"></a>Bootnode registrator

För att under lätta anslutningen kommer varje medlem att vara värd för en uppsättning anslutnings information på [data-API-slutpunkten](#data-api). Dessa data innehåller en lista över bootnodes som tillhandahålls som peering-noder för den anslutande medlemmen. Som en del av detta data-API håller vi den här bootnode-listan uppdaterad

### <a name="bring-your-own-operator"></a>Ta med din egen operatör

En konsortiums medlem kommer ofta att vilja delta i nätverks styrning, men vill inte använda och upprätthålla sin infrastruktur. Till skillnad från traditionella system fungerar en enda operatör över nätverket mot den decentraliserade modellen med blockchain-system. I stället för att anlita en centraliserad mellanhand för att kunna använda ett nätverk kan varje konsortium delegera infrastruktur hantering till operatören om de väljer. På så sätt kan en hybrid modell där varje medlem kan välja att använda sin egen infrastruktur eller delegera drift till en annan partner. Arbets flödet delegerad åtgärd fungerar på följande sätt:

1.  **Konsortiet medlem** genererar en Ethereum-adress (innehar privat nyckel)

2.  **Konsortiet medlem** tillhandahåller en offentlig Ethereum-adress till **operatören**

3.  **Operatorn** distribuerar och konfigurerar POA validator-noder med vår Azure Resource Manager-lösning

4.  **Operatör** ger RPC-och hanterings slut punkten till **konsortiet medlem**

5.  **Konsortiets medlem** använder sin privata nyckel för att signera en begäran som accepterar att **operatorn** validera noder har distribuerats för att delta i deras räkning

### <a name="azure-monitor"></a>Azure Monitor

Den här lösningen levereras också med Azure Monitor för att spåra nodens och nätverks statistik. För programutvecklare ger detta insyn i de underliggande blockchain för att spåra block generations statistik. Nätverks operatörer kan använda Azure Monitor för att snabbt upptäcka och förhindra nätverks avbrott genom infrastruktur statistik och frågor som kan loggas. Mer information finns i [tjänst övervakning](#service-monitoring).

### <a name="deployment-architecture"></a>Distributions arkitektur

#### <a name="description"></a>Beskrivning

Den här lösningen kan distribuera ett enda eller flera regioner som baseras på ett Ethereum konsortium-nätverk med flera medlemmar. Som standard är RPC-och peering-slutpunkterna tillgängliga över offentlig IP för att möjliggöra förenklad anslutning mellan prenumerationer och moln. Vi rekommenderar att du använder [paritets godkännande avtal](https://wiki.parity.io/Permissioning) för åtkomst på program nivå. Vi har även stöd för nätverk som distribuerats bakom VPN, som utnyttjar VNet-gatewayer för anslutningar mellan prenumerationer. De här distributionerna är mer komplexa, så vi rekommenderar att du börjar med den offentliga IP-modellen först.

#### <a name="consortium-member-overview"></a>Översikt över konsortiets medlemmar

Varje distribution av konsortiet ingår:

-   Virtual Machines för att köra PoA-verifierarna

-   Azure Load Balancer för distribution av RPC-, peering-och styrnings DApp-begäranden

-   Azure Key Vault för att skydda verifieraren identiteter

-   Azure Storage för att vara värd för beständig nätverksinformation och koordinera leasing

-   Azure Monitor för agg regering av loggar och prestanda statistik

-   VNet Gateway (valfritt) för att tillåta VPN-anslutningar över privata virtuella nätverk

![Distributions arkitektur](./media/ethereum-poa-deployment/deployment-architecture.png)

Vi utnyttjar Docker-behållare för tillförlitlighet och modulärhet. Vi använder Azure Container Registry för att vara värd för och hantera versions avbildningar som en del av varje distribution. Behållar avbildningarna består av:

-   Orchestrator

    -   Körs en gång under distributionen

    -   Genererar identiteter och styrnings avtal

    -   Lagrar identiteter i identitets arkivet

-   Paritets klient

    -   Lånar identitet från identitets lager

    -   Identifierar och ansluter till peer-datorer

-   EthStats-agent

    -   Samlar in lokala loggar och statistik via RPC och push-meddelanden till Azure Monitor

-   Styrnings DApp

    -   Webb gränssnitt för att interagera med styrnings avtal

## <a name="how-to-guides"></a>Instruktionsguider
### <a name="governance-dapp"></a>Styrnings DApp

När det gäller certifikat beviset är decentraliserad styrning. Styrnings DApp är en uppsättning fördistribuerade [smarta kontrakt](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) och ett webb program som används för att styra myndigheterna i nätverket.
Utfärdarna är indelade i admin identiteter och validator-noder.
Administratörer har möjlighet att delegera konsensus deltagande till en uppsättning validator-noder. Administratörer kan också rösta andra administratörer i eller ut ur nätverket.

![styrnings Dapp](./media/ethereum-poa-deployment/governance-dapp.png)

-   **Decentraliserad styrning –** Ändringar i nätverks myndigheterna administreras genom en röst markering genom att välja administratörer.

-   **Verifiera delegering –** Myndigheter kan hantera sina verifierade noder som ställs in i varje PoA-distribution.

-   **Gransknings bar ändrings historik-** Varje ändring registreras på blockchain och ger insyn och gransknings möjlighet.

#### <a name="getting-started-with-governance"></a>Komma igång med styrning
Om du vill utföra någon typ av transaktioner via styrnings DApp måste du använda en Ethereum plån bok.  Den enklaste metoden är att använda en plån bok i en webbläsare, till exempel [MetaMask](https://metamask.io); men eftersom dessa är smarta kontrakt som distribueras i nätverket kan du också automatisera dina interaktioner till styrnings kontraktet.

När du har installerat MetaMask navigerar du till styrnings-DApp i webbläsaren.  Du kan hitta URL: en i bekräftelse meddelandet för distribution eller genom Azure Portal i distributions resultatet.  Om du inte har en plån bok för en webbläsare som har installerats kan du inte utföra några åtgärder. Du kan dock fortfarande läsa administratörs tillstånd.  

#### <a name="becoming-an-admin"></a>Bli administratör
Om du är den första medlem som distribueras i nätverket blir du automatiskt en administratör och dina paritets-noder visas som verifierare.  Om du ansluter till nätverket måste du ha röstat in som administratör med en majoritet (större än 50%) av den befintliga administratörs uppsättningen.  Om du väljer att inte bli administratör kommer dina noder fortfarande att synkroniseras och validera blockchain; de kommer dock inte att delta i processen för att skapa block. Starta röstnings processen och bli administratör genom att klicka på __nominera__ och ange din Ethereum-adress och ditt alias.

![Nominera](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Beds
Om du väljer fliken __kandidater__ visas den aktuella uppsättningen av kandidat administratörer.  När en kandidat når en majoritet av de aktuella administratörerna kommer kandidaten att bli uppgraderad till en administratör.  Om du vill rösta på en kandidat markerar du raden och klickar på "rösta in" längst upp.  Om du ändrar dig för en röst kan du välja kandidaten och klicka på "återkalla röst".

![Beds](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Gruppen
Fliken __Administratörer__ visar den aktuella uppsättningen administratörer och ger dig möjlighet att rösta mot.  När en administratör har förlorat mer än 50% stöd kommer de att tas bort som administratör i nätverket.  Alla validerade noder som administratören äger förlorar verifierings status och blir transaktions noder i nätverket.  En administratör kan tas bort av valfritt antal orsaker. Det är dock upp till konsortiet att enas om en princip i förväg.

![Gruppen](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Verifierare
Om du väljer fliken __verifierare__ på den vänstra menyn visas de aktuella distribuerade paritets noderna för den här instansen och deras aktuella status (nodtyp).  Varje medlem i konsortiet har en annan uppsättning verifierare i listan, eftersom den här vyn representerar den aktuella distribuerade konsortiet medlem.  Om det här är en nyligen distribuerad instans och du ännu inte har lagt till dina verifierare visas alternativet för att lägga till verifierare.  Om du väljer det här alternativet väljs automatiskt en regionalt balanserad uppsättning paritets noder och tilldelar dem till din verifierings uppsättning.  Om du har distribuerat fler noder än den tillåtna kapaciteten kommer de återstående noderna att bli Transaction-noder i nätverket.

Adressen för varje verifierare tilldelas automatiskt via [identitets arkivet](#identity-store) i Azure.  Om en nod kraschar, kommer den att ge sin identitet, vilket gör att en annan nod i distributionen kan ta sin plats.  Detta säkerställer att ditt samförstånds bidrag är högt tillgängligt.

![Verifierare](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Namn på konsortium
Alla administratörer kan uppdatera namnet på konsortiet, som visas längst upp på sidan.  Välj kugg hjuls ikonen längst upp till vänster för att uppdatera namnet på konsortiet.

#### <a name="account-menu"></a>Menyn konto
I det övre högra hörnet är ditt Ethereum-kontos alias och Identicon.  Om du är administratör kan du uppdatera ditt alias.

![Konto](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Distribuera Ethereum-proof-of-Authority

Här är ett exempel på ett distributions flöde med flera parter:

1.  Tre medlemmar varje generera ett Ethereum-konto med MetaMask

2.  *Medlem A* distribuerar Ethereum POA, vilket ger sin Ethereum offentliga adress

3.  *Medlem A* innehåller konsortiets URL till *medlem B* och *medlem C*

4.  *Medlem B* och *medlem C* -distribution, Ethereum POA, som tillhandahåller sin Ethereum offentliga adress och *medlem A*: s konsortiums-URL

5.  *Medlem A* röster i *medlem B* som administratör

6.  *Medlem A* och *medlem B* både röst *medlem C* som administratör

Den här processen kräver en Azure-prenumeration som har stöd för distribution av flera virtuella datorer och hanterade diskar. Om det behövs kan du [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/) för att börja.

När en prenumeration har skyddats går du till Azure Portal. Välj ' + ', Marketplace (' Se alla ') och Sök efter Ethereum PoA Consortium.

I följande avsnitt får du hjälp med att konfigurera den första medlemmens storlek i nätverket. Distributions flödet delas in i fem steg: grunderna, distributions regioner, nätverks storlek och prestanda, Ethereum-inställningar Azure Monitor.

#### <a name="basics"></a>Grundläggande inställningar

Under **grunderna**anger du värden för standard parametrar för alla distributioner, till exempel prenumeration, resurs grupp och grundläggande egenskaper för virtuella datorer.

En detaljerad beskrivning av varje parameter är följande:

Parameternamn|Beskrivning|Tillåtna värden|Standardvärden
---|---|---|---
Vill du skapa ett nytt nätverk eller ansluta till ett befintligt nätverk?|Skapa ett nytt nätverk eller Anslut till ett befintligt konsortiums nätverk|Skapa ny koppling befintlig|Skapa Ny
E-postadress (valfritt)|Du får ett e-postmeddelande när distributionen är klar med information om distributionen.|Giltig e-postadress|Ej tillämpligt
Användar namn för virtuell dator|Administratörs användar namn för varje distribuerad virtuell dator (endast alfanumeriska tecken)|1-64 tecken|Ej tillämpligt
Autentiseringstyp|Metoden för att autentisera till den virtuella datorn.|Lösen ord eller offentlig SSH-nyckel|Lösenord
Lösen ord (autentiseringstyp = lösen ord)|Lösen ordet för administratörs kontot för var och en av de virtuella datorerna som distribueras.  Lösen ordet måste innehålla 3 av följande: 1 gemen bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken. Även om alla virtuella datorer ursprungligen har samma lösen ord, kan du ändra lösen ordet efter etableringen.|12-72 tecken|Ej tillämpligt
SSH-nyckel (autentiseringstyp = offentlig nyckel)|Den säkra shell-nyckel som används för fjärrinloggning.||Ej tillämpligt
Prenumeration|Den prenumeration som används för att distribuera konsortiets nätverk||Ej tillämpligt
Resursgrupp|Den resurs grupp som används för att distribuera konsortial nätverket.||Ej tillämpligt
Plats|Azure-regionen för resurs gruppen.||Ej tillämpligt

En exempel distribution visas nedan: ![Basic-bladet](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>Distributions regioner

Under distributions regioner anger du indata för antalet regioner som ska distribueras i konsortiet och valet av Azure-regioner baserat på antalet regioner som anges. Användaren kan distribuera högst 5 regioner. Vi rekommenderar att du väljer den första regionen för att matcha resurs gruppens plats från avsnittet med grundläggande information. För utvecklings-eller test nätverk rekommenderas en enda region per medlem. För produktion rekommenderar vi att du distribuerar över två eller flera regioner för hög tillgänglighet.

En detaljerad beskrivning av varje parameter är följande:

  Parameternamn|Beskrivning|Tillåtna värden|Standardvärden
  ---|---|---|---
  Antal regioner|Antal regioner för att distribuera konsortiets nätverk|1, 2, 3, 4, 5|1
  Första regionen|Första regionen för att distribuera konsortiet nätverk|Alla tillåtna Azure-regioner|Ej tillämpligt
  Andra regionen|Den andra regionen som används för att distribuera konsortiet nätverk (endast synlig när antalet regioner har valts som 2)|Alla tillåtna Azure-regioner|Ej tillämpligt
  Tredje regionen|Tredje region för att distribuera konsortiet nätverk (endast synlig när antalet regioner är markerat som 3)|Alla tillåtna Azure-regioner|Ej tillämpligt
  Fjärde regionen|Fjärde regionen för att distribuera konsortiet nätverk (endast synlig när antalet regioner är markerat som 4)|Alla tillåtna Azure-regioner|Ej tillämpligt
  Femte regionen|Femte regionen för att distribuera konsortiet nätverk (endast synlig när antalet regioner har valts som 5)|Alla tillåtna Azure-regioner|Ej tillämpligt

En exempel distribution visas nedan: ![distributions regioner](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Nätverks storlek och prestanda

Under "nätverks storlek och prestanda" anger du indata för storleken på konsortiet nätverk, till exempel antal och storlek på validerade noder.
Lagrings storleken för validator-noden avgör den potentiella storleken på blockchain. Detta kan ändras efter distributionen.

En detaljerad beskrivning av varje parameter är följande:

  Parameternamn|Beskrivning|Tillåtna värden|Standardvärden
  ---|---|---|---
  Antal noder för belastningsutjämnad verifiering|Antalet verifierade noder som ska etableras som en del av nätverket|2-15|2
  Lagrings prestanda för validator-nod|Vilken typ av hanterad disk som ska användas för att säkerhetskopiera var och en av de distribuerade verifierarna.|Standard SSD eller Premium|Standard SSD
  Virtuell dator storlek för validator-nod|Den virtuella dator storleken som används för verifierade noder.|Standard A, standard D, standard D-V2, standard F-serien, standard DS och standard FS|Standard D1 v2

[Pris information om lagring](https://azure.microsoft.com/pricing/details/managed-disks/)

[Pris information om virtuell dator](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

Den virtuella datorn och lagrings nivån kommer att påverka nätverks prestanda.  Vi rekommenderar följande SKU: er baserat på önskad kostnads effektivitet:

  SKU för virtuell dator|Lagrings nivå|Pris|Dataflöde|Svarstid
  ---|---|---|---|---
  F1|Standard SSD|börjar|börjar|högt
  D2_v3|Standard SSD|säker|säker|säker
  F16s|Premium SSD|högt|högt|börjar

En exempel distribution visas nedan: ![nätverks storlek och prestanda](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ethereum-inställningar

Under Inställningar för Ethereum anger du Ethereum konfigurations inställningar, t. ex. nätverks-ID och Ethereum-konto eller Genesis-block.

En detaljerad beskrivning av varje parameter är följande:

  Parameternamn|Beskrivning|Tillåtna värden|Standardvärden
  ---|---|---|---
Konsortiums medlems-ID|Det ID som är kopplat till varje medlem som deltar i konsortiet nätverk som används för att konfigurera IP-adressutrymme för att undvika kollision. Om det gäller ett privat nätverk ska medlems-ID: t vara unikt för olika organisationer i samma nätverk.  Ett unikt medlems-ID krävs även om samma organisation distribuerar till flera regioner. Anteckna värdet för den här parametern eftersom du måste dela den med andra medlemmar för att se till att det inte finns någon kollision.|0-255|Ej tillämpligt
Nätverks-ID|Nätverks-ID för konsortiet Ethereum-nätverk som ska distribueras.  Varje Ethereum nätverk har sitt eget nätverks-ID, med 1 som ID för det offentliga nätverket.|5 – 999 999 999|10101010
Admin Ethereum-adress|Ethereum-konto adress som används för deltagande i PoA-styrning.  Vi rekommenderar att du använder MetaMask för att skapa en Ethereum-adress.|42 alfanumeriska tecken som börjar med 0x|Ej tillämpligt
Avancerade alternativ|Avancerade alternativ för Ethereum-inställningar|Aktivera eller inaktivera|Inaktivera
Offentlig IP (avancerade alternativ = aktivera)|Distribuerar nätverket bakom en VNet-gateway och tar bort peering-åtkomst. Om det här alternativet väljs måste alla medlemmar använda en VNet-Gateway för att anslutningen ska vara kompatibel.|Offentligt IP privat VNet|Offentlig IP-adress
Block gas gräns (avancerade alternativ = aktivera)|Nätverkets start Blocks gräns|Alla numeriska|50000000
Blockera omförslutning av period (SEK)|Den frekvens med vilken tomma block skapas när det inte finns några transaktioner i nätverket. En högre frekvens kommer att ha snabbare slut för and öka lagrings kostnaderna.|Alla numeriska|15
Transaktions behörighets kontrakt (avancerade alternativ = aktivera)|Bytekod för transaktions behörighets avtal. Begränsar distribution och körning av smarta kontrakt till en lista över tillåtna Ethereum-konton.|Kontraktets bytekod|Ej tillämpligt

En exempel distribution visas nedan: ![ethereum inställningar](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>Övervakning

På bladet övervakning kan du konfigurera en Azure Monitor loggar resurs för nätverket. Övervaknings agenten samlar in och använder användbara mått och loggar från nätverket, vilket ger möjlighet att snabbt kontrol lera nätverkets hälso tillstånd eller fel söknings problem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Parameternamn|Beskrivning|Tillåtna värden|Standardvärden
  ---|---|---|---
Övervakning|Alternativ för att aktivera övervakning|Aktivera eller inaktivera|Aktivera
Ansluta till befintliga Azure Monitor loggar|Skapa en ny Azure Monitor loggar instans eller Anslut till en befintlig instans|Skapa ny eller koppla befintliga|Skapa ny
Övervaka plats (Anslut till befintliga Azure Monitor loggar = skapa ny)|Den region där den nya Azure Monitor loggar instansen ska distribueras|Alla Azure Monitor loggar regioner|Ej tillämpligt
Befintligt logganalys-arbetsyte-ID (Anslut till befintliga Azure Monitor loggar = koppling befintlig)|Arbetsyte-ID för den befintliga Azure Monitor loggar instansen||Ej tillämpligt
Befintlig Log Analytics primär nyckel (Anslut till befintliga Azure Monitor loggar = koppling befintlig)|Den primära nyckel som används för att ansluta till den befintliga Azure Monitor loggar instansen||Ej tillämpligt


En exempel distribution visas nedan: ![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Sammanfattning

Klicka på sammanfattnings bladet för att granska de indata som angetts och för att köra grundläggande verifiering före distribution. Innan du distribuerar kan du hämta mallen och parametrarna.

Granska juridiska och sekretess villkor och klicka på köp för att distribuera. Om distributionen inkluderar VNet-gatewayer tar distributionen upp 45 till 50 minuter.

#### <a name="post-deployment"></a>Efter distribution

##### <a name="deployment-output"></a>Distributions utdata

När distributionen har slutförts kan du komma åt de nödvändiga parametrarna via bekräftelse meddelandet eller via Azure Portal. I följande parametrar hittar du:

-   Ethereum RPC-slutpunkt

-   Instrument panels URL för styrning

-   Azure Monitor-URL

-   Data-URL

-   Resurs-ID för VNet-Gateway (valfritt)

##### <a name="confirmation-email"></a>Bekräfta e-post

Om du anger en e-postadress ([avsnittet grundläggande](#basics)) skickas ett e-postmeddelande till e-postadressen med utmatnings informationen för distributionen.

![e-postdistribution](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portal

När distributionen har slutförts och alla resurser har etablerats kan du Visa utdataparametrar i resurs gruppen.

1.  Leta upp resurs gruppen i portalen

2.  Navigera till *distributioner*

3.  Välj den översta distributionen med samma namn som din resurs grupp

4.  Välj *utdata*

### <a name="growing-the-consortium"></a>Växande konsortiet

Om du vill expandera ditt konsortium måste du först ansluta det fysiska nätverket.
Det första steget är sömlöst med den offentliga IP-baserade distributionen. Om du distribuerar bakom en VPN-anslutning, se avsnittet [ansluta VNet-gatewayen](#connecting-vnet-gateways) för att göra nätverks anslutningen som en del av den nya medlems distributionen.  När distributionen är klar använder du [styrnings DApp](#governance-dapp) för att bli nätverks administratör.

#### <a name="new-member-deployment"></a>Ny medlems distribution

1.  Dela följande information med den anslutna medlemmen. Den här informationen finns i e-postmeddelandet för post-Deployment eller i portalen för distribution av utdata.

    -  Data-URL för konsortiet

    -  Antalet noder som du har distribuerat

    -  Resurs-ID för VNet-Gateway (om du använder VPN)

2.  Distributions medlemmen bör använda [samma lösning](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) när du distribuerar sin nätverks närvaro med följande i åtanke:

    -  Välj *Anslut befintlig*

    -  Välj samma antal validator-noder som resten av medlemmarna i nätverket för att säkerställa en rättvis representation

    -  Använd samma Ethereum-adress som angavs i föregående steg

    -  Skicka in den angivna *konsortiet data-URL: en* på fliken *Inställningar för Ethereum*

    -  Om resten av nätverket ligger bakom en VPN väljer du *privat VNet* under avsnittet Avancerat

#### <a name="connecting-vnet-gateways"></a>Ansluta VNet-gatewayer

Du kan ignorera det här steget om du har distribuerat med standard inställningarna för offentliga IP-adresser. När det gäller ett privat nätverk är de olika medlemmarna anslutna via VNet Gateway-anslutningar. Innan en medlem kan ansluta till nätverket och se transaktions trafik, måste en befintlig medlem utföra en slutgiltig konfiguration på sin VPN-gateway för att godkänna anslutningen. Det innebär att Ethereum-noderna i den anslutna medlemmen inte körs förrän en anslutning har upprättats. Vi rekommenderar att du skapar redundanta nätverks anslutningar (nät) i konsortiet för att minska risken för en enskild felpunkt.

När den nya medlemmen har distribuerats måste den befintliga medlemmen slutföra den dubbelriktade anslutningen genom att konfigurera en VNet Gateway-anslutning till den nya medlemmen. För att uppnå detta behöver befintlig medlem:

1.  VNet Gateway-ResourceID för den anslutna medlemmen (se distributions utdata)

2.  Den delade anslutnings nyckeln

Den befintliga medlemmen måste köra följande PowerShell-skript för att slutföra anslutningen. Vi rekommenderar att du använder Azure Cloud Shell som finns i det övre högra navigerings fältet i portalen.

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

### <a name="service-monitoring"></a>Tjänstövervakning

Du kan leta upp din Azure Monitor Portal genom att följa länken i e-postadressen för distribution eller genom att leta reda på parametern i distributionen av utdata \[OMS\_PORTAL\_URL\].

Portalen visar först nätverks statistik och node-översikt på hög nivå.

![övervaka kategorier](./media/ethereum-poa-deployment/monitor-categories.png)

Genom att välja **noden översikt** kommer du till en portal för att Visa infrastruktur statistik per nod.

![nods statistik](./media/ethereum-poa-deployment/node-stats.png)

Om du väljer **nätverks** statistik kommer du att Visa Ethereum Network Statistics.

![nätverks statistik](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Bakom dessa instrument paneler är en uppsättning frågor som kan ställas till RAW. Du kan använda dessa obehandlade loggar för att anpassa instrument panelerna, undersöka felen eller aviseringar om tröskeln för konfigurationen. Nedan hittar du en uppsättning exempel frågor som kan köras i loggs öknings verktyget:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Visar en lista över block som har rapporter ATS av fler än en verifierare. Användbart för att hitta kedje delar.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Få ett genomsnittligt antal peer-datorer för en angiven validator-nod i genomsnitt över 5 minuters buckets.

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

Av säkerhets skäl nekas åtkomst till SSH-porten av en säkerhets regel för nätverks grupper som standard. Om du vill komma åt de virtuella dator instanserna i PoA-nätverket måste du ändra den här regeln till \"Tillåt\"

1.  Starta i översikts avsnittet i den distribuerade resurs gruppen från Azure Portal.

    ![Översikt över SSH](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Välj Nätverks säkerhets gruppen för den region i den virtuella dator som du vill komma åt

    ![SSH-NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Välj \"Tillåt-SSH\"-regeln

    ![SSH-Tillåt](./media/ethereum-poa-deployment/ssh-allow.png)

4.  Ändra \"åtgärd\" att tillåta

    ![Tillåt att SSH aktive ras](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  Klicka på \"Spara\" (ändringar kan ta några minuter att tillämpa)

Du kan nu fjärrans luta till de virtuella datorerna för validator-noderna via SSH med ditt tillhandahållna administratörs användar namn och lösen ord/SSH-nyckel.
SSH-kommandot som ska köras för att få åtkomst till den första validator-noden visas i Utdataparametern som, "SSH\_att\_först\_VL\_NODE\_REGION1" (för exempel distributionen: SSH-p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com). För att få till gång till ytterligare transaktions noder, ökar du Port numret med ett (till exempel den första Transaction-noden på port 4000).

Om du har distribuerat till fler än en region ändrar du kommandot ovan till DNS-namnet eller IP-adressen för belastningsutjämnaren i den regionen. Om du vill hitta DNS-namn eller IP-adress för de andra regionerna letar du reda på resursen med namngivnings konventionen \*\*\*\*\*-lbpip-REG\#, och visar dess egenskaper för DNS-namn och IP-adress.

### <a name="azure-traffic-manager-load-balancing"></a>Belastnings utjämning i Azure Traffic Manager

Azure Traffic Manager kan hjälpa till att minska stillestånds tiden och förbättra svars tiden för PoA-nätverket genom att dirigera inkommande trafik över flera distributioner i olika regioner. Inbyggda hälso kontroller och automatisk omdirigering säkerställer hög tillgänglighet för RPC-slutpunkterna och styrnings DApp. Den här funktionen är användbar om du har distribuerat till flera regioner och är redo för produktion.

Med Traffic Manager kan du:

-   Förbättra PoA nätverks tillgänglighet med automatisk redundans.

-   Öka nätverkets svars tid genom att dirigera slutanvändare till Azure-platsen med lägsta nätverks fördröjning.

Om du väljer att skapa en Traffic Manager-profil kan du använda DNS-namnet på profilen för att komma åt nätverket. När andra medlemmar i konsortiet har lagts till i nätverket kan Traffic Manager också användas för att belastningsutjämna över sina distribuerade verifierare.

#### <a name="creating-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Sök efter och välj \"Traffic Manager profil\" när du har klickat på knappen \"skapa en resurs\" i Azure Portal.

![Sök efter Azure Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

Ge profilen ett unikt namn och välj den resurs grupp som skapades under distributionen av PoA. Klicka på knappen "skapa" för att distribuera.

![Skapa Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-create.png)

När den har distribuerats väljer du instansen i resurs gruppen. DNS-namnet som du kan använda för att komma åt Traffic Manager finns på fliken Översikt

![Hitta Traffic Manager DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Välj fliken slut punkter och klicka på knappen Lägg till. Ge slut punkten ett unikt namn. Ändra mål resurs typ till offentlig IP-adress. Välj sedan den offentliga IP-adressen för den första regionen\'s Load Balancer.

![Routning Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Upprepa för varje region i det distribuerade nätverket. När slut punkterna har \"aktive rad\" status läses de in automatiskt och regions balansen sker i Traffic Managers DNS-namn. Du kan nu använda DNS-namnet i stället för \[konsortiet\_DATA\_URL\] parameter i andra steg i dokumentet.

### <a name="data-api"></a>Data-API

Varje konsortiums medlem är värd för den information som behövs för att andra ska kunna ansluta till nätverket. Den befintliga medlemmen tillhandahåller [CONSORTIUM_DATA_URL] innan medlemmens distribution. Vid distributionen hämtar en medlem som ansluter information från JSON-gränssnittet vid följande slut punkt:

`<CONSORTIUM_DATA_URL>/networkinfo`

Svaret innehåller information som är användbar för att ansluta till medlemmar (Genesis-block, validator set kontrakts-ABI, bootnodes) och information som är användbar för den befintliga medlemmen (verifierare adresser). Vi rekommenderar att du använder denna standardisering för att utöka konsortiet över moln leverantörer. Detta API kommer att returnera ett JSON-formaterat svar med följande struktur:
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

### <a name="programmatically-interacting-with-a-smart-contract"></a>Interagera med ett smart kontrakt program mässigt

> [!WARNING]
> Skicka aldrig din privata Ethereum-nyckel över nätverket! Se till att varje transaktion signeras lokalt först och att den signerade transaktionen skickas över nätverket.

I följande exempel använder vi *ethereumjs-plån boks* tjänsten för att generera en Ethereum-adress, *ethereumjs-TX* för att signera lokalt och *Web3* för att skicka den obehandlade transaktionen till Ethereum RPC-slutpunkten.

Vi ska använda det här enkla Hello – världs omsamhället i det här exemplet:

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

I det här exemplet förutsätts att kontraktet redan har distribuerats. Du kan använda *solc* och *Web3* för att distribuera ett kontrakt program mässigt. Installera först följande Node-moduler:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Detta nodeJS-skript kommer att utföra följande:

-   Skapa en rå transaktion: postMsg

-   Signera transaktionen med den genererade privata nyckeln

-   Skicka den signerade transaktionen till Ethereum-nätverket

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

### <a name="deploy-smart-contract-with-truffle"></a>Distribuera Smart kontrakt med Truffle

-   Installera nödvändiga bibliotek

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   I Truffle. js lägger du till följande kod för att låsa upp ditt MetaMask-konto och konfigurera PoA-noden som start punkt genom att ange den ledande frasen (MetaMask/Settings/Visa Dirigerings ord)

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

-   Distribuera till PoA-nätverk

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Felsöka Smart kontrakt med Truffle

Truffle har ett lokalt utvecklar nätverk som är tillgängligt för fel sökning av Smart kontrakt. Du hittar den fullständiga självstudien [här](https://truffleframework.com/tutorials/debugging-a-smart-contract).

### <a name="webassembly-wasm-support"></a>WASM-stöd (WebAssembly)

WebAssembly support har redan Aktiver ATS för dig i nyligen distribuerade PoA-nätverk. Det gör det möjligt att utveckla Smart-kontrakt på alla språk som instaplar i en webb sammansättning (Rust, C++C). Se länkarna nedan om du vill ha mer information

-   Paritets översikt över WebAssembly – <https://wiki.parity.io/WebAssembly-Home>

-   Självstudie från paritet Tech – <https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>Referens

### <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Jag märker att det finns många transaktioner i nätverket som jag överensstämde\'t Send. Var kommer de att komma från?

Det är inte säkert att låsa upp det [personliga API: et](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). Robotar lyssnar efter olåsta Ethereum-konton och försöker tömma penning medlen. Roboten förutsätter att dessa konton innehåller verklig och försöker vara det första för att Siphon saldot. Aktivera inte det personliga API: t i nätverket. I stället försignera transaktionerna manuellt med en plån boks gilla som MetaMask eller program mässigt enligt beskrivningen i avsnittet [program mässigt interagerar med ett smart kontrakt](#programmatically-interacting-with-a-smart-contract).

#### <a name="how-to-ssh-onto-a-vm"></a>Hur ska SSH på en virtuell dator?

SSH-porten exponeras inte av säkerhets skäl. Följ [den här guiden för att aktivera SSH-porten](#ssh-access).

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Hur gör jag för att skapar du en gransknings medlems-eller Transaction-noder?

Transaction Nodes är en uppsättning paritets klienter som peer-kopplas till nätverket men inte deltar i samförstånd. Dessa noder kan fortfarande användas för att skicka Ethereum-transaktioner och läsa status för smart kontrakt.
Detta fungerar bra som en mekanism för att tillhandahålla granskning av medlemmar som inte tillhör en instans i nätverket. För att åstadkomma detta följer du bara steg 2 från att utveckla konsortiet.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Varför tar MetaMask-transaktioner längre tid?

För att säkerställa att transaktioner tas emot i rätt ordning, levereras varje Ethereum-transaktion med en stegvis ökning av nonce. Om du har använt ett konto i MetaMask i ett annat nätverk måste du återställa värdet för nonce. Klicka på inställnings ikonen (3 fält), inställningar, Återställ konto. Transaktions historiken tas bort och nu kan du skicka transaktionen igen.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Måste jag ange en gas avgift i MetaMask?

Eter är inte ett syfte i proof-of-Authority-konsortiet. Därför behöver du inte ange någon avgift för naturgas när du skickar transaktioner i MetaMask.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Vad ska jag göra om min distribution Miss lyckas på grund av att det inte går att etablera Azure OMS?

Övervakning är en valfri funktion. I vissa sällsynta fall där distributionen Miss lyckas på grund av att det inte går att etablera Azure Monitor resurs kan du distribuera om utan Azure Monitor.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Är offentliga IP-distributioner kompatibla med privata nätverks distributioner?

Nej, peering kräver tvåvägs kommunikation så att hela nätverket antingen måste vara offentligt eller privat.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Vad är det förväntade transaktions flödet för proof-of-Authority?

Transaktions data flödet är mycket beroende av typerna av transaktioner och nätverk sto pol Ogin.  Med enkla transaktioner har vi förvaltat ett genomsnitt på 400 transaktioner per sekund med ett nätverk som distribuerats över flera regioner.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Hur gör jag för att prenumerera på smarta kontrakt händelser?

Ethereum proof-of-Authority stöder nu webb-Sockets.  Kontrol lera e-postadressen för distributionen eller distributionen för att hitta webbsocket-URL: en och porten.

## <a name="next-steps"></a>Nästa steg

Kom igång genom att använda Ethereum-lösningen för [proof-of-Authority](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) .
