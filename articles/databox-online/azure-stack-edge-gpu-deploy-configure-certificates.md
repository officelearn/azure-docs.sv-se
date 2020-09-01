---
title: Självstudie för att konfigurera certifikat för Azure Stack Edge-enhet med GPU i Azure Portal | Microsoft Docs
description: Självstudie för att distribuera Azure Stack Edge med GPU instruerar dig att konfigurera certifikat på den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 80a857f80fd2c164637e591fbab43123659cd2f7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268184"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-with-gpu"></a>Självstudie: Konfigurera certifikat för din Azure Stack Edge med GPU

I den här självstudien beskrivs hur du kan konfigurera certifikat för din Azure Stack Edge-enhet med en onboard-GPU med hjälp av det lokala webb gränssnittet.

Den tid det tar för det här steget kan variera beroende på vilket alternativ du väljer och hur certifikat flödet upprättas i din miljö.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera certifikat för den fysiska enheten

## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar din Azure Stack Edge-enhet med GPU måste du kontrol lera att:

* Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
* Om du planerar att ta med dina egna certifikat:
    - Du bör ha dina certifikat klara i rätt format, inklusive signerings kedjans certifikat.
<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-j-series-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Konfigurera certifikat för enheten


1. På **säkerhets** panelen väljer du **Konfigurera** för certifikat. 

    ![Sidan certifikat för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)

2. Beroende på om du har ändrat enhets namnet eller DNS-domänen på **enhets installations** panelen kan du välja något av följande alternativ för dina certifikat.

    - Om du inte har ändrat enhets namnet eller DNS-domänen i det tidigare steget och inte vill ta med dina egna certifikat kan du hoppa över det här steget och fortsätta till nästa steg. Enheten har automatiskt genererat självsignerade certifikat som börjar med. 

        ![Sidan certifikat för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Om du har ändrat enhets namnet eller DNS-domänen kan du välja något av följande alternativ för att aktivera enheten: 
    
        - **Generera alla enhets certifikat**. Enhets certifikaten bör endast användas för testning och används inte med produktions arbets belastningar. * * mer information finns i [skapa enhets certifikat på Azure Stack Edge](#generate-device-certificates).

        - **Ta med dina egna certifikat**. Du kan ta med dina egna signerade slut punkts certifikat och motsvarande signerings kedjor. Du lägger först till signerings kedjan och laddar sedan upp slut punkts certifikaten. **Vi rekommenderar att du alltid använder dina egna certifikat för produktions arbets belastningar.** Mer information finns i ta med [dina egna certifikat på din Azure Stack Edge-enhet](#bring-your-own-certificates).
    
        - Du kan ta med några av dina egna certifikat och generera vissa enhets certifikat. Alternativet för att **Skapa certifikat** återskapar bara enhets certifikaten.

    - Om du har ändrat enhets namnet eller DNS-domänen och inte genererar certifikat eller tar med dina egna certifikat, blockeras aktiveringen.


### <a name="generate-device-certificates"></a>Generera enhets certifikat

Följ dessa steg om du vill generera enhets certifikat.

Använd de här stegen för att återskapa och hämta Azure Stack gräns enhetens certifikat:

1. I enhetens lokala användar gränssnitt går du till **konfiguration > certifikat**. Välj **Skapa certifikat**.

    ![Generera och hämta certifikat 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. I **generera enhets certifikat**väljer du **generera**.

    ![Generera och hämta certifikat 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Enhets certifikaten skapas och appliceras nu. 
    
    > [!IMPORTANT]
    > Ta inte med dina egna certifikat och försök att lägga till dem via alternativet **+ Lägg till certifikat** för att skapa en process.

    Du meddelas när åtgärden har slutförts. Starta om webbläsaren för att undvika eventuella problem med cachen. 
    
    ![Generera och hämta certifikat 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. På sidan **certifikat** visas kolumnen **Ladda ned** nu och länkar för att hämta de återskapade certifikaten är tillgängliga. 

    ![Generera och hämta certifikat 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

4. Välj länken Hämta för ett certifikat och när du uppmanas att spara certifikatet. 

    ![Generera och hämta certifikat 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Upprepa processen för alla certifikat som du vill ladda ned. 
    
    ![Generera och hämta certifikat 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Enhets genererade certifikat sparas som DER-certifikat med följande namn format: 

    `<Device name>_<Endpoint name>.cer`. Dessa certifikat innehåller den offentliga nyckeln för motsvarande certifikat som är installerade på enheten. 

Du måste installera dessa certifikat på klient systemet som du använder för att få åtkomst till slut punkterna på ASE-enheten. Dessa certifikat etablerar förtroende mellan klienten och enheten.

Om du vill importera och installera dessa certifikat på klienten som du använder för att komma åt enheten följer du stegen i [Importera certifikat på klienterna som har åtkomst till din Azure Stack Edge-enhet](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Om du använder Azure Storage Explorer måste du installera certifikat på klienten i PEM-format och du måste konvertera enheten genererade certifikat till PEM-format. 

> [!IMPORTANT]
> - Nedladdnings länken är bara tillgänglig för enhets genererade certifikat och inte om du använder dina egna certifikat.
> - Du kan välja att ha en blandning av enhets genererade certifikat och ta med dina egna certifikat så länge andra certifikat krav uppfylls. Mer information finns i [certifikat krav](azure-stack-edge-j-series-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Ta med dina egna certifikat

Följ dessa steg om du vill lägga till egna certifikat, inklusive signerings kedjan.

1. För att överföra certifikat, på sidan **certifikat** , väljer du **+ Lägg till certifikat**.

    ![Sidan certifikat för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Ladda upp signerings kedjan först och välj **verifiera & Lägg till**.

    ![Sidan certifikat för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Nu kan du ladda upp andra certifikat. Du kan till exempel Ladda upp certifikaten för Azure Resource Manager och Blob Storage-slutpunkten.

    ![Sidan certifikat för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    Du kan också ladda upp det lokala webb GRÄNSSNITTs certifikatet. När du har laddat upp det här certifikatet måste du starta webbläsaren och rensa cacheminnet. Sedan måste du ansluta till det lokala webb gränssnittet för enheten.  

    ![Sidan certifikat för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    Du kan också ladda upp Node-certifikatet.

    ![Sidan certifikat för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    Sidan certifikat bör uppdateras för att avspegla de nyligen tillagda certifikaten.

    ![Sidan certifikat för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)  

    > [!NOTE]
    > Förutom för det offentliga Azure-molnet behövs signerings kedjans certifikat innan aktiveringen för alla molnappar (Azure Government eller Azure Stack).


Enheten är nu redo att aktive ras.


## <a name="next-steps"></a>Nästa steg

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera certifikat för den fysiska enheten

Information om hur du aktiverar Azure Stack Edge-enheten finns i:

> [!div class="nextstepaction"]
> [Aktivera Azure Stack gräns enhet](./azure-stack-edge-gpu-deploy-activate.md)
