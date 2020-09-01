---
title: Självstudie för att konfigurera nätverks inställningar för Azure Stack Edge-enhet med GPU i Azure Portal | Microsoft Docs
description: Självstudier för att distribuera Azure Stack Edge instruerar dig att konfigurera inställningar för nätverk, beräknings nätverk och webbproxy för den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 36d331de3a93e04932c4bce4d14704b33f69fab6
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181552"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-with-gpu"></a>Självstudie: Konfigurera nätverk för Azure Stack Edge med GPU

I den här självstudien beskrivs hur du konfigurerar nätverk för din Azure Stack Edge-enhet med en onboard-GPU med hjälp av det lokala webb gränssnittet.

Anslutnings processen kan ta ungefär 20 minuter att slutföra.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Krav
> * Konfigurera nätverk
> * Aktivera beräknings nätverk
> * Konfigurera webbproxy


## <a name="prerequisites"></a>Krav

Innan du konfigurerar och konfigurerar din Azure Stack Edge-enhet med GPU måste du kontrol lera att:

* Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
* Du har anslutit till det lokala webb gränssnittet på enheten enligt beskrivningen i [Anslut till Azure Stack Edge](azure-stack-edge-gpu-deploy-connect.md)


## <a name="configure-network"></a>Konfigurera nätverk

Sidan **Kom igång** visar de olika inställningar som krävs för att konfigurera och registrera den fysiska enheten med Azure Stack Edge-tjänsten. 

Följ dessa steg om du vill konfigurera nätverket för enheten.

1. Gå till sidan **Kom igång** i enhetens lokala webb gränssnitt. 

2. I panelen **nätverk** väljer du **Konfigurera**.  
    
    ![Panelen nätverks inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    Det finns sex nätverks gränssnitt på den fysiska enheten. PORT 1 och PORT 2 är 1-Gbps nätverks gränssnitt. PORT 3, PORT 4, PORT 5 och PORT 6 är alla 25 Gbit/s nätverks gränssnitt som också kan fungera som 10 Gbit/s nätverks gränssnitt. PORT 1 konfigureras automatiskt som en port för endast hantering, och PORT 2 till PORT 6 är alla data portar. För en ny enhet visas sidan **nätverks inställningar** enligt nedan.
    
    ![Sidan nätverks inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)


   
3. Om du vill ändra nätverks inställningarna väljer du en port och i den högra rutan som visas ändrar du IP-adress, undernät, Gateway, primär DNS och sekundär DNS. 

    - Om du väljer port 1 kan du se att den är förkonfigurerad som statisk. 

        ![Lokalt webb gränssnitt "port 1 nätverks inställningar"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - Om du väljer port 2, Port 3, Port 4 eller port 5, konfigureras alla dessa portar som DHCP som standard.

        ![Lokalt webb gränssnitt "Port 3 nätverks inställningar"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    Tänk på följande när du konfigurerar nätverks inställningarna:

   * Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. En IP-adress, undernät, gateway och DNS tilldelas automatiskt.
   * Om DHCP inte är aktive rad kan du tilldela statiska IP-adresser om det behövs.
   * Du kan konfigurera nätverks gränssnittet som IPv4.
   * På 25 Gbit/s-gränssnitten kan du ange RDMA-läget (Remote Direct Access Memory) till iWarp eller RoCE (RDMA över konvergerat Ethernet). Om låg fördröjning är det primära kravet och skalbarheten inte är ett problem, Använd RoCE. När svars tiden är ett nyckel krav, men enkel användning och skalbarhet också är höga prioriteringar, är iWARP den bästa kandidaten.
   * Serie numret för vilken port som helst motsvarar nodens serie nummer.

    När enhets nätverket har kon figurer ATS uppdateras sidan på det sätt som visas nedan.

    ![Sidan nätverks inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     >[!NOTE]
     >
     > * Vi rekommenderar att du inte byter lokal IP-adress för nätverks gränssnittet från statisk till DCHP, om du inte har en annan IP-adress för att ansluta till enheten. Om du använder ett nätverks gränssnitt och växlar till DHCP, kan det vara något sätt att fastställa DHCP-adressen. Om du vill ändra till en DHCP-adress väntar du tills enheten har Aktiver ATS med tjänsten och ändrar sedan. Du kan sedan Visa IP-adresserna för alla nätverkskort i **enhets egenskaperna** i Azure Portal för din tjänst.


    När du har konfigurerat och tillämpat nätverks inställningarna går du tillbaka för att **komma igång**.

## <a name="enable-compute-network"></a>Aktivera beräknings nätverk

Följ dessa steg om du vill aktivera Compute och konfigurera Compute Network.

1. Gå till sidan **Kom igång** i enhetens lokala webb gränssnitt. Välj **beräknings nätverk**på panelen **nätverk** .  

    ![Compute-sidan i lokalt användar gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)

2. På sidan **Beräkna** väljer du ett nätverks gränssnitt som du vill aktivera för beräkning. 

    ![Compute-sidan i lokalt användar gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

2. I dialog rutan **nätverks inställningar** väljer du **Aktivera**. När du aktiverar beräkning skapas en virtuell växel på enheten i nätverks gränssnittet. Den virtuella växeln används för beräknings infrastrukturen på enheten. 
    
3. Tilldela **Kubernetes Node IP-adresser**. Dessa statiska IP-adresser är för den virtuella Compute-datorn. 

    För en *n*-Node-enhet anges ett sammanhängande intervall med minst *n + 1* IPv4-adresser (eller mer) för den virtuella beräknings datorn med hjälp av start-och slut-IP-adresserna. Den angivna Azure Stack Edge är en 1-nods enhet, minst 2 sammanhängande IPv4-adresser tillhandahålls. 

4. Tilldela **Kubernetes external service IP-adresser**. Dessa är också IP-adresser för belastnings utjämning. Dessa sammanhängande IP-adresser är för tjänster som du vill exponera utanför Kubernetes-klustret och du anger det statiska IP-intervallet beroende på antalet tjänster som exponeras. 
    
    > [!IMPORTANT]
    > Vi rekommenderar starkt att du anger minst 1 IP-adress för Azure Stack Edge Hub-tjänsten för att få åtkomst till Compute-moduler. Alternativt kan du ange ytterligare IP-adresser för andra tjänster/IoT Edge moduler (1 per tjänst/modul) som behöver nås från utanför klustret. IP-adresserna för tjänsten kan uppdateras senare. 
    
5. Välj **Använd**.

    ![Compute-sidan i lokalt användar gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)


## <a name="configure-web-proxy"></a>Konfigurera webbproxy

Detta är en valfri konfiguration.

> [!IMPORTANT]
> * Om du aktiverar beräknings-och användnings IoT Edge modul på Azure Stack Edge-enhet rekommenderar vi att du ställer in webbproxy-autentisering som **ingen**. NTLM stöds inte.
>* PAC-filer (Proxy-Auto config) stöds inte. En PAC-fil definierar hur webbläsare och andra användar agenter automatiskt kan välja rätt proxyserver (åtkomst metod) för att hämta en viss URL. Proxyservrar som försöker avlyssna och läsa all trafik (och sedan signera om allting med sin egen certifiering) är inte kompatibelt eftersom proxyns certifikat inte är betrott. Oftast fungerar transparenta proxyservrar bra med Azure Stack Edge. Icke-transparenta webb-proxyservrar stöds inte.

1. Gå till sidan **Kom igång** i enhetens lokala webb gränssnitt.
2. Konfigurera inställningarna för webbproxyservern på panelen **nätverk** . Även om webbproxy-konfigurationen är valfri, kan du bara konfigurera den på den här sidan om du använder en webbproxy.

   ![Sidan Web Proxy-inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

3. Gör följande på sidan **Web Proxy-inställningar** :

    1. I rutan **webbproxy-URL** anger du webb adressen i följande format: `http://host-IP address or FQDN:Port number` . HTTPS-URL: er stöds inte.

    2. Under **autentisering**väljer du **ingen** eller **NTLM**. Om du aktiverar beräknings-och användnings IoT Edge-modulen på Azure Stack Edge-enhet rekommenderar vi att du ställer in webbproxy-autentisering på **ingen**. **NTLM** stöds inte.

    3. Om du använder autentisering anger du ett användar namn och lösen ord.

    4. Om du vill validera och tillämpa de konfigurerade webbproxyinställningarna väljer du **tillämpa**.
    
   ![Sidan Web Proxy-inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

4. När inställningarna har tillämpats går du tillbaka till **Kom igång**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Krav
> * Konfigurera nätverk
> * Aktivera beräknings nätverk
> * Konfigurera webbproxy


Information om hur du konfigurerar Azure Stack Edge-enheten finns i:

> [!div class="nextstepaction"]
> [Konfigurera enhetsinställningar](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
