---
title: Beställa Microsoft Azure Data Box-diskar | Microsoft Docs
description: Under den här självstudiekursen får du lära dig att registrera dig och beställa Azure Data Box-diskar för att importera data till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 11/05/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 961352dc99a164b8537fb588e038ad1b385941cc
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567459"
---
# <a name="tutorial-order-an-azure-data-box-disk-preview"></a>Självstudie: Beställa Azure Data Box Disk (förhandsversion)

Azure Data Box Disk är en hybridmolnlösning. Via den kan du importera lokala data till Azure på ett snabbt, enkelt och tillförlitligt sätt. Du överför data till SSD-diskar som tillhandahålls av Microsoft och skickar sedan tillbaka diskarna. Dessa data överförs sedan till Azure.

I den här självstudien beskriver vi hur du kan beställa Azure Data Box-diskar. I den här självstudien lär du dig:

> [!div class="checklist"]
> * Registrera dig för Data Box Disk
> * Beställa Data Box-diskar
> * Spåra beställningen
> * Avbryta beställningen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!IMPORTANT]
> - Data Box Disk är tillgängligt som förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen. 
> - Under förhandsversionsfasen kan Data Box Disk levereras till kunder i USA, västra och norra Europa, Kanada och Australien. Mer information finns i [Regional tillgänglighet](data-box-disk-overview.md#region-availability).

## <a name="sign-up"></a>Registrera dig

Data Box Disk är en förhandsversion och du behöver registrera dig för tjänsten. Registrera dig för Data Box-tjänsten via följande steg:

1. Logga in på Azure-portalen på: [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).
2. Välj vilken prenumeration du vill aktivera för förhandsversionen. Svara på frågor om datamängden, var de finns och dataöverföringsfrekvens. Klicka på **Registrera mig!**.
3. När du är registrerad och aktiverad för förhandsversionen kan du beställa du Data Box-diskar.

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

När du har skapat beställningen kan du spåra statusen för ordern via Azure-förhandsportalen. Öppna beställningen och navigera till **Overview** (Översikt) för att visa status. Portalen visar jobbet i tillståndet **Ordered** (beställt). 

![Data Box Disk-status beställd](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Om diskarna inte är tillgängliga får du ett meddelande. Om diskarna är tillgängliga identifierar Microsoft vilka diskar som ska skickas och förbereder dem för leverans. Vid förberedelsen av disk utförs följande åtgärder:

- Diskarna krypteras med AES-128 BitLocker-kryptering.  
- Diskarna är låsta för att förhindra obehörig åtkomst.
- Nyckeln som låser upp diskarna genereras under den här processen.

När diskförberedelserna är klara visar portalen ordningen i tillståndet **Processed** (Bearbetat).

Microsoft förbereder sedan diskarna och skickar dem via en regional leverantör. Du får ett spårningsnummer när diskarna har skickats. Portalen visar ordningen för statusen **Dispatched** (Skickad).



## <a name="cancel-the-order"></a>Avbryta beställningen

Om du vill avbryta beställningen öppnar du Azure-förhandsgranskningsportalen, navigerar till **Overview** och klickar på **Cancel** (Översikt > Avbryt) i kommandofältet. 

Du kan bara avbryta beställningen medan den bearbetas inför leveransen. När beställningen har bearbetats går det inte att avbryta den. 

![Avbryta beställning](media/data-box-disk-deploy-ordered/cancel-order1.png)

Om du vill ta bort en avbruten beställning navigerar du till **Översikt** och klickar på **Ta bort** i kommandofältet. 


## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Registrera dig för Data Box Disk
> * Beställa Data Box Disk
> * Spåra beställningen
> * Avbryta beställningen

Gå vidare till nästa självstudie och lär dig hur du ställer in Data Box-disken.

> [!div class="nextstepaction"]
> [Ställ in en Azure Data Box-disk](./data-box-disk-deploy-set-up.md)


