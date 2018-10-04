---
title: Skydda Azure Machine Learning-webbtjänster med SSL
description: Lär dig hur du skyddar en webbtjänst som distribueras med Azure Machine Learning-tjänsten. Du kan begränsa åtkomsten till webbtjänster och skydda data som skickats av klienter som använder secure socket lager (SSL) och nyckel för autentisering.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: 8a26491acc7215598e57ce6074fffe26a4374a96
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48251149"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>Skydda Azure Machine Learning-webbtjänster med SSL

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

> [!IMPORTANT]
> Självsignerat certifikat ska användas endast för utveckling. De bör inte användas i produktion. Om du använder ett självsignerat certifikat, se den [förbrukar webbtjänster med självsignerade certifikat](#self-signed) för specifika anvisningar.


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

+ **Distribuera på fältet programmerbart gamma matriser (FPGA)**

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

  [ ![Azure Machine Learning-tjänsten: skydda webbtjänster med SSL](./media/how-to-secure-web-service/aks-public-ip-address.png) ] ((.media/how-to-secure-web-service/aks-public-ip-address.png#lightbox)

## <a name="consume-authenticated-services"></a>Använda autentiserade tjänster

### <a name="how-to-consume"></a>Hur du använder 
+ **För ACI och AKS**: 

  För ACI och AKS webbtjänster, Lär dig att använda webbtjänster i de här artiklarna:
  + [Hur du distribuerar till ACI](how-to-deploy-to-aci.md)

  + [Hur du distribuerar till AKS](how-to-deploy-to-aks.md)

+ **För ACI och FPGA**:  

  I följande exempel visar hur du använder en autentiserad FPGA-tjänst i Python och C#.
  Ersätt `authkey` med den primära eller sekundära nyckeln som returnerades när tjänsten har distribuerats.

  Python-exempel:
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  C#-exempel:
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>Ange auktoriseringsrubriken
Andra gRPC-klienter kan autentisera begäranden genom att ange en auktoriseringsrubrik. Den allmänna riktlinjen att skapa en `ChannelCredentials` objekt som kombinerar `SslCredentials` med `CallCredentials`. Detta har lagts till auktoriseringshuvudet för begäran. Läs mer om hur du implementerar stöd för din specifika rubriker, [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Följande exempel visar hur du ställer in rubriken i C# och Go:

+ Använda C# för att ange rubriken:
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ Använda Go för att ange rubriken:
    ```go
    conn, err := grpc.Dial(serverAddr, 
        grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
        grpc.WithPerRPCCredentials(&authCreds{
        Key: "authKey"}))
    
    type authCreds struct {
        Key string
    }
    
    func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
        return map[string]string{
            "authorization": c.Key,
        }, nil
    }
    
    func (c *authCreds) RequireTransportSecurity() bool {
        return true
    }
    ```

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>Använda tjänster med självsignerade certifikat

Det finns två sätt att aktivera klienten för att autentisera mot en server som skyddas med ett självsignerat certifikat:

* På klientsystemet, ange den `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` miljövariabeln på klientsystemet så att den pekar till certifikatfilen.

* När en `SslCredentials` objekt, skicka innehållet i certifikatfilen till konstruktorn.

Med någon av metoderna gör gRPC ska kunna använda certifikatet som rotcertifikatet.

> [!IMPORTANT]
> gRPC accepterar inte icke betrodda certifikat. Med ett icke betrott certifikat misslyckas med ett `Unavailable` statuskod. Innehåller information om felet `Connection Failed`.
