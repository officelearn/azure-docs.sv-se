---
title: Ledger-konsortiet i Azure Kubernetes service (AKS)
description: Så här distribuerar och konfigurerar du nätverk för huvud konto för infrastruktur resurser i Azure Kubernetes service
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476448"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Ledger-konsortiet i Azure Kubernetes service (AKS)

Du kan använda HLF (Ledger) i Azure Kubernetes service (AKS)-mallen för att distribuera och konfigurera ett huvud nätverk för ett huvud nätverk i Azure.

När du har läst den här artikeln, kommer du att:

- Få kunskaper om huvud infrastruktur resurser och de olika komponenterna som utgör Bygg stenarna i blockchain-nätverket i huvud boken.
- Lär dig hur du distribuerar och konfigurerar ett Ledger-konsortium i Azure Kubernetes service för dina produktions scenarier.

## <a name="hyperledger-fabric-consortium-architecture"></a>Arkitektur för mikroredovisningens Fabric-konsortiet

Om du vill bygga ett huvud företags nätverk på Azure måste du distribuera beställnings tjänsten och organisationen med peer-noder. De olika grundläggande komponenterna som skapas som en del av mall distributionen är:

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

- **Azure Key Vault**: en Key Vault-instans distribueras för att spara autentiseringsuppgifterna för Fabric-certifikat utfärdare och de rot certifikat som tillhandahålls av kunden, vilket används vid omförsök för mall distribution, det här är för att hantera Mechanics för mallen.
- **Azure-hanterad disk**: Azure Managed disk är till för beständigt lagrings utrymme för Ledger-och peer Node-tillstånds databas.
- **Offentlig IP**: en offentlig IP-slutpunkt för det AKS-kluster som har distribuerats för samverkan med klustret.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Nätverks konfiguration för blockchain i huvud boken

För att börja måste du ha en Azure-prenumeration som har stöd för distribution av flera virtuella datorer och standard lagrings konton. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

Konfigurera blockchain Network i ett huvud nätverk med hjälp av följande steg:

- [Distribuera ordnings-/peer-organisationen](#deploy-the-ordererpeer-organization)
- [Bygg konsortiet](#build-the-consortium)
- [Kör inbyggda HLF-åtgärder](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Distribuera ordnings-/peer-organisationen

Kom igång med distributionen av HLF-nätverks komponenter genom att navigera till [Azure Portal](https://portal.azure.com). Välj **skapa en resurs > Blockchain** > Sök efter **huvud infrastruktur resurser i Azure Kubernetes-tjänsten**.

1. Starta mallen genom att välja **skapa** . Fönstret **skapa redovisnings infrastruktur resurs i Azure Kubernetes-tjänsten** visas.

    ![Mall för redovisnings infrastruktur resurser i Azure Kubernetes service-mall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

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
    - **Certifikat**: om du vill använda dina egna rot certifikat för att initiera Fabric-certifikat utfärdaren väljer du alternativet för att ladda upp rot certifikat för certifikat utfärdare, annars skapas självsignerade certifikat med standard certifikat utfärdare.
    - **Rot certifikat**: Ladda upp rot certifikat (offentlig nyckel) med vilken Fabric-certifikatutfärdare måste initieras. Certifikat av. pem-format stöds, certifikaten bör vara giltiga i UTC-tidszonen.
    - **Privat nyckel för rot certifikat**: Ladda upp den privata nyckeln för rot certifikatet. Om du har ett. PEM-certifikat, som har både offentlig och privat nyckel sammansatt, laddar du upp det här även.


6. Välj fliken **kluster inställningar för AKS** om du vill definiera Azure Kubernetes-kluster konfigurationen som är den underliggande infrastruktur där infrastruktur resursens nätverks komponenter ska installeras.

    ![Mall för redovisnings infrastruktur resurser i Azure Kubernetes service-mall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Ange följande uppgifter:
    - **Kubernetes-kluster namn**: namnet på det AKS-kluster som skapas. Det här fältet fylls i automatiskt baserat på det resurs-prefix som du har angett, men du kan ändra om det behövs.
    - **Kubernetes-version**: den version av Kubernetes som ska distribueras i klustret. Baserat på den region som valts i fliken **grundläggande** , kan de tillgängliga versioner som stöds ändras.
    - **DNS-prefix**: Domain Name System (DNS) namn prefix för AKS-kluster. Du använder DNS för att ansluta till Kubernetes-API: et när du hanterar behållare när du har skapat klustret.
    - **Node-storlek**: Kubernetes-nodens storlek kan du välja från listan med SKU: er (VM lagerhållning Unit) i Azure. För bästa prestanda rekommenderar vi standard DS3 v2.
    - **Antal noder**: antalet Kubernetes-noder som ska distribueras i klustret. Vi rekommenderar att du behåller antalet noder minst lika med eller mer än antalet HLF-noder som anges i Fabric-inställningarna.
    - **Klient-ID för tjänstens huvud namn**: Ange klient-ID för ett befintligt huvud namn för tjänsten eller skapa ett nytt, vilket krävs för AKS-autentiseringen. Se steg för att [skapa tjänstens huvud namn](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
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

Om du vill bygga blockchain-konsortiet efter att du distribuerar beställnings tjänsten och peer-noderna, måste du utföra stegen nedan i följd. **Skapa ett nätverks** skript (byn.sh) som hjälper dig att konfigurera konsortiet, skapa en kanal och installera chaincode.

> [!NOTE]
> Byn-skriptet (Build Your Network) är enbart tillgängligt för demo-/DevTest-scenarier. För inställning av produktions klass rekommenderar vi att du använder de inbyggda API: erna för HLF.

Alla kommandon för att köra byn-skriptet kan köras via kommando rads gränssnittet i Azure bash (CLI). Du kan logga in på Azure Shell Web-versionen via ![Mall för redovisnings infrastruktur resurser i Azure Kubernetes service-mall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) alternativ i det övre högra hörnet av Azure Portal. I kommando tolken skriver du bash och retur för att växla till bash CLI.

Mer information finns i [Azure Shell](https://docs.microsoft.com/azure/cloud-shell/overview) .

![Mall för redovisnings infrastruktur resurser i Azure Kubernetes service-mall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Ladda ned byn.sh och Fabric-admin. yaml-filen.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Ange under miljövariabler på Azure CLI bash-gränssnittet**:

Ange information om kanal information och ordning för organisations information

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Ange information om peer-organisationen

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Skapa en Azure-filresurs för att dela olika offentliga certifikat mellan peer-och ordnings organisationer.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**Kommandon för kanal hantering**

Gå till ordnings organisationens AKS kluster och Issue-kommando för att skapa en ny kanal

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Kommandon för hantering av konsortier**

Kör följande kommandon i den här ordningen för att lägga till en peer-organisation i en kanal och konsortiet.

1. Gå till peer-organisationens AKS-kluster och ladda upp dess medlems tjänst (MSP) på en Azure-File Storage.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Gå till ordnings organisationens AKS-kluster och Lägg till peer-organisationen i kanal och konsortium.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Gå tillbaka till peer-organisation och utfärdande-kommando för att ansluta peer-noder i kanalen.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

På samma sätt kan du lägga till fler peer-organisationer i kanalen genom att uppdatera peer AKS-miljövariabler enligt den nödvändiga peer-organisationen och utföra steg 1 till 3.

**Chaincode hanterings kommandon**

Kör kommandot nedan för att utföra chaincode-relaterad åtgärd. De här kommandona utför alla åtgärder på en demo chaincode. Den här demo chaincode har två variabler "a" och "b". Vid instansiering av chaincode initieras "a" med 1000 och "b" initieras med 2000. Vid varje anrop av chaincode överförs 10 enheter från "a" till "b". Fråga i chaincode visar världs läget för variabeln "a".

Kör följande kommandon som körs i peer-organisationens AKS-kluster.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Chaincode åtgärds kommandon**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Kör inbyggda HLF-åtgärder

För att hjälpa våra kunder att komma igång med att köra inbyggda inbyggda kommandon i HLF Network på AKS. Exempel programmet tillhandahålls som använder Fabric NodeJS SDK för att utföra HLF-åtgärder. Kommandona har angetts för att skapa en ny användar identitet och installera din egen chaincode.

### <a name="before-you-begin"></a>Innan du börjar

Följ kommandona nedan för den första installationen av programmet:

- Hämta programfiler
- Skapa anslutnings profil och administratörs profil
- Importera administratörens användar identitet

När du har slutfört den inledande installationen kan du använda SDK: n för att uppnå följande åtgärder:

- Generering av användar identitet
- Chaincode-åtgärder

De kommandon som nämns ovan kan köras från Azure Cloud Shell.

### <a name="download-application-files"></a>Hämta programfiler

Den första inställningen för att köra program är att ladda ned alla programfiler i en mapp.

**Skapa app-mapp och ange i mappen**:

```bash
mkdir app
cd app
```
Kör följande kommando för att ladda ned alla filer och paket som krävs:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Det tar tid att läsa in alla paket med det här kommandot. När kommandot har körts kan du se en `node_modules` mapp i den aktuella katalogen. Alla nödvändiga paket läses in i `node_modules` mappen.

### <a name="generate-connection-profile-and-admin-profile"></a>Skapa anslutnings profil och administratörs profil

Skapa `profile` en katalog i `app` mappen

```bash
cd app
mkdir ./profile
```
Ange de här miljövariablerna i Azure Cloud Shell

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Kör följande kommando för att skapa anslutnings profilen och administratörs profilen för organisationen

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Den skapar anslutnings profilen och administratören `profile` för organisationen i mappen profil med namn respektive `<orgname>-ccp.json` `<orgname>-admin.json` namn.

På samma sätt genererar du anslutnings profil och administratörs profil för varje beställare och peer-organisation.


### <a name="import-admin-user-identity"></a>Importera administratörens användar identitet

Det sista steget är att importera organisationens administratörs användar identitet i plån boken.

```bash
npm run importAdmin -- -o <orgName>

```
Kommandot ovan kör importAdmin. js för att importera administratörs användar identiteten till plån boken. Skriptet läser administratörs identiteten från administratörs `<orgname>-admin.json` profilen och importerar den i plån boken för att köra HLF-åtgärder.

Skripten använder fil systemets plån bok för att lagra identiteterna. Den skapar en plån bok enligt den sökväg som anges i fältet. plån boks fält i anslutnings profilen. Som standard initieras ". plån boks fältet med `<orgname>`, vilket innebär att en mapp med namnet `<orgname>` skapas i den aktuella katalogen för att lagra identiteterna. Om du vill skapa en plån bok på någon annan sökväg ändrar du fältet ". plån bok" i anslutnings profilen innan du kör registrera administratörs användare och andra HLF åtgärder.

Importera administratörs användar identitet på samma sätt för varje organisation.

Mer information om argumenten som skickas i kommandot finns i kommando hjälpen.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Generering av användar identitet

Kör följande kommandon i den här ordningen för att generera nya användar identiteter för HLF-organisationen.

> [!NOTE]
> Innan du börjar med stegen för generering av användar identitet måste du kontrol lera att den ursprungliga installationen av programmet är färdig.

Ange under miljövariabler i Azure Cloud Shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Registrera och Registrera ny användare

Kör kommandot nedan för att registrera och registrera en ny användare som kör registerUser. js. Den sparar den genererade användar identiteten i plån boken.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> Administratörens användar identitet används för att utfärda registrerings kommandot för den nya användaren. Därför är det nödvändigt att ha användar identiteten admin i plån boken innan du kör det här kommandot. Annars fungerar inte det här kommandot.

Mer information om argumenten som skickas i kommandot finns i kommando hjälpen

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Chaincode-åtgärder


> [!NOTE]
> Innan du börjar med en chaincode-åtgärd bör du kontrol lera att den ursprungliga installationen av programmet är färdig.

Ange nedan chaincode-miljövariabler i Azure Cloud Shell:

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

Nedanstående chaincode-åtgärder kan utföras:

- Installera chaincode
- Instansiera chaincode
- Anropa chaincode
- Fråga chaincode

### <a name="install-chaincode"></a>Installera chaincode

Kör kommandot nedan för att installera chaincode i peer-organisationen.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Den kommer att installera chaincode på alla peer-noder i organisationen som anges `ORGNAME` i miljövariabeln. Om det finns två eller flera peer-organisationer i din kanal och du vill installera chaincode på alla, kör du kommandona separat för varje peer-organisation.

Följ stegen:

- Ange `ORGNAME` till `<peerOrg1Name>` och utfärda `installCC` kommando.
- Ange `ORGNAME` till `<peerOrg2Name>` och utfärda `installCC` kommando.

  Kör den för varje peer-organisation.

Mer information om argumenten som skickas i kommandot finns i kommando hjälpen.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Instansiera chaincode

Kör kommandot nedan för att instansiera chaincode på peer-datorn.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Skicka Instansierings funktions namn och kommaavgränsad lista med argument i `<instantiateFunc>` `<instantiateFuncArgs>` respektive. Till exempel, i [fabrcar-chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go), för att instansiera chaincode `<instantiateFunc>` inställt på `"Init"` och `<instantiateFuncArgs>` till en tom sträng `""`.

> [!NOTE]
> Kör kommandot för en gång från en peer-organisation i kanalen.
> När transaktionen har skickats till ordern distribuerar beställaren transaktionen till alla peer-organisationer i kanalen. Därför instansieras chaincode på alla peer-noder på alla peer-organisationer i kanalen.

Mer information om argumenten som skickas i kommandot finns i kommando hjälpen

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Anropa chaincode

Kör kommandot nedan för att anropa funktionen chaincode:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Skicka anrops funktions namn och kommaavgränsad lista med argument i `<invokeFunction>` `<invokeFuncArgs>` respektive. Du fortsätter med exemplet fabcar chaincode för att anropa initLedger-funktionen `<invokeFunction>` inställd `"initLedger"` på `<invokeFuncArgs>` och `""`till.

> [!NOTE]
> Kör kommandot för en gång från en peer-organisation i kanalen.
> När transaktionen har skickats till ordern distribuerar beställaren transaktionen till alla peer-organisationer i kanalen. Därför uppdateras världs läget på alla peer-noder i alla peer-organisationer i kanalen.

Mer information om argumenten som skickas i kommandot finns i kommando hjälpen

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Fråga chaincode

Kör följande kommando för att fråga chaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Skicka fråge funktions namn och kommaavgränsad lista med argument i `<queryFunction>` `<queryFuncArgs>` respektive. Återigen, med `fabcar` chaincode som referens, för att fråga alla bilar i världs delstaten `<queryFunction>` inställt på `"queryAllCars"` och `<queryArgs>` till `""`.

Mer information om argumenten som skickas i kommandot finns i kommando hjälpen

```bash
npm run queryCC -- -h

```
