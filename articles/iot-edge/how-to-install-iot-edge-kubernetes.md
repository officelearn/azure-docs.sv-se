---
title: Så här installerar du IoT Edge på Kubernetes | Microsoft Docs
description: Lär dig hur du installerar IoT Edge på Kubernetes med en lokal miljö för utvecklings kluster
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7f3627a79cad6833b5fb20f3c829c1e3bcbd9c3e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457341"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Så här installerar du IoT Edge på Kubernetes (för hands version)

IoT Edge kan integreras med Kubernetes med hjälp av den som ett elastiskt infrastruktur lager med hög tillgänglighet. Den registrerar en IoT Edge *anpassad resurs definition* (CRD) med Kubernetes API-servern. Dessutom tillhandahåller den en *operatör* (IoT Edge agent) som stämmer överens med det lokala klustrets tillstånd. 

Livs längden för modulen hanteras av Kubernetes Scheduler, som underhåller modulens tillgänglighet och väljer deras placering. IoT Edge hanterar Edge-programplattformen som körs överst och som kontinuerligt avstämr det önskade läget som anges i IoT Hub med tillstånd i gräns klustret. Edge-programmodellen är fortfarande den bekanta modellen baserat på IoT Edge moduler och vägar. Operatorn IoT Edge agent utför *Automatisk* översättning till Kubernetes-inbyggda konstruktioner som poddar, distributioner, tjänster osv.

Här är ett diagram med hög nivå arkitektur:

![Kubernetes båge](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Varje komponent i Edge-distributionen är begränsad till ett Kubernetes-namnområde som är unikt för enheten, vilket gör det möjligt att dela samma kluster resurser mellan flera gräns enheter och deras distributioner.

>[!NOTE]
>IoT Edge på Kubernetes finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Installera lokalt för en snabb test miljö

### <a name="prerequisites"></a>Förutsättningar

* Kubernetes 1,10 eller senare. Om du inte har en befintlig kluster konfiguration kan du använda [Minikube](https://kubernetes.io/docs/setup/minikube/) för en lokal kluster miljö. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), Kubernetes Package Manager.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) för att visa och interagera med klustret.

### <a name="setup-steps"></a>Installations steg

1. Starta **Minikube**

    ``` shell
    minikube start
    ```

1. Initiera **Helm** -Server komponenten (*till*-) i klustret

    ``` shell
    helm init
    ```

1. Lägg till IoT Edge lagrings platsen och uppdatera Helm-installationen

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Skapa en IoT Hub](../iot-hub/iot-hub-create-through-portal.md), [Registrera en IoT Edge enhet](how-to-register-device.md)och anteckna anslutnings strängen.

1. Installera iotedged och IoT Edge agent i klustret

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

    Under kluster namn rymder visas en för den IoT Edge enheten enligt konventionen *msiot-\<iothub >-\<edgedevice-name >* . IoT Edge-agenten och iotedged-poddar bör vara igång i det här namn området.

1. Lägg till en simulerad temperatur sensor-modul med stegen i avsnittet [distribuera en modul](quickstart-linux.md#deploy-a-module) i snabb starten. Hantering av IoT Edge-modulen görs från IoT Hub Portal precis som vilken annan IoT Edge enhet som helst. Att göra lokala ändringar i Kubernetes-verktyg rekommenderas inte eftersom de kan komma att skrivas över.

1. Om några sekunder uppdaterar sidan **poddar** under gräns enhetens namnrymd på instrument panelen, visar IoT Edge hubben och den simulerade sensor poddar som körs med IoT Edge Hub-Pod som matar in data i IoT Hub.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla resurser som har skapats av Edge-distributionen använder du följande kommando med det namn som användes i steg 5 i föregående avsnitt.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Nästa steg

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Distribuera som en Edge-Gateway med hög tillgänglighet 

Gräns enheten i ett Kubernetes-kluster kan användas som IoT-Gateway för underordnade enheter. Det kan konfigureras så att det blir flexibelt för nodfel, vilket ger hög tillgänglighet till Edge-distributioner. I den här [detaljerade genom gången](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) ska du använda IoT Edge i det här scenariot.