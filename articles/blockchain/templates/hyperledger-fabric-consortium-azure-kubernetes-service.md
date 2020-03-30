---
title: Hyperledger Fabric-konsortium på Azure Kubernetes Service (AKS)
description: Distribuera och konfigurera konsortienätverket Hyperledger Fabric på Azure Kubernetes Service
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476448"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Hyperledger Fabric-konsortium på Azure Kubernetes Service (AKS)

Du kan använda HLF (Hyperledger Fabric) på Azure Kubernetes Service (AKS) för att distribuera och konfigurera ett Hyperledger Fabric-konsortiumnätverk på Azure.

När du har läst den här artikeln, kommer du att:

- Få arbetskunskap om Hyperledger Fabric och de olika komponenter som utgör byggstenarna i Hyperledger Fabric blockchain-nätverk.
- Lär dig hur du distribuerar och konfigurerar ett Hyperledger Fabric-konsortium på Azure Kubernetes-tjänsten för dina produktionsscenarier.

## <a name="hyperledger-fabric-consortium-architecture"></a>Arkitektur för Hyperledger Fabric Consortium

Om du vill skapa Hyperledger Fabric-nätverk på Azure måste du distribuera ordertjänst och organisation med peer-noder. De olika grundläggande komponenter som skapas som en del av malldistributionen är:

- **Ordernoder**: En nod som ansvarar för transaktionsbeställning i redovisningen. Tillsammans med andra noder utgör de beställda noderna beställningstjänsten för Hyperledger Fabric-nätverket.

- **Peer-noder:** En nod som främst är värd för liggare och smarta kontrakt, dessa grundläggande element i nätverket.

- **Fabric CA**: Fabric CA är certifikatutfärdaren (CA) för Hyperledger Fabric. Med fabric-certifikatutfärdaren kan du initiera och starta serverprocess som är värd för certifikatutfärdaren. Det gör att du kan hantera identiteter och certifikat. Varje AKS-kluster som distribueras som en del av mallen har en Fabric CA-pod som standard.

- **CouchDB eller LevelDB:** World state databas för peer-noder kan lagras antingen i LevelDB eller CouchDB. LevelDB är standardtillståndsdatabasen som är inbäddad i peer-noden och lagrar kedjekoddata som enkla nyckelvärdespar och stöder endast nyckel-, nyckelintervall och sammansatta nyckelfrågor. CouchDB är en valfri alternativ tillståndsdatabas som stöder omfattande frågor när kedjekodsdatavärden modelleras som JSON.

Mallen för distribution konfigurerar olika Azure-resurser i din prenumeration. De olika Azure-resurser som distribueras är:

- **AKS-kluster**: Azure Kubernetes-kluster som är konfigurerat enligt indataparametrarna som tillhandahålls av kunden. AKS-klustret har olika poddar konfigurerade för att köra nätverkskomponenterna Hyperledger Fabric. De olika poddar som skapas är:

  - **Tygverktyg**: Tygverktyget ansvarar för att konfigurera Hyperledger Fabric-komponenterna.
  - **Orderer/peer pods**: Noderna i HLF-nätverket.
  - **Proxy**: En NGNIX proxy pod genom vilken klientprogrammen kan samverka med AKS-klustret.
  - **Tyg CA:** Pod som kör Fabric CA.
- **PostgreSQL**: En instans av PostgreSQL distribueras för att underhålla Fabric CA-identiteter.

- **Azure Key vault**: En nyckelvalvsinstans distribueras för att spara fabric-certifikatutfärdarens autentiseringsuppgifter och rotcertifikat som tillhandahålls av kunden, som används vid återförsök i malldistributionen, detta är att hantera mekaniken i mallen.
- **Azure Managed disk:** Azure Managed disk är för beständig lagring för redovisning och peer nod world state database.
- **Offentlig IP:** En offentlig IP-slutpunkt för AKS-klustret som distribueras för gränssnitt med klustret.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger Fabric Blockchain nätverksinstallation

Till att börja med behöver du en Azure-prenumeration som kan stödja distribution av flera virtuella datorer och standardlagringskonton. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

Konfigurera Hyperledger Fabric Blockchain-nätverk med följande steg:

- [Distribuera beställaren/peer-organisationen](#deploy-the-ordererpeer-organization)
- [Bygg konsortiet](#build-the-consortium)
- [Köra inbyggda HLF-åtgärder](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Distribuera beställaren/peer-organisationen

Om du vill komma igång med HLF-distributionen av nätverkskomponenter navigerar du till [Azure-portalen](https://portal.azure.com). Välj **Skapa en resurs > Blockchain** > söka efter **Hyperledger Fabric på Azure Kubernetes Service**.

1. Välj **skapa** för att starta malldistributionen. **Skapa hyperledger fabric på Azure Kubernetes Service** visar.

    ![Hyperledger Fabric på Azure Kubernetes servicemall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Ange projektinformationen på sidan **Grunderna.**

    ![Hyperledger Fabric på Azure Kubernetes servicemall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Ange följande uppgifter:
    - **Prenumeration**: Välj prenumerationsnamnet där du vill distribuera HLF-nätverkskomponenterna.
    - **Resursgrupp**: Skapa en ny resursgrupp eller välj en befintlig tom resursgrupp, resursgruppen innehåller alla resurser som distribueras som en del av mallen.
    - **Region**: Välj den Azure-region där du vill distribuera Azure Kubernetes-klustret för HLF-komponenterna. Mallen är tillgänglig i alla regioner där AKS är tillgängligt Kontrollera att du väljer en region där din prenumeration inte når kvotgränsen för virtuell dator (VM).
    - **Resursprefix**: Prefix för namngivning av resurser som distribueras. Resursprefixet måste vara mindre än sex tecken i längd och kombinationen av tecken måste innehålla både siffror och bokstäver.
4. Välj fliken **Infrastrukturinställningar** för att definiera de HLF-nätverkskomponenter som ska distribueras.

    ![Hyperledger Fabric på Azure Kubernetes servicemall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Ange följande uppgifter:
    - **Organisationsnamn**: Namnet på fabric-organisationen, som krävs för olika dataplanoperationer. Organisationsnamnet måste vara unikt per distribution. 
    - **Infrastrukturnätverkskomponent:** Välj antingen Beställningstjänst eller peer-noder baserat på Blockchain-nätverkskomponent som du vill konfigurera.
    - **Antal noder** - Följande är de två typerna av noder:
        - Beställningstjänst - välj antalet noder som tillhandahålls feltolerans till nätverket. Endast 3,5 och 7 är antalet ordernoder som stöds.
        - Peer-noder - du kan välja 1-10 noder baserat på dina krav.
    - **Peer nod världstillstånd databas:** Välj mellan LevelDB och CoucbDB. Det här fältet visas när användaren väljer peer-nod i listrutan Fabric-nätverkskomponent.
    - **Fabric användarnamn**: Ange användarnamn som används för Fabric CA-autentisering.
    - **Fabric CA-lösenord:** Ange lösenordet för Fabric CA-autentisering.
    - **Bekräfta lösenord**: Bekräfta Fabric CA-lösenordet.
    - **Certifikat**: Om du vill använda dina egna rotcertifikat för att initiera fabric-certifikatutfärdaren väljer du Ladda upp rotcertifikat för fabric-certifikatutfärdare, annars skapar fabric-certifikat som standard självsignerade certifikat.
    - **Rotcertifikat**: Ladda upp rotcertifikat (offentlig nyckel) som Fabric-certifikatutfärdaren måste initieras med. Certifikat med .pem-format stöds, certifikaten ska vara giltiga i UTC-tidszonen.
    - **Root Certificate privat nyckel:** Ladda upp den privata nyckeln till rotcertifikatet. Om du har ett .pem-certifikat, som har både offentlig och privat nyckel kombinerad, laddar du upp det här också.


6. Välj **fliken AKS-klusterinställningar** för att definiera Azure Kubernetes klusterkonfiguration som är den underliggande infrastrukturen där fabric-nätverkskomponenterna ska konfigureras.

    ![Hyperledger Fabric på Azure Kubernetes servicemall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Ange följande uppgifter:
    - **Kubernetes klusternamn**: Namnet på AKS-klustret som skapas. Det här fältet är förifolkat baserat på resursprefixet, du kan ändra om det behövs.
    - **Kubernetes version**: Den version av Kubernetes som kommer att distribueras på klustret. Baserat på den region som valts på fliken Grunderna kan de tillgängliga **versionerna** som stöds ändras.
    - **DNS-prefix**: DNS-namnnamnsprefix (Domain Name System) för AKS-kluster. Du använder DNS för att ansluta till Kubernetes API när du hanterar behållare när du har skapat klustret.
    - **Nodstorlek:** Storleken på kubernetes-noden kan du välja från listan över VM Stock keeping unit (SKU: tillgänglig på Azure. För optimal prestanda rekommenderar vi Standard DS3 v2.
    - **Antal noder**: Antalet kubernetes-noder som ska distribueras i klustret. Vi rekommenderar att du behåller det här nodantalet minst lika med eller mer än antalet HLF-noder som anges i fabric-inställningarna.
    - **Klient-ID:** Ange klient-ID för ett befintligt tjänsthuvudnamn eller skapa ett nytt, vilket krävs för AKS-autentiseringen. Se steg för att [skapa tjänstens huvudnamn](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Tjänstens huvudkundhemlighet**: Ange kundhemligheten för tjänstens huvudnamn som tillhandahålls i tjänstens huvudkund-ID.
    - **Bekräfta klienthemligheten**: Bekräfta klienthemligheten som anges i tjänstens huvudkundhemlighet.
    - **Aktivera behållarövervakning:** Välj att aktivera AKS-övervakning, vilket gör att AKS-loggarna kan skicka till den angivna logganalysarbetsytan.
    - **Log Analytics arbetsyta:** Logganalys arbetsyta kommer att fyllas med standard arbetsyta som skapas om övervakning är aktiverad.

8. När du har lämnat alla ovanstående detaljer väljer du **Granska och skapa** fliken. Granskningen och skapa utlöser valideringen för de värden som du angav.
9. När valideringen har gått kan du välja **skapa**.
Distributionen tar vanligtvis 10-12 minuter, kan variera beroende på storlek och antal AKS-noder som anges.
10. Efter den lyckade distributionen får du ett meddelande via Azure-aviseringar i det övre högra hörnet.
11. Välj **Gå till resursgrupp** om du vill kontrollera alla resurser som har skapats som en del av malldistributionen. Alla resursnamn börjar med prefixet i inställningen **Grunderna.**

## <a name="build-the-consortium"></a>Bygg konsortiet

Om du vill skapa blockchain-konsortiet efter distribution av beställningstjänsten och peer-noder måste du utföra stegen nedan i följd. **Skapa ditt nätverksskript** (byn.sh), vilket hjälper dig att konfigurera konsortiet, skapa kanal och installera kedjekod.

> [!NOTE]
> Build Your Network (byn) skript som tillhandahålls är strikt att användas för demo / devtest scenarier. För produktionsresultatinställningar rekommenderar vi att du använder de inbyggda HLF-API:erna.

Alla kommandon för att köra skriptet i byn kan köras via AZURE Bash Command Line Interface (CLI). Du kan logga in på Azure shell webbversion via ![Hyperledger Fabric på Azure Kubernetes servicemall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) längst upp till höger i Azure-portalen. Skriv bash i kommandotolken och gå in för att växla till bash CLI.

Mer information finns i [Azure-skalet.](https://docs.microsoft.com/azure/cloud-shell/overview)

![Hyperledger Fabric på Azure Kubernetes servicemall](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Ladda ner byn.sh och fabric-admin.yaml fil.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Ange miljövariabler under Azure CLI Bash:**

Ange information om kanalinformation och beställare

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Ange information om peer-organisation

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Skapa en Azure File-resurs för att dela olika offentliga certifikat mellan peer- och orderorganisationer.

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
**Kommandon för kanalhantering**

Gå till aks-kluster och ärendekommando för beställare för att skapa en ny kanal

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Kommandon för konsortiehantering**

Kör underkommandon i den angivna ordningen för att lägga till en peer-organisation i en kanal och ett konsortium.

1. Gå till AKS-peer-organisationen AKS-kluster och ladda upp dess medlemstjänst tillhandahålla (MSP) på en Azure File Storage.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Gå till orderer Organization AKS-klustret och lägg till peer-organisationen i kanal och konsortium.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Gå tillbaka till peer-organisationen och utfärda kommandot för att ansluta till peer-noder i kanalen.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

På samma sätt, för att lägga till fler peer-organisationer i kanalen, uppdatera peer AKS-miljövariabler enligt den peer-organisation som krävs och kör stegen 1 till 3.

**Kommandon för hantering av kedjekoder**

Kör kommandot nedan för att utföra kedjekodsrelaterad åtgärd. Dessa kommandon utför alla åtgärder på en demo chaincode. Denna demo chaincode har två variabler "a" och "b". Vid instansiering av kedjekoden initieras "a" med 1000 och "b" initieras med 2000. På varje anrop av kedjekoden överförs 10 enheter från "a" till "b". Frågeåtgärd på kedjekod visar världstillståndet för "en" variabel.

Kör följande kommandon som körs på AKS-peer-organisationen AKS-klustret.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Kommandon för kedjekodsåtgärd**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Köra inbyggda HLF-åtgärder

För att hjälpa kunder att komma igång med att köra inbyggda Hyperledger-kommandon i HLF-nätverket på AKS. Exempelprogrammet tillhandahålls som använder fabric NodeJS SDK för att utföra HLF-åtgärderna. Kommandona anges för att skapa ny användaridentitet och installera din egen kedjekod.

### <a name="before-you-begin"></a>Innan du börjar

Följ nedanstående kommandon för den första installationen av programmet:

- Ladda ner programfiler
- Generera anslutningsprofil och administratörsprofil
- Importera administratörsanvändaridentitet

När du har slutfört den första installationen kan du använda SDK för att uppnå nedanstående åtgärder:

- Generering av användaridentitet
- Kedjekodåtgärder

Ovanstående kommandon kan köras från Azure Cloud Shell.

### <a name="download-application-files"></a>Ladda ner programfiler

Den första inställningen för att köra programmet är att hämta alla programfiler i en mapp.

**Skapa appmapp och gå in i mappen:**

```bash
mkdir app
cd app
```
Kör nedan kommando för att ladda ner alla nödvändiga filer och paket:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Det här kommandot tar tid att läsa in alla paket. När kommandot har implementerats `node_modules` kan du se en mapp i den aktuella katalogen. Alla nödvändiga paket läses `node_modules` in i mappen.

### <a name="generate-connection-profile-and-admin-profile"></a>Generera anslutningsprofil och administratörsprofil

Skapa `profile` katalog `app` i mappen

```bash
cd app
mkdir ./profile
```
Ange dessa miljövariabler på Azure-molnskal

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Kör nedan kommando för att generera anslutningsprofil och administratörsprofil för organisationen

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Det kommer att skapa `profile` anslutningsprofil och administratör `<orgname>-ccp.json` för `<orgname>-admin.json` organisationen i profilmappen med namn respektive.

På samma sätt, generera anslutningsprofil och administratörsprofil för varje beställare och peer-organisation.


### <a name="import-admin-user-identity"></a>Importera administratörsanvändaridentitet

Det sista steget är att importera organisationens administratörsanvändaridentitet i plånboken.

```bash
npm run importAdmin -- -o <orgName>

```
Kommandot ovan kör importAdmin.js för att importera administratörsanvändaridentiteten till plånboken. Skriptet läser administratörsidentitet `<orgname>-admin.json` från administratörsprofilen och importerar den i plånboken för att köra HLF-åtgärder.

Skripten använder filsystemplånboken för att lagra identiteterna. Den skapar en plånbok enligt sökvägen som anges i fältet ".wallet" i anslutningsprofilen. Som standard initieras fältet ".wallet" med `<orgname>`, `<orgname>` vilket innebär att en mapp med namnet skapas i den aktuella katalogen för att lagra identiteterna. Om du vill skapa plånbok på någon annan sökväg ändrar du fältet ".wallet" i anslutningsprofilen innan du kör registrera administratörsanvändare och andra HLF-åtgärder.

På samma sätt importera administratörsanvändaridentitet för varje organisation.

Se kommandohjälpen för mer information om de argument som skickas i kommandot.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Generering av användaridentitet

Kör underkommandon i den angivna ordern för att generera nya användaridentiteter för HLF-organisationen.

> [!NOTE]
> Innan du börjar med steg för generering av användaridentitet, se till att den första installationen av programmet görs.

Ange miljövariabler under miljö på azure cloud shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Registrera och registrera nya användare

Om du vill registrera och registrera en ny användare kör du kommandot nedan som kör registerUser.js. Den sparar den genererade användaridentiteten i plånboken.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> Administratörsanvändaridentitet används för att utfärda registerkommandot för den nya användaren. Därför är det obligatoriskt att ha administratörsanvändaridentiteten i plånboken innan du kör det här kommandot. Annars misslyckas det här kommandot.

Se kommandohjälp för mer information om de argument som skickas i kommandot

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Kedjekodåtgärder


> [!NOTE]
> Innan du börjar med någon kedjekodsåtgärd, se till att den första installationen av programmet är klar.

Ange nedan kedjekodspecifika miljövariabler för Azure Cloud-skalet:

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

Nedanstående kedjekodsoperationer kan utföras:

- Installera kedjekod
- Instansiera kedjekod
- Anropa kedjekod
- Fråga kedjekod

### <a name="install-chaincode"></a>Installera kedjekod

Kör nedan kommando för att installera chaincode på peer-organisationen.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Det kommer att installera chaincode på alla peer-noder i organisationsuppsättningen i `ORGNAME` miljövariabeln. Om det finns två eller flera peer-organisationer i kanalen och du vill installera kedjekod på dem alla kör du kommandona separat för varje peer-organisation.

Följ stegen:

- Ange `ORGNAME` `<peerOrg1Name>` till `installCC` och utfärda kommando.
- Ange `ORGNAME` `<peerOrg2Name>` till `installCC` och utfärda kommando.

  Kör den för varje peer-organisation.

Mer information om argumenten som skickas i kommandot finns i kommandohjälpen.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Instansiera kedjekod

Kör nedan kommando för att instansiera kedjekod på peer.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Pass instantiation funktionsnamn och kommaavgränsad lista över argument i `<instantiateFunc>` respektive. `<instantiateFuncArgs>` Till exempel i [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go), att instansiera `<instantiateFuncArgs>` chaincode `""`inställd på `<instantiateFunc>` `"Init"` och tömma strängen .

> [!NOTE]
> Kör kommandot för en gångs skull från en peer-organisation i kanalen.
> När transaktionen har skickats till beställaren distribuerar beställaren transaktionen till alla peer-organisationer i kanalen. Därför instansieras kedjekoden på alla peer-noder på alla peer-organisationer i kanalen.

Se kommandohjälp för mer information om de argument som skickas i kommandot

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Anropa kedjekod

Kör kommandot nedan för att anropa kedjekodsfunktionen:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Skicka anropa funktionsnamn och kommaavgränsad lista med argument i `<invokeFunction>` respektive. `<invokeFuncArgs>` Fortsätter med fabcar chaincode exempel, att åberopa initLedger funktion inställd `<invokeFunction>` på `"initLedger"` och `<invokeFuncArgs>` till `""`.

> [!NOTE]
> Kör kommandot för en gångs skull från en peer-organisation i kanalen.
> När transaktionen har skickats till beställaren distribuerar beställaren transaktionen till alla peer-organisationer i kanalen. Därför uppdateras världsstaten på alla peer-noder för alla peer-organisationer i kanalen.

Se kommandohjälp för mer information om de argument som skickas i kommandot

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Fråga kedjekod

Kör nedan kommando för att fråga chaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Skicka frågefunktionsnamn och kommaavgränsad lista med argument i `<queryFunction>` respektive. `<queryFuncArgs>` Återigen, `fabcar` med chaincode som referens, att fråga alla `<queryFunction>` `"queryAllCars"` bilar `<queryArgs>` `""`i världen staten inställd på och till .

Se kommandohjälp för mer information om de argument som skickas i kommandot

```bash
npm run queryCC -- -h

```
