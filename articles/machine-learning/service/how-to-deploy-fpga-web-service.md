---
title: Distribuera en modell som en webbtjänst på en FPGA med Azure Machine Learning
description: Lär dig hur du distribuerar en webbtjänst med en modell som körs på en FPGA med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 10/01/2018
ms.openlocfilehash: df6637f1a52b679ba9ad0a49fb37d4e4b72f35e4
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237831"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Distribuera en modell som en webbtjänst på en FPGA med Azure Machine Learning

Du kan distribuera en modell som en webbtjänst på [fältet programmable gate matriser (FPGA)](concept-accelerate-with-fpgas.md).  FPGA ger extremt låg latens inferensjobb, även med en enda gruppstorlek.   

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Du måste begära och godkännas för FPGA kvot. För att begära åtkomst, fyller du i formuläret för begäran av kvot: https://aka.ms/aml-real-time-ai

- En arbetsyta för Azure Machine Learning-tjänsten och Azure Machine Learning-SDK för Python installerat. Lär dig hur du hämtar dessa krav med hjälp av den [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md) dokumentet.
 
  - Din arbetsyta måste finnas i den *östra USA 2* region.

  - Installera contrib-tillägg:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Skapa och distribuera din modell
Skapa en pipeline för att Förbearbeta inmatad bild, förtränade med ResNet-50 på en FPGA och kör sedan funktionerna via en classifer tränats på ImageNet-datauppsättning.

Följ anvisningarna för att:

* Definiera modellen pipelinen
* Distribuera modellen
* Använd distribuerade modell
* Ta bort distribuerade tjänster

> [!IMPORTANT]
> Din klient ska vara i samma Azure-region som slutpunkt för att optimera svarstid och dataflöde.  För närvarande skapas av API: er i regionen östra USA Azure.



### <a name="preprocess-image"></a>Förbearbeta bild
Det första steget i pipelinen är att Förbearbeta bilderna.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Lägg till Upplärda
Initiera modellen och ladda ned en TensorFlow kontrollpunkt för den quantized versionen av ResNet50 som ska användas som en upplärda.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Lägg till klassificerare
Den här klassificerare har tränats på ImageNet-datauppsättning.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Skapa tjänstdefinition
Nu när du har definied bild Förbearbeta, upplärda och klassificerare som körs på tjänsten, kan du skapa en tjänstdefinition. Tjänstdefinitionen är en uppsättning filer som genereras från den modell som har distribuerats till FPGA-tjänsten. Tjänstdefinitionen består av en pipeline. Pipelinen är en serie steg som körs i ordning.  TensorFlow faser, Keras faser och steg BrainWave stöds.  Stegen körs på tjänsten, med utdata för varje steg som indata i efterföljande steg i ordning.

Att skapa ett TensorFlow-steg, ange en session som innehåller diagrammet (i det här fallet används standard-graph) och indata och utdata tensors till den här fasen.  Den här informationen används för att spara diagrammet så att den kan köras på tjänsten.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Distribuera modell
Skapa en tjänst från tjänstdefinitionen.  Din arbetsyta måste vara på plats i USA, östra 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>Testa tjänsten
Att skicka en bild-API: et och testa svaret, lägga till en mappning från utdata klass-ID till ImageNet klassnamn.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Anropar din tjänst och Ersätt ”your image.jpg” filnamnet nedan med en avbildning från din dator. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Rensa service
Ta bort tjänsten.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Skydda FPGA-webbtjänster

Azure Machine Learning-modeller som körs på FPGA ger stöd för SSL- och nyckel för autentisering. På så sätt kan du begränsa åtkomsten till din tjänst och säkra data som skickats av klienterna.

> [!IMPORTANT]
> Autentisering är bara aktiverat för tjänster som har angetts ett SSL-certifikat och nyckel. 
>
> Om du inte aktiverar SSL kan kommer alla användare på internet att kunna göra anrop till tjänsten.
>
> Om du aktiverar SSL och autentiseringsnyckeln krävs vid åtkomst till tjänsten.

SSL krypterar data som skickas mellan klienten och tjänsten. Det används också av klienten för att verifiera identiteten för servern.

Du kan distribuera en tjänst med SSL aktiverat eller uppdatera en redan distribuerad tjänst för att aktivera den. Stegen är desamma:

1. Hämta ett domännamn.

2. Skaffa ett SSL-certifikat.

3. Distribuera eller uppdatera tjänsten med SSL aktiverat.

4. Uppdatera din DNS så att den pekar till tjänsten.

### <a name="acquire-a-domain-name"></a>Skaffa ett domännamn

Om du inte redan har ett domännamn kan du köpa en från en __domännamnsregistratorn__. Processen skiljer sig mellan registratorer, precis kostnaden. Registratorn ger dig också med verktyg för att hantera domännamnet. Dessa verktyg används för att mappa ett fullständigt kvalificerat domännamn (t.ex www.contoso.com) till IP-adressen som din tjänst finns i.

### <a name="acquire-an-ssl-certificate"></a>Skaffa ett SSL-certifikat

Det finns många sätt att få ett SSL-certifikat. De vanligaste är att köpa en från en __certifikatutfärdare__ (CA). Oavsett om du har fått certifikatet, behöver du följande filer:

* En __certifikat__. Certifikatet måste innehålla fullständig certifikatkedjan och måste vara PEM-kodat.
* En __nyckeln__. Nyckeln måste vara PEM-kodat.

> [!TIP]
> Om certifikatutfärdaren inte kan tillhandahålla certifikat och nyckel som PEM-kodat filer, kan du använda ett verktyg som [OpenSSL](https://www.openssl.org/) att ändra format.

> [!IMPORTANT]
> Självsignerat certifikat ska användas endast för utveckling. De bör inte användas i produktion.
>
> Om du använder ett självsignerat certifikat, se den [förbrukar tjänster med självsignerade certifikat](#self-signed) för specifika anvisningar.

> [!WARNING]
> När du begär ett certifikat, måste du ange det fullständigt kvalificerade domännamnet (FQDN) för den adress som du planerar att använda för tjänsten. Till exempel www.contoso.com. Den adressen stämplad i certifikatet och den adress som används av klienterna jämförs vid verifiering av identiteten för tjänsten.
>
> Om adresserna som inte matchar får klienterna ett felmeddelande. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Distribuera eller uppdatera tjänsten med SSL aktiverat

Om du vill distribuera tjänsten med SSL aktiverat, ange den `ssl_enabled` parameter `True`. Ange den `ssl_certificate` parametern till värdet för den __certifikat__ fil och `ssl_key` till värdet för den __nyckel__ fil. I följande exempel beskrivs hur du distribuerar en tjänst med SSL aktiverat:

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

Svaret på den `create_service` åtgärden innehåller IP-adressen för tjänsten. IP-adressen används när du kartlägger DNS-namn till IP-adressen för tjänsten.

Svaret innehåller även en __primärnyckel__ och __sekundärnyckel__ som används för att använda tjänsten.

### <a name="update-your-dns-to-point-to-the-service"></a>Uppdatera din DNS-server så att den pekar till tjänsten

Använd de verktyg som tillhandahålls av din domänregistrator för att uppdatera DNS-posten för ditt domännamn. Posten måste peka på IP-adressen för tjänsten.

> [!NOTE]
> Beroende på Registratorn och hur lång tid att live (TTL) som är konfigurerad för domännamnet så det kan ta flera minuter till flera timmar innan klienter kan matcha domännamnet.

### <a name="consume-authenticated-services"></a>Använda autentiserade tjänster

Följande exempel visar hur du använder en autentiserad med Python och C#-tjänst:

> [!NOTE]
> Ersätt `authkey` med den primära eller sekundära nyckeln returneras när du skapar tjänsten.

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

Andra gRPC-klienter kan autentisera begäranden genom att ange en auktoriseringsrubrik. Den allmänna riktlinjen att skapa en `ChannelCredentials` objekt som kombinerar `SslCredentials` med `CallCredentials`. Detta har lagts till auktoriseringshuvudet för begäran. Läs mer om hur du implementerar stöd för din specifika rubriker, [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Följande exempel visar hur du ställer in rubriken i C# och Go:

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

### <a id="self-signed"></a>Använda tjänster med självsignerade certifikat

Det finns två sätt att aktivera klienten för att autentisera mot en server som skyddas med ett självsignerat certifikat:

* På klientsystemet, ange den `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` miljövariabeln på klientsystemet så att den pekar till certifikatfilen.

* När en `SslCredentials` objekt, skicka innehållet i certifikatfilen till konstruktorn.

Med någon av metoderna gör gRPC ska kunna använda certifikatet som rotcertifikatet.

> [!IMPORTANT]
> gRPC accepterar inte icke betrodda certifikat. Med ett icke betrott certifikat misslyckas med ett `Unavailable` statuskod. Innehåller information om felet `Connection Failed`.

## <a name="sample-notebook"></a>Exempel-anteckningsbok

Begreppen i den här artikeln är visas i den `project-brainwave/project-brainwave-quickstart.ipynb` anteckningsboken.

Hämta den här anteckningsboken:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
