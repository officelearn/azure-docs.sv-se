---
title: Beställa Microsoft Azure Data Box | Microsoft Docs
description: Lär dig om förutsättningarna för distribution och hur du beställer en Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 07ccd2aaec6b2325d6eef09a466a5d0707836b4b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57834276"
---
# <a name="tutorial-order-azure-data-box"></a>Självstudier: Beställa Azure Data Box

Azure Data Box är en hybridmolnlösning som gör att du kan importera lokala data till Azure på ett snabbt, enkelt och tillförlitligt sätt. Du överför data till en lagringsenhet med 80 TB (användbar kapacitet) från Microsoft och skickar sedan tillbaka enheten. Dessa data överförs sedan till Azure.

I den här självstudien beskriver vi hur du kan beställa en Azure Data Box. I den här självstudien lär du dig:

> [!div class="checklist"]
> * Förutsättningar för att distribuera Data Box
> * Beställa en Data Box
> * Spåra beställningen
> * Avbryta beställningen

## <a name="prerequisites"></a>Förutsättningar

Slutför följande konfigurationskrav för Data Box-tjänsten och enheten innan du distribuerar enheten.

### <a name="for-service"></a>För tjänsten

Innan du börjar ska du kontrollera att:
- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
- Kontrollera att den prenumeration du använder för Data Box-tjänsten är någon av följande typer:
    - Microsoft Enterprise-avtal (EA). Läs mer om [EA-prenumerationer](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Leverantör av molnlösningar (CSP). Läs mer om [Azure CSP-program](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure-sponsring. Läs mer om [Azure-sponsringsprogrammet](https://azure.microsoft.com/offers/ms-azr-0036p/).

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
    |Prenumeration     | Välj en prenumeration för EA, CSP eller Azure-sponsring för Data Box-tjänsten. <br> Prenumerationen är kopplad till ditt faktureringskonto.       |
    |Överföringstyp     | Välj **Importera till Azure**.        |
    |Källand     |   Välj landet där dina data finns.         |
    |Azure-målregion     |     Välj den Azure-region dit du vill överföra data.        |

5. Välj **Data Box**. Den maximala kapaciteten som kan användas för en enskild beställning är 786 TB. Du kan skapa flera beställningar för större datamängder.

      [![Välja Data Box-alternativ 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. I **Order** (Beställa) anger du **Order details** (Beställningsinformation). Ange eller välj följande information och klicka på **Nästa**.
    
    |Inställning  |Värde  |
    |---------|---------|
    |Namn     |  Välj ett smeknamn så att du kan spåra beställningen. <br> Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. <br> Namnet måste börja och sluta med en bokstav eller en siffra.      |
    |Resursgrupp     |   Använd ett befintligt eller skapa ett nytt. <br> En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans.         |
    |Azure-målregion     | Välj en region för lagringskontot. <br> Mer information finns i [regional tillgänglighet](data-box-overview.md#region-availability).        |
    |Lagringsmål     | Välj från storage-konto eller hanterade diskar, eller bådadera. <br> Baserat på den angivna Azure-regionen väljer du ett eller flera lagringskonton från den filtrerade listan med befintliga lagringskonton. Data Box kan länkas med upp till 10 lagringskonton. <br> Du kan också skapa en ny **General-purpose v1**, **gpv2**, eller **Blob storage-konto**. <br>Storage-konton med virtuella nätverk stöds. Aktivera betrodda tjänster inom storage-konto nätverksinställningar brandväggen så att Data Box-tjänsten att arbeta med säker storage-konton. Läs mer om hur du [lägga till Azure Data Box som en betrodd tjänst](../storage/common/storage-network-security.md#exceptions).|

    Om du använder storage-konto som lagringsplats, visas följande skärmbild:

    ![Data Box-beställning för storage-konto](media/data-box-deploy-ordered/order-storage-account.png)

    Om du använder Data Box för att skapa hanterade diskar från de lokala virtuella hårddiskarna, behöver du också ange följande information:

    |Inställning  |Värde  |
    |---------|---------|
    |Resursgrupper     | Skapa nya resursgrupper om du planerar att skapa hanterade diskar från en lokal virtuell hårddisk. Du kan använda en befintlig resursgrupp endast om resursgruppen har skapats tidigare när du skapar en Data Box-beställning för hanterad disk av Data Box-tjänsten. <br> Ange flera resursgrupper, avgränsade med semikolon. Högst 10 resursgrupper stöds.|

    ![Data Box-beställning för hanterad disk](media/data-box-deploy-ordered/order-managed-disks.png)

    Storage-konto som angetts för hanterade diskar används som en mellanlagringskontot. Data Box-tjänsten uppladdningar de virtuella hårddiskarna som sida BLOB-objekt till mellanlagringskontot innan du konvertera den till hanterade diskar och flytta den till resursgrupper. Mer information finns i [verifiera data Överför till Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. I **Leveransadress** uppger du för- och efternamn, företagets postadress och ett giltigt telefonnummer. Klicka på **Verifiera adress**. Tjänsten verifierar leveransadressen och tjänstens tillgänglighet. Om tjänsten är tillgänglig för den angivna leveransadressen får du ett meddelande om det. Klicka på **Nästa**.

8. Ange e-postadresser i **Notification details** (Information om meddelande). Tjänsten skickar e-postmeddelanden om alla uppdateringar rörande orderstatus.

    Vi rekommenderar att du använder en grupp-e-postadress, så att du kan fortsätta att ta emot meddelanden även om en gruppadministratör lämnar företaget.

9. Granska informationen **Summary** (Sammanfattning) som rör beställningen, kontakt, meddelanden och sekretess. Markera rutan för avtalet till sekretesspolicyn.

10. Klicka på **Order** (Ordning). Det tar några minuter att skapa beställningen.


## <a name="track-the-order"></a>Spåra beställningen

När du har skapat beställningen kan du spåra statusen för ordern via Azure-portalen. Öppna Data Box-beställningen och navigera till **Overview** (Översikt) för att visa status. Portalen visar beställningen i tillståndet **Ordered** (beställd).

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


