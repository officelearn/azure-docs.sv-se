---
title: Ansluta till, konfigurera och aktivera Azure Data Box-Gateway i Azure-portalen | Microsoft Docs
description: Tredje självstudien för att distribuera Data Box Gateway du instrueras att ansluta, konfigurera och aktivera den virtuella enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 5e83eb46a4d62c6aaf0862d4bb1aa046c5d64dde
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120521"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>Självstudier: Ansluta, konfigurera, aktiverar Azure Data Box-Gateway (förhandsversion) 

## <a name="introduction"></a>Introduktion

Den här självstudien beskrivs hur du ansluter för att konfigurera och aktivera din Data Box-Gateway-enhet med hjälp av det lokala webbgränssnittet. 

Installationen och aktiveringen kan ta upp till 10 minuter för att slutföra. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ansluta till virtuella enheten
> * Konfigurera och aktivera virtuell enhet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


> [!IMPORTANT]
> - Data Box Gateway är en förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen. 


## <a name="prerequisites"></a>Förutsättningar

Kontrollera följande innan du konfigurerar och ställa in din Data Box-Gateway:

* Du har etablerat en virtuell enhet och fick en ansluten URL till den som beskrivs i den [etablera en Data Box-Gateway i Hyper-V](data-box-gateway-deploy-provision-hyperv.md) eller [etablera en Data Box-Gateway i VMware](data-box-gateway-deploy-provision-vmware.md).
* Du har aktiveringsnyckeln från Data Box-Gateway-tjänsten som du skapade för att hantera Data Box Gateway-enheter. Mer information går du till [förbereda för distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md).

<!--* If this is the second or subsequent virtual device that you are registering with an existing StorSimple Device Manager service, you should have the service data encryption key. This key was generated when the first device was successfully registered with this service. If you have lost this key, see [Get the service data encryption key](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) for your Data Box Gateway.-->

## <a name="connect-to-the-local-web-ui-setup"></a>Ansluta till lokala web UI-installationen 

1. Öppna ett webbläsarfönster och Anslut till det lokala webbgränssnittet. Ange:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Använd anslutnings-URL som anges i föregående självstudie. Du ser ett felmeddelande om att det finns ett problem med webbplatsens säkerhetscertifikat. Klicka på **Fortsätt till den här webbsidan**. (De här stegen kan vara annorlunda utifrån webbläsaren som används.)
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. Logga in på webbgränssnittet på den virtuella enheten. Standardlösenordet är *Password1*. 
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. Du uppmanas att ändra enhetens administratörslösenord. Ange ett nytt lösenord som innehåller mellan 8 och 16 tecken. Lösenordet måste innehålla 3 av följande: versaler, gemener, siffror och specialtecken.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Du har kommit till den **instrumentpanelen** för din enhet.

## <a name="set-up-and-activate-the-virtual-device"></a>Konfigurera och aktivera den virtuella enheten
 
1. Du kan gå till olika inställningar som krävs för att konfigurera och registrera den virtuella enheten med Data Box-Gateway-tjänsten från instrumentpanelen. Den **nätverksinställningar**, **Web proxyinställningar**, och **tidsinställningar** är valfria. De enda obligatoriska inställningarna är **enhetsnamn** och **Molninställningar**.
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. I den **enhetsnamn** konfigurerar du ett eget namn för din enhet. Det egna namnet kan vara 1 och 15 tecken långt och får innehålla bokstäver, siffror och bindestreck.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. (Valfritt) Konfigurera din **nätverksinställningar**. Du kan se minst 1 nätverksgränssnitt och beroende på hur många du konfigurerade i den underliggande virtuella datorn. Den **nätverksinställningar** sidan för en virtuell enhet med ett nätverksgränssnitt som är aktiverad är enligt nedan.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    När du konfigurerar nätverksinställningar, Tänk på följande:

    - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. Därför kan tilldelas automatiskt en IP-adress, undernät, gateway och DNS.
    - Om DHCP inte är aktiverad kan du tilldela statiska IP-adresser om det behövs.
    - Du kan konfigurera nätverksgränssnittets som IPv4.

    >[!NOTE] 
    > Vi rekommenderar att du inte växla lokala IP-adressen för gränssnittet netowrk från statisk till DCHP, såvida du inte har en annan IP-adress att ansluta till enheten. Om du använder ett nätverksgränssnitt och du växlar till DHCP och det är inget sätt att avgöra DHCP-adress. Om du vill ändra till en DHCP-adress, vänta tills enheten har registrerats med tjänsten och sedan ändra. Du kan visa IP-adresserna för alla adpaters i den **enhetsegenskaper** i Azure-portalen för din tjänst.

4. (Valfritt) Konfigurera din webbproxyserver. Men webbproxykonfigurationen är valfritt, Tänk på att om du använder en webbproxy, du kan bara konfigurera den här.
   
   ![](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   I den **webbproxy** sidan:
   
   1. Ange den **webbadress proxy** i följande format: *http://&lt;värd-IP-adress eller fullständigt domännamn&gt;: portnummer*. Observera att HTTPS-URL: er inte stöds.
   2. Ange **autentisering** som **grundläggande** eller **ingen**.
   3. Om du använder autentisering, du måste också tillhandahålla en **användarnamn** och **lösenord**.
   4. Klicka på **Verkställ**. På så sätt validera och tillämpa konfigurerade Webbproxyinställningar.

5. (Valfritt) Konfigurera inställningarna för tid för din enhet, till exempel tidszon och de primära och sekundära NTP-servrarna. NTP-servrar krävs eftersom din enhet måste synkronisera tiden så att den kan autentisera med molntjänstleverantören.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    I den **tidsinställningar** sidan:
    
    1. I listrutan, Välj den **tidszon** baserat på den geografiska plats där enheten ska distribueras. Standardtidszon för din enhet är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.
    2. Ange en **primär NTP-server** för din enhet eller acceptera standardvärdet för time.windows.com. Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.
    3. Om du vill ange en **sekundär NTP-server** för din enhet.
    4. Klicka på **Verkställ**. På så sätt validera och tillämpa inställningarna för konfigurerade tid.

6. I den **Molninställningar** sidan, aktiverar din enhet med Data Box-Gateway-tjänsten i Azure-portalen.
    
    1. Ange den **aktiveringsnyckeln** som du fick i [hämta aktiveringsnyckeln](data-box-gateway-deploy-prep.md#get-the-activation-key) för Data Box-Gateway.

    2. Klicka på **Aktivera**. 
       
         ![](./media/data-box-gateway-deploy-connect-setup-activate/image10.png)
    
    3. Du kan behöva vänta en minut innan enheten har aktiverats. Efter aktiveringen uppdaterar sidan som visar att enheten har aktiverats.


## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Data Gateway-ämnen som att:

> [!div class="checklist"]
> * Ansluta till virtuella enheten
> * Konfigurera och aktivera virtuell enhet


Gå vidare till nästa självstudie och lär dig att överföra data med din Data Box-Gateway.

> [!div class="nextstepaction"]
> [Överföra data med Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
