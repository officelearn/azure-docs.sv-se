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
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1a6aa75f3d25cd88cd1edb9b2cdcfabc3b4ec8f9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103901"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Använda SSL för att skydda webbtjänster med Azure Machine Learning-tjänsten

I den här artikeln får du lära dig hur du skyddar en webbtjänst som distribueras med Azure Machine Learning-tjänsten. Du kan begränsa åtkomsten till webbtjänster och skydda data som skickats av klienter som använder [Hypertext Transfer Protocol Secure (HTTPS)](https://en.wikipedia.org/wiki/HTTPS).

HTTPS används för att skydda kommunikationen mellan en klient och din webbtjänst genom att kryptera kommunikationen mellan två. Kryptering hanteras med [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Ibland refereras det fortfarande till som SSL Secure Sockets Layer (), som var föregångare till TLS.

> [!TIP]
> Azure Machine Learning SDK använder termen ”SSL” för egenskaper som har att göra säker kommunikation. Detta innebär inte att TLS används inte av din webbtjänst, precis som SSL är mer identifierbara giltighetstiden för många läsare.

TLS och SSL nåde förlitar sig på __digitala certifikat__, som används för att utföra verifiering av kryptering och identitet. Mer information om hur digitala certifikat fungerar finns i Wikipedia-posten på [offentlig nyckelinfrastruktur (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Om du inte aktivera och använda HTTPS för din webbtjänst, kanske data som skickas till tjänsten visas vidare till andra på internet.
>
> HTTPS kan också klienten för att kontrollera att den server som den ansluter till. Detta skyddar klienter mot [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) attacker.

Processen för att skydda en ny webbtjänst eller en befintlig är följande:

1. Få ett domännamn.

2. Få ett digitalt certifikat.

3. Distribuera eller uppdatera webbtjänsten med SSL-inställningen aktiverad.

4. Uppdatera din DNS så att den pekar till webbtjänsten.

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

  När du distribuerar till FPGA, anger du värden för parametrarna SSL-relaterade som visas i kodfragmentet:

    ```python
    from azureml.contrib.brainwave import BrainwaveWebservice

    deployment_config = BrainwaveWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem")
    ```

## <a name="update-your-dns"></a>Uppdatera din DNS-Server

Därefter måste du uppdatera din DNS så att den pekar till webbtjänsten.

+ **För ACI**:

  Använd de verktyg som tillhandahålls av din domänregistrator för att uppdatera DNS-posten för ditt domännamn. Posten måste peka på IP-adressen för tjänsten.

  Beroende på Registratorn och hur lång tid att live (TTL) som är konfigurerad för domännamnet så det kan ta flera minuter till flera timmar innan klienter kan matcha domännamnet.

+ **För AKS**:

  Uppdatera DNS under fliken ”Configuration” i ”offentliga IP-adressen” för AKS-kluster som du ser i bilden. Du hittar den offentliga IP-adressen som en av de resurstyper som skapats under resursgruppen med agentnoderna AKS och andra nätverksresurser.

  ![Azure Machine Learning-tjänsten: Skydda webbtjänster med SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Nästa steg
Lär dig att:
+ [Använda en maskininlärningsmodell som distribueras som en webbtjänst](how-to-consume-web-service.md)
+ [Kör säkert experiment och inferensjobb i Azure Virtual Network](how-to-enable-virtual-network.md)
