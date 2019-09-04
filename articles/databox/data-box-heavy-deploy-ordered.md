---
title: Självstudie för att beställa Azure Data Box Heavy | Microsoft Docs
description: Lär dig mer om distributions kraven och hur du beställer en Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dce3549bde4c62245e1d2b1f8ac0c88c0b70260c
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241396"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Självstudier: Beställ Azure Data Box Heavy


Azure Data Box Heavy är en hybrid lösning som gör att du kan importera dina lokala data till Azure på ett snabbt, enkelt och tillförlitligt sätt. Du överför dina data till en Microsoft-770 TB-lagrings enhet (ungefärlig användbar kapacitet) och skickar sedan tillbaka enheten. Dessa data överförs sedan till Azure.

I den här självstudien beskrivs hur du beställer en Azure Data Box Heavy. I den här självstudien lär du dig:

> [!div class="checklist"]
> * Krav för Data Box Heavy
> * Beställ en Data Box Heavy
> * Spåra beställningen
> * Avbryta beställningen

## <a name="prerequisites"></a>Förutsättningar

Slutför följande konfigurationskrav för Data Box-tjänsten och enheten innan du distribuerar enheten.

### <a name="for-installation-site"></a>För installations plats

Innan du börjar ska du kontrollera att:

- Enheten passar genom standard doorways och entryways. Se dock till att enheten får plats med alla dina entryways. Enhets mått är: bredd: 26-"längd: 48 "höjd: 28 ".
- Om det är installerat på en annan våning än jord golven måste du ha åtkomst till enheten via en hiss eller en ramp. Enheten väger cirka ~ 500 kg.
- Se till att du har en platt plats i data centret med närhet till en tillgänglig nätverks anslutning som kan hantera en enhet med det här utrymmet.


### <a name="for-service"></a>För tjänsten

Innan du börjar ska du kontrollera att:
- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
- Kontrollera att den prenumeration du använder för Data Box-tjänsten är någon av följande typer:
    - Microsoft Enterprise-avtal (EA). Läs mer om [EA-prenumerationer](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Leverantör av molnlösningar (CSP). Läs mer om [Azure CSP-program](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure-sponsring. Läs mer om [Azure-sponsringsprogrammet](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Se till att du har ägar-eller deltagar åtkomst till prenumerationen för att skapa en Data Box Heavys order.

### <a name="for-device"></a>För enheten

Innan du börjar ska du kontrollera att:
- Enheten packas upp.
- Du bör ha en värddator som är ansluten till datacenternätverket. Data Box Heavy kommer att kopiera data från den här datorn. Värd datorn måste köra ett operativ system som stöds enligt beskrivningen i [Azure Data Box Heavy system krav](data-box-system-requirements.md).
- Du måste ha en bärbar dator med RJ-45-kabel för att kunna ansluta till det lokala användar gränssnittet och konfigurera enheten. Använd den bärbara datorn för att konfigurera varje nod av enheten en gång.
- Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE.
- Du behöver 1 40 Gbit/s eller 10 Gbit/s-kabel per enhet-nod. Välj kablar som är kompatibla med [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) Network Interface:

    - För 40 Gbit/s-kabeln måste enhetens ände av kabeln vara QSFP +.
    - För 10 Gbit/s-kabeln behöver du en SFP + kabel som ansluts till en 10 G-växel i ena änden, med en QSFP + till SFP + adapter (eller uppfyller kraven enligt-adaptern) för det slut som kopplas till enheten.
    - Ström kablarna ingår i enheten.

## <a name="order-data-box-heavy"></a>Beställ Data Box Heavy

Utför följande steg på Azure-portalen för att beställa en enhet.

1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på denna URL: [https://portal.azure.com](https://portal.azure.com).
2. Välj **+ skapa en resurs** och sök efter *Azure Data Box*. Välj **Azure Data Box**.
    
   [![Söka efter Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Välj **Skapa**.

4. Kontrol lera om tjänsten Data Box-enhet är tillgänglig i din region. Ange eller Välj följande information och välj sedan **Använd**.

    |Inställning  |Value  |
    |---------|---------|
    |Subscription     | Välj en prenumeration för EA, CSP eller Azure-sponsring för Data Box-tjänsten. <br> Prenumerationen är kopplad till ditt faktureringskonto.       |
    |Överföringstyp     | Välj **Importera till Azure**.        |
    |Källand     | Välj land/region där dina data finns.         |
    |Azure-målregion     | Välj den Azure-region dit du vill överföra data.        |

    [![Välj Data Box-enhet Family-tillgänglighet](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Välj **data Box Heavy**. Den högsta användbara kapaciteten för en enskild order är 770 TB.

    [![Välj Data Box Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. I **Order** (Beställa) anger du **Order details** (Beställningsinformation). Ange eller Välj följande information och välj **Nästa**.
    
    |Inställning  |Värde  |
    |---------|---------|
    |Name     | Välj ett smeknamn så att du kan spåra beställningen. <br> Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. <br> Namnet måste börja och sluta med en bokstav eller en siffra.      |
    |Resource group     | Använd ett befintligt eller skapa ett nytt. <br> En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans.         |
    |Azure-målregion     | Välj en region för lagringskontot. <br> Mer information finns i [regional tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Lagringsmål     | Välj mellan lagrings konto eller hanterade diskar eller båda. <br> Baserat på den angivna Azure-regionen väljer du ett eller flera lagringskonton från den filtrerade listan med befintliga lagringskonton. <br>Data Box Heavy kan länkas med upp till 10 lagrings konton. <br> Du kan också skapa ett nytt **Allmänt v1**-, **General-Purpose v2**-eller **Blob Storage-konto**. <br> Konton för Azure Data Lake Storage gen 2 stöds inte. Se de [lagrings konton som stöds med din enhet](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Lagrings konton med virtuella nätverk stöds. Om du vill tillåta att Data Box-enhet-tjänsten arbetar med skyddade lagrings konton aktiverar du betrodda tjänster i inställningarna för nätverks brand väggen för lagrings kontot. Mer information finns i så här [lägger du till Azure Data Box tjänst som en betrodd tjänst](../storage/common/storage-network-security.md#exceptions).|

    Om du använder lagrings kontot som lagrings mål visas följande skärm bild:

    ![Data Box Heavy ordning för lagrings konto](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Om du lägger till lagrings kontot som lagrings mål använder du också Data Box Heavy för att skapa hanterade diskar från lokala virtuella hård diskar. du måste ange följande information:

    |Inställning  |Value  |
    |---------|---------|
    |Resursgrupper     | Skapa nya resurs grupper om du tänker skapa hanterade diskar från lokala virtuella hård diskar. Du kan bara använda en befintlig resurs grupp om resurs gruppen skapades tidigare när du skapade en Data Box Heavys ordning för den hanterade disken av Data Box-enhet-tjänsten. <br> Ange flera resurs grupper avgränsade med semikolon. Högst 10 resurs grupper stöds.|

    ![Data Box Heavy ordning för hanterad disk](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Det angivna lagrings kontot för hanterade diskar används som ett lagrings konto för mellanlagring. Data Box-enhet tjänsten laddar upp de virtuella hård diskarna som Page blobbar till mellanlagrings kontot innan du konverterar det till hanterade diskar och flyttar det till resurs grupperna. Mer information finns i [Verifiera data överföring till Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. I **Leveransadress** uppger du för- och efternamn, företagets postadress och ett giltigt telefonnummer. Välj **Verifiera adress**. 

    Tjänsten verifierar leveransadressen och tjänstens tillgänglighet. Om tjänsten är tillgänglig för den angivna leveransadressen får du ett meddelande om det. Välj **Nästa**.

8. Ange e-postadresser i **Notification details** (Information om meddelande). Tjänsten skickar e-postmeddelanden om alla uppdateringar rörande orderstatus.

    Vi rekommenderar att du använder en grupp-e-postadress, så att du kan fortsätta att ta emot meddelanden även om en gruppadministratör lämnar företaget.

9. Granska informationen **Summary** (Sammanfattning) som rör beställningen, kontakt, meddelanden och sekretess. Markera rutan för avtalet till sekretesspolicyn.

10. Välj **ordning**. Det tar några minuter att skapa beställningen.


## <a name="track-the-order"></a>Spåra beställningen

När du har skapat beställningen kan du spåra statusen för ordern via Azure-portalen. Gå till din Data Box Heavys ordning och gå sedan till **Översikt** för att visa statusen. Portalen visar beställningen i tillståndet **Ordered** (beställd).

Om enheten inte är tillgänglig får du ett meddelande. Om enheten är tillgänglig identifierar Microsoft enheten för leverans och förbereder försändelsen. Vid enhetsförberedelsen utförs följande åtgärder:

- SMB-resurser skapas för varje lagringskonto som är associerat med enheten.
- För varje resurs genereras autentiseringsuppgifter för åtkomst såsom användarnamn och lösenord.
- Det enhetslösenord som hjälper till att låsa upp enheten skapas också.
- Data Box Heavy är låst för att förhindra obehörig åtkomst till enheten vid något tillfälle.

När enhetsförberedelserna är klara visar portalen beställningen i tillståndet **Processed** (Behandlad).

![Data Box Heavy bearbetad order](media/data-box-overview/data-box-order-status-processed.png)

Microsoft förbereder sedan enheten och skickar den via en regional transportör. Du får ett spårningsnummer när enheten har skickats. Portalen visar ordningen för statusen **Dispatched** (Skickad).

![Data Box Heavy order har skickats](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Avbryta beställningen

Om du vill avbryta beställningen öppnar du Azure-portalen, navigerar till **Översikt** och klickar på **Avbryt** i kommandofältet.

När du har gjort en beställning kan du avbryta den när som helst innan orderstatusen markeras som Processed (Behandlad).
 
Om du vill ta bort en avbruten beställning navigerar du till **Översikt** och klickar på **Ta bort** i kommandofältet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Azure Data Box Heavy ämnen som:

> [!div class="checklist"]
> * Förutsättningar
> * Beställ Data Box Heavy
> * Spåra beställningen
> * Avbryta beställningen

Gå vidare till nästa självstudie och lär dig hur du konfigurerar din Data Box Heavy.

> [!div class="nextstepaction"]
> [Konfigurera din Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
