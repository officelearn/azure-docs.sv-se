---
title: Anpassa användardefinierade vägar (UDR) i Azure Kubernetes service (AKS)
description: Lär dig hur du definierar en anpassad utgående väg i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: d108c6f49a8f483dc489fd644db6b480fc0e74fc
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595815"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Anpassa utgående kluster med en användardefinierad väg (förhands granskning)

Utgående från ett AKS-kluster kan anpassas så att de passar vissa scenarier. Som standard tillhandahåller AKS en standard-SKU Load Balancer som ska installeras och användas för utgående trafik. Men standard installationen kanske inte uppfyller kraven i alla scenarier om offentliga IP-adresser inte tillåts eller om ytterligare hopp krävs för utgående trafik.

Den här artikeln beskriver hur du anpassar ett klusters utgående väg för att stödja anpassade nätverks scenarier, till exempel sådana som inte tillåter offentliga IP-adresser och kräver att klustret placeras bakom en virtuell nätverks installation (NVA).

> [!IMPORTANT]
> AKS för hands versions funktionerna är självbetjänings tjänster och erbjuds på ett valbart sätt. För hands versioner tillhandahålls och *är* *tillgängliga* och omfattas inte av service nivå avtalet (SLA) och begränsad garanti. AKS för hands versionerna omfattas delvis av kund supporten på *bästa* möjliga sätt. Därför är funktionerna inte avsedda att användas för produktion. Mer information finns i följande support artiklar:
>
> * [Support principer för AKS](support-policies.md)
> * [Vanliga frågor och svar om support för Azure](faq.md)

## <a name="prerequisites"></a>Förutsättningar
* Azure CLI-version 2.0.81 eller senare
* Azure CLI Preview-tillägg version 0.4.28 eller senare
* API-version av `2020-01-01` eller större

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Installera det senaste för hands tillägget för Azure CLI-AKS
Om du vill ange utgående typ för ett kluster måste du ha Azure CLI-AKS för hands versions version 0.4.18 eller senare. Installera Azure CLI-AKS för hands versions tillägg med hjälp av kommandot AZ Extension Add och Sök sedan efter eventuella tillgängliga uppdateringar med hjälp av följande kommando för AZ-tillägg:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Begränsningar
* Under för hands versionen kan `outboundType` bara definieras i klustrets skapande tid och kan inte uppdateras efteråt.
* `outboundType` AKS-kluster bör använda Azure CNI under för hands versionen. Kubernetes kan konfigureras, användning kräver manuella kopplingar av routningstabellen till AKS-undernätet.
* Inställningen `outboundType` kräver AKS-kluster med `vm-set-type` av `VirtualMachineScaleSets` och `load-balancer-sku` `Standard`.
* Om du anger `outboundType` till ett värde för `UDR` krävs en användardefinierad väg med giltig utgående anslutning för klustret.
* Om du anger `outboundType` till ett värde för `UDR` betyder det att den inkommande käll-IP-vägen till belastningsutjämnaren **inte matchar** klustrets utgående mål adress för utgående trafik.

## <a name="overview-of-outbound-types-in-aks"></a>Översikt över utgående typer i AKS

Ett AKS-kluster kan anpassas med en unik `outboundType` av typen belastningsutjämnare eller användardefinierad routning.

> [!IMPORTANT]
> Utgående typ påverkar bara utgående trafik i klustret. Mer information finns i Konfigurera ingångs [styrenheter](ingress-basic.md) .

### <a name="outbound-type-of-loadbalancer"></a>Utgående typ av loadBalancer

Om `loadBalancer` har angetts, slutför AKS följande konfiguration automatiskt. Belastningsutjämnaren används för utgående trafik genom en AKS tilldelad offentlig IP. En utgående typ `loadBalancer` stöder Kubernetes-tjänster av typen `loadBalancer`, som förväntar sig utgående från belastningsutjämnaren som skapats av AKS-resurs leverantören.

Följande inställningar utförs av AKS.
   * En offentlig IP-adress har allokerats för utgående kluster.
   * Den offentliga IP-adressen är tilldelad till belastnings Utjämnings resursen.
   * Backend-pooler för belastningsutjämnaren konfigureras för agent-noder i klustret.

Nedan är en nätverkstopologi som distribueras i AKS-kluster som standard, som använder en `outboundType` av `loadBalancer`.

![outboundtype – lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Utgående typ av userDefinedRouting

> [!NOTE]
> Använd utgående typ är ett avancerat nätverks scenario och kräver rätt nätverks konfiguration.

Om `userDefinedRouting` har angetts konfigureras inte AKS automatiskt utgående sökvägar. Följande förväntas göras av **användaren**.

Klustret måste distribueras i ett befintligt virtuellt nätverk med ett undernät som har kon figurer ATS. En giltig användardefinierad väg (UDR) måste finnas i under nätet med utgående anslutning.

AKS Resource Provider kommer att distribuera en standard Load Balancer (SLB). Belastningsutjämnaren har inte kon figurer ATS med några regler och [debiteras inte förrän en regel har lagts](https://azure.microsoft.com/pricing/details/load-balancer/)till. AKS etablerar **inte** automatiskt en offentlig IP-adress för SLB-klient. AKS konfigurerar **inte** backend-poolen för belastningsutjämnare automatiskt.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Distribuera ett kluster med utgående typ av UDR och Azure-brandvägg

För att illustrera programmet för ett kluster med utgående typ med en användardefinierad väg, kan ett kluster konfigureras i ett virtuellt nätverk som är peer-kopplat med en Azure-brandvägg.

![Låst topologi](media/egress-outboundtype/outboundtype-udr.png)

* Ingressen tvingas att flöda genom brand Väggs filter
   * Ett isolerat undernät innehåller en intern belastningsutjämnare för routning till agent-noder
   * Agent-noderna är isolerade i ett dedikerat undernät
* Utgående begär Anden börjar från agent-noder till den interna Azure-brandväggen med en användardefinierad väg
   * Begär Anden från AKS agent-noder följer en UDR som har placerats i under nätet som AKS-klustret distribuerades till.
   * Azure-brandväggen går ut från det virtuella nätverket från en offentlig IP-klient
   * Åtkomst till AKS-kontroll planet skyddas av en NSG, som har aktiverat IP-adressen för brand Väggs klient delen
   * Åtkomst till offentliga Internet eller andra Azure-tjänster flödar till och från brand Väggs klient delens IP-adress

### <a name="set-configuration-via-environment-variables"></a>Konfigurera konfiguration via miljövariabler

Definiera en uppsättning miljövariabler som ska användas för att skapa resurser.

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

Ange sedan prenumerations-ID: n.

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Skapa ett virtuellt nätverk med flera undernät

Etablera ett virtuellt nätverk med tre separata undernät, ett för klustret, ett för brand väggen och ett för ingångs tjänsten.

![Tom nätverkstopologi](media/egress-outboundtype/empty-network.png)

Skapa en resurs grupp för att lagra alla resurser.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Skapa två virtuella nätverk som ska vara värdar för AKS-klustret och Azure-brandväggen. Var och en får sitt eget undernät. Vi börjar med AKS-nätverket.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Skapa och konfigurera en Azure-brandvägg med en UDR

Regler för inkommande och utgående Azure-brandvägg måste konfigureras. Huvud syftet med brand väggen är att göra det möjligt för organisationer att konfigurera detaljerade ingångs-och utgångs trafik regler till och från AKS-klustret.

![Brand vägg och UDR](media/egress-outboundtype/firewall-udr.png)

Skapa en offentlig IP-resurs (standard-SKU) som ska användas som Azure Firewall-frontend-adress.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registrera Preview CLI-tillägget för att skapa en Azure-brandvägg.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

Den IP-adress som skapades tidigare kan nu tilldelas brand Väggs klient delen.
> [!NOTE]
> Det kan ta några minuter att konfigurera den offentliga IP-adressen till Azure-brandväggen.
> 
> Om fel tas emot flera gånger i kommandot nedan tar du bort den befintliga brand väggen och den offentliga IP-adressen och etablerar den offentliga IP-adressen och Azure-brandväggen via portalen på samma gång.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

När det föregående kommandot har slutförts, sparar du IP-adressen för brand Väggs klient delen för konfiguration senare.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Skapa en UDR med hopp till Azure-brandväggen

Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du vill ändra någon av Azures standardroutning gör du det genom att skapa en routningstabell.

Skapa en tom routningstabell som ska associeras med ett angivet undernät. Routningstabellen definierar nästa hopp som den Azure-brandvägg som skapats ovan. Varje undernät kan ha noll eller en associerad routningstabell.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Mer information om hur du kan åsidosätta Azures standard system vägar eller lägga till ytterligare vägar till ett undernäts routningstabell finns i [dokumentationen för virtuella nätverks vägar](../virtual-network/virtual-networks-udr-overview.md#user-defined) .

## <a name="adding-network-firewall-rules"></a>Lägga till nätverks brand Väggs regler

> [!WARNING]
> Nedan visas ett exempel på hur du lägger till en brand Väggs regel. Alla utgående slut punkter som definieras i de [obligatoriska utgående slut punkterna](egress.md) måste aktive ras av brand Väggs regler för AKS för att fungera. Det går inte att använda klustret, utan att dessa slut punkter har Aktiver ATS.

Nedan visas ett exempel på en nätverks-och program regel. Vi lägger till en nätverks regel som tillåter alla protokoll, käll adresser, mål adresser och mål portar. Vi lägger också till en program regel för **några** av slut punkterna som krävs av AKS.

I ett produktions scenario bör du bara aktivera åtkomst till obligatoriska slut punkter för ditt program och de som definierats i [AKS-nödvändigt utgående](egress.md).

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

Mer information om Azure Firewall-tjänsten finns i [dokumentationen för Azure-brandväggen](https://docs.microsoft.com/azure/firewall/overview) .

## <a name="associate-the-route-table-to-aks"></a>Koppla routningstabellen till AKS

För att associera klustret med brand väggen måste det dedikerade under nätet för klustrets undernät referera till routningstabellen som skapats ovan. Kopplingen kan göras genom att ett kommando utfärdas till det virtuella nätverket som innehåller både klustret och brand väggen för att uppdatera routningstabellen för klustrets undernät.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Distribuera AKS med utgående typ av UDR till det befintliga nätverket

Nu kan ett AKS-kluster distribueras till den befintliga konfigurationen av virtuella nätverk. För att kunna ange en utgående kluster typ till användardefinierad routning måste ett befintligt undernät tillhandahållas till AKS.

![AKS – distribuera](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Skapa ett huvud namn för tjänsten med åtkomst till etablering i det befintliga virtuella nätverket

Ett huvud namn för tjänsten används av AKS för att skapa kluster resurser. Tjänstens huvud namn som skickades vid skapande tillfället används för att skapa underliggande AKS-resurser, till exempel virtuella datorer, lagrings enheter och belastningsutjämnare som används av AKS. Om det har beviljats för få behörigheter, kan det inte etablera ett AKS-kluster.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Ersätt nu `APPID` och `PASSWORD` nedan med tjänstens huvud namn och lösen ord för tjänstens huvud namn som genererats av föregående kommandoutdata. Vi kommer att referera till resurs-ID: t för VNET för att bevilja behörighet till tjänstens huvud namn så att AKS kan distribuera resurser till den.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>Distribuera AKS

Slutligen kan AKS-klustret distribueras till det befintliga under nätet som vi har dedikerat för klustret. Mål under nätet som ska distribueras till definieras med miljövariabeln `$SUBNETID`.

Vi definierar den utgående typen för att följa UDR som finns i under nätet, vilket gör det möjligt för AKS att hoppa över installations-och IP-etablering för belastningsutjämnaren som nu kan vara helt internt.

AKS-funktionen för [tillåtna IP-intervall för API-servrar](api-server-authorized-ip-ranges.md) kan läggas till för att begränsa åtkomsten till API-servern till endast brand väggens offentliga slut punkt. Funktionen auktoriserade IP-adressintervall anges i diagrammet som NSG som måste skickas för att få åtkomst till kontroll planet. När du aktiverar funktionen auktoriserat IP-intervall för att begränsa åtkomsten till API-servern, måste utvecklarverktyg använda en hoppsida från brand väggens virtuella nätverk, eller så måste du lägga till alla utvecklares slut punkter i det auktoriserade IP-intervallet.

> [!TIP]
> Ytterligare funktioner kan läggas till i kluster distributionen, t. ex. (privat kluster) []. När du använder auktoriserade IP-intervall krävs en hoppning i kluster nätverket för att få åtkomst till API-servern.

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>Aktivera åtkomst till utvecklare till API-servern

På grund av de auktoriserade IP-adressintervall som ska konfigureras för klustret måste du lägga till dina IP-adresser för utvecklarverktyg i AKS-kluster listan över godkända IP-adressintervall för att få åtkomst till API-servern. Ett annat alternativ är att konfigurera en hoppsida med nödvändiga verktyg i ett separat undernät i brand väggens virtuella nätverk.

Lägg till en annan IP-adress till de godkända intervallen med följande kommando

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

### <a name="setup-the-internal-load-balancer"></a>Konfigurera den interna belastningsutjämnaren

AKS har distribuerat en belastningsutjämnare med klustret som kan konfigureras som en [intern belastningsutjämnare](internal-lb.md).

Skapa en intern belastningsutjämnare genom att skapa ett tjänst manifest med namnet Internal-lb. yaml med tjänst typen LoadBalancer och Azure-belastningsutjämnaren – intern anteckning som visas i följande exempel:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Distribuera den interna belastningsutjämnaren med kubectl tillämpa och ange namnet på ditt YAML-manifest:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Distribuera en Kubernetes-tjänst

Eftersom utgående kluster typ har angetts som UDR, associerar-agent-noderna som backend-poolen för belastningsutjämnaren inte har slutförts automatiskt av AKS vid kluster skapande tid. En Association för Server delens pool hanteras dock av Kubernetes Azure Cloud Provider när Kubernetes-tjänsten distribueras.

Distribuera programmet Azure röstning app genom att kopiera yaml nedan till en fil med namnet `example.yaml`.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Distribuera tjänsten genom att köra:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Lägg till en DNAT-regel i Azure-brandväggen

Om du vill konfigurera inkommande anslutning måste en DNAT-regel skrivas till Azure-brandväggen. För att testa anslutningen till vårt kluster definieras en regel för den offentliga IP-adressen för brand Väggs-frontend att dirigera till den interna IP-adressen som exponeras av den interna tjänsten.

Mål adressen kan anpassas eftersom det är porten i brand väggen som ska nås. Den översatta adressen måste vara IP-adressen för den interna belastningsutjämnaren. Den översatta porten måste vara den exponerade porten för din Kubernetes-tjänst.

Du måste ange den interna IP-adress som har tilldelats till belastningsutjämnaren som skapats av Kubernetes-tjänsten. Hämta adressen genom att köra:

```bash
kubectl get services
```

Den IP-adress som behövs visas i kolumnen extern IP-adress, som ser ut ungefär så här.

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>Rensa resurser

> [!NOTE]
> När du tar bort den interna Kubernetes-tjänsten och den interna belastningsutjämnaren inte längre används av någon tjänst, tar Azure Cloud Provider bort den interna belastningsutjämnaren. Vid nästa tjänst distribution distribueras en belastningsutjämnare om ingen kan hittas med den begärda konfigurationen.

Ta bort resurs gruppen AKS om du vill rensa Azure-resurser.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Verifiera anslutning

Gå till Azure Firewall-klientens IP-adress i en webbläsare för att verifiera anslutningen.

Du bör se en bild av appen för Azure-röstning.

## <a name="next-steps"></a>Nästa steg

Se [Översikt över Azure Networking-UDR](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Se [hur du skapar, ändrar eller tar bort en](https://docs.microsoft.com/azure/virtual-network/manage-route-table)routningstabell.