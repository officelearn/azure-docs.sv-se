---
title: Skydda webbtjänster med hjälp av SSL
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skyddar en webbtjänst som har distribuerats via Azure Machine Learning-tjänsten genom att aktivera HTTPS. HTTPS skyddar data från klienter med hjälp av transport layer security (TLS), en ersättning för secure socket lager (SSL). Klienter använder också HTTPS för att kontrollera identiteten på webbtjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: c176458cfc404a9d55d7fb71a36ea63110b3a6d6
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657957"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Använda SSL för att skydda en webbtjänst via Azure Machine Learning

Den här artikeln visar hur du skyddar en webbtjänst som har distribuerats via Azure Machine Learning-tjänsten.

Du använder [HTTPS](https://en.wikipedia.org/wiki/HTTPS) att begränsa åtkomst till webbtjänster och skydda data som klienter skickar. HTTPS hjälper att skydda kommunikationen mellan en klient och en webbtjänst genom att kryptera kommunikationen mellan två. Kryptering använder [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS är fortfarande kallas ibland *Secure Sockets Layer* (SSL), som var föregående av TLS.

> [!TIP]
> Azure Machine Learning SDK använder termen ”SSL” för egenskaper som är relaterade till säker kommunikation. Detta innebär inte att webbtjänsten inte använder *TLS*. SSL är bara ett mer allmänt erkända begrepp.

TLS och SSL nåde förlitar sig på *digitala certifikat*, som hjälper med kryptering och identitet verifiering. Mer information om hur digitala certifikat fungerar finns i avsnittet Wikipedia [offentlig nyckelinfrastruktur](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Om du inte använder HTTPS för din webbtjänst kan vara data som skickas till och från tjänsten synliga för andra på internet.
>
> HTTPS kan också klienten för att kontrollera att den server som den ansluter till. Den här funktionen skyddar klienter mot [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) attacker.

Det här är den allmänna processen för att skydda en webbtjänst:

1. Få ett domännamn.

2. Få ett digitalt certifikat.

3. Distribuera eller uppdatera webbtjänsten med SSL aktiverat.

4. Uppdatera din DNS så att den pekar till webbtjänsten.

> [!IMPORTANT]
> Om du distribuerar till Azure Kubernetes Service (AKS), kan du köpa egna certifikat eller använda ett certifikat som tillhandahålls av Microsoft. Om du använder ett certifikat från Microsoft, behöver du inte hämta en domän- eller SSL-certifikat. Mer information finns i den [aktivera SSL och distribuera](#enable) i den här artikeln.

Det finns mindre skillnader när du skyddar webbtjänster över [distributionskanaler](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Få ett domännamn

Om du inte redan har ett domännamn, köpa ett från en *domännamnsregistratorn*. Skiljer sig mellan registratorer processen och pris. Registratorn innehåller verktyg för att hantera domännamnet. Du använder dessa verktyg för att mappa ett fullständigt kvalificerat domännamn (FQDN) (till exempel www\.contoso.com) till IP-adressen som är värd för webbtjänsten.

## <a name="get-an-ssl-certificate"></a>Hämta ett SSL-certifikat

Det finns många sätt att få ett SSL-certifikat (digitala certifikat). De vanligaste är att köpa en från en *certifikatutfärdare* (CA). Oavsett var du hämtar certifikatet, behöver du följande filer:

* En **certifikat**. Certifikatet måste innehålla fullständig certifikatkedjan och den måste vara ”PEM-kodat”.
* En **nyckeln**. Nyckeln måste också vara PEM-kodat.

När du begär ett certifikat måste du ange det fullständiga Domännamnet för den adress som du planerar att använda för webbtjänsten (till exempel www\.contoso.com). Adressen som är stämplad i certifikatet och den adress som klienterna använda jämförs för att verifiera identiteten för webbtjänsten. Om de adresserna inte matchar får klienten ett felmeddelande.

> [!TIP]
> Om certifikatutfärdaren inte kan tillhandahålla certifikat och nyckel som PEM-kodat filer, kan du använda ett verktyg som [OpenSSL](https://www.openssl.org/) att ändra format.

> [!WARNING]
> Använd *självsignerade* certifikat endast för utveckling. Använd inte dem i produktionsmiljöer. Självsignerade certifikat kan orsaka problem i din klient program. Mer information finns i dokumentationen för nätverksbibliotek som använder klientprogrammet.

## <a id="enable"></a> Aktivera SSL och distribuera

Om du vill distribuera (eller distribuera om) tjänsten med SSL aktiverat, ange den *ssl_enabled* parametern till ”True” oavsett var de är tillämpliga. Ange den *ssl_certificate* parametern till värdet för den *certifikat* fil. Ange den *ssl_key* till värdet för den *nyckel* fil.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Distribuera på AKS och fältet-programmable gate array FPGA)

  > [!NOTE]
  > Informationen i det här avsnittet gäller även när du distribuerar en säker webbtjänst för det visuella gränssnittet. Om du inte är bekant med Python SDK kan se [vad är Azure Machine Learning-SDK för Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

När du distribuerar till AKS kan du skapa ett nytt AKS-kluster eller bifoga en befintlig.
  
-  Om du skapar ett nytt kluster kan du använda  **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** .
- Om du kopplar ett befintligt kluster kan du använda  **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Båda att returnera ett konfigurationsobjekt som har en **enable_ssl** metod.

Den **enable_ssl** metod kan använda ett certifikat som tillhandahålls av Microsoft eller ett certifikat som du köper.

  * När du använder ett certifikat från Microsoft, måste du använda den *leaf_domain_label* parametern. Den här parametern genererar DNS-namn för tjänsten. Till exempel värdet ”mintjänst” skapar ett domännamn på ”mintjänst\<sex slumpmässiga tecken >.\< azureregion >. cloudapp.azure.com ”, där \<azureregion > är den region som innehåller tjänsten. Du kan även använda den *overwrite_existing_domain* parameter för att skriva över den befintliga *leaf_domain_label*.

    Om du vill distribuera (eller distribuera om) tjänsten med SSL aktiverat, ange den *ssl_enabled* parametern till ”True” oavsett var de är tillämpliga. Ange den *ssl_certificate* parametern till värdet för den *certifikat* fil. Ange den *ssl_key* till värdet för den *nyckel* fil.

    > [!IMPORTANT]
    > När du använder ett certifikat från Microsoft, behöver du inte köpa egna certifikat eller domän namn.

    I följande exempel visar hur du skapar en konfiguration som gör att ett SSL-certifikat från Microsoft:

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

  * När du använder *ett certifikat som du har köpt*, du använder den *ssl_cert_pem_file*, *ssl_key_pem_file*, och *ssl_cname* parametrar. I följande exempel visar hur du använder *.pem* filer för att skapa en konfiguration som använder ett SSL-certifikat som du har köpt:

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

Mer information om *enable_ssl*, se [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) och [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Distribuera i Azure Container Instances

När du distribuerar till Azure Container Instances kan ange du värden för SSL-relaterade parametrar, som i följande kodutdrag:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Mer information finns i [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-).

## <a name="update-your-dns"></a>Uppdatera din DNS-Server

Därefter måste du uppdatera din DNS så att den pekar till webbtjänsten.

+ **För Container Instances:**

  Använda verktyg från din domänregistrator för att uppdatera DNS-posten för ditt domännamn. Posten måste peka på IP-adressen för tjänsten.

  Det kan finnas en fördröjning på minuter eller timmar innan klienter kan matcha domännamnet, beroende på Registratorn och ”time to live” (TTL) som är konfigurerad för domännamnet.

+ **För AKS:**

  > [!WARNING]
  > Om du har använt *leaf_domain_label* för att skapa tjänsten med hjälp av ett certifikat från Microsoft, manuellt inte uppdatera DNS-värdet för klustret. Värdet ska anges automatiskt.

  Uppdatera DNS på den **Configuration** fliken offentliga IP-adressen för AKS-klustret. (Se följande bild). Offentliga IP-adress är en resurstyp som har skapats under resursgruppen med agentnoderna AKS och andra nätverksresurser.

  ![Azure Machine Learning-tjänsten: Skydda webbtjänster med SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Nästa steg
Lär dig att:
+ [Använda en maskininlärningsmodell som distribueras som en webbtjänst](how-to-consume-web-service.md)
+ [Kör säkert experiment och inferens i Azure-nätverk](how-to-enable-virtual-network.md)
