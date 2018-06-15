---
title: Så här distribuerar du en modell som en webbtjänst på en då FPGA med Azure Machine Learning
description: Lär dig hur du distribuerar en webbtjänst med en modell som körs på en då FPGA med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33789399"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Distribuera en modell som en webbtjänst på en då FPGA med Azure Machine Learning

I det här dokumentet du lär dig hur du konfigurerar miljön arbetsstation och distribuera en modell som en webbtjänst på [fältet programmable gate matriser (då FPGA)](concept-accelerate-with-fpgas.md). Webbtjänsten använder projektet Brainwave för att köra modellen på då FPGA.

Med hjälp av FPGAs ger mycket låg latens inferencing, även om en enskild batch-storlek.

## <a name="create-an-azure-machine-learning-model-management-account"></a>Skapa ett konto i Azure Machine Learning modellen Management

1. Gå till sidan för att skapa modellen Hanteringskontot på den [Azure Portal](https://aka.ms/aml-create-mma).

2. I portalen, skapa ett konto för hantering av modellen i den **östra USA 2** region.

   ![Bild av skapa modellen Hanteringskontot skärmen](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. Namnge ditt konto för hantering av modellen, Välj en prenumeration och välja en resursgrupp.

   >[!IMPORTANT]
   >Du måste välja för plats **östra USA 2** som region.  Inga andra regioner är tillgängliga.

4. Välj en prisnivå (S1 räcker, men också fungera S2 och S3).  DevTest-nivån stöds inte.  

5. Klicka på **Välj** bekräfta prisnivån.

6. Klicka på **skapa** i ML-modell Management till vänster.

## <a name="get-model-management-account-information"></a>Hämta information om hantering av modellen

Få information om din modell Management konto (MMA) klickar du på den __modellen Hanteringskontot__ på Azure-portalen.

Kopiera värdena för följande objekt:

+ Modellnamn för Hanteringskontot (i på det övre vänstra hörnet)
+ Resursgruppsnamn
+ Prenumerations-ID:t
+ Plats (Använd ”eastus2”)

![Modellen Management kontoinformation](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>Konfigurera din dator

Följ dessa steg om du vill konfigurera din arbetsstation för då FPGA distribution:

1. Hämta och installera den senaste versionen av [Git](https://git-scm.com/downloads).

2. Installera [Anaconda (Python 3,6)](https://conda.io/miniconda.html).

3. Om du vill hämta Anaconda-miljön, använder du följande kommando från en kommandotolk med Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. För att skapa miljön, öppna ett **Anaconda Prompt** (inte en Azure Machine Learning arbetsstationen fråga) och kör följande kommando:

    > [!IMPORTANT]
    > Den `environment.yml` filen har git-lagringsplats som du har klonat i föregående steg. Ändra sökvägen som behövs för att peka till filen på din arbetsstation.

    ```
    conda env create -f environment.yml
    ```

5. Om du vill aktivera miljön, använder du följande kommando:

    ```
    conda activate amlrealtimeai
    ```

6. Om du vill starta Jupyter Notebook-server, använder du följande kommando:

    ```
    jupyter notebook
    ```

    Kommandots utdata liknar följande:

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > Du får en annan token varje gång du kör kommandot.

    Om din webbläsare inte öppnas automatiskt i Jupyter-anteckningsboken, öppna sidan med HTTP-URL som returneras av kommandot tidigare.

    ![Bild av sidan Jupyter-anteckningsbok](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>Distribuera din modell

Öppna från Jupyter-anteckningsbok den `00_QuickStart.ipynb` bärbar dator från den `notebooks/resnet50` directory. Följ instruktionerna i den bärbara datorn att:

* Definiera tjänsten
* Distribuera modellen
* Använda distribuerade modellen
* Ta bort distribuerade tjänster

> [!IMPORTANT]
> Om du vill optimera svarstid och genomströmning måste din arbetsstation vara i samma Azure-region som slutpunkt.  För närvarande skapas för API: er i East oss Azure-region.

## <a name="ssltls-and-authentication"></a>SSL/TLS- och autentisering

Azure Machine Learning ger stöd för SSL- och nyckel för autentisering. På så sätt kan du begränsa åtkomsten till tjänsten och säkra data som skickats av klienterna.

> [!NOTE]
> Stegen i det här avsnittet gäller bara för Azure Machine Learning maskinvara snabbare modeller. Standard Azure Machine Learning-tjänster finns i [hur du konfigurerar SSL på Azure Machine Learning Compute](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc) dokumentet.

> [!IMPORTANT]
> Autentisering är endast aktiverad för tjänster som har angett ett SSL-certifikat och nyckel. 
>
> Om du inte aktiverar SSL kan kommer alla användare på internet att kunna göra anrop till tjänsten.
>
> Om du aktiverar SSL och autentiseringsnyckel krävs vid åtkomst till tjänsten.

SSL krypterar data som skickas mellan klienten och tjänsten. Det används också av klienten för att verifiera identiteten för servern.

Du kan distribuera en tjänst med SSL aktiverat eller uppdatera en redan distribuerad tjänst för att aktivera den. Stegen är samma:

1. Hämta ett domännamn.

2. Skaffa ett SSL-certifikat.

3. Distribuera om eller uppdatera tjänsten med SSL aktiverat.

4. Uppdatera din DNS så att den pekar till tjänsten.

### <a name="acquire-a-domain-name"></a>Skaffa ett domännamn

Om du inte redan äger ett domännamn kan du köpa ett från en __domännamnsregistratorn__. Processen skiljer sig mellan registratorer, som kostar. Registratorn också ger dig verktyg för att hantera domännamnet. Verktygen används för att mappa ett fullständigt kvalificerat domännamn (t.ex www.contoso.com) till IP-adressen som finns på din tjänst.

### <a name="acquire-an-ssl-certificate"></a>Skaffa ett SSL-certifikat

Det finns många sätt att få ett SSL-certifikat. De vanligaste är att köpa ett från en __certifikatutfärdare__ (CA). Oavsett om du har fått certifikatet, behöver du följande filer:

* En __certifikat__. Certifikatet måste innehålla den fullständiga certifikatkedjan och måste vara PEM-kodat.
* En __nyckeln__. Nyckeln måste vara PEM-kodad.

> [!TIP]
> Om certifikatutfärdaren inte kan tillhandahålla certifikatet och nyckel som PEM-kodade filer, kan du använda ett verktyg som [OpenSSL](https://www.openssl.org/) ändra formatet.

> [!IMPORTANT]
> Självsignerade certifikat ska användas endast för utveckling. De bör inte användas i produktionen.
>
> Om du använder ett självsignerat certifikat, finns det [förbrukar tjänster med självsignerade certifikat](#self-signed) avsnittet specifika instruktioner.

> [!WARNING]
> När du begär ett certifikat måste du ange det fullständigt kvalificerade domännamnet (FQDN) i den adress som du planerar att använda för tjänsten. Till exempel www.contoso.com. Adressen stämplad i certifikatet och den adress som används av klienterna jämförs att verifiera identiteten för tjänsten.
>
> Om adresserna inte matchar, kommer klienterna får ett felmeddelande. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Distribuera om eller uppdatera tjänsten med SSL aktiverat

Om du vill distribuera tjänsten med SSL aktiverat, ange den `ssl_enabled` parameter till `True`. Ange den `ssl_certificate` parametern med värdet för den __certifikat__ fil och `ssl_key` till värdet för den __nyckeln__ fil. I följande exempel beskrivs hur du distribuerar en tjänst med SSL aktiverat:

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

Svaret från den `create_service` åtgärden innehåller IP-adressen för tjänsten. IP-adressen används när du kartlägger DNS-namn till IP-adressen för tjänsten.

Svaret innehåller också en __primärnyckel__ och __sekundärnyckeln__ som används för att använda tjänsten.

### <a name="update-your-dns-to-point-to-the-service"></a>Uppdatera din DNS så att den pekar till tjänsten

Använda de verktyg som tillhandahålls av din domännamnsregistrator för att uppdatera DNS-post för ditt domännamn. Posten måste peka på IP-adressen för tjänsten.

> [!NOTE]
> Beroende på Registratorn och tiden på live (TTL) som konfigurerats för domännamnet på, det kan ta flera minuter till flera timmar innan klienter kan matcha domännamnet.

### <a name="consuming-authenticated-services"></a>Förbrukar autentiserade tjänster

Följande exempel visar hur du använder en autentiserad av Python och C#-tjänst:

> [!NOTE]
> Ersätt `authkey` returneras när du skapar tjänsten med de primära och sekundära nycklarna.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Andra gRPC klienter kan autentisera begäranden genom att ange ett authorization-huvud. Det allmänna tillvägagångssättet är att skapa en `ChannelCredentials` objekt som kombinerar `SslCredentials` med `CallCredentials`. Detta läggs till authorization-huvud i begäran. Mer information om hur du implementerar stöd för din specifika huvuden finns [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Följande exempel visar hur du ställer in rubriken i C# och gå:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

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

### <a id="self-signed"></a>Förbrukar-tjänster med självsignerade certifikat

Det finns två sätt att aktivera klienten autentisera till en server som skyddas med ett självsignerat certifikat:

* På klientdatorn, ange den `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` -miljövariabeln på klientdatorn så att den pekar till certifikatfilen.

* När man skapar en `SslCredentials` objekt, skicka innehållet i certifikatfilen till konstruktorn.

Med någon av metoderna gör gRPC ska kunna använda certifikatet som rot-certifikat.

> [!IMPORTANT]
> gRPC accepterar inte icke betrodda certifikat. Med hjälp av ett icke betrott certifikat misslyckas med ett `Unavailable` statuskod. Innehåller information om felet `Connection Failed`.
