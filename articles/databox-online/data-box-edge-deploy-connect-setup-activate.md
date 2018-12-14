---
title: Ansluta till, konfigurera och aktivera en Azure Data Box Edge-enhet i Azure portal | Microsoft Docs
description: Tredje självstudien för att distribuera Data Box Edge du instrueras att ansluta, konfigurera och aktivera den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: f4ca513e3c2e2345dc0005b95ddb7927c0164ffe
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383025"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge-preview"></a>Självstudiekurs: Ansluta, konfigurera och aktivera Azure Data Box Edge (förhandsversion) 

Den här självstudien beskrivs hur du kan ansluta, konfigurera och aktivera din Azure Data Box Edge-enhet med hjälp av det lokala webbgränssnittet. 

Installationen och aktiveringen kan ta cirka 20 minuter för att slutföra. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ansluta till en fysisk enhet
> * Konfigurera och aktivera den fysiska enheten

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Innan du ordna och distribuera den här lösningen kan du granska den [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Förutsättningar

Kontrollera följande innan du konfigurerar och ställer in din Data Box Edge-enhet:

* Du har installerat den fysiska enheten som beskrivs i [installera Edge till Data](data-box-edge-deploy-install.md).
* Du har aktiveringsnyckeln från Data Box Edge-tjänsten som du skapade för att hantera Data Box Edge-enhet. Mer information går du till [förbereda för distribution av Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Ansluta till lokala web UI-installationen 

1. Konfigurera Ethernet-adaptern på datorn för att ansluta till Edge-enhet med en statisk IP-adress på 192.168.100.5 och undernät 255.255.255.0.

1. Anslut datorn till PORT 1 på din enhet. 

1. Öppna ett webbläsarfönster och åtkomst till lokalt Webbgränssnitt för enheten på https://192.168.100.10.  
    Den här åtgärden kan ta några minuter när du har aktiverat på enheten. 

    Du ser ett fel eller en varning som anger att det finns ett problem med webbplatsens säkerhetscertifikat. 
   
    ![Felmeddelande för webbplatsen security-certifikat](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

1. Välj **Fortsätt till den här webbsidan**.  
    De här stegen kan variera beroende på vilken webbläsare du använder.

1. Logga in på webbgränssnittet på din enhet. Standardlösenordet är *Password1*. 
   
    ![Data Box-Edge-enhet på inloggningssidan](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

1. Ändra administratörslösenordet för enheten i Kommandotolken.  
    Det nya lösenordet måste innehålla mellan 8 och 16 tecken. Det måste innehålla tre av följande tecken: versaler, gemener, siffror och specialtecken.

Du är nu på instrumentpanelen för din enhet.

## <a name="set-up-and-activate-the-physical-device"></a>Konfigurera och aktivera den fysiska enheten
 
Instrumentpanelen visar olika inställningar som krävs för att konfigurera och registrera den fysiska enheten med Edge för Data Box-tjänsten. Den **enhetsnamn**, **nätverksinställningar**, **Web proxyinställningar**, och **tidsinställningar** är valfria. De enda obligatoriska inställningarna är **Molninställningar**.
   
![Instrumentpanelen för Data Box Edge-enheten](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. I den vänstra rutan väljer **enhetsnamn**, och sedan ange ett eget namn för din enhet.  
    Det egna namnet måste innehålla mellan 1 och 15 tecken och innehålla bokstäver, siffror och bindestreck.

    ![Sidan ”enhetsnamn”](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

1. (Valfritt) I den vänstra rutan väljer **nätverksinställningar** och konfigurerar sedan inställningarna.  
    Finns sex nätverksgränssnitt på den fysiska enheten. PORT 1 och PORT 2 är 1 Gbit/s-nätverksgränssnitt. PORT 3, PORT 4, PORT 5 och 6 PORT är alla 25 Gbit/s-nätverksgränssnitt. PORT 1 konfigureras automatiskt som en endast management port och PORT 2 till 6 för PORTEN är alla dataportar. Den **nätverksinställningar** sidan som visas nedan.
    
    ![Sidan ”nätverksinställningar”](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    När du konfigurerar inställningarna för ha i åtanke:

    - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. En IP-adress, undernät, gateway och DNS-tilldelas automatiskt.
    - Om DHCP inte är aktiverad kan du tilldela statiska IP-adresser om det behövs.
    - Du kan konfigurera nätverksgränssnittets som IPv4.
   
1. (Valfritt) I den vänstra rutan väljer **Web proxyinställningar**, och sedan konfigurera din webbproxyserver. Även om webbproxykonfigurationen är valfritt, om du använder en webbproxy kan du konfigurera det på den här sidan endast.
   
   ![Sidan ”Webbproxyinställningar”](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   På den **Web proxyinställningar** gör följande:
   
   a. I den **webbadress proxy** anger Webbadressen i följande format: `http://host-IP address or FDQN:Port number`. HTTPS-URL: er stöds inte.

   b. Under **autentisering**väljer **ingen** eller **NTLM**.

   c. Om du använder autentisering anger du ett användarnamn och lösenord.

   d. Om du vill verifiera och tillämpa konfigurerade web proxy-inställningar, Välj **tillämpa inställningar**.

1. (Valfritt) I den vänstra rutan väljer **tidsinställningar**, och sedan konfigurera tidszonen och de primära och sekundära NTP-servrarna för din enhet.  
    NTP-servrar krävs eftersom din enhet måste synkronisera tiden så att den kan autentisera med molntjänstleverantören.
    
    ![Sidan ”tidsinställningar”](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    På den **tidsinställningar** gör följande:
    
    a. I den **tidszon** listrutan väljer du den tidszon som motsvarar den geografiska plats där enheten ska distribueras.  
        Standardtidszon för din enhet är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.

    b. I den **primär NTP-server** anger du den primära servern för enheten eller godkänner du standardvärdet för time.windows.com.  
        Se till att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till internet.

    c. Valfritt: i den **sekundär NTP-server** anger en sekundär server för din enhet.

    d. Om du vill verifiera och tillämpa inställningarna för konfigurerade tid, Välj **tillämpa**.

6. I den vänstra rutan väljer **Molninställningar**, och sedan aktivera din enhet med Data Box Edge-tjänsten i Azure-portalen.
    
    a. I den **aktiveringsnyckeln** anger aktiveringsnyckeln som du fick i [hämta aktiveringsnyckeln](data-box-edge-deploy-prep.md#get-the-activation-key) för Data Box Edge.

    b. Välj **Använd**. 
       
    ![Sidan ”molninställningar”](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    När enheten har har aktiverats kan visas med alternativ för läge. Dessa inställningar konfigureras om du vill arbeta med enheten i delvis frånkopplade eller frånkopplat läge. 

    ![Bekräftelse för ”Molninställningar” aktivering](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

Installationen av enheten har slutförts. Du kan nu lägga till resurser på din enhet.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ansluta till en fysisk enhet
> * Konfigurera och aktivera den fysiska enheten


Om du vill lära dig mer om att överföra data med din Data Box Edge-enhet, se:

> [!div class="nextstepaction"]
> [Överföra data med Data Box Edge](./data-box-edge-deploy-add-shares.md).