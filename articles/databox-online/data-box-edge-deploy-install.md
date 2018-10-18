---
title: Självstudiekurs om att installera en fysisk Azure Data Box Edge-enhet | Microsoft Docs
description: Den andra självstudiekursen om att installera Azure Data Box Edge omfattar att packa upp, racka och kabelansluta den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 21ac3de793f5ce559c3a03de2a09f11ccb86b12a
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167366"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>Självstudie: Installera Azure Data Box Edge (förhandsversion)

Den här självstudien beskriver hur du installerar en fysisk Data Box Edge-enhet. Installationsproceduren omfattar uppackning, rackmontering och kabelanslutning av enheten. 

Installationen kan ta cirka 2 timmar att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Packa upp enheten
> * Rackmontera enheten
> * Kabelansluta enheten

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen.

## <a name="prerequisites"></a>Nödvändiga komponenter

De nödvändiga komponenterna för att installera en fysisk enhet är följande.

### <a name="for-the-data-box-edge-resource"></a>För Data Box Edge-resursen

Innan du börjar ska du kontrollera att:

* Du har slutfört alla steg i [Förbereda portalen för Data Box Edge](data-box-edge-deploy-prep.md).
    * Du har skapat Data Box Edge-resursen för att distribuera din enhet.
    * Du har genererat aktiveringsnyckeln för att aktivera din enhet med Data Box Edge-resursen.

 
### <a name="for-the-data-box-edge-physical-device"></a>För den fysiska Data Box Edge-enheten

Innan du distribuerar en enhet:

- Kontrollera att enheten på ett säkert sätt vilar på en plan, stabil och jämn arbetsyta (eller liknande).
- Kontrollera att den plats där du ska konfigurera har:
    - Vanlig nätström från en oberoende källa eller
    - En strömfördelare (PDU) för rack med en avbrottsfri strömkälla (UPS).
- Se till att en 1U-plats är tillgänglig på det rack där du ska montera enheten.

### <a name="for-the-network-in-the-datacenter"></a>För nätverket i datacentret

Innan du börjar:

- Granska nätverkskraven för att distribuera en Data Box Edge och konfigurera datacenternätverket enligt kraven. Mer information finns i [Nätverkskrav för Data Box Edge](data-box-gateway-system-requirements.md#networking-requirements).
- Se till att den minsta Internetbandbredden är 20 Mbps för att enheten ska fungera optimalt.


## <a name="unpack-the-device"></a>Packa upp enheten

Den här enheten levereras i en enda låda. Slutför följande steg för att packa upp enheten. 

1. Placera lådan på en plan, jämn yta.
2. Kontrollera lådan och förpackningsskummet för att se om det förekommer krosskador, skärningar, vattenskador eller andra uppenbara skador. Om lådan eller förpackningen är allvarligt skadad ska du inte öppna lådan. Kontakta Microsoft Support för att få hjälp att bedöma om enheten är i gott skick.
3. Packa upp lådan. När du har packat upp lådan kontrollerar du att det finns:
    - En Edge-enhet med ett enskilt hölje
    - Två strömkablar
    - En rackmonteringssats med verktygslös skena (två sidoskenor och monteringsverktyg ingår)
4. Om något av ovanstående saknas kontaktar du supporten för Data Box Edge. Nästa steg är att rackmontera enheten.


## <a name="rack-the-device"></a>Rackmontera enheten

Enheten måste installeras i ett 19-tums standardrack. Använd följande procedur för att rackmontera enheten i ett 19-tums standardrack med främre och bakre ben.

> [!IMPORTANT]
> Data Box Edge-enheter måste rackmonteras för att fungera korrekt.


1. Dra ut framspärren för att låsa upp det inre spåret från skenenheten. Frigör spärrlåset och tryck mittenspåret inåt så att spåret dras in. De inre och yttre spåren bör du vara separerade.

    ![Installera rackmonteringsspår](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. Installera nu de yttre spåren på rackskåpets lodräta delar. För att underlätta orienteringen är spårskenorna markerade med Front (Framsida), och den änden är fäst mot höljets framsida. 
    
    1. Leta upp spårstiften på framsidan och baksidan av spårenheten. Dra ut spåret så att det får plats mellan rackbenen. Fäst först det yttre spåret på baksidan av racket. Justera det bakre monteringsfästet så att det hamnar på insidan av de bakre rackmonteringhålen.   
    2. Tryck och håll ned utlösaren på det bakre fästet så att metallkrokarna visas. Rikta in och infoga i monteringshålen och släpp sedan utlösaren.
    3. Rikta in det främre fästet med monteringshålet.
    4. Det främre fästet bör nu vara fast i racket. Alternativt kan du använda M5 X 10L-skruvar för att fästa spåren vid benen om det behövs. 

    ![Installera rackmonteringsspår](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. Fäst de inre spåren på chassit genom att rikta in nyckelhålsöppningarna på det inre spåret med orienteringsstiften på sidan av chassit. Se till att huvudena på chassits orienteringsstift sticker ut genom nyckelhålsöppningarna i det inre spåret. Dra ut spåret mot framsidan av chassit tills du hör ett klick som indikerar att spåret har låsts fast. Upprepa med det andra inre spåret. Tryck in chassit med de inre spåret i skenan för att slutföra installationen av racket.

    ![Installera rackmonteringsspår](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>Kabelansluta enheten

Följande procedurer beskriver hur du kabelansluter Edge-enheten för strömförsörjning och nätverk.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar kabelansluta enheten behöver du:

- Den fysiska Edge-enheten uppackad och rackmonterad.
- Två strömkablar. 
- Två 1-GbE RJ-45-nätverkskablar och fyra 25-GbE SFP+-kopparkablar.
- Åtkomst till två strömfördelare (rekommenderas).

Edge-enheten 8 NVMe SSD-enheter. Frontpanel har även statuslampor och strömknappar. Enheten innehåller redundanta nätaggregat (PSU) i den bakre delen. Enheten har sex nätverksgränssnitt: två gränssnitt på 1 Gbit/s och fyra gränssnitt på 25 Gbit/s. Enheten har en styrenhet för baskorthantering (BMC). Identifiera de olika portarna på backsidan av enheten.
 
  ![Baksidan på en kabelansluten enhet](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
Utför följande steg för att kabelansluta enheten för strömförsörjning och nätverk.

1. Anslut strömkablarna till vart och ett av nätaggregaten i höljet. För att säkerställa hög tillgänglighet installerar och ansluter du båda nätaggregaten till olika strömkällor.
2. Anslut strömkablarna till rackets strömfördelare (PDU). Se till att de två strömfördelarna använder separata strömkällor.
3. Anslut PORT 1 på nätverksgränssnittet med 1 GbE till den dator som används för att konfigurera den fysiska enheten. PORT 1 är det dedikerade hanteringsgränssnittet.
4. Anslut PORT 2 på nätverksgränssnittet med 1 GbE via RJ-45-nätverkskablarna till datacenternätverket/Internet. 
5. Anslut de fyra PORT 3, PORT 4, PORT 5 och PORT 6 på nätverksgränssnittet med 25 GbE med hjälp av SFP+-kopparkablar till datacenternätverket/Internet. 

> [!NOTE]
> - Minst ett datanätverksgränssnitt – PORT 2, PORT 3, PORT 4, PORT 5 eller PORT 6 måste vara anslutet till Internet (anslutningar till Azure). 
> - Vi rekommenderar att du använder ett nätverksgränssnitt med 25 GbE såsom PORT 3, PORT 4, PORT 5 eller PORT 6 för att skicka data till Azure. 
> - Edge-enheten bör anslutas till datacenternätverket så att den kan mata in data från datakällservrar.  


## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Data Box Edge-ämnen som att:

> [!div class="checklist"]
> * Packa upp enheten
> * Rackmontera enheten
> * Kabelansluta enheten

Gå vidare till nästa självstudie och lär dig hur du ansluter, konfigurerar och aktiverar enheten.

> [!div class="nextstepaction"]
> [Ansluta och konfigurera din Data Box Edge](./data-box-edge-deploy-connect-setup-activate.md)


