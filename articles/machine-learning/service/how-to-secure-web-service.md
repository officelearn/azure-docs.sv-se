---
title: Skydda webbtjänster med SSL
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skyddar en webbtjänst som distribueras med Azure Machine Learning-tjänsten. Du kan begränsa åtkomsten till webbtjänster och skydda data som skickats av klienter som använder secure socket lager (SSL) och nyckel för autentisering.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.custom: seodec18
ms.openlocfilehash: 14350a04326ba22dcc5c8608b6ac6b9180666832
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101217"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Använda SSL för att skydda webbtjänster med Azure Machine Learning-tjänsten

I den här artikeln får du lära dig hur du skyddar en webbtjänst som distribueras med Azure Machine Learning-tjänsten. Du kan begränsa åtkomsten till webbtjänster och skydda data som skickats av klienter som använder secure socket lager (SSL) och nyckel för autentisering.

> [!Warning]
> Om du inte aktiverar SSL kan kommer alla användare på internet att kunna göra anrop till webbtjänsten.

SSL krypterar data som skickas mellan klienten och webbtjänsten. Det används också av klienten för att verifiera identiteten för servern. Autentisering är bara aktiverat för tjänster som har angetts ett SSL-certifikat och nyckel.  Om du aktiverar SSL krävs en autentiseringsnyckel vid åtkomst till webbtjänsten.

Oavsett om du distribuerar en webbtjänst som aktiverats med SSL eller om du aktiverar SSL för befintliga distribuerade webbtjänsten är samma stegen:

1. Få ett domännamn.

2. Få ett SSL-certifikat.

3. Distribuera eller uppdatera webbtjänsten med SSL-inställningen aktiverad.

4. Uppdatera din DNS så att den pekar till webbtjänsten.

Det finns mindre skillnader när du skyddar webbtjänster över den [distributionskanaler](how-to-deploy-and-where.md). 

## <a name="get-a-domain-name"></a>Få ett domännamn

Om du inte redan har ett domännamn kan du köpa en från en __domännamnsregistratorn__. Processen skiljer sig mellan registratorer, precis kostnaden. Registratorn ger dig också med verktyg för att hantera domännamnet. Dessa verktyg används för att mappa ett fullständigt kvalificerat domännamn (t.ex www.contoso.com) till IP-adressen som är värd för webbtjänsten.

## <a name="get-an-ssl-certificate"></a>Hämta ett SSL-certifikat

Det finns många sätt att få ett SSL-certifikat. De vanligaste är att köpa en från en __certifikatutfärdare__ (CA). Oavsett om du har fått certifikatet, behöver du följande filer:

* En __certifikat__. Certifikatet måste innehålla fullständig certifikatkedjan och måste vara PEM-kodat.
* En __nyckeln__. Nyckeln måste vara PEM-kodat.

När du begär ett certifikat, måste du ange det fullständigt kvalificerade domännamnet (FQDN) för den adress som du planerar att använda för webbtjänsten. Till exempel www.contoso.com. Den adressen stämplad i certifikatet och den adress som används av klienterna jämförs vid verifiering av identiteten för webbtjänsten. Om adresserna som inte matchar får klienterna ett felmeddelande. 

> [!TIP]
> Om certifikatutfärdaren inte kan tillhandahålla certifikat och nyckel som PEM-kodat filer, kan du använda ett verktyg som [OpenSSL](https://www.openssl.org/) att ändra format.

> [!WARNING]
> Självsignerat certifikat ska användas endast för utveckling. De bör inte användas i produktion. Självsignerade certifikat kan orsaka problem i din klient program. Mer information finns i dokumentationen för nätverksbibliotek som används i klientprogrammet.

## <a name="enable-ssl-and-deploy"></a>Aktivera SSL och distribuera

Om du vill distribuera (eller omdistribuera) tjänsten med SSL aktiverat, ange den `ssl_enabled` parameter `True`, kan du när det är tillämpligt. Ange den `ssl_certificate` parametern till värdet för den __certifikat__ fil och `ssl_key` till värdet för den __nyckel__ fil. 

+ **Distribuera i Azure Kubernetes Service (AKS)**
  
  När du etablerar AKS-kluster, anger du värden för SSL-relaterade parametrar som visas i kodfragmentet:

    ```python
    from azureml.core.compute import AksCompute
    
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Distribuera i Azure Container Instances (ACI)**
 
  När du distribuerar till ACI, anger du värden för SSL-relaterade parametrar som visas i kodfragmentet:

    ```python
    from azureml.core.webservice import AciWebservice
    
    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Distribuera på fältet Programmable Gate matriser (FPGA)**

  Svaret på den `create_service` åtgärden innehåller IP-adressen för tjänsten. IP-adressen används när du kartlägger DNS-namn till IP-adressen för tjänsten. Svaret innehåller även en __primärnyckel__ och __sekundärnyckel__ som används för att använda tjänsten. Ange värden för SSL-relaterade parametrar som du ser i kodfragmentet:

    ```python
    from amlrealtimeai import DeploymentClient
    
    subscription_id = "<Your Azure Subscription ID>"
    resource_group = "<Your Azure Resource Group Name>"
    model_management_account = "<Your AzureML Model Management Account Name>"
    location = "eastus2"
    
    model_name = "resnet50-model"
    service_name = "quickstart-service"
    
    deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)
    
    with open('cert.pem','r') as cert_file:
        with open('key.pem','r') as key_file:
            cert = cert_file.read()
            key = key_file.read()
            service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
    ```

## <a name="update-your-dns"></a>Uppdatera din DNS-Server

Därefter måste du uppdatera din DNS så att den pekar till webbtjänsten.

+ **För ACI och FPGA**:  

  Använd de verktyg som tillhandahålls av din domänregistrator för att uppdatera DNS-posten för ditt domännamn. Posten måste peka på IP-adressen för tjänsten.  

  Beroende på Registratorn och hur lång tid att live (TTL) som är konfigurerad för domännamnet så det kan ta flera minuter till flera timmar innan klienter kan matcha domännamnet.

+ **För AKS**: 

  Uppdatera DNS under fliken ”Configuration” i ”offentliga IP-adressen” för AKS-kluster som du ser i bilden. Du hittar den offentliga IP-adressen som en av de resurstyper som skapats under resursgruppen med agentnoderna AKS och andra nätverksresurser.

  ![Azure Machine Learning-tjänsten: skydda webbtjänster med SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [förbruka en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md).