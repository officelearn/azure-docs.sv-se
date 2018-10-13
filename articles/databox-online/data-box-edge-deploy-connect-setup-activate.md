---
title: Ansluta till, konfigurera och aktivera Azure Data Box Edge i Azure-portalen | Microsoft Docs
description: Tredje självstudien för att distribuera Data Box Edge du instrueras att ansluta, konfigurera och aktivera den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3d6742bec54e612b5dca6d9ef6c4f67a33929448
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166584"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-edge-preview"></a>Självstudier: Ansluta, konfigurera, aktiverar Azure Data Box Edge (förhandsversion) 

Den här självstudien beskrivs hur du ansluter för att konfigurera och aktivera din Data Box Edge-enhet med hjälp av det lokala webbgränssnittet. 

Installationen och aktiveringen kan ta cirka 20 minuter för att slutföra. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ansluta till fysisk enhet
> * Konfigurera och aktivera fysisk enhet

> [!IMPORTANT]
> Data Box Edge genomgår förhandsgranskning. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen. 


## <a name="prerequisites"></a>Förutsättningar

Kontrollera följande innan du konfigurerar och ställa in din Data Box-Edge:

* Du har installerat den fysiska enheten som beskrivs i [installera Edge till Data](data-box-edge-deploy-install.md).
* Du har aktiveringsnyckeln från Data Box Edge-tjänsten som du skapade för att hantera Data Box Edge-enhet. Mer information går du till [förbereda för distribution av Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Ansluta till lokala web UI-installationen 

1. Konfigurera Ethernet-adaptern på den dator som du använder för att ansluta till Edge-enhet med en statisk IP-adress på 192.168.100.5 och undernät 255.255.255.0.
2. Anslut datorn till PORT 1 på din enhet. 
3. Öppna ett webbläsarfönster och åtkomst till lokalt Webbgränssnitt för enheten på https://192.168.100.10. Den här åtgärden kan ta några minuter när du har aktiverat på enheten. 
4. Du ser ett fel eller en varning som anger att det finns ett problem med webbplatsens säkerhetscertifikat. Klicka på **Fortsätt till den här webbsidan**. (De här stegen kan vara annorlunda utifrån webbläsaren som används.)
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

2. Logga in på webbgränssnittet på din enhet. Standardlösenordet är *Password1*. 
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

3. Du uppmanas att ändra enhetens administratörslösenord. Ange ett nytt lösenord som innehåller mellan 8 och 16 tecken. Lösenordet måste innehålla 3 av följande tecken: versaler, gemener, siffror och specialtecken.

Nu är du på den **instrumentpanelen** för din enhet.

## <a name="set-up-and-activate-the-physical-device"></a>Konfigurera och aktivera den fysiska enheten
 
1. Du kan gå till olika inställningar som krävs för att konfigurera och registrera den fysiska enheten med tjänsten Data Box Edge från instrumentpanelen. Den **enhetsnamn**, **nätverksinställningar**, **Web proxyinställningar**, och **tidsinställningar** är valfria. De enda obligatoriska inställningarna är **Molninställningar**.
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

2. I den **enhetsnamn** konfigurerar du ett eget namn för din enhet. Det egna namnet kan vara 1 och 15 tecken långt och får innehålla bokstäver, siffror och bindestreck.

    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

3. (Valfritt) Konfigurera din **nätverksinställningar**. På den fysiska enheten ser du sex nätverksgränssnitt. PORT 1 och PORT 2 är 1 Gbit/s-nätverksgränssnitt. PORT 3, PORT 4, PORT 5 och 6 PORT är alla 25 Gbit/s-nätverksgränssnitt. PORT 1 konfigureras automatiskt som enbart management port PORT 2 till 6 för PORTEN är alla dataportar. Den **nätverksinställningar** sidan som visas nedan.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    När du konfigurerar nätverksinställningar kan ha i åtanke:

    - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. En IP-adress, undernät, gateway och DNS-tilldelas automatiskt.
    - Om DHCP inte är aktiverad kan du tilldela statiska IP-adresser om det behövs.
    - Du kan konfigurera nätverksgränssnittets som IPv4.
   
4. (Valfritt) Konfigurera din webbproxyserver. Även om webbproxykonfigurationen är valfritt, om du använder en webbproxy, kan du bara konfigurera den här.
   
   ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   I den **webbproxy** sidan:
   
   1. Ange den **webbadress proxy** i det här formatet: `http://host-IP address or FDQN:Port number`. HTTPS-URL: er stöds inte.
   2. Ange **autentisering** som **grundläggande** eller **ingen**.
   3. Om du använder autentisering kan du också måste ange en **användarnamn** och **lösenord**.
   4. Klicka på **tillämpa** att validera och tillämpa de konfigurerade webbproxyinställningarna.

5. (Valfritt) Konfigurera inställningarna för tid för din enhet, till exempel tidszon och de primära och sekundära NTP-servrarna. NTP-servrar krävs eftersom din enhet måste synkronisera tiden så att den kan autentisera med molntjänstleverantören.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    I den **tidsinställningar** sidan:
    
    1. I listrutan, Välj den **tidszon** baserat på den geografiska plats där enheten ska distribueras. Standardtidszon för din enhet är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.
    2. Ange en **primär NTP-server** för din enhet eller acceptera standardvärdet för time.windows.com. Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.
    3. Om du vill ange en **sekundär NTP-server** för din enhet.
    4. Klicka på **tillämpa** att validera och tillämpa inställningarna för konfigurerade tid.

6. I den **Molninställningar** sidan, aktiverar din enhet med Data Box Edge-tjänsten i Azure-portalen.
    
    1. Ange den **aktiveringsnyckeln** som du fick i [hämta aktiveringsnyckeln](data-box-edge-deploy-prep.md#get-the-activation-key) för Data Box Edge.

    2. Klicka på **Verkställ**. 
       
         ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    3. När enheten har har aktiverats kan visas med alternativ för läge. Dessa inställningar konfigureras om du vill arbeta med enheten i delvis frånkopplade eller frånkopplat läge. 

        ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

Installationen av enheten har slutförts. Du kan nu lägga till resurser på din enhet.


## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du Data Box Edge ämnen som:

> [!div class="checklist"]
> * Ansluta till fysisk enhet
> * Konfigurera och aktivera fysisk enhet


Gå vidare till nästa självstudie och lär dig hur du överför data med dina Data Box-Edge.

> [!div class="nextstepaction"]
> [Överföra data med Data Box Edge](./data-box-edge-deploy-add-shares.md).