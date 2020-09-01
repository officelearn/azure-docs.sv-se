---
title: Självstudie för att ansluta till, konfigurera, aktivera Azure Stack Edge-enhet med GPU i Azure Portal | Microsoft Docs
description: Självstudier för att distribuera Azure Stack Edge instruerar dig att ansluta, konfigurera och aktivera den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: c98d6644dba0b3ca51f07f01ec27fee697369f2b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89088260"
---
# <a name="tutorial-connect-to-azure-stack-edge-with-gpu"></a>Självstudie: Anslut till Azure Stack Edge med GPU

I den här självstudien beskrivs hur du kan ansluta till din Azure Stack Edge-enhet med en onboard-GPU med hjälp av det lokala webb gränssnittet.

Anslutnings processen kan ta cirka 5 minuter att slutföra.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Krav
> * Ansluta till en fysisk enhet


## <a name="prerequisites"></a>Krav

Innan du konfigurerar och konfigurerar din Azure Stack Edge-enhet med GPU måste du kontrol lera att:

* Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Anslut till konfiguration av lokalt webb gränssnitt

1. Konfigurera Ethernet-kortet på datorn för att ansluta till Azure Stack Edge-enheten med en statisk IP-adress för 192.168.100.5 och undernät 255.255.255.0.

2. Anslut datorn till PORT 1 på enheten. Använd följande bild för att identifiera PORT 1 på enheten.

    ![Baksidan på en kabelansluten enhet](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)


3. Öppna ett webbläsarfönster och öppna det lokala webb gränssnittet för enheten på `https://192.168.100.10` .  
    Den här åtgärden kan ta några minuter efter att enheten har Aktiver ATS. 

    Du ser ett fel eller en varning som anger att det är problem med webbplatsens säkerhetscertifikat. 
   
    ![Webbplats säkerhets certifikat fel meddelande](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Välj **Fortsätt till den här webb sidan**.  
    De här stegen kan variera beroende på vilken webbläsare du använder.

5. Logga in på enhetens webb gränssnitt. Standard lösen ordet är *Password1*. 
   
    ![Inloggnings sida för Azure Stack Edge-enhet](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. Ändra enhetens administratörs lösen ord vid prompten.  
    Det nya lösen ordet måste innehålla mellan 8 och 16 tecken. Det måste innehålla tre av följande tecken: versaler, gemener, numeriska tecken och specialtecken.

Du är nu på sidan **Översikt** på enheten. Nästa steg är att konfigurera nätverks inställningarna för enheten.


## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Krav
> * Ansluta till en fysisk enhet


Information om hur du konfigurerar nätverks inställningar på din Azure Stack Edge-enhet finns i:

> [!div class="nextstepaction"]
> [Konfigurera nätverk](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
