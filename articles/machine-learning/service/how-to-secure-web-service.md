---
title: Säkra webbtjänster med SSL
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skyddar en webbtjänst som distribueras med Azure Machine Learning-tjänsten genom att aktivera HTTPS. HTTPS skyddar de data som skickats av klienterna som använder transport layer security (TLS), en ersättning för secure socket lager (SSL). Den används också av klienter för att verifiera identiteten för webbtjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 50e42172af6ca6b966f9f60d3e037f9ae3dc5cbe
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023766"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Använda SSL för att skydda webbtjänster med Azure Machine Learning-tjänsten

I den här artikeln får du lära dig hur du skyddar en webbtjänst som distribueras med Azure Machine Learning-tjänsten. Du kan begränsa åtkomsten till webbtjänster och skydda data som skickats av klienter som använder [Hypertext Transfer Protocol Secure (HTTPS)](https://en.wikipedia.org/wiki/HTTPS).

HTTPS används för att skydda kommunikationen mellan en klient och din webbtjänst genom att kryptera kommunikationen mellan två. Kryptering hanteras med [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Ibland refereras TLS fortfarande till som SSL Secure Sockets Layer (), som var föregångare till TLS.

> [!TIP]
> Azure Machine Learning SDK använder termen ”SSL” för egenskaper som har att göra säker kommunikation. Detta innebär inte att TLS används inte av din webbtjänst, precis som SSL är mer identifierbara giltighetstiden för många läsare.

TLS och SSL nåde förlitar sig på __digitala certifikat__, som används för att utföra verifiering av kryptering och identitet. Mer information om hur digitala certifikat fungerar finns i Wikipedia-posten på [offentlig nyckelinfrastruktur (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Om du inte aktivera och använda HTTPS för din webbtjänst, kanske data som skickas till tjänsten visas vidare till andra på internet.
>
> HTTPS kan också klienten för att kontrollera att den server som den ansluter till. Detta skyddar klienter mot [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) attacker.

Den allmänna processen för att skydda en ny webbtjänst eller en befintlig är följande:

1. Få ett domännamn.

2. Få ett digitalt certifikat.

3. Distribuera eller uppdatera webbtjänsten med SSL-inställningen aktiverad.

4. Uppdatera din DNS så att den pekar till webbtjänsten.

> [!IMPORTANT]
> Om du distribuerar till Azure Kubernetes Service (AKS), kan du tillhandahålla ditt eget certifikat eller använda ett certifikat som tillhandahålls av Microsoft. Om du använder Microsoft tillhandahåller certifikat behöver du inte att hämta en domän- eller SSL-certifikat. Mer information finns i den [aktivera SSL och distribuera](#enable) avsnittet.

Det finns mindre skillnader när du skyddar webbtjänster över den [distributionskanaler](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Få ett domännamn

Om du inte redan har ett domännamn kan du köpa en från en __domännamnsregistratorn__. Processen skiljer sig mellan registratorer, precis kostnaden. Registratorn ger dig också med verktyg för att hantera domännamnet. Dessa verktyg används för att mappa ett fullständigt kvalificerat domännamn (till exempel www\.contoso.com) till IP-adressen som är värd för webbtjänsten.

## <a name="get-an-ssl-certificate"></a>Hämta ett SSL-certifikat

Det finns många sätt att få ett SSL-certifikat (digitala certifikat). De vanligaste är att köpa en från en __certifikatutfärdare__ (CA). Oavsett om du har fått certifikatet, behöver du följande filer:

* En __certifikat__. Certifikatet måste innehålla fullständig certifikatkedjan och måste vara PEM-kodat.
* En __nyckeln__. Nyckeln måste vara PEM-kodat.

När du begär ett certifikat, måste du ange det fullständigt kvalificerade domännamnet (FQDN) för den adress som du planerar att använda för webbtjänsten. Till exempel www\.contoso.com. Den adressen stämplad i certifikatet och den adress som används av klienterna jämförs vid verifiering av identiteten för webbtjänsten. Om adresserna som inte matchar får klienterna ett felmeddelande.

> [!TIP]
> Om certifikatutfärdaren inte kan tillhandahålla certifikat och nyckel som PEM-kodat filer, kan du använda ett verktyg som [OpenSSL](https://www.openssl.org/) att ändra format.

> [!WARNING]
> Självsignerat certifikat ska användas endast för utveckling. De bör inte användas i produktion. Självsignerade certifikat kan orsaka problem i din klient program. Mer information finns i dokumentationen för nätverksbibliotek som används i klientprogrammet.

## <a id="enable"></a> Aktivera SSL och distribuera

Om du vill distribuera (eller distribuera om) tjänsten med SSL aktiverat, ange den `ssl_enabled` parameter `True`, kan du när det är tillämpligt. Ange den `ssl_certificate` parametern till värdet för den __certifikat__ fil och `ssl_key` till värdet för den __nyckel__ fil.

+ **Visual gränssnitt – skapa säkra Azure Kubernetes Service (AKS) för distribution** 
    
    Ta hänsyn till detta om du vill skapa säkra distribuera beräkning för visuella gränssnittet. Ange värden för SSL-relaterade parametrar när du etablerar AKS-kluster, och klicka sedan skapa en ny AKS.  Finns nedan kodfragmentet:
    

    > [!TIP]
    >  Om du inte är bekant med Python SDK kan starta från [Azure Python SDK översikt över Machine Learning.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


    ```python
    from azureml.core.compute import AksCompute, ComputeTarget

    # Provide SSL-related parameters when provisioning the AKS cluster
    prov_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")   
 
    aks_name = 'secure-aks'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace = ws,
                                        name = aks_name,
                                        provisioning_configuration = prov_config)
    
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```
    
   

+ **Distribuera i Azure Kubernetes Service (AKS) och FPGA**

  När du distribuerar till AKS, kan du skapa ett nytt AKS-kluster eller bifoga en befintlig. Skapa ett nytt kluster använder [AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-) trots att koppla ett befintligt kluster använder [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-). Båda att returnera ett konfigurationsobjekt som har en `enable_ssl` metod.

  Den `enable_ssl` metod kan antingen använda ett certifikat som tillhandahålls av Microsoft, eller en som du anger.

  * När du använder ett certifikat __som tillhandahålls av Microsoft__, måste du använda den `leaf_domain_label` parametern. Med den här parametern skapar tjänsten med hjälp av ett certifikat som tillhandahålls av Microsoft. Den `leaf_domain_label` används för att generera DNS-namn för tjänsten. Till exempel värdet `myservice` skapar ett domännamn på `myservice<6-random-characters>.<azureregion>.cloudapp.azure.com`, där `<azureregion>` är den region som innehåller tjänsten. Du kan även använda den `overwrite_existing_domain` parameter för att skriva över den befintliga löv domänetiketten.

    Om du vill distribuera (eller omdistribuera) tjänsten med SSL aktiverat, ange den `ssl_enabled` parameter `True`, kan du när det är tillämpligt. Ange den `ssl_certificate` parametern till värdet för den __certifikat__ fil och `ssl_key` till värdet för den __nyckel__ fil.

    > [!IMPORTANT]
    > När du använder ett certifikat som tillhandahålls av Microsoft, behöver du inte köpa egna certifikat eller domän namn.

    I följande exempel visar hur du skapar konfigurationer som gör att ett SSL-certifikat som skapats av Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration().enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name).enable_ssl(leaf_domain_label = "myservice")
    ```

  * När du använder __ett certifikat som du har köpt__, använda den `ssl_cert_pem_file`, `ssl_key_pem_file`, och `ssl_cname` parametrar.  I följande exempel visar hur du skapar konfigurationer som använder ett SSL-certifikat som du anger med hjälp av `.pem` filer:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    provisioning_config = AksCompute.provisioning_configuration().enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name).enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  Mer information om `enable_ssl`, se [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) och [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

+ **Distribuera i Azure Container Instances (ACI)**

  När du distribuerar till ACI, anger du värden för SSL-relaterade parametrar som visas i kodfragmentet:

    ```python
    from azureml.core.webservice import AciWebservice

    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  Mer information finns i [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>Uppdatera din DNS-Server

Därefter måste du uppdatera din DNS så att den pekar till webbtjänsten.

+ **För ACI**:

  Använd de verktyg som tillhandahålls av din domänregistrator för att uppdatera DNS-posten för ditt domännamn. Posten måste peka på IP-adressen för tjänsten.

  Beroende på Registratorn och hur lång tid att live (TTL) som är konfigurerad för domännamnet så det kan ta flera minuter till flera timmar innan klienter kan matcha domännamnet.

+ **För AKS**:

  > [!WARNING]
  > Om du har använt den `leaf_domain_label` för att skapa tjänsten med ett certifikat som tillhandahålls av Microsoft, inte manuellt uppdatera DNS-värdet för klustret. Värdet ska anges automatiskt.

  Uppdatera DNS under fliken ”Configuration” i ”offentliga IP-adressen” för AKS-kluster som du ser i bilden. Du hittar den offentliga IP-adressen som en av de resurstyper som skapats under resursgruppen med agentnoderna AKS och andra nätverksresurser.

  ![Azure Machine Learning-tjänsten: Skydda webbtjänster med SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)



## <a name="next-steps"></a>Nästa steg
Lär dig att:
+ [Använda en maskininlärningsmodell som distribueras som en webbtjänst](how-to-consume-web-service.md)
+ [Kör säkert experiment och inferensjobb i Azure Virtual Network](how-to-enable-virtual-network.md)

