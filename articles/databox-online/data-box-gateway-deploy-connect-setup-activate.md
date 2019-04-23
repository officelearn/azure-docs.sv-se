---
title: Ansluta till, konfigurera och aktivera Azure Data Box-Gateway i Azure-portalen | Microsoft Docs
description: Tredje självstudien för att distribuera Data Box Gateway du instrueras att ansluta, konfigurera och aktivera den virtuella enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 898cb63f8868ce2abaee8784214322edf9a56997
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60000325"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Självstudier: Ansluta, konfigurera, aktiverar Azure Data Box-Gateway

## <a name="introduction"></a>Introduktion

Den här självstudien beskrivs hur du ansluter till, konfigurera och aktivera din Data Box-Gateway-enhet med hjälp av det lokala webbgränssnittet. 

Installationen och aktiveringen kan ta upp till 10 minuter för att slutföra. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ansluta till en virtuell enhet
> * Konfigurera och aktivera den virtuella enheten

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera följande innan du konfigurerar och ställa in din Data Box-Gateway:

* Du har etablerat en virtuell enhet och fick en ansluten URL till den som beskrivs i den [etablera en Data Box-Gateway i Hyper-V](data-box-gateway-deploy-provision-hyperv.md) eller [etablera en Data Box-Gateway i VMware](data-box-gateway-deploy-provision-vmware.md).
* Du har aktiveringsnyckeln från Data Box-Gateway-tjänsten som du skapade för att hantera Data Box Gateway-enheter. Mer information går du till [förbereda för distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Ansluta till lokala web UI-installationen 

1. Öppna ett webbläsarfönster och åtkomst till lokalt Webbgränssnitt för enheten på:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Använd anslutnings-URL som anges i föregående självstudie. Du ser ett fel eller en varning som anger att det finns ett problem med webbplatsens säkerhetscertifikat.

2. Välj **Fortsätt till den här webbsidan**. De här stegen kan variera beroende på vilken webbläsare du använder.
   
    ![Felmeddelande för webbplatsen security-certifikat](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Logga in på webbgränssnittet på den virtuella enheten. Standardlösenordet är *Password1*. 
   
    ![Logga in på lokala webbgränssnittet](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Ändra lösenordet för enheten i Kommandotolken. Det nya lösenordet måste innehålla mellan 8 och 16 tecken. Det måste innehålla 3 av följande: versaler, gemener, siffror och specialtecken.

    ![Ändra enhetens lösenord](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Nu är du på den **instrumentpanelen** för din enhet.

## <a name="set-up-and-activate-the-virtual-device"></a>Konfigurera och aktivera den virtuella enheten
 
Instrumentpanelen visar olika inställningar som krävs för att konfigurera och registrera den virtuella enheten med Data Box-Gateway-tjänsten. Den **enhetsnamn**, **nätverksinställningar**, **Web proxyinställningar**, och **tidsinställningar** är valfria. De enda obligatoriska inställningarna är **Molninställningar**.
   
![Lokala webbgränssnittet ”instrumentpanel” sidan](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. I den vänstra rutan väljer **enhetsnamn**, och sedan ange ett eget namn för din enhet. Det egna namnet måste innehålla mellan 1 och 15 tecken långt och ha bokstäver, siffror och bindestreck.

    ![Lokala Användargränssnittet ”enhetsnamn” webbsida](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Valfritt) I den vänstra rutan väljer **nätverksinställningar** och konfigurerar sedan inställningarna. På din virtuella enhet ser du minst ett nätverksgränssnitt och beroende på hur många du konfigurerade i den underliggande virtuella datorn. Den **nätverksinställningar** sidan för en virtuell enhet med ett nätverksgränssnitt som är aktiverad är enligt nedan.
    
    ![Lokala Användargränssnittet ”nätverksinställningar” webbsida](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    När du konfigurerar nätverksinställningar ha i åtanke:

    - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. En IP-adress, undernät, gateway och DNS-tilldelas automatiskt.
    - Om DHCP inte är aktiverad kan du tilldela statiska IP-adresser om det behövs.
    - Du kan konfigurera nätverksgränssnittets som IPv4.

     >[!NOTE] 
     > Vi rekommenderar att du inte växla lokala IP-adressen för nätverksgränssnittet från statisk till DHCP, om du inte har en annan IP-adress att ansluta till enheten. Om du använder ett nätverksgränssnitt och du växlar till DHCP och det är inget sätt att avgöra DHCP-adress. Om du vill ändra till en DHCP-adress, vänta tills enheten har registrerats med tjänsten och sedan ändra. Du kan visa IP-adresserna för alla kort i den **enhetsegenskaper** i Azure-portalen för din tjänst.

3. (Valfritt) konfigurera din webbproxyserver. Även om webbproxykonfigurationen är valfritt, om du använder en webbproxy kan du konfigurera det endast på den här sidan.
   
   ![Lokala Användargränssnittet ”Webbproxyinställningar” webbsida](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   På den **webbproxy** gör följande:
   
   1. I den **webbadress proxy** anger Webbadressen i följande format: `http://&lt;host-IP address or FQDN&gt;:Port number`. HTTPS-URL: er stöds inte.
   2. Under **autentisering**väljer **ingen** eller **NTLM**.
   3. Om du använder autentisering anger du en **användarnamn** och **lösenord**.
   4. Om du vill verifiera och tillämpa konfigurerade web proxy-inställningar, Välj **tillämpa**.

4. (Valfritt) I den vänstra rutan väljer **tidsinställningar**, och sedan konfigurera tidszonen och de primära och sekundära NTP-servrarna för din enhet. 

    NTP-servrar krävs eftersom din enhet måste synkronisera tiden så att den kan autentisera med molntjänstleverantören.
    
    ![Local web UI "Time settings" page](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    I den **tidsinställningar** gör följande:
    
    1. I den **tidszon** listrutan väljer du den tidszon som motsvarar den geografiska plats där enheten distribueras.
        Standardtidszon för din enhet är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.

    2. Ange en **primär NTP-server** för din enhet eller acceptera standardvärdet för `time.windows.com`.   
        Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.

    3. Valfritt: i den **sekundär NTP-server** anger en sekundär server för din enhet.

    4. Om du vill verifiera och tillämpa inställningarna för konfigurerade tid, Välj **tillämpa**.

6. I den vänstra rutan väljer **Molninställningar**, och sedan aktivera din enhet med Data Box-Gateway-tjänsten i Azure-portalen.
    
    1. I den **aktiveringsnyckeln** anger du den **aktiveringsnyckeln** som du fick i [hämta aktiveringsnyckeln](data-box-gateway-deploy-prep.md#get-the-activation-key) för Data Box-Gateway.

    2. Välj **aktivera**.
       
         ![Local web UI "Cloud settings" page](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Enheten aktiveras och viktiga uppdateringar, i förekommande fall, tillämpas automatiskt. Du ser ett meddelande om detta. Övervaka uppdateringsförloppet via Azure portal.

        ![Local web UI "Cloud settings" page](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Dialogrutan har också en återställningsnyckel som du bör kopiera och spara på en säker plats. Den här nyckeln används för att återställa dina data i den händelse att enheten inte kan starta.**


    4. Du kan behöva vänta några minuter att slutföra uppdateringen. När uppdateringen är slutförd, logga in på enheten. Den **Molninställningar** sidan uppdateras för att visa att enheten har aktiverats.

        ![Local web UI "Cloud settings" page updated](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Installationen av enheten har slutförts. Du kan nu lägga till resurser på din enhet.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ansluta till en virtuell enhet
> * Konfigurera och aktivera den virtuella enheten

Mer information om hur du överför data med din Data Box-Gateway finns:

> [!div class="nextstepaction"]
> [Överföra data med Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
