---
title: Ansluta till, konfigurera och aktivera Azure Data Box Gateway i Azure Portal
description: Tredje självstudiekursen för att distribuera Data Box Gateway instruerar dig att ansluta, konfigurera och aktivera den virtuella enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: b3cf4fd958202c28586b7c15932dc88a21d7c60f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686866"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Självstudiekurs: Anslut, konfigurera, aktivera Azure Data Box Gateway

## <a name="introduction"></a>Introduktion

I den här självstudien beskrivs hur du ansluter till, konfigurerar och aktiverar databoxgatewayenheten med hjälp av det lokala webbgränssnittet. 

Installationen och aktiveringsprocessen kan ta cirka 10 minuter att slutföra. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ansluta till en virtuell enhet
> * Konfigurera och aktivera den virtuella enheten

## <a name="prerequisites"></a>Krav

Innan du konfigurerar och konfigurerar databoxgatewayen ska du se till att:

* Du har etablerat en virtuell enhet och fått en ansluten URL till den som beskrivs i [etableringen en databoxgateway i Hyper-V](data-box-gateway-deploy-provision-hyperv.md) eller [etablera en databoxgateway i VMware](data-box-gateway-deploy-provision-vmware.md).
* Du har aktiveringsnyckeln från tjänsten Data Box Gateway som du har skapat för att hantera Data Box Gateway-enheter. Mer information finns i [Förbereda för att distribuera Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Ansluta till den lokala webbgränssnittskonfigurationen 

1. Öppna ett webbläsarfönster och få tillgång till enhetens lokala webbgränssnitt på:
   
   https:\//ip-address-of-network-interface https: /ip-address-of-network-interface https: /ip-address-of-network-interface https:
   
   Använd anslutnings-URL:en som anges i föregående självstudiekurs. Du ser ett fel eller en varning som anger att det finns ett problem med webbplatsens säkerhetscertifikat.

2. Välj **Fortsätt till den här webbsidan**. De här stegen kan variera beroende på vilken webbläsare du använder.
   
    ![Felmeddelande om webbplatssäkerhetscertifikat](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Logga in på webbgränssnittet för din virtuella enhet. Standardlösenordet är *Password1*. 
   
    ![Logga in på lokalt webbgränssnitt](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Ändra enhetens lösenord vid prompten. Det nya lösenordet måste innehålla mellan 8 och 16 tecken. Den måste innehålla 3 av följande: versaler, gemener, numeriska tecken och specialtecken.

    ![Ändra enhetens lösenord](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Du är nu på **instrumentpanelen** på din enhet.

## <a name="set-up-and-activate-the-virtual-device"></a>Konfigurera och aktivera den virtuella enheten
 
Instrumentpanelen visar de olika inställningar som krävs för att konfigurera och registrera den virtuella enheten med tjänsten Data Box Gateway. **Enhetsnamnet,** **Nätverksinställningarna,** **webbproxyinställningarna**och **tidsinställningarna** är valfria. De enda nödvändiga inställningarna är **Molninställningar**.
   
![Sidan "Instrumentpanel" för lokalt webbgränssnitt](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. Välj **Enhetsnamn**i den vänstra rutan och ange sedan ett eget namn för enheten. Det egna namnet måste innehålla mellan 1 och 15 tecken långt och ha bokstavs-, tal- och bindestreck.

    ![Sidan Lokalt webbgränssnitt "Enhetsnamn"](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Valfritt) Välj **Nätverksinställningar i** den vänstra rutan och konfigurera inställningarna. På den virtuella enheten ser du minst ett nätverksgränssnitt med mera beroende på hur många du har konfigurerat i den underliggande virtuella datorn. Sidan **Nätverksinställningar** för en virtuell enhet med ett nätverksgränssnitt aktiverat är som visas nedan.
    
    ![Sidan Lokalt webbgränssnitt "Nätverksinställningar"](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    När du konfigurerar nätverksinställningar bör du tänka på:

    - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. En IP-adress, ett undernät, en gateway och DNS tilldelas automatiskt.
    - Om DHCP inte är aktiverat kan du tilldela statiska IP-adresser om det behövs.
    - Du kan konfigurera nätverksgränssnittet som IPv4.

     >[!NOTE] 
     > Vi rekommenderar att du inte växlar den lokala IP-adressen för nätverksgränssnittet från statisk till DHCP, såvida du inte har en annan IP-adress för att ansluta till enheten. Om du använder ett nätverksgränssnitt och växlar till DHCP kan det inte finnas något sätt att fastställa DHCP-adressen. Om du vill byta till en DHCP-adress väntar du tills enheten har registrerats hos tjänsten och sedan ändras. Du kan sedan visa IPs för alla kort i **enhetsegenskaperna** i Azure-portalen för din tjänst.

3. (Valfritt) konfigurera din webbproxyserver. Även om webbproxykonfiguration är valfritt, om du använder en webbproxy, kan du konfigurera den bara på den här sidan.
   
   ![Sidan Lokala webbgränssnitt "Webbproxyinställningar"](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Gör följande på **sidan Webbproxy:**
   
   1. Ange URL:en i den här formaten `http://&lt;host-IP address or FQDN&gt;:Port number`för **webbproxy-URL:** . HTTPS-url:er stöds inte.
   2. Under **Autentisering**väljer du **Ingen** eller **NTLM**.
   3. Om du använder autentisering anger du ett **användarnamn** och **lösenord**.
   4. Om du vill validera och tillämpa de konfigurerade webbproxyinställningarna väljer du **Använd**.

   > [!NOTE]
   > PAC-filer (Proxy-auto config) stöds inte. En PAC-fil definierar hur webbläsare och andra användaragenter automatiskt kan välja lämplig proxyserver (åtkomstmetod) för att hämta en viss WEBBADRESS.
   > Proxyservrar som försöker fånga upp och läsa all trafik (sedan på nytt underteckna allt med sin egen certifiering) är inte kompatibla eftersom proxyns cert inte är betrodd.
   > Vanligtvis fungerar transparenta proxyservrar bra med Azure Data Box Gateway.

4. (Valfritt) I den vänstra rutan väljer du **Tidsinställningar**och konfigurerar sedan tidszonen och de primära och sekundära NTP-servrarna för enheten. 

    NTP-servrar krävs eftersom enheten måste synkronisera tiden så att den kan autentisera med dina molntjänstleverantörer.
    
    ![Sidan Lokalt webbgränssnitt "Tidsinställningar"](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Gör följande på sidan **Tidsinställningar:**
    
    1. I listrutan **Tidszon** väljer du den tidszon som motsvarar den geografiska plats där enheten distribueras.
        Standardtidszonen för enheten är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.

    2. Ange en **primär NTP-server** för enheten `time.windows.com`eller acceptera standardvärdet för .   
        Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.

    3. Ange eventuellt en sekundär server för enheten i rutan **Sekundär NTP-server.**

    4. Om du vill validera och tillämpa de konfigurerade tidsinställningarna väljer du **Använd**.

6. I den vänstra rutan väljer du **Molninställningar**och aktiverar sedan enheten med tjänsten Data Box Gateway i Azure-portalen.
    
    1. I rutan **Aktiveringsnyckel** anger du **aktiveringsnyckeln** som du fick i [Hämta aktiveringsnyckeln](data-box-gateway-deploy-prep.md#get-the-activation-key) för Data Box Gateway.

    2. Välj **Aktivera**.
       
         ![Sidan Lokalt webbgränssnitt "Molninställningar"](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Enheten aktiveras och viktiga uppdateringar, om sådana finns, tillämpas automatiskt. Du ser ett meddelande om detta. Övervaka uppdateringsutvecklingen via Azure-portalen.

        ![Sidan Lokalt webbgränssnitt "Molninställningar"](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Dialogrutan har också en återställningsnyckel som du bör kopiera och spara på en säker plats. Den här nyckeln används för att återställa dina data i händelse av att enheten inte kan starta upp.**


    4. Du kan behöva vänta flera minuter på att uppdateringen ska slutföras. När uppdateringen är klar loggar du in på enheten. Sidan **Molninställningar** uppdateras för att visa att enheten har aktiverats.

        ![Sidan "Molninställningar" uppdaterats på den lokala webbgränssnittssidan](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Enhetskonfigurationen är klar. Nu kan du lägga till resurser på enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ansluta till en virtuell enhet
> * Konfigurera och aktivera den virtuella enheten

Mer information om hur du överför data med datarutegatewayen finns i:

> [!div class="nextstepaction"]
> [Överför data med Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
