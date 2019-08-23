---
title: Skydda webb tjänster med hjälp av SSL
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skyddar en webb tjänst som distribueras via Azure Machine Learnings tjänsten genom att aktivera HTTPS. HTTPS säkrar data från av klienter genom att använda TLS (Transport Layer Security), en ersättning för SSL (Secure Sockets Layer). Klienter använder också HTTPS för att verifiera webb tjänstens identitet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: 5a2cab9dff4a075545d919cb41e72cf6e446e9d2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897339"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Använd SSL för att skydda en webb tjänst via Azure Machine Learning

Den här artikeln visar hur du skyddar en webb tjänst som distribueras via Azure Machine Learnings tjänsten.

Du använder [https](https://en.wikipedia.org/wiki/HTTPS) för att begränsa åtkomsten till webb tjänster och säkra de data som klienter skickar. HTTPS skyddar kommunikationen mellan en klient och en webb tjänst genom att kryptera kommunikationen mellan de två. Kryptering använder [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS kallas ibland även *Secure Sockets Layer* (SSL), som var den föregående aktiviteten TLS.

> [!TIP]
> Azure Machine Learning SDK använder termen "SSL" för egenskaper som är relaterade till säker kommunikation. Detta innebär inte att din webb tjänst inte använder *TLS*. SSL är bara en oftare erkänd term.

TLS och SSL är beroende av *digitala certifikat*, som hjälper till med kryptering och identitets verifiering. Mer information om hur digitala certifikat fungerar finns i avsnittet om infrastrukturen för [offentliga nycklar](https://en.wikipedia.org/wiki/Public_key_infrastructure)i Wikipedia-ämnet.

> [!WARNING]
> Om du inte använder HTTPS för din webb tjänst kan data som skickas till och från tjänsten vara synliga för andra på Internet.
>
> HTTPS gör det också möjligt för klienten att verifiera äktheten på den server som den ansluter till. Den här funktionen skyddar klienter mot [man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) -attacker.

Detta är den allmänna processen för att skydda en webb tjänst:

1. Få ett domännamn.

2. Skaffa ett digitalt certifikat.

3. Distribuera eller uppdatera webb tjänsten med SSL aktiverat.

4. Uppdatera din DNS så att den pekar till webbtjänsten.

> [!IMPORTANT]
> Om du distribuerar till Azure Kubernetes service (AKS) kan du köpa ditt eget certifikat eller använda ett certifikat från Microsoft. Om du använder ett certifikat från Microsoft behöver du inte skaffa ett domän namn eller SSL-certifikat. Mer information finns i avsnittet [Aktivera SSL och distribution](#enable) i den här artikeln.

Det finns små skillnader när du skyddar webb tjänster mellan [distributions mål](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Få ett domännamn

Om du inte redan har ett domän namn kan du köpa ett från en *domän namns registrator*. Processen och priset skiljer sig mellan registratorn. Registratorn innehåller verktyg för att hantera domän namnet. Du använder dessa verktyg för att mappa ett fullständigt kvalificerat domän namn (FQDN) (till\.exempel www-contoso.com) till den IP-adress som är värd för webb tjänsten.

## <a name="get-an-ssl-certificate"></a>Hämta ett SSL-certifikat

Det finns många sätt att hämta ett SSL-certifikat (digitalt certifikat). Det vanligaste är att köpa en från en *certifikat utfärdare* (ca). Oavsett var du får certifikatet behöver du följande filer:

* En **certifikat**. Certifikatet måste innehålla den fullständiga certifikat kedjan och måste vara "PEM-kodad".
* En **nyckeln**. Nyckeln måste också vara PEM-kodad.

När du begär ett certifikat måste du ange det fullständiga domän namnet för den adress som du planerar att använda för webb tjänsten (till exempel www\.-contoso.com). Adressen som stämplas in i certifikatet och den adress som klienterna använder jämförs för att verifiera webb tjänstens identitet. Om dessa adresser inte matchar får klienten ett fel meddelande.

> [!TIP]
> Om certifikat utfärdaren inte kan ange certifikatet och nyckeln som PEM-kodade filer kan du använda ett verktyg som [openssl](https://www.openssl.org/) för att ändra formatet.

> [!WARNING]
> Använd endast självsignerade certifikat för utveckling. Använd dem inte i produktions miljöer. Självsignerade certifikat kan orsaka problem i din klient program. Mer information finns i dokumentationen för de nätverks bibliotek som används av klient programmet.

## <a id="enable"></a>Aktivera SSL och distribuera

Om du vill distribuera (eller distribuera om) tjänsten med SSL aktiverat, anger du parametern *ssl_enabled* till "true" oavsett var den gäller. Ange *ssl_certificate* -parametern till värdet för *certifikat* filen. Ange *ssl_key* till *nyckel* filens värde.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Distribuera på AKS och Field-programmerbar grind mat ris (FPGA)

  > [!NOTE]
  > Informationen i det här avsnittet gäller även när du distribuerar en säker webb tjänst för det visuella gränssnittet. Om du inte är bekant med att använda python SDK, se [Vad är Azure Machine Learning SDK för python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

När du distribuerar till AKS kan du skapa ett nytt AKS-kluster eller koppla ett befintligt.
  
-  Om du skapar ett nytt kluster använder du **[AksCompute. provisionining_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** .
- Om du ansluter ett befintligt kluster använder du **[AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Båda returnerar ett konfigurations objekt som har en **enable_ssl** -metod.

Metoden **enable_ssl** kan använda ett certifikat från Microsoft eller ett certifikat som du köper.

  * När du använder ett certifikat från Microsoft måste du använda parametern *leaf_domain_label* . Den här parametern genererar DNS-namnet för tjänsten. Till exempel skapar värdet "unservice" ett domän namn för "Mina tjänster\<med sex-slumpmässiga tecken >.\< azureregion >. cloudapp. Azure. com ", där \<azureregion > är den region som innehåller tjänsten. Alternativt kan du använda parametern *overwrite_existing_domain* för att skriva över den befintliga *leaf_domain_label*.

    Om du vill distribuera (eller distribuera om) tjänsten med SSL aktiverat, anger du parametern *ssl_enabled* till "true" oavsett var den gäller. Ange *ssl_certificate* -parametern till värdet för *certifikat* filen. Ange *ssl_key* till *nyckel* filens värde.

    > [!IMPORTANT]
    > När du använder ett certifikat från Microsoft behöver du inte köpa ditt eget certifikat eller domän namn.

    Följande exempel visar hur du skapar en konfiguration som aktiverar ett SSL-certifikat från Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * När du använder *ett certifikat som du har köpt*använder du parametrarna *ssl_cert_pem_file*, *ssl_key_pem_file*och *ssl_cname* . Följande exempel visar hur du använder *. pem* -filer för att skapa en konfiguration som använder ett SSL-certifikat som du har köpt:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Mer information om *enable_ssl*finns i [AksProvisioningConfiguration. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) och [AksAttachConfiguration. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Distribuera på Azure Container Instances

När du distribuerar till Azure Container Instances anger du värden för SSL-relaterade parametrar, eftersom följande kodfragment visar:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Mer information finns i [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-).

## <a name="update-your-dns"></a>Uppdatera din DNS-Server

Därefter måste du uppdatera din DNS så att den pekar till webbtjänsten.

+ **För Container Instances:**

  Använd verktygen från din domän namns registrator för att uppdatera DNS-posten för ditt domän namn. Posten måste peka på tjänstens IP-adress.

  Det kan vara en fördröjning på minuter eller timmar innan klienter kan matcha domän namnet, beroende på registratorn och TTL-värdet (Time to Live) som har kon figurer ATS för domän namnet.

+ **För AKS:**

  > [!WARNING]
  > Om du har använt *leaf_domain_label* för att skapa tjänsten med hjälp av ett certifikat från Microsoft uppdaterar du inte DNS-värdet manuellt för klustret. Värdet ska anges automatiskt.

  Uppdatera DNS för den offentliga IP-adressen för AKS-klustret på fliken **konfiguration** under **Inställningar** i det vänstra fönstret. (Se följande bild.) Den offentliga IP-adressen är en resurs typ som skapas under resurs gruppen som innehåller AKS-agentens noder och andra nätverks resurser.

  [![Azure Machine Learning tjänst: Skydda webb tjänster med SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-ssl-certificate"></a>Uppdatera SSL-certifikatet

SSL-certifikat upphör att gälla och måste förnyas. Detta sker vanligt vis varje år. Använd informationen i följande avsnitt för att uppdatera och förnya ditt certifikat för modeller som distribueras till Azure Kubernetes-tjänsten:

### <a name="update-a-microsoft-generated-certificate"></a>Uppdatera ett Microsoft-genererat certifikat

Om certifikatet ursprungligen genererades av Microsoft (när du använder *leaf_domain_label* för att skapa tjänsten) kan du använda något av följande exempel för att uppdatera certifikatet:

**Använd SDK: n**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Använda CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

Mer information finns i följande referens dokument:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Uppdatera anpassat certifikat

Om certifikatet ursprungligen genererades av en certifikat utfärdare kan du använda följande steg:

1. Använd dokumentationen från certifikat utfärdaren för att förnya certifikatet. Den här processen skapar nya certifikatfiler.

1. Använd antingen SDK eller CLI för att uppdatera tjänsten med det nya certifikatet:

    **Använd SDK: n**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Använda CLI**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Mer information finns i följande referens dokument:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-ssl"></a>Inaktivera SSL

Om du vill inaktivera SSL för en modell som distribueras till Azure Kubernetes- `SslConfiguration` tjänsten `status="Disabled"`skapar du en med och utför sedan en uppdatering:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable SSL
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Nästa steg
Lär dig att:
+ [Använda en maskin inlärnings modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
+ [Köra experiment och härledning på ett säkert sätt i ett virtuellt Azure-nätverk](how-to-enable-virtual-network.md)
