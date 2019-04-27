---
title: Ansluta till, konfigurera och aktivera en Azure Data Box Edge-enhet i Azure portal | Microsoft Docs
description: Tredje självstudien för att distribuera Data Box Edge du instrueras att ansluta, konfigurera och aktivera den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: b97334ae60715f021cce387f9d73b5ea69eea7fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758627"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Självstudier: Ansluta, konfigurera och aktivera Azure Data Box Edge 

Den här självstudien beskrivs hur du kan ansluta, konfigurera och aktivera din Azure Data Box Edge-enhet med hjälp av det lokala webbgränssnittet.

Installationen och aktiveringen kan ta cirka 20 minuter för att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ansluta till en fysisk enhet
> * Konfigurera och aktivera den fysiska enheten

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera följande innan du konfigurerar och ställer in din Data Box Edge-enhet:

* Du har installerat den fysiska enheten som beskrivs i [installera Edge till Data](data-box-edge-deploy-install.md).
* Du har aktiveringsnyckeln från Data Box Edge-tjänsten som du skapade för att hantera Data Box Edge-enhet. Mer information går du till [förbereda för distribution av Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Ansluta till lokala web UI-installationen 

1. Konfigurera Ethernet-adaptern på datorn för att ansluta till Data Box Edge-enhet med en statisk IP-adress på 192.168.100.5 och undernät 255.255.255.0.

2. Anslut datorn till PORT 1 på din enhet. Använd följande bild för att identifiera PORT 1 på din enhet.

    ![Baksidan på en kabelansluten enhet](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Öppna ett webbläsarfönster och åtkomst till lokalt Webbgränssnitt för enheten på https://192.168.100.10.  
    Den här åtgärden kan ta några minuter när du har aktiverat på enheten. 

    Du ser ett fel eller en varning som anger att det finns ett problem med webbplatsens säkerhetscertifikat. 
   
    ![Felmeddelande för webbplatsen security-certifikat](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Välj **Fortsätt till den här webbsidan**.  
    De här stegen kan variera beroende på vilken webbläsare du använder.

5. Logga in på webbgränssnittet på din enhet. Standardlösenordet är *Password1*. 
   
    ![Data Box-Edge-enhet på inloggningssidan](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. Ändra administratörslösenordet för enheten i Kommandotolken.  
    Det nya lösenordet måste innehålla mellan 8 och 16 tecken. Det måste innehålla tre av följande tecken: versaler, gemener, siffror och specialtecken.

Du är nu på instrumentpanelen för din enhet.

## <a name="set-up-and-activate-the-physical-device"></a>Konfigurera och aktivera den fysiska enheten
 
Instrumentpanelen visar olika inställningar som krävs för att konfigurera och registrera den fysiska enheten med Edge för Data Box-tjänsten. Den **enhetsnamn**, **nätverksinställningar**, **Web proxyinställningar**, och **tidsinställningar** är valfria. De enda obligatoriska inställningarna är **Molninställningar**.
   
![Lokala webbgränssnittet ”instrumentpanel” sidan](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. I den vänstra rutan väljer **enhetsnamn**, och sedan ange ett eget namn för din enhet.  
    Det egna namnet måste innehålla mellan 1 och 15 tecken och ha bokstäver, siffror och bindestreck.

    ![Lokala Användargränssnittet ”enhetsnamn” webbsida](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Valfritt) I den vänstra rutan väljer **nätverksinställningar** och konfigurerar sedan inställningarna.  
    Det finns sex nätverksgränssnitt på den fysiska enheten. PORT 1 och PORT 2 är 1 Gbit/s-nätverksgränssnitt. PORT 3, PORT 4, 5 PORT och PORT 6 är alla 25 Gbit/s-nätverksgränssnitt som kan också fungera som 10 Gbit/s-nätverksgränssnitt. PORT 1 konfigureras automatiskt som en endast management port och PORT 2 till 6 för PORTEN är alla dataportar. Den **nätverksinställningar** är enligt nedan.
    
    ![Lokala Användargränssnittet ”nätverksinställningar” webbsida](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    När du konfigurerar inställningarna för ha i åtanke:

   - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. En IP-adress, undernät, gateway och DNS-tilldelas automatiskt.
   - Om DHCP inte är aktiverad kan du tilldela statiska IP-adresser om det behövs.
   - Du kan konfigurera nätverksgränssnittets som IPv4.

     >[!NOTE] 
     > Vi rekommenderar att du inte växla lokala IP-adressen för nätverksgränssnittet från statisk till DCHP, såvida du inte har en annan IP-adress att ansluta till enheten. Om du använder ett nätverksgränssnitt och du växlar till DHCP och det är inget sätt att avgöra DHCP-adress. Om du vill ändra till en DHCP-adress, vänta tills enheten har registrerats med tjänsten och sedan ändra. Du kan visa IP-adresserna för alla kort i den **enhetsegenskaper** i Azure-portalen för din tjänst.

3. (Valfritt) I den vänstra rutan väljer **Web proxyinställningar**, och sedan konfigurera din webbproxyserver. Även om webbproxykonfigurationen är valfritt, om du använder en webbproxy kan du konfigurera det på den här sidan endast.
   
   ![Lokala Användargränssnittet ”Webbproxyinställningar” webbsida](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   På den **Web proxyinställningar** gör följande:
   
   a. I den **webbadress proxy** anger Webbadressen i följande format: `http://host-IP address or FQDN:Port number`. HTTPS-URL: er stöds inte.

   b. Under **autentisering**väljer **ingen** eller **NTLM**.

   c. Om du använder autentisering anger du ett användarnamn och lösenord.

   d. Om du vill verifiera och tillämpa konfigurerade web proxy-inställningar, Välj **tillämpa inställningar**.

4. (Valfritt) I den vänstra rutan väljer **tidsinställningar**, och sedan konfigurera tidszonen och de primära och sekundära NTP-servrarna för din enhet.  
    NTP-servrar krävs eftersom din enhet måste synkronisera tiden så att den kan autentisera med molntjänstleverantören.
       
    På den **tidsinställningar** gör följande:
    
    1. I den **tidszon** listrutan väljer du den tidszon som motsvarar den geografiska plats där enheten ska distribueras.
        Standardtidszon för din enhet är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.

    2. I den **primär NTP-server** anger du den primära servern för enheten eller godkänner du standardvärdet för time.windows.com.  
        Se till att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till internet.

    3. Valfritt: i den **sekundär NTP-server** anger en sekundär server för din enhet.

    4. Om du vill verifiera och tillämpa inställningarna för konfigurerade tid, Välj **tillämpa inställningar**.

        ![Local web UI "Time settings" page](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Valfritt) I den vänstra rutan väljer **Lagringsinställningar** att konfigurera storage-känslighet på din enhet. Den här funktionen är för närvarande en förhandsversion. Som standard lagring på enheten är inte flexibel och det finns data går förlorade om en datadisk misslyckas på enheten. När du aktiverar alternativet flexibel lagring på enheten ska konfigureras och enheten klarar att fel på en datadisk utan att förlora data. Konfigurera lagringen som elastiska minskar användbar kapacitet för din enhet.

    > [!IMPORTANT] 
    > Elasticiteten kan endast konfigureras innan du aktiverar enheten. 

    ![Local web UI "Storage settings" page](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. I den vänstra rutan väljer **Molninställningar**, och sedan aktivera din enhet med Data Box Edge-tjänsten i Azure-portalen.
    
    1. I den **aktiveringsnyckeln** anger aktiveringsnyckeln som du fick i [hämta aktiveringsnyckeln](data-box-edge-deploy-prep.md#get-the-activation-key) för Data Box Edge.
    2. Välj **Använd**.
       
        ![Local web UI "Cloud settings" page](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Enheten aktiveras först. Enheten genomsöks sedan viktiga uppdateringar och om det finns uppdateringar tillämpas automatiskt. Du ser ett meddelande om detta.

        Dialogrutan har också en återställningsnyckel som du bör kopiera och spara den på en säker plats. Den här nyckeln används för att återställa dina data i den händelse att enheten inte kan starta.

        ![Local web UI "Cloud settings" page updated](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Du kan behöva vänta några minuter när uppdateringen har slutförts. Sidan att uppdateras för att indikera att enheten har aktiverats.

        ![Local web UI "Cloud settings" page updated](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Installationen av enheten har slutförts. Du kan nu lägga till resurser på din enhet.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ansluta till en fysisk enhet
> * Konfigurera och aktivera den fysiska enheten

Om du vill lära dig mer om att överföra data med din Data Box Edge-enhet, se:

> [!div class="nextstepaction"]
> [Överföra data med Data Box Edge](./data-box-edge-deploy-add-shares.md).
