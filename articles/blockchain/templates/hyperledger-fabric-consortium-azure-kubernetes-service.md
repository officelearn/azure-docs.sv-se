---
title: Ledger-konsortiet i Azure Kubernetes service (AKS)
description: Så här distribuerar och konfigurerar du nätverk för huvud konto för infrastruktur resurser i Azure Kubernetes service
ms.date: 07/27/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 4bc55090234a4ab33125ba43b8416de1eadb702f
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533435"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Ledger-konsortiet i Azure Kubernetes service (AKS)

Du kan använda HLF (Ledger) i Azure Kubernetes service (AKS)-mallen för att distribuera och konfigurera ett huvud nätverk för ett huvud nätverk i Azure.

När du har läst den här artikeln, kommer du att:

- Få kunskaper om huvud infrastruktur resurser och de olika komponenterna som utgör Bygg stenarna i blockchain-nätverket i huvud boken.
- Lär dig hur du distribuerar och konfigurerar ett Ledger-konsortium i Azure Kubernetes service för dina produktions scenarier.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Välj en Azure blockchain-lösning

Innan du väljer att använda en lösnings mall bör du jämföra scenariot med vanliga användnings fall av tillgängliga alternativ för Azure-blockchain.

Alternativ | Tjänst modell | Vanligt användnings fall
-------|---------------|-----------------
Lösningsmallar | IaaS | Solution templates är Azure Resource Manager mallar som du kan använda för att etablera en helt konfigurerad blockchain-nätverkstopologi. Mallarna distribuerar och konfigurerar Microsoft Azure beräknings-, nätverks-och lagrings tjänster för en specifik nätverks typ för blockchain. Solution templates tillhandahålls utan något service nivå avtal. Använd [sidan Microsoft Q&en fråga](/answers/topics/azure-blockchain-workbench.html) för support.
[Azure Blockchain Service](../service/overview.md) | PaaS | För hands versionen av Azure blockchain service fören klar investeringen, hanteringen och styrningen av konsortiet blockchain-nätverk. Använd Azure blockchain-tjänsten för lösningar som kräver PaaS, konsortiets hantering eller kontrakt och transaktions sekretess.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS och PaaS | Azure blockchain Workbench Preview är en samling Azure-tjänster och-funktioner som är utformade för att hjälpa dig att skapa och distribuera blockchain-program för att dela affärs processer och data med andra organisationer. Använd Azure blockchain Workbench för prototyp av en blockchain-lösning eller ett koncept för blockchain-program. Azure Blockchain Workbench tillhandahålls utan serviceavtal. Använd [sidan Microsoft Q&en fråga](/answers/topics/azure-blockchain-workbench.html) för support.

## <a name="hyperledger-fabric-consortium-architecture"></a>Arkitektur för mikroredovisningens Fabric-konsortiet

Om du vill bygga ett huvud företags nätverk på Azure måste du distribuera beställnings tjänsten och organisationen med peer-noder. Med hjälp av den här mallen för huvud lösningar för Kubernetes i Azure kan du skapa databasnoder eller peer-noder. Du måste distribuera mallen för varje nod som du vill skapa.

De olika grundläggande komponenterna som skapas som en del av mall distributionen är:

- **Order, noder**: en nod som ansvarar för transaktions sortering i redovisningen. Tillsammans med andra noder utgör de beställda noderna beställnings tjänsten för det högliggande Fabric-nätverket.

- **Peer-noder**: en nod som främst är värd för redovisning och smarta kontrakt, dessa grundläggande delar av nätverket.

- **Infrastruktur certifikat utfärdare**: Fabric ca är certifikat utfärdare (ca) för redovisnings infrastruktur resurser. Med infrastruktur certifikat utfärdaren kan du initiera och starta Server process som är värd för certifikat utfärdaren. Med den kan du hantera identiteter och certifikat. Varje AKS-kluster som distribueras som en del av mallen kommer att ha en POD för Fabric-certifikat som standard.

- **Couchdb eller LevelDB**: världs tillstånds databas för peer-noder kan lagras antingen i LevelDB eller couchdb. LevelDB är standard läges databasen inbäddad i peer-noden och lagrar chaincode-data som enkla nyckel/värde-par och stöder nyckel-, nyckel intervall och sammansatta nyckel frågor. CouchDB är en valfri alternativ tillstånds databas som stöder omfattande frågor när chaincode-datavärden modelleras som JSON.

Mallen för distributioner snurrar upp olika Azure-resurser i din prenumeration. De olika Azure-resurser som distribueras är:

- **AKS-kluster**: Azure Kubernetes-kluster som har kon figurer ATS enligt de indataparametrar som angetts av kunden. AKS-klustret har olika poddar som har kon figurer ATS för att köra de olika nätverks komponenterna i huvud boks infrastrukturen. De olika poddar som skapats är:

  - **Infrastruktur verktyg**: Fabric-verktyget ansvarar för konfigurering av de båda komponenterna i huvud boken.
  - **Ordnings-/peer-poddar**: noderna i HLF-nätverket.
  - **Proxy**: en ngnix proxy-Pod genom vilken klient programmen kan gränssnitts med AKS-klustret.
  - **Infrastruktur certifikat utfärdare**: Pod som kör Fabric ca: n.
- **Postgresql**: en instans av postgresql distribueras för att underhålla Fabric-certifikatens ca-identiteter.

- **Azure Key Vault**: en Key Vault-instans distribueras för att spara autentiseringsuppgifterna för Fabric-certifikat utfärdare och de rot certifikat som tillhandahålls av kunden, vilket används vid omförsök för mal Lav drift för att hantera Mechanics för mallen.
- **Azure-hanterad disk**: Azure Managed disk är till för beständigt lagrings utrymme för Ledger-och peer Node-tillstånds databas.
- **Offentlig IP**: en offentlig IP-slutpunkt för det AKS-kluster som har distribuerats för samverkan med klustret.

## <a name="deploy-the-ordererpeer-organization"></a>Distribuera ordnings-/peer-organisationen

För att börja måste du ha en Azure-prenumeration som har stöd för distribution av flera virtuella datorer och standard lagrings konton. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

Kom igång med distributionen av HLF-nätverks komponenter genom att navigera till [Azure Portal](https://portal.azure.com).

1. Välj **skapa en resurs > Blockchain** > söka efter **Ledger Fabric på Azure Kubernetes service (för hands version)**.

2. Ange projekt informationen på sidan **grundläggande** information.

    ![Mall för redovisnings infrastruktur resurser i Azure Kubernetes service-mall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Ange följande uppgifter:
    - **Prenumeration**: Välj namnet på den prenumeration där du vill distribuera HLF-nätverks komponenterna.
    - **Resurs grupp**: skapa en ny resurs grupp eller Välj en befintlig tom resurs grupp, resurs gruppen innehåller alla resurser som distribueras som en del av mallen.
    - **Region**: Välj den Azure-region där du vill distribuera Azure Kubernetes-klustret för HLF-komponenterna. Mallen är tillgänglig i alla regioner där AKS är tillgänglig se till att välja en region där din prenumeration inte når kvot gränsen för den virtuella datorn (VM).
    - **Resource prefix**: prefix för namngivning av resurser som har distribuerats. Resource prefix måste vara kortare än sex tecken och kombinationen av tecken måste innehålla både siffror och bokstäver.
4. Välj fliken **infrastruktur inställningar** om du vill definiera de HLF nätverks komponenter som ska distribueras.

    ![Mall för redovisnings infrastruktur resurser i Azure Kubernetes service-mall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Ange följande uppgifter:
    - **Organisations namn**: namnet på Fabric-organisationen, vilket krävs för olika data Plans åtgärder. Organisations namnet måste vara unikt för varje distribution.
    - **Nätverks komponent för infrastruktur resurser**: Välj antingen beställnings tjänst eller peer-noder baserat på blockchain nätverks komponent som du vill konfigurera.
    - **Antal noder** – följande är två typer av noder:
        - Beställ tjänst – Välj antalet noder för att tillhandahålla fel tolerans till nätverket. Endast 3, 5 och 7 är antalet noder som stöds för order.
        - Peer-noder – du kan välja 1-10-noder baserat på ditt krav.
    - **Peer-nodens världs tillstånds databas**: Välj mellan LevelDB och CoucbDB. Det här fältet visas när användaren väljer noden peer-nod i nätverks komponenten för infrastruktur resursen.
    - **Användar namn för infrastruktur resurs**: Ange det användar namn som används för Fabric ca-autentisering.
    - **Lösen ord för infrastruktur certifikat utfärdare**: Ange lösen ordet för Fabric-ca-autentisering.
    - **Bekräfta lösen ord**: bekräfta lösen ordet för infrastruktur certifikat utfärdaren.
    - **Certifikat**: om du vill använda dina egna rot certifikat för att INITIERA Fabric ca: n väljer du alternativet för att ladda upp rot certifikat för certifikat utfärdare, annars skapas självsignerade certifikat med standard certifikat utfärdare.
    - **Rot certifikat**: Ladda upp rot certifikat (offentlig nyckel) med vilken Fabric-certifikatutfärdare måste initieras. Certifikat av. pem-format stöds, certifikaten bör vara giltiga i UTC-tidszonen.
    - **Privat nyckel för rot certifikat**: Ladda upp den privata nyckeln för rot certifikatet. Om du har ett. PEM-certifikat, som har både offentlig och privat nyckel sammansatt, laddar du upp det här även.


6. Välj fliken **kluster inställningar för AKS** om du vill definiera den underliggande infrastruktur som är den underliggande infrastruktur där infrastruktur resursens nätverks komponenter ska konfigureras.

    ![Mall för redovisnings infrastruktur resurser i Azure Kubernetes service-mall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Ange följande uppgifter:
    - **Kubernetes-kluster namn**: namnet på det AKS-kluster som skapas. Det här fältet fylls i automatiskt baserat på det resurs-prefix som du har angett, men du kan ändra om det behövs.
    - **Kubernetes-version**: den version av Kubernetes som ska distribueras i klustret. Baserat på den region som valts i fliken **grundläggande** , kan de tillgängliga versioner som stöds ändras.
    - **DNS-prefix**: Domain Name System (DNS) namn prefix för AKS-kluster. Du använder DNS för att ansluta till Kubernetes-API: et när du hanterar behållare när du har skapat klustret.
    - **Node-storlek**: Kubernetes-nodens storlek kan du välja från listan med SKU: er (VM lagerhållning Unit) i Azure. För bästa prestanda rekommenderar vi standard DS3 v2.
    - **Antal noder**: antalet Kubernetes-noder som ska distribueras i klustret. Vi rekommenderar att du behåller antalet noder minst lika med eller mer än antalet HLF-noder som anges i Fabric-inställningarna.
    - **Klient-ID för tjänstens huvud namn**: Ange klient-ID för ett befintligt huvud namn för tjänsten eller skapa ett nytt, vilket krävs för AKS-autentiseringen. Se steg för att [skapa tjänstens huvud namn](/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Tjänstens huvud namn**: Ange klient hemligheten för tjänstens huvud namn som anges i klient-ID: t för tjänstens huvud namn.
    - **Bekräfta klient hemligheten**: bekräfta den klient hemlighet som tillhandahålls i tjänstens huvud namn klient hemlighet.
    - **Aktivera övervakning av behållare**: Välj att aktivera övervakning av AKS, vilket gör att AKS-loggarna kan push-överföras till den Log Analytics arbets ytan som angetts.
    - **Log Analytics arbets yta**: Log Analytics-arbetsyta fylls med standard arbets ytan som skapas om övervakning har Aktiver ATS.

8. När du har angett all information ovan väljer du **Granska och skapa** fliken. Granska och skapa utlöser verifieringen för de värden du har angett.
9. När verifieringen har passerat kan du välja **skapa**.
Distributionen tar vanligt vis 10-12 minuter, kan variera beroende på storleken på och antalet AKS-noder som anges.
10. När distributionen har slutförts meddelas du via Azure-meddelanden i det övre högra hörnet.
11. Välj **gå till resurs grupp** för att kontrol lera alla resurser som skapats som en del av mall distributionen. Alla resurs namn kommer att börja med det prefix som anges i inställningen **grundläggande** .

## <a name="build-the-consortium"></a>Bygg konsortiet

Om du vill bygga blockchain-konsortiet efter att du distribuerar beställnings tjänsten och peer-noderna, måste du utföra stegen nedan i följd. Azure HLF-skript (azhlf), som hjälper dig att konfigurera konsortiet, skapa kanal-och chaincode-åtgärder.

> [!NOTE]
> Det finns en uppdatering i skriptet, den här uppdateringen är till för att ge fler funktioner med Azure HLF-skript. Om du vill referera till det gamla skriptet, [Se här](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Det här skriptet är kompatibelt med huvud strukturen i Azure Kubernetes Service Template version 2.0.0 och senare. Följ anvisningarna i [fel sökning](#troubleshoot)för att kontrol lera distributions versionen.

> [!NOTE]
> Azure HLF (azhlf)-skript som tillhandahålls är att hjälpa till med demo-och DevTest scenarier. Kanalen och konsortiet som skapats av det här skriptet har grundläggande HLF-principer för att förenkla demo-/DevTest-scenariot. För produktions konfigurationen rekommenderar vi att du uppdaterar HLF-principer för Channel/Consortium i enlighet med organisationens krav på efterlevnad med hjälp av interna HLF-API: er.


Alla kommandon för att köra Azure HLF-skriptet kan köras via Azure bash-kommandoraden. Gränssnitt (CLI). Du kan logga in på Azure Shell Web-versionen via   ![ huvud strukturen för Azure Kubernetes i ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) det övre högra hörnet av Azure Portal. I kommando tolken skriver du bash och anger för att växla till bash CLI eller väljer *bash* från verktygsfältet Shell.

Mer information finns i [Azure Shell](../../cloud-shell/overview.md) .

![Mall för redovisnings infrastruktur resurser i Azure Kubernetes service-mall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Följande bild visar steg för steg-processen för att bygga konsortiet mellan en ordnings organisation och en peer-organisation. Detaljerade kommandon för att utföra de här stegen samlas in i följande avsnitt.

![Mall för redovisnings infrastruktur resurser i Azure Kubernetes service-mall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Slutför avsnitten för den första installationen av klient programmet: 

1. Hämta filer för klient program
1. Konfigurera miljövariabler
1. Importera organisations anslutnings profil, administratörs användare och MSP

När du har slutfört den inledande installationen använder du klient programmet för att uppnå följande åtgärder:  

- Kanal hantering
- Hantering av konsortiet
- Hantering av Chaincode

### <a name="download-client-application-files"></a>Hämta filer för klient program

Den första installationen är att ladda ned klient program filerna. Kör följande kommando för att ladda ned alla filer och paket som krävs:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

De här kommandona kommer att klona klient program koden för Azure HLF från offentliga GitHub-lagrings platsen och genom att läsa in alla beroende NPM-paket. När kommandot har körts kan du se en node_modules-mapp i den aktuella katalogen. Alla nödvändiga paket läses in i mappen node_modules.

### <a name="setup-environment-variables"></a>Konfigurera miljövariabler

> [!NOTE]
> Alla miljövariabler följer namngivnings konventionen för Azure-resurser.

#### <a name="set-environment-variables-for-orderer-organization-client"></a>Ange miljövariabler för beställnings organisations klient

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-the-environment-variables-for-peer-organization-client"></a>Ange miljövariabler för peer-organisationens klient

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> Baserat på antalet peer-organisationer i konsortiet kan du behöva upprepa peer-kommandona och ställa in miljövariabeln på motsvarande sätt.

#### <a name="set-the-environment-variables-for-setting-up-azure-storage-account"></a>Ange miljövariabler för att ställa in Azure Storage konto

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Använd följande steg för att skapa Azure Storage-konto. Om du redan har skapat Azure Storage konto kan du hoppa över de här stegen.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Använd följande steg för att skapa en fil resurs i Azure Storage konto. Hoppa över de här stegen om du redan har skapat en fil resurs

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Använd följande steg för att skapa en anslutnings sträng för Azure-filresursen.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>Importera organisations anslutnings profil, administratörs användarens identitet och MSP

Problem nedan visar hur du hämtar organisationens anslutnings profil, administratörs identitet och MSP från Azure Kubernetes-kluster och lagrar dessa identiteter i katalogen för lokala klient program, t. ex. i katalogen "azhlfTool/Store".

För ordnings organisation:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

För peer-organisation:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-channel-command"></a>Kommandot Skapa kanal

Från ordnings organisationens klient, kommandot utfärdande för att skapa en ny kanal. Med det här kommandot skapas en kanal med endast ordnings organisation i den.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="consortium-management-commands"></a>Kommandon för hantering av konsortier

>[!NOTE]
> Innan du börjar med en konsortiums åtgärd bör du kontrol lera att den första installationen av klient programmet är färdig.  

Kör följande kommandon i angiven ordning för att lägga till en peer-organisation i en kanal och konsortiet
1.  Från peer-organisationens klient, ladda upp peer-organisationens MSP på Azure Storage

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Från ordnings organisations klienten laddar du ned peer-organisationens MSP från Azure Storage och utfärdar sedan kommandot för att lägga till peer-organisation i kanal/konsortium.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Från ordnings organisationens klient, ladda upp anslutnings profil för beställare på Azure Storage så att peer-organisationen kan ansluta till beställnings noder med den här anslutnings profilen

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Från peer-organisationens klient, hämta anslutnings profil för beställare från Azure Storage och sedan utfärda kommando för att lägga till peer-noder i kanalen

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

På samma sätt kan du lägga till fler peer-organisationer i kanalen genom att uppdatera variablerna för peer-miljön enligt den nödvändiga peer-organisationen och utföra steg 1 till 4.

### <a name="set-anchor-peers-command"></a>Kommandot Set Anchor peer (s)

Från peer-organisationens klient skickar du kommandot för att ställa in Anchor-peer (a) för peer-organisationen på den angivna kanalen.

>[!NOTE]
> Innan du kör det här kommandot bör du se till att peer-organisationen läggs till i kanalen med hjälp av konsortiet hanterings kommandon.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>`är en blankstegsavgränsad lista med peer-noder som ska anges som en Anchor-peer. Exempel:

  - Ange `<anchorPeersList>` som "peer1" om du bara vill ange peer1 Node som Anchor-peer.
  - Ange `<anchorPeersList>` som "peer1" "peer3" om du vill ställa in både peer1-och peer3-noden som Anchor-peer.

## <a name="chaincode-management-commands"></a>Chaincode hanterings kommandon

>[!NOTE]
> Innan du börjar med en chaincode-åtgärd kontrollerar du att den första installationen av klient programmet är färdig.  

### <a name="set-the-below-chaincode-specific-environment-variables"></a>Ange de nedan chaincode-företagsspecifika miljövariablerna

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Installera chaincode  

Kör kommandot nedan för att installera chaincode i peer-organisationen.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Den kommer att installera chaincode på alla peer-noder i peer-organisationen som anges i ORGNAMN-miljövariabeln. Om det finns två eller flera peer-organisationer i din kanal och du vill installera chaincode på alla, kör du kommandot separat för varje peer-organisation.  

Följ stegen:  

1.  Set `ORGNAME` och `USER_IDENTITY` as per peerOrg1 och Issue- `./azhlf chaincode install` kommando.  
2.  Set `ORGNAME` och `USER_IDENTITY` as per peerOrg2 och Issue- `./azhlf chaincode install` kommando.  

### <a name="instantiate-chaincode"></a>Instansiera chaincode  

Från peer-klientprogrammet kör du kommandot nedan för att instansiera chaincode i kanalen.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```

Skicka en lista över argument för instansiering av funktions namn och blank steg `<instantiateFunc>` `<instantiateFuncArgs>` . Till exempel, i chaincode_example02. go-chaincode, för att instansiera chaincode inställt `<instantiateFunc>` på `init` och `<instantiateFuncArgs>` till "a" "2000" "b" "1000".

> [!NOTE]
> Kör kommandot för en gång från en peer-organisation i kanalen. När transaktionen har skickats till ordern distribuerar beställaren transaktionen till alla peer-organisationer i kanalen. Därför instansieras chaincode på alla peer-noder på alla peer-organisationer i kanalen.  

### <a name="invoke-chaincode"></a>Anropa chaincode  

Kör kommandot nedan från peer-organisationens klient för att anropa funktionen chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Skicka anrops funktions namn och blank stegs lista med argument i respektive  `<invokeFunction>`    `<invokeFuncArgs>`   . Om du fortsätter med chaincode_example02. go chaincode-exemplet för att utföra Invoke-åtgärden  `<invokeFunction>`   till  `invoke`   och  `<invokeFuncArgs>`   till "a" "b" "10".  

>[!NOTE]
> Kör kommandot för en gång från en peer-organisation i kanalen. När transaktionen har skickats till ordern distribuerar beställaren transaktionen till alla peer-organisationer i kanalen. Därför uppdateras världs läget på alla peer-noder i alla peer-organisationer i kanalen.  


### <a name="query-chaincode"></a>Fråga chaincode  

Kör följande kommando för att fråga chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
Skicka fråge funktions namn och blankstegsavgränsad lista med argument i respektive  `<queryFunction>`    `<queryFuncArgs>`   . Återigen, med chaincode_example02. go-chaincode som referens, för att ställa in värdet "a" i världs läget inställt  `<queryFunction>`   på  `query` och  `<queryArgs>` till "a".  

## <a name="troubleshoot"></a>Felsöka

**Så här verifierar du den mall version som körs**

Kör kommandona nedan för att hitta versionen av mall distributionen.

Ange under miljövariabler enligt resurs gruppen där mallen har distribuerats.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
Kör följande kommando för att skriva ut mall versionen
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>Support och feedback

För Azure blockchain News går du till [Azure blockchain-bloggen](https://azure.microsoft.com/blog/topics/blockchain/) för att hålla dig uppdaterad om blockchain service-erbjudanden och information från Azures teknik team för blockchain.

För att ge feedback på produkter eller för att begära nya funktioner, post eller rösta för en idé via [Azure feedback-forumet för blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Community-support

Engagera med Microsoft-tekniker och Azure blockchain community-experter.

- [Sidan Microsoft Q&en fråga](/answers/topics/azure-blockchain-workbench.html). Teknisk support för blockchain-mallar är begränsad till distributions problem.
- [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
