---
title: Konfigurera Azure Data Box| Microsoft Docs
description: Lär dig hur du kabelansluter och ansluter till Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: alkohli
ms.openlocfilehash: 78e56286e0dec3307c7481a6097c53a1362f57cc
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427530"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Självstudie: Kabelansluta och ansluta till Azure Data Box

I den här självstudien beskrivs hur du kabelansluter, ansluter och slår på Azure Data Box-enheten.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kabelansluta Data Box
> * Ansluta till Data Box

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du kontrollera att:

1. Du har slutfört [självstudien Beställa Azure Data Box](data-box-deploy-ordered.md).
2. Du har fått din Data Box och att orderstatusen i portalen är **Levererad**. 
    - Det finns en adressetikett i den genomskinliga fickan som är fäst under den befintliga etiketten på enheten. Spara etiketten – du behöver den till returleveransen.
    - Vissa regioner i Europa kan få enheten inpackad i en låda. Se till att packa upp enheten och spara lådan för returförsändelse.
3. Du har läst [säkerhetsföreskrifterna för Data Box](data-box-safety.md).
4. Du har fått en jordad strömkabel att använda med lagringsenheten på 100 TB.
5. Du har en värddator som har de data du vill kopiera över till Data Box. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-system-requirements.md).
    - Vara ansluten till höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas, men då påverkas kopieringshastigheten. 
6. Du måste ha tillgång till en plan yta där du kan placera Data Box. Om du vill placera enheten på en standardrackhylla måste du ha en 7U-plats i datacenterracket. Du kan placera enheten liggande eller stående i racket.
7. Du har skaffat följande kablar för att ansluta Data Box till värddatorn.
    - Två 10 GbE SFP+ Twinax-kopparkablar (använd med DATA 1-, DATA 2-nätverksgränssnitt)
    - En RJ-45 CAT 6-nätverkskabel (använd med MGMT-nätverksgränssnitt)
    - En RJ-45 CAT 6A- eller en RJ-45 CAT 6-nätverkskabel (använd med DATA 3-nätverksgränssnitt konfigurerat som 10 Gbps respektive 1 Gbps)

## <a name="cable-your-device"></a>Kabelanslut din enhet

Kabelanslut enheten med hjälp av följande steg.

1. Inspektera enheten och leta efter tecken på manipulation eller andra uppenbara skador. Fortsätt inte om enheten har manipulerats eller skadats. Kontakta Microsoft Support omedelbart för att få hjälp med att bedöma om enheten är i gott skick eller om de bör ersättas.
2. Transportera enheten till den plats där du vill starta den. Placera enheten på en plan yta. Du kan även placera enheten på en standardrackhylla.
3. Anslut strömmen och nätverkskablarna. Bakplanet på en ansluten enhet för en vanlig konfiguration visas nedan. 
    
    ![Bakplan på kabelansluten Data Box-enhet](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Anslut strömkabeln till den märkta strömingången. Den andra änden av strömkabeln ansluts till en strömfördelare.
    2. Använd RJ-45 CAT 6-kabeln till att ansluta MGMT-porten i ena änden och en bärbar dator i den andra änden.            
    3. Använd RJ-45 CAT 6A-kabeln till att ansluta DATA 3-porten i ena änden. DATA 3 konfigureras som 10 GbE om du ansluter via RJ-45 CAT 6A-kabel och som 1 GbE om du ansluter via RJ-45 CAT 6-kabel.
    4. Använd 10 GbE SFP+ Twinax-kopparkablarna för att ansluta till DATA 1- respektive DATA 2-porten. 
    5. De andra ändarna av kablarna från dataportarna ansluts till värddatorn via en 10 GbE-switch.

4. Leta reda på strömknappen på enhetens frontpanel. Slå på enheten.

    ![Strömknappen på Data Box](media/data-box-deploy-set-up/data-box-powered-door-open.png)

## <a name="connect-to-your-device"></a>Ansluta till din enhet

Konfigurera din enhet med hjälp av följande steg via det lokala webbgränssnittet och portalens användargränssnitt.

1. Konfigurera Ethernet-nätverkskortet på den bärbara dator du använder för att ansluta till enheten med den statiska IP-adressen 192.168.100.5 och undernätet 255.255.255.0. 
2. Anslut till MGMT-porten på enheten och öppna dess lokala webbgränssnitt på https://192.168.100.10. Det kan ta upp till 5 minuter efter att du slagit på enheten.
3. Klicka på **Details** (Information) och klicka sedan på **Go on to the webpage** (Fortsätt till webbsidan).

   ![Ansluta till det lokala webbgränssnittet](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. En **inloggningssida** visas för det lokala webbgränssnittet. Kontrollera att enhetens serienummer matchar både portalens användargränssnitt och det lokala webbgränssnittet. Enheten är låst i det här läget.
5. Logga in på [Azure-portalen](https://portal.azure.com).
6. Ladda ned enhetens autentiseringsuppgifter från portalen. Gå till **Allmänt > Enhetsinformation**. Kopiera **enhetslösenordet**. Enhetslösenordet är knutet till en specifik order i portalen. 

    ![Hämta enhetsautentiseringsuppgifter](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Ange det enhetslösenord som du har fått från Azure-portalen i det föregående steget för att logga in på det lokala webbgränssnittet för enheten. Klicka på **Logga in**.
8. På **instrumentpanelen** kontrollerar du att nätverksgränssnitten är konfigurerade. 
    - Om DHCP har aktiverats i din miljö konfigureras nätverksgränssnitten automatiskt. 
    - Om DHCP inte har aktiverats går du till **Ställ in nätverksgränssnitt** och tilldelar statiska IP-adresser, om det behövs.

    ![Instrumentpanel för enhet](media/data-box-deploy-set-up/data-box-dashboard-1.png)

När datanätverksgränssnitten har konfigurerats kan du även använda IP-adressen för något av DATA 1–DATA 3-gränssnitten för att komma åt det lokala webbgränssnittet på `https://<IP address of a data network interface>`. 

När enhetskonfigurationen är klar kan du ansluta till enhetsresurserna och kopiera data från datorn till enheten. 

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Kabelansluta Data Box
> * Ansluta till Data Box

Gå vidare till nästa självstudiekurs och lär dig hur du kopierar data på en Data Box-enhet.

> [!div class="nextstepaction"]
> [Kopiera data till Azure Data Box](./data-box-deploy-copy-data.md)

