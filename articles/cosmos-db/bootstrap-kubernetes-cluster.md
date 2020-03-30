---
title: Så här använder du Azure Kubernetes med Azure Cosmos DB
description: Lär dig hur du startar ett Kubernetes-kluster på Azure som använder Azure Cosmos DB (förhandsversion)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70093732"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Så här använder du Azure Kubernetes med Azure Cosmos DB (förhandsversion)

Med ETCD-API:et i Azure Cosmos DB kan du använda Azure Cosmos DB som serverningslagring för Azure Kubernetes. Azure Cosmos DB implementerar etcd-trådprotokollet, vilket gör att huvudnodens API-servrar kan använda Azure Cosmos DB precis som det skulle komma åt en lokalt installerad etcd. etcd API i Azure Cosmos DB är för närvarande i förhandsversion. När du använder Azure Cosmos etcd API som stödlager för Kubernetes får du följande fördelar: 

* Inget behov av att manuellt konfigurera och hantera ETCD.
* Hög tillgänglighet m.m. etcd, garanterad av Cosmos (99,99% i en enda region, 99,999% i flera regioner).
* Elastisk skalbarhet av etcd.
* Säker som standard & företag redo.
* Branschledande, omfattande serviceavtal.

Mer information om API med etcd i Azure Cosmos DB finns i [översiktsartikeln.](etcd-api-introduction.md) Den här artikeln visar hur du använder [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) för att bootstrap a Kubernetes cluster på Azure som använder [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) i stället för en lokalt installerad och konfigurerad etcd. 

## <a name="prerequisites"></a>Krav

1. Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Du kan hämta Azure CLI som är specifika för ditt operativsystem och installera.

1. Installera den [senaste versionen](https://github.com/Azure/aks-engine/releases) av Azure Kubernetes Engine. Installationsinstruktionerna för olika operativsystem finns på sidan [Azure Kubernetes Engine.](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) Du behöver bara stegen från **Installera AKS Engine** delen av den länkade doc. När du har hämtat extraherar du zip-filen.

   Azure Kubernetes Engine (**aks-engine**) genererar Azure Resource Manager-mallar för Kubernetes-kluster på Azure. Indata till aks-motorn är en klusterdefinitionsfil som beskriver det önskade klustret, inklusive orchestrator, funktioner och agenter. Strukturen för indatafilerna liknar det offentliga API:et för Azure Kubernetes Service.

1. Etcd-API:et i Azure Cosmos DB är för närvarande i förhandsversion. Registrera dig för att använda https://aka.ms/cosmosetcdapi-signupförhandsgranskningsversionen på: . När du har skickat formuläret kommer din prenumeration att vitlistas för att använda Azure Cosmos etcd API. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Distribuera klustret med Azure Cosmos DB

1. Öppna ett kommandotolksfönster och logga in på Azure med följande kommando:

   ```azurecli-interactive
   az login 
   ```

1. Om du har mer än en prenumeration växlar du till prenumerationen som har vitlistats för Azure Cosmos DB etcd API. Du kan växla till den prenumeration som krävs med följande kommando:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Skapa sedan en ny resursgrupp där du ska distribuera de resurser som krävs av Azure Kubernetes-klustret. Se till att skapa resursgruppen i regionen "centralus". Det är inte obligatoriskt för resursgruppen att vara i "centralus"-regionen, men Azure Cosmos etcd API är för närvarande tillgänglig för distribution i "centralus" regionen bara. Så det är bäst att ha Kubernetes klustret att vara samlokaliseras med Cosmos etcd instans:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Skapa sedan ett tjänsthuvudnamn för Azure Kubernetes-klustret så att det kan kommunicera med de resurser som ingår i samma resursgrupp. Du kan skapa ett tjänsthuvudnamn med Azure CLI, PowerShell eller Azure-portalen, i det här exemplet kommer du CLI att skapa den.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Det här kommandot matar ut information om ett tjänsthuvudnamn, till exempel:
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Anteckna **appId** och lösenordsfälten, eftersom du använder dessa parametrar i nästa steg. **password** 

1. Från kommandotolken navigerar du till mappen där Azure Kubernetes Engine körbar finns. I kommandotolken kan du till exempel navigera till mappen som:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Öppna en textredigerare som du väljer och definiera en Resource Manager-mall som distribuerar Azure Kubernetes-klustret med Azure Cosmos DB etcd API. Kopiera följande JSON-definition till textredigeraren och spara filen som: `apiModel.json`

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   I JSON/klusterdefinitionsfilen är nyckelparametern att notera **"cosmosEtcd": true**. Den här parametern finns i egenskaperna "masterProfile" och anger distributionen för att använda Azure Cosmos etcd API i stället för vanlig etcd. 

1. Distribuera Azure Kubernetes-klustret som använder Azure Cosmos DB med följande kommando:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Azure Kubernetes Engine använder en klusterdefinition som beskriver önskad form, storlek och konfiguration av Azure Kubernetes. Det finns flera funktioner som kan aktiveras via klusterdefinitionen. I det här exemplet ska du använda följande parametrar:

   * **prenumeration-id:** Azure-prenumerations-ID som har Azure Cosmos DB etcd API aktiverat.
   * **klient-ID:** Tjänstens huvudnamns appId. Den `appId` returnerades som utdata i steg 4.
   * **Klient-hemlighet:** Tjänstens huvudnamnslösenord eller ett slumpmässigt genererat lösenord. Det här värdet returnerades som utdata i parametern "lösenord" i steg 4. 
   * **dnsPrefix:** Ett regionunikt DNS-namn. Det här värdet kommer att utgöra en del av värdnamnet (exempelvärden är- myprod1, mellanlagring).
   * **Plats:**  Plats där klustret ska distribueras till, för närvarande stöds endast "centralus".

   > [!Note]
   > Azure Cosmos etcd API är för närvarande endast tillgängligt för distribution i "centralus"-regionen. 
 
   * **api-modell:** Fullständigt kvalificerad sökväg till mallfilen.
   * **tvångsöverskrivning:** Det här alternativet används för att automatiskt skriva över befintliga filer i utdatakatalogen.
 
   Följande kommando visar en exempeldistribution:

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>Verifiera distributionen

Malldistributionen tar flera minuter att slutföra. När distributionen har slutförts visas följande utdata i kommandotolken:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Resursgruppen innehåller nu resurser som- virtuell dator, Azure Cosmos-konto(etcd API), virtuellt nätverk, tillgänglighetsuppsättning och andra resurser som krävs av Kubernetes-klustret. 

Azure Cosmos-kontots namn matchar ditt angivna DNS-prefix som läggs till med k8:er. Ditt Azure Cosmos-konto etableras automatiskt med en databas med namnet **EtcdDB** och en behållare med namnet **EtcdData**. Behållaren kommer att lagra alla ETCD-relaterade data. Behållaren är etablerad med ett visst antal begärandeenheter och du kan [skala (öka/minska) dataflödet](scaling-throughput.md) baserat på din arbetsbelastning. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [arbetar med Azure Cosmos-databas, behållare och objekt](databases-containers-items.md)
* Lär dig hur du [optimerar etablerade dataflödeskostnader](optimize-cost-throughput.md)

