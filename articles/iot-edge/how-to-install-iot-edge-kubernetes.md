---
title: Hur du installerar IoT Edge på Kubernetes | Microsoft Docs
description: Läs om hur du installerar IoT Edge på Kubernetes med hjälp av en lokal utvecklingsmiljö för kluster
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160702"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Hur du installerar IoT Edge på Kubernetes (förhandsversion)

IoT Edge kan integreras med Kubernetes som använder den som ett lager för flexibel infrastruktur med hög tillgänglighet. Den registrerar en IoT Edge *anpassade Resursdefinitionen* (CRD) med Kubernetes API-servern. Dessutom skapar en *operatorn* (IoT Edge-agent) som synkroniserar molnhanterad önskat tillstånd med det lokala klustret tillståndet. 

Modulen livstid hanteras av Kubernetes-scheduler som underhåller modulen tillgänglighet och väljer deras placering. IoT Edge hanterar edge programplattform som körs på översta, kontinuerligt avstämning av önskat tillstånd som anges i IoT Hub med tillståndet i edge-klustret. Programmodell edge är fortfarande den välbekanta modell som baseras på IoT Edge-moduler och rutter. Operatorn IoT Edge-agenten utför *automatisk* vädertrender till Kubernetes-natives konstruktioner som poddar, distributioner, tjänster osv.

Här är en övergripande Arkitekturdiagram:

![kubernetes arch](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Alla komponenter i edge-distribution är begränsad till ett Kubernetes-namnområde som är specifik för enheten, vilket gör det möjligt att dela samma klusterresurser bland flera edge-enheter och distributioner.

>[!NOTE]
>IoT Edge på Kubernetes finns i [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Installera lokalt för en snabb testmiljö

### <a name="prerequisites"></a>Nödvändiga komponenter

* Kubernetes 1.10 eller senare. Om du inte har en befintlig konfiguration kan du använda [Minikube](https://kubernetes.io/docs/setup/minikube/) för ett lokalt kluster-miljö. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), Kubernetes pakethanterare.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) för att visa och interagera med klustret.

### <a name="setup-steps"></a>Konfigurationssteg

1. Starta **Minikube**

    ``` shell
    minikube start
    ```

1. Initiera den **Helm** serverkomponenten (*tiller*) i klustret

    ``` shell
    helm init
    ```

1. Lägg till IoT Edge-lagringsplatsen och uppdatera helm-installation

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Skapa en IoT Hub](../iot-hub/iot-hub-create-through-portal.md), [registrera en IoT Edge-enhet](how-to-register-device-portal.md), och notera sin anslutningssträng.

1. Installera iotedged och IoT Edge-agenten i ditt kluster

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Öppna Kubernetes-instrumentpanelen i webbläsaren

    ```shell
    minikube dashboard
    ```

    Under klusternamnområden, ser du en för IoT Edge-enheten följer konventionen *msiot -\<iothub-name >-\<edgedevice-name >*. IoT Edge-agenten och iotedged poddarna måste vara igång i det här namnområdet.

1. Lägg till en simulerad temperatur sensor-modulen med hjälp av stegen i den [distribuera en modul](quickstart-linux.md#deploy-a-module) avsnittet i snabbstarten. IoT Edge-modulhantering görs från IoT Hub-portalen precis som andra IoT Edge-enhet. Gör lokala ändringar i modulkonfigurationen via Kubernetes verktyg rekommenderas inte eftersom de kan komma att skrivas över.

1. Inom några sekunder, uppdateras den **Poddar** IoT Edge hub visas på sidan under namnområdet edge-enhet i instrumentpanelen och simulerade sensor poddar som körs med IoT Edge hub pod mata in data i IoT Hub.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla resurser som skapats av edge-distribution, använder du följande kommando med det namn som används i steg 5 i föregående avsnitt.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Nästa steg

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Distribuera som en högtillgänglig edge-gateway 

Edge-enhet i ett Kubernetes-kluster kan användas som en IoT-gateway för efterföljande enheter. Den kan konfigureras för att vara motståndskraftiga mot nodfel, vilket ger hög tillgänglighet till edge-distributioner. Se den här [detaljerad genomgång](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) att använda IoT Edge i det här scenariot.