---
title: Övervaka din Azure Stack Edge-enhet via Kubernetes-instrumentpanelen | Microsoft Docs
description: Beskriver hur du kommer åt och använder Kubernetes-instrumentpanelen för att övervaka din Azure Stack Edge-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 9224888a38c86e35df9ad516c761fd7012824c15
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085284"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-gpu-device"></a>Använd Kubernetes-instrumentpanelen för att övervaka Azure Stack Edge-GPU-enhet

Den här artikeln beskriver hur du kommer åt och använder Kubernetes-instrumentpanelen för att övervaka din Azure Stack Edge-GPU-enhet. Om du vill övervaka din enhet kan du använda diagram i Azure Portal, Visa Kubernetes-instrumentpanelen eller köra `kubectl` kommandon via enhetens PowerShell-gränssnitt. 

Den här artikeln fokuserar bara på de övervaknings aktiviteter som kan utföras på Kubernetes-instrumentpanelen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Öppna Kubernetes-instrumentpanelen på din enhet
> * Visa moduler som har distribuerats på din enhet
> * Hämta IP-adress för program som distribuerats på din enhet
> * Visa behållar loggar för moduler som har distribuerats på din enhet


## <a name="about-kubernetes-dashboard"></a>Om Kubernetes-instrumentpanel

Kubernetes-instrumentpanelen är ett webbaserat användar gränssnitt som du kan använda för att felsöka dina program i behållare. Kubernetes-instrumentpanelen är ett UI-baserat alternativ till Kubernetes- `kubectl` kommandoraden. 

På din Azure Stack Edge-enhet kan du använda Kubernetes-instrumentpanelen i skrivskyddat läge för att få en översikt över de program som körs på din Azure Stack Edge-enhet, Visa status för Kubernetes kluster resurser och se eventuella fel som har inträffat på enheten.

## <a name="access-dashboard"></a>Åtkomst instrument panel

Kubernetes-instrumentpanelen är skrivskyddad och körs på den Kubernetes huvud noden på port 31000. Följ de här stegen för att få åtkomst till instrument panelen: 

1. I enhetens lokala användar gränssnitt, gå till **enhet** och gå sedan till **enhetens slut punkter**. Välj instrument panelens URL för Kubernetes för att öppna instrument panelen i en webbläsare.

    ![Kubernetes Dashboard-URL på enhets sidan i lokalt användar gränssnitt](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. På **Kubernetes instrument panels inloggning** väljer du **token**. 
1. Ange en token. 
    1. Om du vill hämta token [ansluter du via PowerShell-gränssnittet på enheten](azure-stack-edge-gpu-connect-powershell-interface.md).
    1. Kör kommandot:  `Get-HcsKubernetesDashboardToken`
    
    1. Kopiera den token-sträng som visas för dig vid prompten. Här är exempel på utdata:
        
        ```powershell
        [10.100.10.10]: PS>Get-HcsKubernetesDashboardToken
        eyJhbGciOiJSUzI1NiIsImtpZCI6IkpFTEtBYTMyZ0Ezb01OYTVFSnVaUV85OWtLdXNETTZQR0k0UlFybGdReFUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi03czZ6ayIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU3NzY3ZDAzLTJlYWUtNDlkMi1hNDEyLTNkOTU3MDFiMThiMyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.UgNrpVYVJBEaWxFlljuENUQQmzFXMYG2VsJUIYFdp2AO20zX0k5dRvwcCpeGlqSKb9MyYjG0c6RmT9uCOZk-vAwt7btszQLD7KPCwh_nn_NiIyO8ApgGRYZP8NuP8CBTX3tl_hpwfHtZ0ksbuKAduIL-0uPF0rG5wgLk9cTEw6fKSc2UZW6bIzhNSp_uSiP6MexOS6OftF9JFZejkIGd33dSp-k-tgFlm2Zy96sdFJC0q-XsH7jygiVnfxA9XMs5wqW26LkCh0rfO2WI3C1XFK-4TpufRZLJHo5WPlu-Tnsxa8xmtk2jQ3us-sXcBRrvhPNPrNKkbqc9hbjmWfGD0Q
        [10.100.10.10]: PS>
        ```
        
1. Välj **Logga in**.

    ![Logga in på Kubernetes-instrumentpanelen](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png)

6. Nu kan du Visa Kubernetes-instrumentpanelen för din Azure Stack Edge-enhet i skrivskyddat läge.

    ![Kubernetes instrument panels huvud sida](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Visa status för modul

Compute-moduler är behållare som har en affärs logik implementerad. Du kan använda instrument panelen för att kontrol lera om en Compute-modul har distribuerats på Azure Stack Edge-enhet.

Om du vill visa status för modulen följer du de här stegen på instrument panelen:

1. Gå till **namn område**i det vänstra fönstret på instrument panelen. Filtrera efter namn området där IoT Edge moduler visas, i det här fallet **iotedge**.
1. I den vänstra rutan går du till **arbets belastningar > distributioner**.
1. I den högra rutan visas alla moduler som har distribuerats på enheten. I det här fallet distribuerades en GettingStartedWithGPU-modul på Azure Stack Edge. Du kan se att modulen har distribuerats.

    ![Visa modul distribution](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Hämta IP-adress för tjänster eller moduler

Du kan använda instrument panelen för att hämta IP-adresserna till tjänsterna eller modulerna som du vill exponera utanför Kubernetes-klustret. 

Du tilldelar IP-intervallet för de här externa tjänsterna via det lokala webb gränssnittet på enheten på sidan **Compute Network Settings** . När du har distribuerat IoT Edge moduler kanske du vill hämta IP-adressen som tilldelats till en speciell modul eller tjänst. 

Följ dessa steg på instrument panelen för att hämta IP-adressen:

1. Gå till **namn område**i det vänstra fönstret på instrument panelen. Filtrera efter det namn område där en extern tjänst distribueras, i det här fallet **iotedge**.
1. I den vänstra rutan går du till **identifiering och belastnings utjämning > tjänster**.
1. I den högra rutan visas alla tjänster som körs i `iotedge` namn området på Azure Stack Edge-enheten.

    ![Hämta IP för externa tjänster](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Visa containerloggar

Det finns instanser där du behöver Visa behållar loggarna. Du kan använda instrument panelen för att hämta loggar för en speciell behållare som du har distribuerat i ditt Kubernetes-kluster.

Följ de här stegen på instrument panelen för att Visa behållar loggarna:

1. Gå till **namn område**i det vänstra fönstret på instrument panelen. Filtrera efter det namn område där IoT Edge moduler distribueras, i det här fallet **iotedge**.
1. I den vänstra rutan går du till **arbets belastningar > poddar**.
1. I den högra rutan visas alla poddar som körs på enheten. Identifiera Pod som kör den modul som du vill visa loggarna för. Välj den lodräta ellipsen för den Pod som du har identifierat och välj **loggar**på snabb menyn.

    ![Visa behållar loggar 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. Loggarna visas i ett logg visnings program som är inbyggt i instrument panelen. Du kan också hämta loggarna.

    ![Visa behållar loggar 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="next-steps"></a>Nästa steg

Lär dig hur du felsöker Kubernetes problem <!--insert link-->.
