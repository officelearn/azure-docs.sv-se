---
title: Självstudie för att ansluta till, konfigurera, aktivera Azure Stack gräns enhet i Azure Portal | Microsoft Docs
description: Självstudier för att distribuera Azure Stack Edge instruerar dig att ansluta, konfigurera och aktivera den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: e0a0d9415cc55c24bb4dc0690c73d9f79fc0ce0e
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "84608442"
---
# <a name="tutorial-connect-set-up-and-activate-azure-stack-edge"></a>Självstudie: ansluta, konfigurera och aktivera Azure Stack Edge 

I den här självstudien beskrivs hur du kan ansluta till, konfigurera och aktivera din Azure Stack Edge-enhet med hjälp av det lokala webb gränssnittet.

Installations-och aktiverings processen kan ta ungefär 20 minuter att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Ansluta till en fysisk enhet
> * Konfigurera och aktivera den fysiska enheten

## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar Azure Stack Edge-enheten måste du kontrol lera att:

* Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge](azure-stack-edge-deploy-install.md).
* Du har aktiverings nyckeln från Azure Stack Edge-tjänsten som du skapade för att hantera Azure Stack Edge-enheten. Mer information finns i [förbereda för att distribuera Azure Stack Edge](azure-stack-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Anslut till konfiguration av lokalt webb gränssnitt

1. Konfigurera Ethernet-kortet på datorn för att ansluta till Azure Stack Edge-enheten med en statisk IP-adress för 192.168.100.5 och undernät 255.255.255.0.

2. Anslut datorn till PORT 1 på enheten. Använd följande bild för att identifiera PORT 1 på enheten.

    ![Baksidan på en kabelansluten enhet](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

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

Du är nu på instrument panelen för din enhet.

## <a name="set-up-and-activate-the-physical-device"></a>Konfigurera och aktivera den fysiska enheten
 
På instrument panelen visas de olika inställningar som krävs för att konfigurera och registrera den fysiska enheten med Azure Stack Edge-tjänsten. **Enhets namnet**, **nätverks inställningarna**, **webbproxyinställningarna**och **tids inställningarna** är valfria. De enda nödvändiga inställningarna är **moln inställningar**.
   
![Sidan instrument panel för lokalt webb gränssnitt](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. I den vänstra rutan väljer du **enhets namn**och anger sedan ett eget namn för din enhet.  
    Det egna namnet måste innehålla mellan 1 och 15 tecken och bestå av bokstäver, siffror och bindestreck.

    ![Sidan enhets namn för lokalt webb gränssnitt](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. Valfritt Välj **nätverks inställningar** i den vänstra rutan och konfigurera sedan inställningarna.  
    Det finns sex nätverks gränssnitt på den fysiska enheten. PORT 1 och PORT 2 är 1-Gbps nätverks gränssnitt. PORT 3, PORT 4, PORT 5 och PORT 6 är alla 25 Gbit/s nätverks gränssnitt som också kan fungera som 10 Gbit/s nätverks gränssnitt. PORT 1 konfigureras automatiskt som en port för endast hantering, och PORT 2 till PORT 6 är alla data portar. Sidan **nätverks inställningar** visas nedan.
    
    ![Sidan nätverks inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Tänk på följande när du konfigurerar nätverks inställningarna:

   - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. En IP-adress, undernät, gateway och DNS tilldelas automatiskt.
   - Om DHCP inte är aktive rad kan du tilldela statiska IP-adresser om det behövs.
   - Du kan konfigurera nätverks gränssnittet som IPv4.

     >[!NOTE] 
     > Vi rekommenderar att du inte byter lokal IP-adress för nätverks gränssnittet från statisk till DCHP, om du inte har en annan IP-adress för att ansluta till enheten. Om du använder ett nätverks gränssnitt och växlar till DHCP, kan det vara något sätt att fastställa DHCP-adressen. Om du vill ändra till en DHCP-adress väntar du tills enheten har registrerats med tjänsten och ändrar sedan. Du kan sedan Visa IP-adresserna för alla nätverkskort i **enhets egenskaperna** i Azure Portal för din tjänst.

3. Valfritt I den vänstra rutan väljer du **Inställningar för webbproxy**och konfigurerar sedan webbproxyservern. Även om webbproxy-konfigurationen är valfri, kan du bara konfigurera den på den här sidan om du använder en webbproxy.
   
   ![Sidan Web Proxy-inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   På sidan **Web Proxy-inställningar** gör du följande:
   
   a. I rutan **webbproxy-URL** anger du webb adressen i följande format: `http://host-IP address or FQDN:Port number` . HTTPS-URL: er stöds inte.

   b. Under **autentisering**väljer du **ingen** eller **NTLM**. Om du aktiverar beräknings-och användnings IoT Edge-modulen på Azure Stack Edge-enhet rekommenderar vi att du ställer in webbproxy-autentisering på **ingen**. **NTLM** stöds inte.

   c. Om du använder autentisering anger du ett användar namn och lösen ord.

   d. Om du vill validera och tillämpa de konfigurerade webbproxyinställningarna väljer du **tillämpa inställningar**.

   > [!NOTE]
   > PAC-filer (Proxy-Auto config) stöds inte. En PAC-fil definierar hur webbläsare och andra användar agenter automatiskt kan välja rätt proxyserver (åtkomst metod) för att hämta en viss URL.
   > Proxyservrar som försöker avlyssna och läsa all trafik (och sedan signera om allting med sin egen certifiering) är inte kompatibelt eftersom proxyns certifikat inte är betrott.
   > Oftast fungerar transparenta proxyservrar bra med Azure Stack Edge.

4. Valfritt I det vänstra fönstret väljer du **tids inställningar**och konfigurerar sedan tids zonen och de primära och sekundära NTP-servrarna för enheten.  
    NTP-servrar krävs eftersom din enhet måste synkronisera tid så att den kan autentiseras med dina moln tjänst leverantörer.
       
    Gör följande på sidan **tids inställningar** :
    
    1. I list rutan **tids zon** väljer du den tidszon som motsvarar den geografiska plats där enheten distribueras.
        Standard tids zonen för enheten är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.

    2. I rutan **primär NTP-server** anger du den primära servern för enheten eller accepterar standardvärdet för Time.Windows.com.  
        Se till att ditt nätverk tillåter att NTP-trafik skickas från ditt data Center till Internet.

    3. Alternativt kan du ange en sekundär server för enheten i rutan **sekundär NTP-server** .

    4. Om du vill validera och tillämpa de konfigurerade tids inställningarna väljer du **tillämpa inställningar**.

        ![Sidan tids inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. Valfritt I det vänstra fönstret väljer du **lagrings inställningar** för att konfigurera lagrings återhämtning på enheten. Den här funktionen finns för närvarande som en förhandsversion. Som standard är lagringen på enheten inte elastisk och det finns data förlust om en datadisk kraschar på enheten. När du aktiverar det elastiska alternativet konfigureras lagringen på enheten om och enheten kan motstå en datadisk som inte har någon data förlust. Att konfigurera lagringen som elastiskt minskar enhetens användbara kapacitet.

    > [!IMPORTANT] 
    > Återhämtnings förmågan kan bara konfigureras innan du aktiverar enheten. 

    ![Sidan lagrings inställningar för lokalt webb gränssnitt](./media/azure-stack-edge-deploy-connect-setup-activate/storage-settings.png)

6. I det vänstra fönstret väljer du **moln inställningar**och aktiverar sedan enheten med Azure Stack Edge-tjänsten i Azure Portal.
    
    1. I rutan **aktiverings nyckel** anger du aktiverings nyckeln som du fick i [Hämta aktiverings nyckeln](azure-stack-edge-deploy-prep.md#get-the-activation-key) för Azure Stack Edge.
    2. Välj **Tillämpa**.
       
        ![Sidan Cloud Settings för lokalt webb gränssnitt](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Först är enheten aktive rad. Enheten genomsöks sedan efter eventuella kritiska uppdateringar och om den är tillgänglig tillämpas uppdateringarna automatiskt. Du ser ett meddelande om detta.

        Dialog rutan innehåller också en återställnings nyckel som du bör kopiera och spara den på en säker plats. Den här nyckeln används för att återställa dina data i händelse av att enheten inte kan starta.

        ![Sidan för lokalt webb gränssnitt "moln inställningar" har uppdaterats](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Du kan behöva vänta flera minuter efter att uppdateringen har slutförts. Sidan uppdateras för att indikera att enheten har Aktiver ATS.

        ![Sidan för lokalt webb gränssnitt "moln inställningar" har uppdaterats](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Enhets installationen är klar. Nu kan du lägga till resurser på enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ansluta till en fysisk enhet
> * Konfigurera och aktivera den fysiska enheten

Information om hur du överför data med Azure Stack Edge-enhet finns i:

> [!div class="nextstepaction"]
> [Överför data med Azure Stack Edge](./azure-stack-edge-deploy-add-shares.md).
