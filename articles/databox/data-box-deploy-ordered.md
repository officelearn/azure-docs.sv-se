---
title: Beställa Microsoft Azure Data Box | Microsoft Docs
description: Lär dig om förutsättningarna för distribution och hur du beställer en Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/10/2018
ms.author: alkohli
ms.openlocfilehash: 0d77353b510dbeb18e96e2d8313e9010c04e25a7
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077941"
---
# <a name="tutorial-order-azure-data-box"></a>Självstudie: Beställa Azure Data Box

Azure Data Box är en hybridmolnlösning som gör att du kan importera lokala data till Azure på ett snabbt, enkelt och tillförlitligt sätt. Du överför data till en lagringsenhet med 80 TB (användbar kapacitet) från Microsoft och skickar sedan tillbaka enheten. Dessa data överförs sedan till Azure.

I den här självstudien beskriver vi hur du kan beställa en Azure Data Box. I den här självstudien lär du dig:

> [!div class="checklist"]
> * Registrera dig för Data Box
> * Beställa en Data Box
> * Spåra beställningen
> * Avbryta beställningen

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför följande konfigurationskrav för Data Box-tjänsten och enheten innan du distribuerar enheten.

### <a name="for-service"></a>För tjänsten

Innan du börjar ska du kontrollera att:
- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
- Kontrollera att den prenumeration du använder för Data Box-tjänsten är någon av följande typer:
    - Microsoft Enterprise-avtal (EA). Läs mer om [EA-prenumerationer](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Leverantör av molnlösningar (CSP). Läs mer om [Azure CSP-program](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Förbrukning – användningsbaserad betalning. Mer information om Azures [prenumerationer med användningsbaserad betalning](https://azure.microsoft.com/offers/ms-azr-0003p/).

- Kontrollera att du har ägar- eller deltagaråtkomst till prenumerationen för att skapa en Data Box-beställning.

### <a name="for-device"></a>För enheten

Innan du börjar ska du kontrollera att:
- Du bör ha en värddator som är ansluten till datacenternätverket. Data Box kopierar data från den här datorn. Värddatorn måste köra ett operativsystem som stöds enligt beskrivningen i [Systemkrav för Azure Data Box](data-box-system-requirements.md).
- Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en anslutning på 10 GbE inte är tillgänglig kan en 1 GbE-datalänk användas, men detta påverkar kopieringshastigheten.


## <a name="order-data-box"></a>Beställa Data Box

Utför följande steg på Azure-portalen för att beställa en enhet.

1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på denna URL: [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **+ Skapa en resurs** och sök efter *Azure Data Box*. Klicka på **Azure Data Box**.
    
   [![Söka efter Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Klicka på **Skapa**.

4. Kontrollera om Data Box-tjänsten är tillgänglig i din region. Ange eller välj följande information och klicka på **Tillämpa**. 
    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration     | Välj en prenumeration för EA, CSP eller betala per användning för Data Box-tjänsten. <br> Prenumerationen är kopplad till ditt faktureringskonto.       |
    |Överföringstyp     | Välj **Importera till Azure**.        |
    |Källand     |   Välj landet där dina data finns.         |
    |Azure-målregion     |     Välj den Azure-region dit du vill överföra data.        |

5. Välj **Data Box**. Den maximala lösningskapaciteten för en enskild beställning är 80 TB. Du kan skapa flera beställningar för större datamängder.

      [![Välja Data Box-alternativ 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. I **Order** (Beställa) anger du **Order details** (Beställningsinformation). Ange eller välj följande information och klicka på **Nästa**.
    
    |Inställning  |Värde  |
    |---------|---------|
    |Namn     |  Välj ett smeknamn så att du kan spåra beställningen. <br> Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. <br> Namnet måste börja och sluta med en bokstav eller en siffra.      |
    |Resursgrupp     |   Använd ett befintligt eller skapa ett nytt. <br> En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans.         |
    |Azure-målregion     | Välj en region för lagringskontot. <br> Mer information finns i [regional tillgänglighet](data-box-overview.md#region-availability).        |
    |Lagringskonto(n)     | Baserat på den angivna Azure-regionen väljer du ett eller flera lagringskonton från den filtrerade listan med befintliga lagringskonton. Data Box kan länkas med upp till 10 lagringskonton. <br> Du kan även ett nytt konto för **Generell användning v1**, **Generell användning v2** eller **Blob Storage**.        |
    
7. I **Leveransadress** uppger du för- och efternamn, företagets postadress och ett giltigt telefonnummer. Klicka på **Verifiera adress**. Tjänsten verifierar leveransadressen och tjänstens tillgänglighet. Om tjänsten är tillgänglig för den angivna leveransadressen får du ett meddelande om det. Klicka på **Nästa**.

8. Ange e-postadresser i **Notification details** (Information om meddelande). Tjänsten skickar e-postmeddelanden om alla uppdateringar rörande orderstatus.

    Vi rekommenderar att du använder en grupp-e-postadress, så att du kan fortsätta att ta emot meddelanden även om en gruppadministratör lämnar företaget.

9. Granska informationen **Summary** (Sammanfattning) som rör beställningen, kontakt, meddelanden och sekretess. Markera rutan för avtalet till sekretesspolicyn.

10. Klicka på **Order** (Ordning). Det tar några minuter att skapa beställningen. 


## <a name="track-the-order"></a>Spåra beställningen

När du har skapat beställningen kan du spåra statusen för ordern via Azure-portalen. Öppna beställningen och navigera till **Overview** (Översikt) för att visa status. Portalen visar beställningen i tillståndet **Ordered** (beställd).

Om enheten inte är tillgänglig får du ett meddelande. Om enheten är tillgänglig identifierar Microsoft enheten för leverans och förbereder försändelsen. Vid enhetsförberedelsen utförs följande åtgärder:

- SMB-resurser skapas för varje lagringskonto som är associerat med enheten. 
- För varje resurs genereras autentiseringsuppgifter för åtkomst såsom användarnamn och lösenord.
- Det enhetslösenord som hjälper till att låsa upp enheten skapas också. 
- Data Box låses för att förhindra obehörig åtkomst till enheten.

När enhetsförberedelserna är klara visar portalen beställningen i tillståndet **Processed** (Behandlad).

![Data Box-beställningen behandlas](media/data-box-overview/data-box-order-status-processed.png)

Microsoft förbereder sedan enheten och skickar den via en regional transportör. Du får ett spårningsnummer när enheten har skickats. Portalen visar ordningen för statusen **Dispatched** (Skickad).

![Data Box-beställningen skickas](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Avbryta beställningen

Om du vill avbryta beställningen öppnar du Azure-portalen, navigerar till **Översikt** och klickar på **Avbryt** i kommandofältet.

När du har gjort en beställning kan du avbryta den när som helst innan orderstatusen markeras som Processed (Behandlad).
 
Om du vill ta bort en avbruten beställning navigerar du till **Översikt** och klickar på **Ta bort** i kommandofältet.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Förutsättningar för att distribuera Data Box
> * Beställa Data Box
> * Spåra beställningen
> * Avbryta beställningen

Gå vidare till nästa självstudie och lär dig hur du ställer in din Data Box.

> [!div class="nextstepaction"]
> [Konfigurera Azure Data Box](./data-box-deploy-set-up.md)


