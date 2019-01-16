---
title: Beställa Microsoft Azure Data Box-diskar | Microsoft Docs
description: Under den här självstudiekursen får du lära dig att registrera dig och beställa Azure Data Box-diskar för att importera data till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: db10361707d83fcda20f0e4bf2adc2abc4176808
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156179"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Självstudier: Beställa en Azure Data Box Disk

Azure Data Box Disk är en hybridmolnlösning. Via den kan du importera lokala data till Azure på ett snabbt, enkelt och tillförlitligt sätt. Du överför data till SSD-diskar som tillhandahålls av Microsoft och skickar sedan tillbaka diskarna. Dessa data överförs sedan till Azure.

I den här självstudien beskriver vi hur du kan beställa Azure Data Box-diskar. I den här självstudien lär du dig:

> [!div class="checklist"]
> * Beställa Data Box-diskar
> * Spåra beställningen
> * Avbryta beställningen

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du distribuerar slutför du följande konfigurationskrav för Data Box-tjänsten och Data Box Disk.

### <a name="for-service"></a>För tjänsten

Innan du börjar ska du kontrollera att:
- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
- Kontrollera att den prenumeration du använder för Data Box-tjänsten är någon av följande typer:
    - Microsoft Enterprise-avtal (EA). Läs mer om [EA-prenumerationer](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Leverantör av molnlösningar (CSP). Läs mer om [Azure CSP-program](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
- Kontrollera att du har ägar- eller deltagaråtkomst till prenumerationen för att skapa en Data Box-beställning.

### <a name="for-device"></a>För enheten

Innan du börjar ska du kontrollera att:
- Du har en tillgänglig klientdator som du kan kopiera data från. Klientdatorn måste:
    - Köra ett [operativsystem som stöds](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Ha en annan[programvara som krävs](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) installerad om det är en Windows-klient.  

## <a name="order-data-box-disk"></a>Beställa Data Box Disk

Utför följande steg på [Azure-portalen](https://aka.ms/azuredataboxfromdiskdocs) för att beställa Data Box Disk-lösningen.

1. I det övre vänstra hörnet i portalen klickar du på **+ Create a resource**, (Skapa en resurs) och titta efter *Azure Data Box*. Klicka på **Azure Data Box**.
    
   ![Sök Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Klicka på **Skapa**.

3. Kontrollera om Data Box-tjänsten är tillgänglig i din region. Ange eller välj följande information och klicka på **Tillämpa**.

    ![Välj Data Box Disk-alternativ](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Inställning|Värde|
    |---|---|
    |Prenumeration|Välj den prenumeration för vilken Data Box-tjänsten är aktiverad.<br> Prenumerationen är kopplad till ditt faktureringskonto. |
    |Överföringstyp| Importera till Azure|
    |Källand | Välj landet där dina data finns.|
    |Azure-målregion|Välj den Azure-region dit du vill överföra data.|

  
5.  Välj **Data Box Disk**. Maximal kapacitet för en enskild beställning på 5 diskar är 35 TB. Du kan skapa flera beställningar om du har större datamängder.

     ![Välj Data Box Disk-alternativ](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  I **Order** (Beställa) anger du **Order details** (Beställningsinformation). Ange eller välj följande information.

    |Inställning|Värde|
    |---|---|
    |Namn|Välj ett smeknamn så att du kan spåra beställningen.<br> Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. <br> Namnet måste börja och sluta med en bokstav eller en siffra. |
    |Resursgrupp| Använd ett befintligt eller skapa ett nytt. <br> En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. |
    |Azure-målregion| Välj en region för lagringskontot.<br> För närvarande stöds lagringskonton i USA, västra och norra Europa, Kanada och Australien. |
    |Lagringskonto(n)|Baserat på den angivna Azure-regionen kan du välja ur en filtrerad lista med befintliga lagringskonton. <br>Du kan också skapa nya konton för generell användning av typen v1 eller v2. |
    |Beräknad datastorlek i TB| Ange en uppskattning i TB. <br>Microsoft skickar baserat på datastorlek ett lämpligt antal SSD-diskar på vardera 8 TB (7 TB användbar kapacitet). <br>Den maximala kapaciteten som kan användas på 5 diskar är upp till 35 TB. |
    |Disknyckel| Ange disknyckeln om du markerar **Använd anpassad nyckel istället för en nyckel som skapats av Azure**. <br> Ange en alfanumerisk nyckel på 12 till 32 tecken som har minst ett numeriskt tecken och ett specialtecken. Tillåtna specialtecken är `@?_+`. <br> Du kan välja att hoppa över det här alternativet och använder den Azure-genererade nyckeln för att låsa upp dina diskar.|

13. Klicka på **Nästa**. 

    ![Uppge beställningsinformation](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. På fliken **Shipping address** (Leveransadress) uppger du för- och efternamn, företagets postadress och ett giltigt telefonnummer. Klicka på **Verifiera adress**. Tjänsten verifierar leveransadressen och tjänstens tillgänglighet. Om tjänsten är tillgänglig för den angivna leveransadressen får du ett meddelande om det. 

    ![Uppge leveransadress](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. Ange e-postadresser i **Notification details** (Information om meddelande). Tjänsten skickar e-postmeddelanden om alla uppdateringar rörande orderstatus. 

    Vi rekommenderar att du använder en grupp-e-postadress, så att du kan fortsätta att ta emot meddelanden även om en gruppadministratör lämnar företaget.

16. Granska informationen **Summary** (Sammanfattning) som rör beställningen, kontakt, meddelanden och sekretess. Markera rutan för avtalet till sekretesspolicyn.

17. Klicka på **Order** (Ordning). Det tar några minuter att skapa beställningen.

 
## <a name="track-the-order"></a>Spåra beställningen

När du har skapat beställningen kan du spåra statusen för ordern via Azure-portalen. Öppna beställningen och navigera till **Overview** (Översikt) för att visa status. Portalen visar jobbet i tillståndet **Ordered** (beställt). 

![Data Box Disk-status beställd](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Om diskarna inte är tillgängliga får du ett meddelande. Om diskarna är tillgängliga identifierar Microsoft vilka diskar som ska skickas och förbereder dem för leverans. Vid förberedelsen av disk utförs följande åtgärder:

- Diskarna krypteras med AES-128 BitLocker-kryptering.  
- Diskarna är låsta för att förhindra obehörig åtkomst.
- Nyckeln som låser upp diskarna genereras under den här processen.

När diskförberedelserna är klara visar portalen ordningen i tillståndet **Processed** (Bearbetat).

Microsoft förbereder sedan diskarna och skickar dem via en regional leverantör. Du får ett spårningsnummer när diskarna har skickats. Portalen visar ordningen för statusen **Dispatched** (Skickad).

## <a name="cancel-the-order"></a>Avbryta beställningen

Om du vill avbryta beställningen öppnar du Azure-portalen, navigerar till **Översikt** och klickar på **Avbryt** i kommandofältet. 

Du kan bara avbryta beställningen medan den bearbetas inför leveransen. När beställningen har bearbetats går det inte att avbryta den. 

![Avbryta beställning](media/data-box-disk-deploy-ordered/cancel-order1.png)

Om du vill ta bort en avbruten beställning navigerar du till **Översikt** och klickar på **Ta bort** i kommandofältet.


## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Beställa Data Box Disk
> * Spåra beställningen
> * Avbryta beställningen

Gå vidare till nästa självstudie och lär dig hur du ställer in Data Box-disken.

> [!div class="nextstepaction"]
> [Ställ in en Azure Data Box-disk](./data-box-disk-deploy-set-up.md)


