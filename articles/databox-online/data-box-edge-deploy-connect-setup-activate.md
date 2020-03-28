---
title: Självstudiekurs för att ansluta till, konfigurera, aktivera Azure Data Box Edge-enhet i Azure portal | Microsoft-dokument
description: Självstudiekurs för att distribuera Data Box Edge instruerar dig att ansluta, konfigurera och aktivera din fysiska enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 144c59c8bc24e8e10584702ec6cd48f7aa8c15c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239058"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Självstudiekurs: Ansluta, konfigurera och aktivera Azure Data Box Edge 

I den här självstudien beskrivs hur du kan ansluta till, konfigurera och aktivera din Azure Data Box Edge-enhet med hjälp av det lokala webbgränssnittet.

Installationen och aktiveringsprocessen kan ta cirka 20 minuter att slutföra.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Ansluta till en fysisk enhet
> * Konfigurera och aktivera den fysiska enheten

## <a name="prerequisites"></a>Krav

Innan du konfigurerar och konfigurerar enheten Data Box Edge ska du se till att:

* Du har installerat den fysiska enheten enligt definitionen i [Install Data Box Edge](data-box-edge-deploy-install.md).
* Du har aktiveringsnyckeln från tjänsten Data Box Edge som du har skapat för att hantera Data Box Edge-enheten. Mer information finns i [Förbereda för att distribuera Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Ansluta till den lokala webbgränssnittskonfigurationen 

1. Konfigurera Ethernet-kortet på datorn för att ansluta till Data Box Edge-enheten med en statisk IP-adress på 192.168.100.5 och undernät 255.255.255.0.

2. Anslut datorn till PORT 1 på enheten. Använd följande bild för att identifiera PORT 1 på enheten.

    ![Baksidan på en kabelansluten enhet](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Öppna ett webbläsarfönster och öppna enhetens lokala `https://192.168.100.10`webbgränssnitt på .  
    Den här åtgärden kan ta några minuter efter att du har aktiverat enheten. 

    Du ser ett fel eller en varning som anger att det finns ett problem med webbplatsens säkerhetscertifikat. 
   
    ![Felmeddelande om webbplatssäkerhetscertifikat](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Välj **Fortsätt till den här webbsidan**.  
    De här stegen kan variera beroende på vilken webbläsare du använder.

5. Logga in på enhetens webbgränssnitt. Standardlösenordet är *Password1*. 
   
    ![Inloggningssida för Data Box Edge-enhet](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. Ändra lösenordet för enhetsadministratören vid prompten.  
    Det nya lösenordet måste innehålla mellan 8 och 16 tecken. Den måste innehålla tre av följande tecken: versaler, gemener, numeriska tecken och specialtecken.

Du är nu på instrumentpanelen på din enhet.

## <a name="set-up-and-activate-the-physical-device"></a>Konfigurera och aktivera den fysiska enheten
 
Instrumentpanelen visar de olika inställningar som krävs för att konfigurera och registrera den fysiska enheten med tjänsten Data Box Edge. **Enhetsnamnet,** **Nätverksinställningarna,** **webbproxyinställningarna**och **tidsinställningarna** är valfria. De enda nödvändiga inställningarna är **Molninställningar**.
   
![Sidan "Instrumentpanel" för lokalt webbgränssnitt](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. Välj **Enhetsnamn**i den vänstra rutan och ange sedan ett eget namn för enheten.  
    Det egna namnet måste innehålla från 1 till 15 tecken och innehålla bokstäver, siffror och bindestreck.

    ![Sidan Lokalt webbgränssnitt "Enhetsnamn"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Valfritt) Välj **Nätverksinställningar i** den vänstra rutan och konfigurera sedan inställningarna.  
    På din fysiska enhet finns det sex nätverksgränssnitt. PORT 1 och PORT 2 är nätverksgränssnitt på 1 Gbit/s. PORT 3, PORT 4, PORT 5 och PORT 6 är alla nätverksgränssnitt på 25 Gbit/s som också kan fungera som nätverksgränssnitt på 10 Gbit/s. PORT 1 konfigureras automatiskt som en endast hanteringsport, och PORT 2 till PORT 6 är alla dataportar. Sidan **Nätverksinställningar** visas som visas nedan.
    
    ![Sidan Lokalt webbgränssnitt "Nätverksinställningar"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    När du konfigurerar nätverksinställningarna bör du tänka på följande:

   - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. En IP-adress, ett undernät, en gateway och DNS tilldelas automatiskt.
   - Om DHCP inte är aktiverat kan du tilldela statiska IP-adresser om det behövs.
   - Du kan konfigurera nätverksgränssnittet som IPv4.

     >[!NOTE] 
     > Vi rekommenderar att du inte växlar den lokala IP-adressen för nätverksgränssnittet från statisk till DCHP, såvida du inte har en annan IP-adress för att ansluta till enheten. Om du använder ett nätverksgränssnitt och växlar till DHCP kan det inte finnas något sätt att fastställa DHCP-adressen. Om du vill byta till en DHCP-adress väntar du tills enheten har registrerats hos tjänsten och sedan ändras. Du kan sedan visa IPs för alla kort i **enhetsegenskaperna** i Azure-portalen för din tjänst.

3. (Valfritt) Välj **Webbproxyinställningar i**den vänstra rutan och konfigurera sedan webbproxyservern. Även om webbproxykonfiguration är valfritt, kan du bara konfigurera den på den här sidan om du använder en webbproxy.
   
   ![Sidan Lokala webbgränssnitt "Webbproxyinställningar"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Gör följande på sidan **Inställningar för webbproxy:**
   
   a. Ange URL:en i den här formaten `http://host-IP address or FQDN:Port number`för **webbproxy-URL:** . HTTPS-url:er stöds inte.

   b. Under **Autentisering**väljer du **Ingen** eller **NTLM**.

   c. Om du använder autentisering anger du ett användarnamn och lösenord.

   d. Om du vill validera och tillämpa de konfigurerade webbproxyinställningarna väljer du **Använd inställningar**.
   
   > [!NOTE]
   > PAC-filer (Proxy-auto config) stöds inte. En PAC-fil definierar hur webbläsare och andra användaragenter automatiskt kan välja lämplig proxyserver (åtkomstmetod) för att hämta en viss WEBBADRESS.
   > Proxyservrar som försöker fånga upp och läsa all trafik (sedan på nytt underteckna allt med sin egen certifiering) är inte kompatibla eftersom proxyns cert inte är betrodd.
   > Vanligtvis fungerar transparenta proxyservrar bra med Azure Data Box Edge.

4. (Valfritt) I den vänstra rutan väljer du **Tidsinställningar**och konfigurerar sedan tidszonen och de primära och sekundära NTP-servrarna för enheten.  
    NTP-servrar krävs eftersom enheten måste synkronisera tiden så att den kan autentisera med dina molntjänstleverantörer.
       
    Gör följande på sidan **Tidsinställningar:**
    
    1. I listrutan **Tidszon** väljer du den tidszon som motsvarar den geografiska plats där enheten distribueras.
        Standardtidszonen för enheten är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.

    2. I rutan **Primär NTP-server** anger du den primära servern för enheten eller accepterar standardvärdet för time.windows.com.  
        Se till att ditt nätverk tillåter NTP-trafik att passera från ditt datacenter till Internet.

    3. Ange eventuellt en sekundär server för enheten i rutan **Sekundär NTP-server.**

    4. Om du vill validera och tillämpa de konfigurerade tidsinställningarna väljer du **Använd inställningar**.

        ![Sidan Lokalt webbgränssnitt "Tidsinställningar"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Valfritt) I den vänstra rutan väljer du **Lagringsinställningar** för att konfigurera lagringsåtersåterslutningen på enheten. Den här funktionen är för närvarande en förhandsversion. Som standard är lagringen på enheten inte flexibel och det finns dataförlust om en datadisk misslyckas på enheten. När du aktiverar alternativet Fjädrande konfigureras lagringen på enheten om och enheten tål fel på en datadisk utan dataförlust. Om du konfigurerar lagringen som flexibel minskar enhetens användbarhetskapacitet.

    > [!IMPORTANT] 
    > Återhämtningen kan bara konfigureras innan du aktiverar enheten. 

    ![Sidan Lokala webbgränssnitt "Lagringsinställningar"](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. I den vänstra rutan väljer du **Molninställningar**och aktiverar sedan enheten med databoxkantstjänsten i Azure-portalen.
    
    1. I rutan **Aktiveringsnyckel** anger du aktiveringsnyckeln som du fick i [Hämta aktiveringsnyckeln](data-box-edge-deploy-prep.md#get-the-activation-key) för Data Box Edge.
    2. Välj **Använd**.
       
        ![Sidan Lokalt webbgränssnitt "Molninställningar"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Först aktiveras enheten. Enheten genomsöks sedan efter viktiga uppdateringar och om det är tillgängligt tillämpas uppdateringarna automatiskt. Du ser ett meddelande om detta.

        Dialogrutan har också en återställningsnyckel som du bör kopiera och spara den på en säker plats. Den här nyckeln används för att återställa dina data i händelse av att enheten inte kan starta upp.

        ![Sidan "Molninställningar" uppdaterats på den lokala webbgränssnittssidan](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Du kan behöva vänta flera minuter efter att uppdateringen har slutförts. Sidan uppdateras för att visa att enheten har aktiverats.

        ![Sidan "Molninställningar" uppdaterats på den lokala webbgränssnittssidan](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Enhetskonfigurationen är klar. Nu kan du lägga till resurser på enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ansluta till en fysisk enhet
> * Konfigurera och aktivera den fysiska enheten

Mer information om hur du överför data med databoxens edge-enhet finns i:

> [!div class="nextstepaction"]
> [Överför data med Data Box Edge](./data-box-edge-deploy-add-shares.md).
