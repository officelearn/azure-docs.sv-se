---
title: Distribuera Azure Cognitive Services till Azure Stack | Microsoft Docs
description: Lär dig hur du distribuerar Azure Cognitive Services i Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 8080355bebf00c9f37c28ae8ed54bba092f8dc17
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099941"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Distribuera Azure Cognitive Services till Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

> [!Note]  
> Azure Cognitive Services på Azure Stack finns i förhandsversion.

Du kan använda Azure Cognitive Services med stöd för behållare på Azure Stack. Stöd för behållare i Azure Cognitive Services kan du använda samma omfattande API: er som är tillgängliga i Azure. Din användning av behållare möjliggör flexibilitet att distribuera och tillhandahålla tjänster som levereras i [Docker-behållare](https://www.docker.com/what-container). Stöd för behållare finns för närvarande i förhandsversion för en delmängd av Azure Cognitive Services, inklusive delar av [visuellt](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [ansikte](https://docs.microsoft.com/azure/cognitive-services/face/overview), och [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), och [Språkförståelse](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

Skapa behållare är en metod för distribution av programvara där ett program eller tjänster, inklusive dess beroenden och konfigurationen är paketerad som en behållaravbildning. Du kan distribuera en avbildning till en behållarvärd med lite eller ingen ändring. Varje behållare är isolerad från andra behållare och det underliggande operativsystemet. Själva, systemet har bara de komponenter som behövs för att köra din avbildning. En behållarvärd har mindre avtryck än en virtuell dator. Dessutom kan du skapa behållare från bilder för kortsiktig uppgifter och tas bort när den inte längre behövs.

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Använd behållare med Cognitive Services på Azure Stack

- **Kontroll över data**  
  Tillåt användarna att ha kontroll över sina data när du använder Cognitive Services. Cognitive Services kan du leverera till appanvändare som inte kan skicka data till globala Azure eller det offentliga molnet.

- **Kontroll över modelluppdateringar**  
  Ange appanvändarna version och uppdatering av modeller som distribuerats i sin lösning.

- **Bärbar arkitektur**  
  Aktivera skapandet av en bärbar apparkitektur så att du kan distribuera din lösning till det offentliga molnet, till ett privat lokalt eller gränsen i molnet. Du kan distribuera din behållare till Azure Kubernetes Service, Azure Container Instances, eller till ett Kubernetes-kluster i till Azure Stack. Mer information finns i [distribuera Kubernetes i Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

- **Högt dataflöde och låg latens**  
   Ge användarna möjlighet att skala med trafiktoppar för stora dataflöden och låg fördröjning. Aktivera Cognitive Services körs i Azure Kubernetes Service fysiskt nära sina programmets logik och data.

Distribuera Cognitive Services-behållare i ett Kubernetes-kluster som tillsammans med dina programbehållare för hög tillgänglighet och elastisk skalning med Azure Stack. Du kan utveckla ditt program genom att kombinera Cognitive services med komponenter som bygger på App Services, Functions, Blob-lagring, eller SQL eller mySQL-databaser. 

Mer information om Cognitive Services-behållare, går du till [stöd för behållare i Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Distribuera Azure Ansikts-API

Den här artikeln beskriver hur du distribuerar Azure Ansikts-API på Kubernetes-kluster på Azure Stack. Du kan använda samma metod för att distribuera andra cognitive services-behållare på Azure Stack Kubernetes-kluster.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du:

1.  Begär åtkomst till behållarregister för att hämta ansikte behållaravbildningar från Azure Cognitive Services Container Registry. För information går du till avsnittet i [begär åtkomst till privat behållarregister](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Förbered ett Kubernetes-kluster på Azure Stack. Du kan följa artikeln [distribuera Kubernetes i Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Skapa Azure-resurser

Skapa en resurs för Cognitive Service på Azure för att förhandsgranska ansikte, LUIS eller identifiera Text behållare, respektive. Du måste använda prenumerationen nyckel och slutpunkt Webbadressen från resursen för att skapa en instans av cognitive service-behållare.

1. Skapa en Azure-resurs i Azure-portalen. Om du vill förhandsgranska Ansikts-behållare, måste du först skapa en motsvarande Ansikts-resurs i Azure-portalen. Mer information finns i [ Snabbstart: Skapa ett Cognitive Services-konto i Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

   > [!Note]
   >  Ansikts- eller visuellt resursen måste använda F0 prisnivån.

2. Hämta slutpunktsnyckeln för URL: en och -prenumeration för Azure-resursen. När Azure-resursen har skapats måste du använda prenumeration nyckel och slutpunkt URL: en från den här resursen för att skapa en instans av behållaren motsvarande ansikte, LUIS eller identifiera Text för förhandsversionen.

## <a name="create-a-kubernetes-secret"></a>Skapa ett Kubernetes-hemlighet 

Gör användningen av Kubectl skapa hemliga kommando för att komma åt det privata behållarregistret. Ersätt **&lt;användarnamn&gt;** med användarnamnet och **&lt;lösenord&gt;** med lösenordet som angavs i autentiseringsuppgifterna som du fick från Azure Cognitive Service-teamet.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Förbereda en YAML-konfigurationsfil

Du kommer att använda YAML-konfigurationsfil för att förenkla distributionen av cognitive service i Kubernetes-klustret.

Här är ett exempel YAML-konfigurationsfil distribuerar Ansikts-tjänst i Azure Stack:

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

I den här YAML-konfigurationsfil, Använd hemlighet som du använde för att hämta behållaravbildningarna cognitive service från Azure Container Registry. Du och Använd hemlighetsfilen att distribuera en specifik replik av behållaren. Du kan också skapa en belastningsutjämnare för att se till att användare kan komma åt den här tjänsten externt.

Information om viktiga fält:

| Fält | Anteckningar |
| --- | --- |
| replicaNumber | Definierar de första replikerna av instanser för att skapa. Du kan visserligen skala den senare efter distributionen. |
| ImageLocation | Anger platsen för specifika cognitive service behållaravbildningen i ACR. Till exempel ansikts-tjänsten: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |Slutpunkts-URL som anges i steg i [skapa Azure-resurs](#create-azure-resources) |
| ApiKey | Prenumerationsnyckeln anges i steg i [skapa Azure-resurs](#create-azure-resources) |
| SecretName | Det hemliga namnet som du precis antecknade i steg för Create secrete för att komma åt det privata behållarregistret |

## <a name="deploy-the-cognitive-service"></a>Distribuera cognitive service

Användning av följande kommando för att distribuera cognitive service-behållare

```bash  
    Kubectl apply -f <yamlFineName>
```
Användning av följande kommando för att övervaka hur distribueras: 
```bash  
    Kubectl get pod – watch
```

## <a name="test-the-cognitive-service"></a>Testa cognitive service

Åtkomst till den [OpenAPI-specifikation](https://swagger.io/docs/specification/about/) (tidigare Swagger-specifikation), som beskriver de åtgärder som stöds av en initierad behållare från den **/swagger** relativ URI för den behållaren. Följande URI: N innehåller till exempel åtkomst till den OpenAPI-specifikationen för Attitydanalys-behållare som har initierats i föregående exempel:

```HTTP  
http:<External IP>:5000/swagger
```

Du kan hämta den externa IP-adressen från följande kommando: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Testa services med Python

Du kan försöka att verifiera om Cognitive services på Azure Stack genom att köra några enkla Python-skript. Det finns officiella Python-quickstart-exempel för [visuellt](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [ansikte](https://docs.microsoft.com/azure/cognitive-services/face/overview), och [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), och [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) () LUIS) för din referens.

Det finns behov av två saker att tänka på att göra Python-appen som körs mot de tjänster som körs i behållare: 
1. Kognitiva tjänster i behållare behöver inte sub-nycklar för autentisering, men vi behöver att ange en sträng som platshållare för att uppfylla SDK: N. 
2. Ersätt base_URL med faktiska tjänstens slutpunkt IP-adress 

Här är ett exempel Python-skriptet som används ansikte services Python SDK för att identifiera och RAM ansikten i en bild. 

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Nästa steg

[Hur du installerar och kör behållare för API för visuellt innehåll.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Hur du installerar och kör Ansikts-API-behållare](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers)

[Hur du installerar och kör behållare för API för textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Hur du installerar och kör behållare för Language Understanding (LIUS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)
