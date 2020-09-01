---
title: Självstudie för att filtrera, analysera data med beräkning på Azure Stack Edge-GPU | Microsoft Docs
description: Lär dig hur du konfigurerar Compute-rollen på Azure Stack Edge-GPU och använder den för att transformera data innan du skickar dem till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: a5e0817050f8411dfc7fc64edae21f1b927e4e5c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89262999"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-gpu-device"></a>Självstudie: Konfigurera Compute på Azure Stack Edge GPU-enhet

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

I den här självstudien beskrivs hur du konfigurerar en beräknings roll och skapar ett Kubernetes-kluster på din Azure Stack Edge-enhet. 

Den här proceduren kan ta cirka 20 till 30 minuter att slutföra.


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Hämta Kubernetes-slutpunkter

 
## <a name="prerequisites"></a>Förutsättningar

Innan du ställer in en beräknings roll på Azure Stack Edge-enhet ser du till att:

- Du har aktiverat Azure Stack Edge-enheten enligt beskrivningen i [aktivera Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md).
- Kontrol lera att du har följt instruktionerna i [Aktivera beräknings nätverk](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) och:
    - Aktiverat ett nätverks gränssnitt för beräkning.
    - Tilldelade Kubernetes Node IP-adresser och Kubernetes external service IP-adresser.

## <a name="configure-compute"></a>Konfigurera beräkning

Om du vill konfigurera Compute på Azure Stack Edge skapar du en IoT Hub resurs via Azure Portal.

1. I Azure Portal av Azure Stack Edge-resursen går du till **Översikt**. Välj **Kom igång**i den högra rutan på **beräknings** panelen.

    ![Kom igång med Compute](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. På panelen **Konfigurera Edge Compute** väljer du **Konfigurera beräkning**.

    ![Konfigurera beräkning](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. Ange följande på bladet **Konfigurera Edge Compute** :

   
    |Fält  |Värde  |
    |---------|---------|
    |IoT Hub     | Välj från **ny** eller **befintlig**. <br> Som standard används en standard-nivå (S1) för att skapa en IoT-resurs. Om du vill använda en IoT-resurs på kostnads fri nivå skapar du en och väljer sedan den befintliga resursen. <br> I varje fall använder IoT Hub resursen samma prenumeration och resurs grupp som används av Azure Stack Edge-resursen.     |
    |Name     |Ange ett namn för din IoT Hub-resurs.         |

    ![Kom igång med Compute](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Välj **Skapa**. Det tar flera minuter att skapa en IoT Hub-resurs. När IoT Hub resursen har skapats kan du **Konfigurera** Compute-panelen för att Visa beräknings konfigurationen. 

    ![Kom igång med Compute](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Bekräfta att Edge Compute-rollen har kon figurer ATS genom att välja **Visa beräkning** på panelen **Konfigurera beräkning** .
    
    ![Kom igång med Compute](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Om dialog rutan **Konfigurera beräkning** stängs innan IoT Hub är kopplad till Azure Stack Edge-enheten, skapas IoT Hub men visas inte i beräknings konfigurationen. 
    
När Edge-beräkningsrollen har konfigurerats på Edge-enheten så skapas två enheter: en IoT-enhet och en IoT Edge-enhet. Bägge enheter kan visas i IoT Hub-resursen. En IoT Edge runtime körs också på den här IoT Edge enheten. För tillfället är det bara Linux-plattformen tillgänglig för din IoT Edge-enhet.

Det kan ta 20-30 minuter för dig att konfigurera Compute sedan bakgrunden, de virtuella datorerna och Kubernetes-klustret skapas. 

När du har konfigurerat beräkningen i Azure Portal finns ett Kubernetes-kluster och en standard användare som är associerad med IoT-namnrymden (ett system namn område som styrs av Azure Stack Edge). 

## <a name="get-kubernetes-endpoints"></a>Hämta Kubernetes-slutpunkter

Om du vill konfigurera en klient för åtkomst till Kubernetes-kluster behöver du Kubernetes-slutpunkten. Följ dessa steg för att hämta Kubernetes API-slutpunkt från det lokala användar gränssnittet för din Azure Stack Edge-enhet.

1. I det lokala webb gränssnittet på enheten går du till sidan **enheter** .
2. Under **enhetens slut punkter**kopierar du **Kubernetes API-tjänstens** slut punkt. Den här slut punkten är en sträng i följande format: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Enhets sida i lokalt användar gränssnitt](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Spara slut punkts strängen. Du kommer att använda detta senare när du konfigurerar en-klient för åtkomst till Kubernetes-klustret via kubectl.

4. När du är i det lokala webb gränssnittet kan du:

    - Gå till Kubernetes-API, Välj **Avancerade inställningar** och ladda ned en avancerad konfigurations fil för Kubernetes. 

        ![Enhets sida i lokalt användar gränssnitt 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Om du har fått en nyckel från Microsoft (Välj användare kan ha detta) kan du använda den här konfigurations filen.

        ![Enhets sida i lokalt användar gränssnitt 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Du kan också gå till **Kubernetes Dashboard** -slutpunkt och ladda ned en `aseuser` konfigurations fil. 
    
        ![Enhets sida i lokalt användar gränssnitt 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        Med `aseuser` konfigurations filen kan du felsöka eventuella problem som rör `iotedge` namn området i ditt Kubernetes-kluster. Mer information finns i [fel sökning av problem med Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge). 


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Hämta Kubernetes-slutpunkter


Information om hur du administrerar Azure Stack Edge-enheten finns i:

> [!div class="nextstepaction"]
> [Använd lokalt webb gränssnitt för att administrera en Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md)
