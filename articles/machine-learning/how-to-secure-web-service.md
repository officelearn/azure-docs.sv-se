---
title: Säkra webbtjänster med TLS
titleSuffix: Azure Machine Learning
description: Lär dig hur du aktiverar HTTPS för att skydda en webbtjänst som distribueras via Azure Machine Learning. Azure Machine Learning använder TLS version 1.2 för att skydda modeller som distribueras som webbtjänster.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: a58b0120feaba907c62bc646f4f85d9185227fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287347"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Använda TLS för att skydda en webbtjänst via Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln visar hur du skyddar en webbtjänst som distribueras via Azure Machine Learning.

Du använder [HTTPS](https://en.wikipedia.org/wiki/HTTPS) för att begränsa åtkomsten till webbtjänster och skydda de data som klienter skickar. HTTPS hjälper till att skydda kommunikationen mellan en klient och en webbtjänst genom att kryptera kommunikation mellan de två. Kryptering använder [TLS (Transport Layer Security).](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS kallas ibland fortfarande *SSL (Secure Sockets Layer),* som var föregångaren till TLS.

> [!TIP]
> Azure Machine Learning SDK använder termen "SSL" för egenskaper som är relaterade till säker kommunikation. Detta betyder inte att webbtjänsten inte använder *TLS*. SSL är bara en mer allmänt erkänd term.
>
> Mer specifikt stöder webbtjänster som distribueras via Azure Machine Learning endast TLS version 1.2.

TLS och SSL förlitar sig båda på digitala certifikat , vilket hjälper till med kryptering och *identitetsverifiering.* Mer information om hur digitala certifikat fungerar finns i Wikipedia-avsnittet [Offentlig nyckelinfrastruktur](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Om du inte använder HTTPS för webbtjänsten kan data som skickas till och från tjänsten vara synliga för andra på internet.
>
> HTTPS gör det också möjligt för klienten att verifiera äktheten hos den server som den ansluter till. Den här funktionen skyddar klienter mot [man-in-the-middle-attacker.](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)

Detta är den allmänna processen för att säkra en webbtjänst:

1. Skaffa ett domännamn.

2. Skaffa ett digitalt certifikat.

3. Distribuera eller uppdatera webbtjänsten med TLS aktiverat.

4. Uppdatera DNS för att peka på webbtjänsten.

> [!IMPORTANT]
> Om du distribuerar till Azure Kubernetes Service (AKS) kan du köpa ditt eget certifikat eller använda ett certifikat som tillhandahålls av Microsoft. Om du använder ett certifikat från Microsoft behöver du inte skaffa ett domännamn eller TLS/SSL-certifikat. Mer information finns i avsnittet [Aktivera TLS och distribuera](#enable) i den här artikeln.

Det finns små skillnader när du säkrar s över [distributionsmål](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Skaffa ett domännamn

Om du inte redan äger ett domännamn köper du ett från en *domännamnsregistratorer*. Processen och priset skiljer sig åt mellan registratorer. Registratorn tillhandahåller verktyg för att hantera domännamnet. Du använder dessa verktyg för att mappa ett fullständigt kvalificerat\.domännamn (FQDN) (till exempel www contoso.com) till den IP-adress som är värd för din webbtjänst.

## <a name="get-a-tlsssl-certificate"></a>Skaffa ett TLS/SSL-certifikat

Det finns många sätt att få ett TLS/SSL-certifikat (digitalt certifikat). Det vanligaste är att köpa en från en *certifikatutfärdarmyndighet.* Oavsett var du får certifikatet behöver du följande filer:

* Ett **certifikat**. Certifikatet måste innehålla hela certifikatkedjan och måste vara "PEM-kodad".
* En **nyckel**. Nyckeln måste också pem-kodas.

När du begär ett certifikat måste du ange FQDN för den adress som du\.planerar att använda för webbtjänsten (till exempel www contoso.com). Adressen som är stämplad i certifikatet och adressen som klienterna använder jämförs med att verifiera webbtjänstens identitet. Om adresserna inte matchar får klienten ett felmeddelande.

> [!TIP]
> Om certifikatutfärdaren inte kan ange certifikatet och nyckeln som PEM-kodade filer kan du använda ett verktyg som [OpenSSL](https://www.openssl.org/) för att ändra formatet.

> [!WARNING]
> Använd *självsignerade* certifikat endast för utveckling. Använd dem inte i produktionsmiljöer. Självsignerade certifikat kan orsaka problem i klientprogrammen. Mer information finns i dokumentationen för de nätverksbibliotek som klientprogrammet använder.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a>Aktivera TLS och distribuera

Om du vill distribuera (eller distribuera om) tjänsten med TLS aktiverat ställer du in *parametern ssl_enabled* till "True" var den än är tillämplig. Ange *parametern ssl_certificate* till *certifikatfilens* värde. Ange *ssl_key* till *nyckelfilens* värde.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Distribuera på AKS och fältprogrammerbar grindmatris (FPGA)

  > [!NOTE]
  > Informationen i det här avsnittet gäller även när du distribuerar en säker webbtjänst för designern. Om du inte är bekant med att använda Python SDK läser du [Vad är Azure Machine Learning SDK för Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

När du distribuerar till AKS kan du skapa ett nytt AKS-kluster eller bifoga ett befintligt. Mer information om hur du skapar eller bifogar ett kluster finns i [Distribuera en modell till ett Azure Kubernetes Service-kluster](how-to-deploy-azure-kubernetes-service.md).
  
-  Om du skapar ett nytt kluster använder du **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**.
- Om du ansluter ett befintligt kluster använder du **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**. Båda returnerar ett konfigurationsobjekt som har en **enable_ssl** metod.

Metoden **enable_ssl** kan använda ett certifikat som tillhandahålls av Microsoft eller ett certifikat som du köper.

  * När du använder ett certifikat från Microsoft måste du använda *parametern leaf_domain_label.* Den här parametern genererar DNS-namnet för tjänsten. Ett värde på "contoso" skapar till exempel ett domännamn\<med "contoso sex-random-characters>. \<azureregion>.cloudapp.azure.com", där \<azureregion> är den region som innehåller tjänsten. Du kan också använda parametern *overwrite_existing_domain* för att skriva över befintliga *leaf_domain_label*.

    Om du vill distribuera (eller distribuera om) tjänsten med TLS aktiverat ställer du in *parametern ssl_enabled* till "True" var den än är tillämplig. Ange *parametern ssl_certificate* till *certifikatfilens* värde. Ange *ssl_key* till *nyckelfilens* värde.

    > [!IMPORTANT]
    > När du använder ett certifikat från Microsoft behöver du inte köpa ett eget certifikat eller domännamn.

    I följande exempel visas hur du skapar en konfiguration som aktiverar ett TLS/SSL-certifikat från Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * När du använder *ett certifikat som du har köpt*använder du *parametrarna ssl_cert_pem_file,* *ssl_key_pem_file*och *ssl_cname.* I följande exempel visas hur du använder *.pem-filer* för att skapa en konfiguration som använder ett TLS/SSL-certifikat som du har köpt:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Mer information om *enable_ssl*finns i [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) och [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Distribuera på Azure Container-instanser

När du distribuerar till Azure Container Instances anger du värden för TLS-relaterade parametrar, vilket visas i följande kodavsnitt:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Mer information finns i [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Uppdatera din DNS

Därefter måste du uppdatera DNS för att peka på webbtjänsten.

+ **För containerinstanser:**

  Använd verktygen från domännamnsregistraren för att uppdatera DNS-posten för ditt domännamn. Posten måste peka på tjänstens IP-adress.

  Det kan ta en fördröjning på minuter eller timmar innan klienter kan matcha domännamnet, beroende på registratorn och "tiden att leva" (TTL) som är konfigurerad för domännamnet.

+ **För AKS:**

  > [!WARNING]
  > Om du använde *leaf_domain_label* för att skapa tjänsten med hjälp av ett certifikat från Microsoft ska du inte uppdatera DNS-värdet manuellt för klustret. Värdet ska ställas in automatiskt.

  Uppdatera DNS för AKS-klustrets offentliga IP-adress på fliken **Konfiguration** under **Inställningar** i den vänstra rutan. (Se följande bild.) Den offentliga IP-adressen är en resurstyp som skapas under resursgruppen som innehåller AKS-agentnoderna och andra nätverksresurser.

  [![Azure Machine Learning: Skydda webbtjänster med TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Uppdatera TLS/SSL-certifikatet

TLS/SSL-certifikat upphör att gälla och måste förnyas. Vanligtvis händer detta varje år. Använd informationen i följande avsnitt för att uppdatera och förnya certifikatet för modeller som distribueras till Azure Kubernetes Service:

### <a name="update-a-microsoft-generated-certificate"></a>Uppdatera ett Microsoft-genererat certifikat

Om certifikatet ursprungligen genererades av Microsoft (när du använder *leaf_domain_label* för att skapa tjänsten) använder du något av följande exempel för att uppdatera certifikatet:

**Använd SDK**

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

Mer information finns i följande referensdokument:

* [SslConfiguration (SSLKonfigurering)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateKonfigurering](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Uppdatera anpassat certifikat

Om certifikatet ursprungligen genererades av en certifikatutfärdare gör du följande:

1. Använd dokumentationen som tillhandahålls av certifikatutfärdaren för att förnya certifikatet. Den här processen skapar nya certifikatfiler.

1. Använd antingen SDK eller CLI för att uppdatera tjänsten med det nya certifikatet:

    **Använd SDK**

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

Mer information finns i följande referensdokument:

* [SslConfiguration (SSLKonfigurering)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateKonfigurering](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>Inaktivera TLS

Om du vill inaktivera TLS för en modell som `SslConfiguration` `status="Disabled"`distribueras till Azure Kubernetes Service skapar du en med och utför sedan en uppdatering:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Nästa steg
Lär dig att:
+ [Använda en maskininlärningsmodell som distribueras som en webbtjänst](how-to-consume-web-service.md)
+ [Kör experiment och slutsatser på ett säkert sätt i ett virtuellt Azure-nätverk](how-to-enable-virtual-network.md)
