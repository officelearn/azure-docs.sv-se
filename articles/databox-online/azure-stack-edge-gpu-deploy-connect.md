---
title: Självstudie för att ansluta till, konfigurera, aktivera Azure Stack Edge-enhet med GPU i Azure Portal | Microsoft Docs
description: Självstudie för att distribuera Azure Stack Edge GPU instruerar dig att ansluta, konfigurera och aktivera den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 713f9eeef87cbfe3d8cb3d9717ad703328b54fe3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89262727"
---
# <a name="tutorial-connect-to-azure-stack-edge-with-gpu"></a>Självstudie: Anslut till Azure Stack Edge med GPU

I den här självstudien beskrivs hur du kan ansluta till din Azure Stack Edge-enhet med en onboard-GPU med hjälp av det lokala webb gränssnittet.

Anslutnings processen kan ta cirka 5 minuter att slutföra.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Ansluta till en fysisk enhet


## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar din Azure Stack Edge-enhet med GPU måste du kontrol lera att:

* Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Anslut till konfiguration av lokalt webb gränssnitt

1. Konfigurera Ethernet-kortet på datorn för att ansluta till Azure Stack Edge-enheten med en statisk IP-adress för 192.168.100.5 och undernät 255.255.255.0.

2. Anslut datorn till PORT 1 på enheten. Använd följande bild för att identifiera PORT 1 på enheten.

    ![Baksidan på en kabelansluten enhet](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    Enhetens bakgrunds plan kan se annorlunda ut beroende på vilken modell du har fått.


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
> * Förutsättningar
> * Ansluta till en fysisk enhet


Information om hur du konfigurerar nätverks inställningar på din Azure Stack Edge-enhet finns i:

> [!div class="nextstepaction"]
> [Konfigurera nätverk](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
