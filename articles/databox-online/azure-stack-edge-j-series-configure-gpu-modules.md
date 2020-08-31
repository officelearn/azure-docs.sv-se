---
title: Köra en GPU-modul på Microsoft Azure Stack Edge-enhet | Microsoft Docs
description: Beskriver hur du konfigurerar och kör en modul på GPU på en Azure Stack Edge-enhet via Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 307cfde3d983c5d821090e5b03e9bf4e9dd6f5fa
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085088"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-device"></a>Konfigurera och köra en modul på GPU på Azure Stack Edge-enhet

Din Azure Stack Edge-enhet innehåller en eller flera GPU: er (Graphics Processing Unit). GPU: er är ett populärt alternativ för AI-beräkningar när de erbjuder parallella bearbetnings möjligheter och är snabbare vid bild åter givning än CPU: er (Central bearbetnings enheter). Om du vill ha mer information om GPU: n som finns i Azure Stack Edge-enheten går du till [Azure Stack gräns enhetens tekniska specifikationer](azure-stack-edge-gpu-technical-specifications-compliance.md).

Den här artikeln beskriver hur du konfigurerar och kör en modul på GPU: n på Azure Stack Edge-enheten. I den här artikeln ska du använda en offentligt tillgänglig **siffra** för container som skrivits för NVIDIA T4-GPU: er. Den här proceduren kan användas för att konfigurera andra moduler som publiceras av NVIDIA för dessa GPU: er.


## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har åtkomst till en GPU-aktiverad Azure Stack Edge-enhet med en nod. Enheten aktive ras med en resurs i Azure.  

## <a name="configure-module-to-use-gpu"></a>Konfigurera modulen för att använda GPU

Följ dessa steg om du vill konfigurera en modul att använda GPU: n på Azure Stack Edge-enheten för att köra en modul.

1. I Azure Portal går du till den resurs som är kopplad till din enhet. 

2. Gå till **Edge compute > kom igång**. I panelen **Konfigurera Edge Compute** väljer du konfigurera.

    ![Konfigurera modulen för att använda GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. I bladet **Konfigurera Edge Compute** :

    1. För **IoT Hub**väljer du **Skapa ny**.
    2. Ange ett namn för den IoT Hub resurs som du vill skapa för din enhet. Om du vill använda en kostnads fri nivå väljer du en befintlig resurs. 
    3. Anteckna IoT Edges enheten och IoT gateway-enheten som skapas med IoT Hub resursen. Du kommer att använda den här informationen i senare steg.

    ![Konfigurera modulen för att använda GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. Det tar flera minuter att skapa IoT Hub resursen. När resursen har skapats väljer du **Visa konfiguration** i panelen **Konfigurera Edge-beräkning** för att visa information om den IoT Hub resursen.

    ![Konfigurera modulen för att använda GPU 4](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-4.png)

5. Gå till **Automatisk enhets hantering > IoT Edge**.

    ![Konfigurera modulen för att använda GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

    I den högra rutan ser du den IoT Edge enhet som är kopplad till Azure Stack Edge-enheten. Detta motsvarar IoT Edge enhet som du skapade i föregående steg när du skapade IoT Hub resursen. 
    
6. Välj den här IoT Edge enheten.

   ![Konfigurera modulen för att använda GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

7.  Välj **Ange moduler**.

    ![Konfigurera modulen för att använda GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

8. Välj **+ Lägg till** och välj sedan **+ IoT Edge modul**. 

    ![Konfigurera modulen för att använda GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

9. På fliken **Lägg till IoT Edge modul** :

    1. Ange **avbildnings-URI: n**. Du kommer att använda de allmänt tillgängliga NVIDIA-modulernas **siffror** här. 
    
    2. Ange att **principen för omstart** är **alltid**.
    
    3. Ange **önskat tillstånd** för att **köra**.
    
    ![Konfigurera modulen för att använda GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

10. På fliken **miljövariabler** anger du namnet på variabeln och motsvarande värde. 

    1. Använd NVIDIA_VISIBLE_DEVICES om du vill att den aktuella modulen ska använda en GPU på den här enheten. 

    2. Ange värdet till 0 eller 1. Detta säkerställer att minst en GPU används av enheten för den här modulen. När du ställer in värdet på 0, 1, betyder det att både GPU på enheten används av den här modulen.

        ![Konfigurera modulen för att använda GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

        Mer information om miljövariabler som du kan använda med nVidia-GPU: n finns i [NVIDIA container runtime](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > En GPU kan bara mappas till en modul. En modul kan dock använda en, båda eller inga GPU: er. 

11. Ange ett namn för modulen. Nu kan du välja att tillhandahålla alternativet för att skapa behållare och ändra modulens dubbla inställningar eller om det är färdigt väljer du **Lägg till**. 

    ![Konfigurera modulen för att använda GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

12. Kontrol lera att modulen körs och välj **Granska + skapa**.    

    ![Konfigurera modulen för att använda GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

13. På fliken **Granska + skapa** visas de distributions alternativ som du har valt. Granska alternativen och välj **skapa**.
    
    ![Konfigurera modulen för att använda GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

14. Anteckna **körnings status** för modulen. 
    
    ![Konfigurera modulen för att använda GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    Det tar några minuter för modulen att distribueras. Välj **Uppdatera** och se till att **körnings status** uppdateringen **körs**.

    ![Konfigurera modulen för att använda GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [miljövariabler som du kan använda med nVidia GPU](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
