---
title: Så här använder du Azure Kubernetes med Azure Cosmos DB
description: Lär dig hur du startar ett Kubernetes-kluster på Azure som använder Azure Cosmos DB (för hands version)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: dbfa780269f28e269fed51a25ec4255d18d8ab39
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277878"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Använda Azure-Kubernetes med Azure Cosmos DB (för hands version)

Med etcd-API: et i Azure Cosmos DB kan du använda Azure Cosmos DB som server dels Arkiv för Azure Kubernetes. Azure Cosmos DB implementerar etcd Wire-protokollet, vilket gör att den primära nodens API-servrar kan använda Azure Cosmos DB precis som den skulle ha åtkomst till en lokalt installerad etcd. etcd-API i Azure Cosmos DB är för närvarande en för hands version. När du använder Azure Cosmos etcd-API som lagrings plats för Kubernetes får du följande fördelar: 

* Du behöver inte konfigurera och hantera etcd manuellt.
* Hög tillgänglighet för etcd, garanterad av Cosmos (99,99% i enkel region, 99,999% i flera regioner).
* Elastisk skalbarhet för etcd.
* Skydda som standard & Enterprise Ready.
* Branschledande och omfattande service avtal.

Mer information om etcd API i Azure Cosmos DB finns i [översikts](etcd-api-introduction.md) artikeln. Den här artikeln visar hur du använder [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (AKS-motor) för att starta ett Kubernetes-kluster på Azure som använder [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) i stället för lokalt installerat och konfigurerat etcd. 

## <a name="prerequisites"></a>Förutsättningar

1. Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Du kan ladda ned Azure CLI som är särskilt för operativ systemet och installera.

1. Installera den [senaste versionen](https://github.com/Azure/aks-engine/releases) av Azure Kubernetes Engine. Installations anvisningarna för olika operativ system är tillgängliga på [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) -sidan. Du behöver bara stegen i avsnittet **Installera AKS Engine** i det länkade dokumentet. Extrahera zip-filen efter nedladdning.

   Azure Kubernetes Engine (**AKS-Engine**) genererar Azure Resource Manager mallar för Kubernetes-kluster på Azure. Indata till AKS-Engine är en kluster definitions fil som beskriver det önskade klustret, inklusive Orchestrator, funktioner och agenter. Indatafilernas struktur liknar det offentliga API: t för Azure Kubernetes-tjänsten.

1. Etcd-API: et i Azure Cosmos DB är för närvarande en för hands version. Registrera dig för att använda för hands versionen på: https://aka.ms/cosmosetcdapi-signup . När du har skickat formuläret kommer din prenumeration att kunna använda Azure Cosmos etcd-API: et. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Distribuera klustret med Azure Cosmos DB

1. Öppna ett kommando tolks fönster och logga in på Azure med följande kommando:

   ```azurecli-interactive
   az login 
   ```

1. Om du har mer än en prenumeration växlar du till den prenumeration som har tillåtits för Azure Cosmos DB etcd-API: et. Du kan växla till den prenumeration som krävs med hjälp av följande kommando:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Skapa sedan en ny resurs grupp där du kommer att distribuera de resurser som krävs av Azure Kubernetes-klustret. Se till att skapa resurs gruppen i regionen "centrala". Det är inte obligatoriskt för resurs gruppen att vara i "Central" region, men Azure Cosmos etcd API är för närvarande endast tillgängligt för att distribuera i "Central"-regionen. Så det är bäst att låta Kubernetes-klustret befinna sig med Cosmos etcd-instansen:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Skapa sedan ett huvud namn för tjänsten för Azure Kubernetes-klustret så att det kan kommunicera med de resurser som ingår i samma resurs grupp. Du kan skapa ett huvud namn för tjänsten med hjälp av Azure CLI, PowerShell eller Azure Portal, i det här exemplet ska du skapa det.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Det här kommandot matar ut information om ett huvud namn för tjänsten, till exempel:
   
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
   
   Anteckna fälten **appId** och **lösen ord** eftersom du kommer att använda parametrarna i nästa steg. 

1. Från kommando tolken navigerar du till mappen där den körbara filen för Azure Kubernetes-motorn finns. I kommando tolken kan du till exempel navigera till mappen som:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Öppna en valfri text redigerare och definiera en Resource Manager-mall som distribuerar Azure Kubernetes-klustret med Azure Cosmos DB etcd API. Kopiera följande JSON-definition till text redigeraren och spara filen som `apiModel.json` :

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

   I JSON/kluster definitions filen är nyckel parametern till Not **"cosmosEtcd": true**. Den här parametern är i egenskaperna "masterProfile" och anger att distributionen ska använda Azure Cosmos etcd API i stället för vanliga etcd. 

1. Distribuera Azure Kubernetes-kluster som använder Azure Cosmos DB med följande kommando:

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

   Azure Kubernetes-motorn använder en kluster definition som beskriver önskad form, storlek och konfiguration för Azure-Kubernetes. Det finns flera funktioner som kan aktive ras via kluster definitionen. I det här exemplet ska du använda följande parametrar:

   * **prenumerations-ID:** Azure-prenumerations-ID som har Azure Cosmos DB etcd-API aktiverat.
   * **klient-ID:** Tjänstens huvud namn (appId). `appId`Returnerades som utdata i steg 4.
   * **Klient hemlighet:** Tjänstens huvud namn eller ett slumpmässigt genererat lösen ord. Värdet returnerades som utdata i parametern "Password" i steg 4. 
   * **dnsPrefix:** En region – unikt DNS-namn. Det här värdet kommer att ingå i värd namnet (exempel värden är-myprod1, mellanlagring).
   * **plats:**  Plats där klustret ska distribueras till, för närvarande stöds endast "centrala".

   > [!Note]
   > Azure Cosmos etcd API är för närvarande endast tillgängligt för distribution i "centralal"-regionen. 
 
   * **API-modell:** Fullständigt kvalificerad sökväg till mallfilen.
   * **Framtvinga-överskrivning:** Det här alternativet används för att automatiskt skriva över befintliga filer i utdatakatalogen.
 
   Följande kommando visar en exempel distribution:

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

Mall distributionen tar flera minuter att slutföra. När distributionen har slutförts visas följande utdata i kommando tolken:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Resurs gruppen innehåller nu resurser, till exempel virtuell dator, Azure Cosmos-konto (etcd-API), virtuellt nätverk, tillgänglighets uppsättning och andra resurser som krävs av Kubernetes-klustret. 

Namnet på Azure Cosmos-kontot motsvarar det angivna DNS-prefixet med K8s. Ditt Azure Cosmos-konto kommer automatiskt att tillhandahållas med en databas med namnet **EtcdDB** och en behållare med namnet **EtcdData**. Behållaren lagrar alla etcd-relaterade data. Behållaren är etablerad med ett visst antal enheter för programbegäran och du kan [skala (öka/minska) data flödet](scaling-throughput.md) baserat på din arbets belastning. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [arbetar med Azure Cosmos Database, behållare och objekt](account-databases-containers-items.md)
* Lär dig hur du [optimerar etablerade data flödes kostnader](optimize-cost-throughput.md)

