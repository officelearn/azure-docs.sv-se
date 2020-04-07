---
title: Anpassa användardefinierade vägar (UDR) i Azure Kubernetes Service (AKS)
description: Lär dig hur du definierar en anpassad utgående väg i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 3780680c485aebf1ffc654d31c577821a9b96fff
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676512"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Anpassa klusterutgående med en användardefinierad rutt (förhandsgranskning)

Utgående från ett AKS-kluster kan anpassas för att passa specifika scenarier. Som standard etablerar AKS en standard-SKU-belastningsutfärdare som ska ställas in och användas för utgående. Standardinställningen kanske inte uppfyller kraven i alla scenarier om offentliga IPs inte tillåts eller ytterligare hopp krävs för utgående.

Den här artikeln går igenom hur du anpassar ett klusters utgående väg för att stödja anpassade nätverksscenarier, till exempel de som inte tillåter offentliga IP-adresser och kräver att klustret sitter bakom en virtuell nätverksinstallation (NVA).

> [!IMPORTANT]
> AKS-förhandsgranskningsfunktioner är självbetjäning och erbjuds på opt-in-basis. Förhandsgranskningar tillhandahålls *i dess fall* och är undantagna från servicenivåavtalet (SLA) och begränsad garanti. *as available* AKS-förhandsvisningar omfattas delvis av kundsupport efter *bästa möjliga ansträngning.* Därför är funktionerna inte avsedda för produktionsanvändning. Mer information finns i följande supportartiklar:
>
> * [Aks-supportpolicyer](support-policies.md)
> * [Vanliga frågor och svar om Azure-support](faq.md)

## <a name="prerequisites"></a>Krav
* Azure CLI version 2.0.81 eller senare
* Azure CLI Preview-tilläggsversion 0.4.28 eller senare
* API-version `2020-01-01` av eller mer

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Installera det senaste Azure CLI AKS Preview-tillägget
Om du vill ange den utgående typen av ett kluster behöver du Azure CLI AKS Preview-tilläggsversionen 0.4.18 eller senare. Installera Azure CLI AKS Preview-tillägget med kommandot az extension add och sök sedan efter tillgängliga uppdateringar med hjälp av följande kommando för uppdatering av az-tillägg:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Begränsningar
* Under förhandsgranskningen `outboundType` kan endast definieras vid klusterskapande tid och kan inte uppdateras efteråt.
* Under förhandsversionen bör `outboundType` AKS-kluster använda Azure CNI. Kubenet är konfigurerbart, användning kräver manuella associationer av flödestabellen till AKS-undernätet.
* Inställningen `outboundType` kräver AKS-kluster `vm-set-type` `VirtualMachineScaleSets` med `load-balancer-sku` `Standard`en av och av .
* Om `outboundType` du anger `UDR` ett värde för krävs en användardefinierad väg med giltig utgående anslutning för klustret.
* Om `outboundType` du ställer `UDR` in värdet för innebär att ip-adressen för ingångskällan som dirigeras till belastningsutjämnaren **kanske inte matchar** klustrets utgående utgående utgående måladress.

## <a name="overview-of-outbound-types-in-aks"></a>Översikt över utgående typer i AKS

Ett AKS-kluster kan anpassas med en unik `outboundType` typbelastningsutjämning eller användardefinierad routning.

> [!IMPORTANT]
> Utgående typ påverkar bara utgående trafik i klustret. Mer information [finns i ställa in ingångskontrollanter.](ingress-basic.md)

### <a name="outbound-type-of-loadbalancer"></a>Utgående typ av loadBalancer

Om `loadBalancer` är inställt slutför AKS följande inställning automatiskt. Belastningsutjämnaren används för utgående via en AKS-tilldelad offentlig IP. En utgående `loadBalancer` typ av stöder Kubernetes-tjänster av typen `loadBalancer`, som förväntar sig utgående från belastningsutjämnaren som skapats av AKS-resursprovidern.

Följande inställningar görs av AKS.
   * En offentlig IP-adress har etablerats för klusteravgående.
   * Den offentliga IP-adressen tilldelas belastningsutjämnarens resurs.
   * Serverdelspooler för belastningsutjämnaren är inställningar för agentnoder i klustret.

Nedan visas en nätverkstopologi som distribueras i AKS-kluster som standard, som använder en `outboundType` av `loadBalancer`.

![utgående typ-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Utgående typ av userDefinedRouting

> [!NOTE]
> Att använda utgående typ är ett avancerat nätverksscenario och kräver korrekt nätverkskonfiguration.

Om `userDefinedRouting` är inställt konfigurerar AKS inte automatiskt utgående sökvägar. Följande förväntas göras av **användaren**.

Klustret måste distribueras till ett befintligt virtuellt nätverk med ett undernät som har konfigurerats. Det måste finnas en giltig användardefinierad väg (UDR) i undernätet med utgående anslutning.

AKS-resursprovidern distribuerar en standardbelastningsutjämnare (SLB). Belastningsutjämnaren är inte konfigurerad med några regler och [ådrar sig ingen debitering förrän en regel har placerats](https://azure.microsoft.com/pricing/details/load-balancer/). AKS kommer **inte** automatiskt att etablera en offentlig IP-adress för SLB-frontend. AKS konfigurerar **inte** serverdpoolen för belastningsutjämnarutjämnaren automatiskt.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Distribuera ett kluster med utgående typ av UDR- och Azure-brandvägg

För att illustrera tillämpningen av ett kluster med utgående typ med hjälp av en användardefinierad väg kan ett kluster konfigureras på ett virtuellt nätverk som peer-ats med en Azure-brandvägg.

![Låst topologi](media/egress-outboundtype/outboundtype-udr.png)

* Ingress tvingas flöda genom brandväggsfilter
   * Ett isolerat undernät innehåller en intern belastningsutjämnare för routning till agentnoder
   * Agentnoder är isolerade i ett dedikerat undernät
* Utgående begäranden startar från agentnoder till den interna IP-adressen för Azure-brandväggen med hjälp av en användardefinierad väg
   * Begäranden från AKS-agentnoder följer en UDR som har placerats på undernätet som AKS-klustret har distribuerats till.
   * Azure-brandväggen utgår från det virtuella nätverket från en offentlig IP-frontend
   * Åtkomst till AKS-kontrollplanet skyddas av en NSG, som har aktiverat brandväggens frontsida IP-adress
   * Åtkomst till det offentliga internet eller andra Azure-tjänster flödar till och från brandväggens klientdels IP-adress

### <a name="set-configuration-via-environment-variables"></a>Ange konfiguration via miljövariabler

Definiera en uppsättning miljövariabler som ska användas i resursskapande.

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

Ange sedan prenumerations-ID: er.

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

Etablera ett virtuellt nätverk med tre separata undernät, ett för klustret, ett för brandväggen och ett för tjänstinträngning.

![Tom nätverkstopologi](media/egress-outboundtype/empty-network.png)

Skapa en resursgrupp för att hålla alla resurser.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Skapa två virtuella nätverk som är värdar för AKS-klustret och Azure-brandväggen. Var och en kommer att ha sitt eget undernät. Låt oss börja med AKS-nätverket.

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

Regler för inkommande och utgående Azure-brandväggen måste konfigureras. Huvudsyftet med brandväggen är att göra det möjligt för organisationer att ställa in detaljerade regler för in- och utgående trafik till och från AKS-klustret.

![Brandvägg och UDR](media/egress-outboundtype/firewall-udr.png)

Skapa en vanlig SKU-offentlig IP-resurs som ska användas som Frontend-adress för Azure-brandväggen.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registrera förhandsgranskningsklimattillägget för att skapa en Azure-brandvägg.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

IP-adressen som skapades tidigare kan nu tilldelas brandväggsfrontend.
> [!NOTE]
> Installationen av den offentliga IP-adressen till Azure-brandväggen kan ta några minuter.
> 
> Om fel tas emot upprepade gånger på kommandot nedan tar du bort den befintliga brandväggen och den offentliga IP-adressen och etablerar den offentliga IP- och Azure-brandväggen via portalen samtidigt.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

När föregående kommando har lyckats sparar du brandväggsfrontend IP-adressen för konfiguration senare.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Skapa en UDR med ett hopp till Azure-brandväggen

Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du vill ändra någon av Azures standardroutning gör du det genom att skapa en vägtabell.

Skapa en tom flödestabell som ska associeras med ett visst undernät. Vägtabellen definierar nästa hopp som Azure-brandväggen som skapats ovan. Varje undernät kan ha noll eller en associerad routningstabell.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Se dokumentation för [virtuella nätverksvägstabeller](../virtual-network/virtual-networks-udr-overview.md#user-defined) om hur du kan åsidosätta Azures standardsystemvägar eller lägga till ytterligare vägar i ett undernäts vägtabell.

## <a name="adding-network-firewall-rules"></a>Lägga till regler för nätverksbrandvägg

> [!WARNING]
> Nedan visas ett exempel på att lägga till en brandväggsregel. Alla utgående slutpunkter som definieras i de [egress-slutpunkter som krävs](egress.md) måste aktiveras av programbrandväggsregler för att AKS-kluster ska fungera. Utan dessa slutpunkter aktiverade kan klustret inte fungera.

Nedan följer ett exempel på en nätverks- och programregel. Vi lägger till en nätverksregel som tillåter protokoll, källadress, måladress och målportar. Vi lägger också till en programregel för **några** av de slutpunkter som krävs av AKS.

I ett produktionsscenario bör du endast aktivera åtkomst till nödvändiga slutpunkter för ditt program och de som definieras i [AKS krävs egress](egress.md).

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

Mer information om Azure-brandväggen finns i [dokumentationen](https://docs.microsoft.com/azure/firewall/overview) till Azure Firewall.

## <a name="associate-the-route-table-to-aks"></a>Associera flödestabellen med AKS

Om du vill associera klustret med brandväggen måste det dedikerade undernätet för klustrets undernät referera till den vägtabell som skapats ovan. Association kan göras genom att utfärda ett kommando till det virtuella nätverket som innehåller både klustret och brandväggen för att uppdatera vägtabellen för klustrets undernät.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Distribuera AKS med utgående typ av UDR till det befintliga nätverket

Nu kan ett AKS-kluster distribueras till den befintliga virtuella nätverkskonfigurationen. För att kunna ange en kluster utgående typ till användardefinierad routning måste ett befintligt undernät tillhandahållas AKS.

![aks-distribuera](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Skapa ett tjänsthuvudnamn med åtkomst till etablering i det befintliga virtuella nätverket

Ett huvudnamn för tjänsten används av AKS för att skapa klusterresurser. Tjänstens huvudnamn som skickas vid skapandetillfället används för att skapa underliggande AKS-resurser som virtuella datorer, lagring och belastningsutjämnare som används av AKS. Om det beviljas för få behörigheter kan det inte etablera ett AKS-kluster.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Nu ersätta `APPID` `PASSWORD` och nedan med tjänstens huvudnamn appid och tjänstens huvudlösenord automatiskt som skapas av föregående kommandoutdata. Vi refererar till VNET-resurs-ID för att bevilja behörigheter till tjänstens huvudnamn så att AKS kan distribuera resurser till det.

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

Slutligen kan AKS-klustret distribueras till det befintliga undernätet som vi har dedikerat för klustret. Målundernätet som ska distribueras till definieras `$SUBNETID`med miljövariabeln . Vi definierade inte `$SUBNETID` variabeln i föregående steg. Om du vill ange värdet för undernäts-ID:et kan du använda följande kommando:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Vi definierar den utgående typen för att följa UDR som finns på undernätet, vilket gör att AKS kan hoppa över inställningar och IP-etablering för belastningsutjämnaren som nu kan vara strikt intern.

AKS-funktionen för [API-serverauktoriserat IP-intervall](api-server-authorized-ip-ranges.md) kan läggas till för att begränsa API-serveråtkomsten till endast brandväggens offentliga slutpunkt. Funktionen för auktoriserade IP-intervall betecknas i diagrammet som NSG som måste skickas för att komma åt kontrollplanet. När du aktiverar den auktoriserade IP-intervallfunktionen för att begränsa API-serveråtkomsten måste utvecklarverktygen använda en jumpbox från brandväggens virtuella nätverk eller lägga till alla utvecklarslutpunkter i det auktoriserade IP-intervallet.

> [!TIP]
> Ytterligare funktioner kan läggas till i klusterdistributionen, till exempel (privat kluster)[]. När du använder auktoriserade IP-intervall krävs en jumpbox inuti klusternätverket för att komma åt API-servern.

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

### <a name="enable-developer-access-to-the-api-server"></a>Aktivera utvecklaråtkomst till API-servern

På grund av den auktoriserade IP-intervallkonfigurationen för klustret måste du lägga till IP-adresser för utvecklarverktyg i AKS-klusterlistan över godkända IP-intervall för att komma åt API-servern. Ett annat alternativ är att konfigurera en jumpbox med det verktyg som behövs i ett separat undernät i brandväggens virtuella nätverk.

Lägga till ytterligare en IP-adress i de godkända intervallen med följande kommando

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Använd kommandot [az aks get-credentials][az-aks-get-credentials] för att konfigurera `kubectl` för att ansluta till ditt nyskapade Kubernetes-kluster. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>Ställ in den interna belastningsutjämnaren

AKS har distribuerat en belastningsutjämnare med klustret som kan ställas in som en [intern belastningsutjämnare](internal-lb.md).

Om du vill skapa en intern belastningsutjämnare skapar du ett tjänstmanifest med namnet internal-lb.yaml med tjänsttypen LoadBalancer och den azure-load-balancer-intern anteckningen som visas i följande exempel:

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

Distribuera den interna belastningsutjämnaren med kubectl-ansök och ange namnet på YAML-manifestet:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Distribuera en Kubernetes-tjänst

Eftersom klusterutgående typ anges som UDR, så slutförs inte associera agentnoderna som serverdelspoolen för belastningsutjämnaren automatiskt av AKS vid klusterskapande tid. Backend pool association hanteras dock av Kubernetes Azure-molnprovidern när Kubernetes-tjänsten distribueras.

Distribuera Azure-röstningsappprogrammet genom att kopiera yaml `example.yaml`nedan till en fil med namnet .

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

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Lägga till en DNAT-regel i Azure-brandväggen

För att konfigurera inkommande anslutning måste en DNAT-regel skrivas till Azure-brandväggen. För att testa anslutningen till vårt kluster definieras en regel för brandväggen frontend offentlig IP-adress för att dirigera till den interna IP som exponeras av den interna tjänsten.

Måladressen kan anpassas eftersom det är porten på brandväggen som ska nås. Den översatta adressen måste vara IP-adressen för den interna belastningsutjämnaren. Den översatta porten måste vara den exponerade porten för din Kubernetes-tjänst.

Du måste ange den interna IP-adressen som tilldelats belastningsutjämnaren som skapats av kubernetes-tjänsten. Hämta adressen genom att köra:

```bash
kubectl get services
```

Den IP-adress som behövs visas i kolumnen EXTERN-IP, liknande följande.

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
> När du tar bort den interna tjänsten Kubernetes, om den interna belastningsutjämnaren inte längre används av någon tjänst, tar Azure-molnleverantören bort den interna belastningsutjämnaren. Vid nästa tjänstdistribution distribueras en belastningsutjämnare om ingen kan hittas med den begärda konfigurationen.

Om du vill rensa Azure-resurser tar du bort AKS-resursgruppen.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Verifiera anslutning

Navigera till AZURE-brandväggens frontend-IP-adress i en webbläsare för att validera anslutningen.

Du bör se en bild av Azure-röstningsappen.

## <a name="next-steps"></a>Nästa steg

Se [ÖVERSIKT över UDR för Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Se [hur du skapar, ändrar eller tar bort en vägtabell](https://docs.microsoft.com/azure/virtual-network/manage-route-table).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
