---
title: Självstudie för att aktivera Azure Stack Edge-enhet med GPU i Azure Portal | Microsoft Docs
description: Självstudier för att distribuera Azure Stack Edge instruerar dig att aktivera den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: be3e7c7f9a5a8c48d8644eefef42b7020e2b37f4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89088339"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>Självstudie: Aktivera Azure Stack Edge med GPU

I den här självstudien beskrivs hur du kan aktivera din Azure Stack Edge-enhet med en inbyggd GPU med hjälp av det lokala webb gränssnittet.

Aktiverings processen kan ta cirka 5 minuter att slutföra.

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Krav
> * Aktivera den fysiska enheten

## <a name="prerequisites"></a>Krav

Innan du konfigurerar och konfigurerar din Azure Stack Edge-enhet med GPU måste du kontrol lera att:

* För den fysiska enheten: 
    
    - Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - Du har konfigurerat nätverks-och beräknings nätverks inställningarna enligt beskrivningen i [Konfigurera nätverk, beräknings nätverk, webbproxy](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    - Du har laddat upp dina egna eller genererat enhets certifikat på enheten om du har ändrat enhets namnet eller DNS-domänen via **enhets** sidan. Om du inte har gjort det här steget visas ett fel meddelande när enheten aktive ras och aktiveringen blockeras. Mer information finns i [Konfigurera certifikat](azure-stack-edge-gpu-deploy-configure-certificates.md).
    
* Du har aktiverings nyckeln från Azure Stack Edge-tjänsten som du skapade för att hantera Azure Stack Edge-enheten. Mer information finns i [förbereda för att distribuera Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Aktivera enheten

1. Gå till sidan **Kom igång** i enhetens lokala webb gränssnitt.
2. På **aktiverings** panelen väljer du **Aktivera**. 

    ![Sidan Cloud information för det lokala webb gränssnittet](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. I fönstret **Aktivera** anger du **aktiverings nyckeln** som du fick i [hämta aktiverings nyckeln för Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Välj **Använd**.

    ![Sidan Cloud information för det lokala webb gränssnittet](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Först är enheten aktive rad. Du uppmanas sedan att ladda ned nyckel filen.
    
    ![Sidan Cloud information för det lokala webb gränssnittet](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Välj **Hämta nyckel fil** och spara *keys.jspå* filen på en säker plats utanför enheten. **Den här nyckel filen innehåller återställnings nycklarna för operativ system disken och data diskarna på enheten**. Här följer innehållet i *keys.jspå* filen:

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    I följande tabell förklaras de olika nycklarna här:
    
    |Fält  |Beskrivning  |
    |---------|---------|
    |`Id`    | Detta är ID för enheten.        |
    |`DataVolumeBitLockerExternalKeys`|Detta är BitLocker-nycklar för data diskarna och används för att återställa lokala data på enheten.|
    |`SystemVolumeBitLockerRecoveryKey`| Det här är BitLocker-nyckeln för system volymen. Den här nyckeln hjälper till att återställa system konfigurationen och system data för enheten. |
    |`ServiceEncryptionKey`| Den här nyckeln skyddar de data som flödar genom Azure-tjänsten. Den här nyckeln garanterar att en kompromiss i Azure-tjänsten inte resulterar i att lagrad information komprometteras. |

6. Gå till **översikts** sidan. Enhetens tillstånd bör visas som **aktive rad**.

    ![Sidan Cloud information för det lokala webb gränssnittet](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
Enhets aktiveringen har slutförts. Nu kan du lägga till resurser på enheten.


## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Krav
> * Aktivera den fysiska enheten

Information om hur du överför data med Azure Stack Edge-enhet finns i:

> [!div class="nextstepaction"]
> [Överföra data med Azure Stack Edge](./azure-stack-edge-j-series-deploy-add-shares.md)
