---
title: Hur du använder Azure Kubernetes med Azure Cosmos DB
description: Lär dig att kunna starta ett Kubernetes-kluster på Azure som använder Azure Cosmos DB (förhandsversion)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 2c6af53aeec5d40f603d65595d93527107c0d80a
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427714"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Hur du använder Azure Kubernetes med Azure Cosmos DB (förhandsversion)

Etcd API i Azure Cosmos DB kan du använda Azure Cosmos DB som backend-store för Azure Kubernetes. Azure Cosmos DB implementerar kabelprotokoll etcd, vilket gör att den överordnade noden API servrar att använda Azure Cosmos DB, precis som den skulle få åtkomst till ett lokalt installerade etcd. etcd API i Azure Cosmos DB är för närvarande i förhandsversion. När du använder Azure Cosmos-etcd API som lagringsenheten för Kubernetes kan få du följande fördelar: 

* Behöver inte konfigurera och hantera etcd manuellt.
* Hög tillgänglighet för etcd, garanterats av Cosmos (99,99% i region, 99,999% i flera regioner).
* Elastiska skalbarheten i etcd.
* Skydda genom standard & redo för företagsanvändning.
* Branschledande, omfattande serviceavtal.

Läs mer om etcd API i Azure Cosmos DB i den [översikt](etcd-api-introduction.md) artikeln. Den här artikeln visar hur du använder [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-motor) ska kunna starta ett Kubernetes-kluster på Azure som använder [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) i stället för ett lokalt installerat och konfigurerat etcd. 

## <a name="prerequisites"></a>Nödvändiga komponenter

1. Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Du kan ladda ned Azure CLI som är specifika för ditt operativsystem och installera.

1. Installera den [senaste versionen](https://github.com/Azure/aks-engine/releases) Azure Kubernetes-motorn. Installationsinstruktioner för olika operativsystem är tillgängliga i [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) sidan. Du behöver bara stegen från **installera AKS-motorn** delen av det länkade dokumentet. Extrahera zip-filen efter nedladdningen.

   Azure Kubernetes-motor (**aks-engine**) genererar Azure Resource Manager-mallar för Kubernetes-kluster på Azure. Indata till aks-motorn är en kluster-definitionsfil som beskriver önskad klustret, inklusive orchestrator, funktioner och agenter. Strukturen för indatafilerna liknar offentliga API: et för Azure Kubernetes Service.

1. Etcd API i Azure Cosmos DB är för närvarande i förhandsversion. Registrera dig för att använda förhandsversionen på: https://aka.ms/cosmosetcdapi-signup. När du har skickat formuläret blir vitlistad för att använda Azure Cosmos-etcd API med din prenumeration. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Distribuera kluster med Azure Cosmos DB

1. Öppna ett kommandotolksfönster och logga in på Azure med följande kommando:

   ```azurecli-interactive
   az login 
   ```

1. Om du har mer än en prenumeration kan du växla till den prenumeration som har varit godkänd för Azure Cosmos DB etcd API. Du kan växla till den nödvändiga prenumeration med hjälp av följande kommando:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Därefter skapa en ny resursgrupp där du distribuerar resurser som krävs av Azure Kubernetes-kluster. Se till att skapa en resursgrupp i regionen ”centralus”. Det är inte obligatoriskt för resursgruppen som ska finnas i ”centralus” region men, Azure Cosmos-etcd API är nu tillgänglig för att distribuera i ”centralus”-region. Därför är det bäst att ha Kubernetes-klustret är samordnat med Cosmos etcd instans:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Därefter skapa tjänstens huvudnamn för Azure Kubernetes-kluster så att den kan kommunicera med de resurser som ingår i samma resursgrupp. Du kan skapa ett huvudnamn för tjänsten med hjälp av Azure CLI, PowerShell eller Azure-portalen i det här exemplet kommer du att du CLI för att skapa den.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Detta kommando visar information om tjänstens huvudnamn, till exempel:
   
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
   
   Anteckna den **appId** och **lösenord** fält, eftersom du ska använda dessa parametrar i nästa steg. 

1. Navigera till mappen där den Azure Kubernetes Engine körbara filen finns från Kommandotolken. Exempelvis kan du navigera till mappen som på Kommandotolken:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Öppna en textredigerare valfri och definiera en Resource Manager-mall som distribuerar Azure Kubernetes-kluster med Azure Cosmos DB etcd API. Kopiera följande JSON-definition till din textredigerare och spara filen som `apiModel.json`:

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

   I definitionsfilen JSON/kluster parametern viktiga att tänka på är **”cosmosEtcd”: true**. Den här parametern är i egenskaperna för ”masterProfile” och det visar att distributionen för att använda API för Azure Cosmos-etcd i stället för vanliga etcd. 

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

   Azure Kubernetes-motorn förbrukar en kluster-definition som beskriver önskad form, storlek och konfiguration av Azure Kubernetes. Det finns flera funktioner som kan aktiveras via klusterdefinitionen. I det här exemplet ska du använda följande parametrar:

   * **prenumerations-id:** Azure-prenumerations-ID som har aktiverats för Azure Cosmos DB etcd API.
   * **klient-id:** Tjänstens huvudnamn appId. Den `appId` returnerades som utdata i steg 4.
   * **Client-secret:** Lösenordet för tjänstens huvudnamn eller ett slumpmässigt genererat lösenord. Det här värdet returnerades som utdata i parametern 'password' i steg 4. 
   * **dnsPrefix:** En region-unikt DNS-namn. Det här värdet utgör en del av värdnamn (exempel värden är-myprod1, mellanlagring).
   * **Plats:**  Platsen där klustret ska distribueras till, för närvarande bara ”centralus” stöds.

   > [!Note]
   > Azure Cosmos etcd API är för närvarande tillgängliga för distribution i ”centralus”-region. 
 
   * **api-model:** Fullständig sökväg till mallfilen.
   * **force-Skriv över:** Det här alternativet används för att automatiskt skriva över befintliga filer i katalogen.
 
   Följande kommando visar ett exempel på distribution:

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

Malldistributionen tar flera minuter att slutföra. När distributionen har slutförts visas följande utdata i meddelandet för kommandon:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Resursgruppen innehåller nu resurser som – virtuell dator, Azure Cosmos-kontot (etcd API), virtuellt nätverk, tillgänglighetsuppsättning, och andra resurser som krävs av Kubernetes-klustret. 

Azure Cosmos-kontots namn matchar den angivna DNS-prefix som bifogats med k8s. Ditt Azure Cosmos-konto ska etableras automatiskt med en databas med namnet **EtcdDB** och en behållare med namnet **EtcdData**. Behållaren lagrar alla etcd relaterade data. Behållaren har etablerats med ett visst antal enheter för programbegäran och du kan [skala (öka/minska) dataflödet](scaling-throughput.md) baserat på din arbetsbelastning. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [fungerar med Azure Cosmos databas, behållare och objekt](databases-containers-items.md)
* Lär dig hur du [optimera kostnader för etablerat dataflöde](optimize-cost-throughput.md)

