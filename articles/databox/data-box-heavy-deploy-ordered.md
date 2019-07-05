---
title: Självstudie för Azure Data Box tung ordning | Microsoft Docs
description: Läs mer om kraven för distribution och beställa en Azure Data Box tung
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: c7fbd37ff8d40f27e0ca18a6f9816d3d96422ab9
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592398"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Självstudier: Ordning Azure Data Box aktiverat


Azure Data Box tung är en lösning för hybridmoln som gör det möjligt att importera dina lokala data till Azure i ett snabbt, enkelt och tillförlitligt sätt. Du överför data till en lagringsenhet från Microsoft 770 TB (ungefärlig användbar kapacitet) och sedan skicka tillbaka enheten. Dessa data överförs sedan till Azure.

Den här självstudien beskrivs hur du kan beställa en Azure Data Box tung. I den här självstudien lär du dig:

> [!div class="checklist"]
> * Krav för Data Box-aktiverat
> * Beställa en Data Box-aktiverat
> * Spåra beställningen
> * Avbryta beställningen

## <a name="prerequisites"></a>Förutsättningar

Slutför följande konfigurationskrav för Data Box-tjänsten och enheten innan du distribuerar enheten.

### <a name="for-installation-site"></a>För installationsplats

Innan du börjar ska du kontrollera att:

- Enheten passar via standard dörrar och entryways. Dock se till att enheten får plats via alla entryways. Enheten är dimensionerna: bredd: 26 ”längd: 48 ”höjd: 28”.
- Om installerad på en våning än maskinrummet, behöver du åtkomst för enheten via en hiss eller en ramp. Enheten väger cirka 500 ~ lbs.
- Se till att du har en fast plats i datacenter med närhet till en tillgänglig nätverksanslutning som kan hantera en enhet med den här tjänsten.


### <a name="for-service"></a>För tjänsten

Innan du börjar ska du kontrollera att:
- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
- Kontrollera att den prenumeration du använder för Data Box-tjänsten är någon av följande typer:
    - Microsoft Enterprise-avtal (EA). Läs mer om [EA-prenumerationer](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Leverantör av molnlösningar (CSP). Läs mer om [Azure CSP-program](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure-sponsring. Läs mer om [Azure-sponsringsprogrammet](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Kontrollera att du har ägare eller deltagare åtkomst till prenumerationen för att kunna skapa en Data Box tung order.

### <a name="for-device"></a>För enheten

Innan du börjar ska du kontrollera att:
- Enheten är uppackat.
- Du bör ha en värddator som är ansluten till datacenternätverket. Data Box tung att kopiera data från den här datorn. Värddatorn måste köra ett operativsystem som stöds enligt beskrivningen i [Azure Data Box tung systemkrav](data-box-system-requirements.md).
- Du måste ha en bärbar dator med RJ-45 kabeln för att ansluta till lokala Användargränssnittet och konfigurera enheten. Använd den bärbara datorn för att konfigurera varje nod i enheten en gång.
- Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE.
- Du behöver en 40 Gbit/s eller kabel – 10 Gbit/s per enhetsnod. Välj kablar som är kompatibla med den [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) nätverksgränssnitt:

    - Enheten änden av kabeln måste vara QSFP + för 40 Gbit/s-kabel.
    - För 10 Gbit/s kabeln behöver du en SFP +-kabel som ansluts till en 10 G-växel på en end med en QSFP + SFP + nätverkskort (eller kortet QSA) för end som ansluts till enheten.
    - Strömkablarna medföljer enheten.

## <a name="order-data-box-heavy"></a>Ordning Data Box aktiverat

Utför följande steg på Azure-portalen för att beställa en enhet.

1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på denna URL: [https://portal.azure.com](https://portal.azure.com).
2. Välj **+ skapa en resurs** och Sök efter *Azure Data Box*. Välj **Azure Data Box**.
    
   [![Söka efter Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Välj **Skapa**.

4. Kontrollera om Data Box-tjänsten är tillgänglig i din region. Ange eller Välj följande information och välj **tillämpa**.

    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration     | Välj en prenumeration för EA, CSP eller Azure-sponsring för Data Box-tjänsten. <br> Prenumerationen är kopplad till ditt faktureringskonto.       |
    |Överföringstyp     | Välj **Importera till Azure**.        |
    |Källand     | Välj land/region där dina data finns.         |
    |Azure-målregion     | Välj den Azure-region dit du vill överföra data.        |

    [![Välj family tillgänglighet för Data Box](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Välj **Data Box aktiverat**. Den maximala kapaciteten som kan användas för en enskild beställning är 770 TB.

    [![Välj Data Box-aktiverat](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. I **Order** (Beställa) anger du **Order details** (Beställningsinformation). Ange eller Välj följande information och välj **nästa**.
    
    |Inställning  |Värde  |
    |---------|---------|
    |Namn     | Välj ett smeknamn så att du kan spåra beställningen. <br> Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. <br> Namnet måste börja och sluta med en bokstav eller en siffra.      |
    |Resursgrupp     | Använd ett befintligt eller skapa ett nytt. <br> En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans.         |
    |Azure-målregion     | Välj en region för lagringskontot. <br> Mer information finns i [regional tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Lagringsplats     | Välj från storage-konto eller hanterade diskar, eller bådadera. <br> Baserat på den angivna Azure-regionen väljer du ett eller flera lagringskonton från den filtrerade listan med befintliga lagringskonton. <br>Data Box tung kan länkas med upp till 10 storage-konton. <br> Du kan också skapa en ny **General-purpose v1**, **gpv2**, eller **Blob storage-konto**. <br> Azure Data Lake Storage Gen 2-konton stöds inte. Se den [lagringskonton som stöds med din enhet](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Storage-konton med virtuella nätverk stöds. Aktivera betrodda tjänster inom storage-konto nätverksinställningar brandväggen så att Data Box-tjänsten att arbeta med säker storage-konton. Läs mer om hur du [lägga till Azure Data Box-tjänsten som en betrodd tjänst](../storage/common/storage-network-security.md#exceptions).|

    Om du använder storage-konto som lagringsplats, visas följande skärmbild:

    ![Data Box tung för storage-konto](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Om förutom lagringskontot som lagringsplats kan du även använder Data Box tung för att skapa hanterade diskar från de lokala virtuella hårddiskarna, måste du ange följande information:

    |Inställning  |Värde  |
    |---------|---------|
    |Resursgrupper     | Skapa nya resursgrupper om du planerar att skapa hanterade diskar från en lokal virtuell hårddisk. Du kan använda en befintlig resursgrupp endast om resursgruppen har skapats tidigare när du skapar en Data Box tung ordning för hanterad disk av Data Box-tjänsten. <br> Ange flera resursgrupper, avgränsade med semikolon. Högst 10 resursgrupper stöds.|

    ![Data Box tung för hanterad disk](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Storage-konto som angetts för hanterade diskar används som en mellanlagringskontot. Data Box-tjänsten uppladdningar de virtuella hårddiskarna som sida BLOB-objekt till mellanlagringskontot innan du konvertera den till hanterade diskar och flytta den till resursgrupper. Mer information finns i [verifiera data Överför till Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. I **Leveransadress** uppger du för- och efternamn, företagets postadress och ett giltigt telefonnummer. Välj **verifiera adressen**. 

    Tjänsten verifierar leveransadressen och tjänstens tillgänglighet. Om tjänsten är tillgänglig för den angivna leveransadressen får du ett meddelande om det. Välj **Nästa**.

8. Ange e-postadresser i **Notification details** (Information om meddelande). Tjänsten skickar e-postmeddelanden om alla uppdateringar rörande orderstatus.

    Vi rekommenderar att du använder en grupp-e-postadress, så att du kan fortsätta att ta emot meddelanden även om en gruppadministratör lämnar företaget.

9. Granska informationen **Summary** (Sammanfattning) som rör beställningen, kontakt, meddelanden och sekretess. Markera rutan för avtalet till sekretesspolicyn.

10. Välj **ordning**. Det tar några minuter att skapa beställningen.


## <a name="track-the-order"></a>Spåra beställningen

När du har skapat beställningen kan du spåra statusen för ordern via Azure-portalen. Gå till din Data Box tung beställning och gå sedan till **översikt** att visa status. Portalen visar beställningen i tillståndet **Ordered** (beställd).

Om enheten inte är tillgänglig får du ett meddelande. Om enheten är tillgänglig identifierar Microsoft enheten för leverans och förbereder försändelsen. Vid enhetsförberedelsen utförs följande åtgärder:

- SMB-resurser skapas för varje lagringskonto som är associerat med enheten.
- För varje resurs genereras autentiseringsuppgifter för åtkomst såsom användarnamn och lösenord.
- Det enhetslösenord som hjälper till att låsa upp enheten skapas också.
- Data Box-tung har låsts för att förhindra obehörig åtkomst till enheten när som helst.

När enhetsförberedelserna är klara visar portalen beställningen i tillståndet **Processed** (Behandlad).

![Data Box tung order bearbetas](media/data-box-overview/data-box-order-status-processed.png)

Microsoft förbereder sedan enheten och skickar den via en regional transportör. Du får ett spårningsnummer när enheten har skickats. Portalen visar ordningen för statusen **Dispatched** (Skickad).

![Data Box tung ordning skickad](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Avbryta beställningen

Om du vill avbryta beställningen öppnar du Azure-portalen, navigerar till **Översikt** och klickar på **Avbryt** i kommandofältet.

När du har gjort en beställning kan du avbryta den när som helst innan orderstatusen markeras som Processed (Behandlad).
 
Om du vill ta bort en avbruten beställning navigerar du till **Översikt** och klickar på **Ta bort** i kommandofältet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Azure Data Box tung ämnen som:

> [!div class="checklist"]
> * Förutsättningar
> * Ordning Data Box aktiverat
> * Spåra beställningen
> * Avbryta beställningen

Gå vidare till nästa självstudie och lär dig hur du ställer in din Data Box tung.

> [!div class="nextstepaction"]
> [Konfigurera din Azure Data Box tung](./data-box-heavy-deploy-set-up.md)
