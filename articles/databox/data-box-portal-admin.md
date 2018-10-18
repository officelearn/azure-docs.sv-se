---
title: Administratörshandbok för Azure Data Box-portalen | Microsoft Docs
description: Beskriver hur du använder Azure Portal för att administrera Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 49c2258100e99742bcb2e22fbce7f05b69c70ef6
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090730"
---
# <a name="use-the-azure-portal-to-administer-your-data-box"></a>Använda Azure-portalen för att administrera din Data Box

Den här artikeln beskriver några av de komplexa arbetsflöden och de administrativa uppgifter som du kan utföra med Data Box. Du kan hantera Data Box via Azure-portalen eller via det lokala webbgränssnittet. 

Den här artikeln fokuserar på de uppgifter som du kan utföra med Azure-portalen. Använd Azure-portalen för att hantera beställningar, hantera Data Box och spåra beställningens status från början till slut.


## <a name="cancel-an-order"></a>Annullera en beställning

Ibland kan du behöva annullera en beställning som du gjort. Du kan bara annullera en beställning innan beställningen behandlas. När beställningen har behandlats och Data Box förberetts går det inte längre att annullera beställningen. 

Följ stegen nedan om du vill annullera en beställning.

1.  Gå till **Översikt > Avbryt**. 

    ![Annullera beställning 1](media/data-box-portal-admin/cancel-order1.png)

2.  Fyll i en orsak som beskriver varför du vill annullera beställningen.  

    ![Annullera beställning 2](media/data-box-portal-admin/cancel-order2.png)

3.  När beställningen har annullerats uppdateras statusen för ordern på portalen och visas som **Avbruten**. 

## <a name="clone-an-order"></a>Klona en order

Kloning är användbart i vissa situationer. Exempelvis har en användare kanske använt Data Box för att överföra vissa data. Allteftersom mer data genereras behövs en till Data Box för att överföra dessa data till Azure. I så fall behöver du bara klona samma order.

Följ stegen nedan om du vill klona en order.

1.  Gå till **Översikt > Klona**. 

    ![Klona order 1](media/data-box-portal-admin/clone-order1.png)

2.  All information för ordern förblir densamma. Namnet på beställningen är det ursprungliga ordernamnet, med tillägget *-Klon*. Markera kryssrutan för att bekräfta att du har läst sekretessinformationen. Klicka på **Skapa**.    

Klonen skapas på några minuter och portalen uppdateras och visar den nya ordern.


## <a name="delete-order"></a>Ta bort en order

Du kanske vill ta bort en order när den har verkställts. Ordern innehåller din personliga information, till exempel namn, adress och kontaktuppgifter. Den här personliga informationen tas bort när ordern tas bort.

Du kan bara ta bort order som har slutförts eller avbrutits. Följ steg nedan om du vill ta bort en order.

1. Gå till **Alla resurser**. Sök efter din order.

2. Klicka på den order som du vill ta bort och gå till **Översikt**. Klicka på **Ta bort** i kommandofältet.

    ![Ta bort Data Box-order 1](media/data-box-portal-admin/delete-order1.png)

3. Ange namnet på ordern när du uppmanas att bekräfta borttagningen av ordern. Klicka på **Ta bort**.

## <a name="download-shipping-label"></a>Ladda ned adressetikett

Du kan behöva ladda ned adressetiketten om E-ink-skärmen på din Data Box inte fungerar och inte visar returfraktsedeln. 

Följ stegen nedan om du behöver ladda ned en fraktsedel.
1.  Öppna **Översikt > Ladda ned adressetikett**. Det här alternativet är endast tillgängligt när enheten har skickats. 

    ![Ladda ned adressetikett](media/data-box-portal-admin/download-shipping-label.png)

2.  Med det här alternativet laddas följande returfraktsedel ned. Spara etiketten och skriv ut den. Vik och sätt in etiketten i den genomskinliga fickan på enheten. Se till att etiketten syns tydligt. Ta bort eventuella klistermärken som finns på enheten från föregående leverans.

    ![Exempel på fraktsedel](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Redigera leveransadress

Du kan behöva ändra leveransadressen när beställningen har gjorts. Det här alternativet är endast tillgängligt innan enheten skickas. När enheten har skickats är det här alternativet inte längre tillgängligt.

Följ stegen nedan om du vill redigera ordern.

1. Gå till **Orderdetaljer > Redigera leveransadress**.

    ![Redigera leveransadress 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. Redigera och verifiera leveransadressen och spara sedan ändringarna.

    ![Redigera leveransadress 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Redigera meddelandeinformation

Du kan behöva ändra vilka användare som ska få e-postmeddelanden om orderstatusen. Exempelvis ska en användare kanske meddelas när enheten levereras eller hämtas. En annan användare kanske behöver informeras när datakopieringen är klar, så att han kan kontrollera att alla data finns i Azure Storage-kontot innan de raderas från källan. I dessa fall kan du redigera meddelandeinformationen.

Följ stegen nedan om du vill redigera meddelandeinformationen.

1. Gå till **Orderdetaljer > Redigera meddelandeinformation**.

    ![Redigera meddelandeinformation 1](media/data-box-portal-admin/edit-notification-details1.png)

2. Nu kan du redigera meddelandeinformationen och spara ändringarna.
 
    ![Redigera meddelandeinformation 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="view-order-status"></a>Visa orderstatus

När enhetens status ändras i portalen meddelas du via e-post.

|Orderstatus |Beskrivning |
|---------|---------|
|Beställt     | En beställning har gjorts. <br>Om enheten är tillgänglig identifierar Microsoft en enhet för leverans och förbereder enheten. <br> Om enheten inte är tillgänglig omedelbart bearbetas ordern när enheten blir tillgänglig. Ordern kan ta flera dagar till några månader att behandla. Om ordern inte kan uppfyllas inom 90 dagar annulleras den och du får ett meddelande.         |
|Bearbetad     | Orderbearbetningen har slutförts. Enligt din order förbereds enheten för leverans i datacentret.         |
|Skickat     | Beställningen har skickats. Använd det spårnings-ID som visas i din order i portalen för att spåra leveransen.        |
|Levererade     | Försändelsen har levererats till den adress som angetts i ordern.        |
|Hämtat     |Din returleverans har hämtats och genomsökts av transportören.         |
|Mottaget     | Enheten tas emot och genomsöks vid Azure-datacentret. <br> När leveransen kontrolleras startar enhetsöverföringen.      |
|Datakopiering     | Data kopieras. Spåra kopieringsprocessen för din order i Azure-portalen. <br> Vänta tills datakopieringen är klar. |
|Slutfört       |Ordern har slutförts.<br> Kontrollera att dina data finns i Azure innan du tar bort lokala data från servrarna.         |
|Slutfört med fel| Datakopieringen slutfördes men fel inträffade under kopieringen. <br> Granska kopieringsloggarna med hjälp av sökvägen som anges i Azure-portalen.   |
|Avbrutna            |Ordern har avbrutits. <br> Antingen annullerade du beställningen eller så påträffades ett fel som gjorde att tjänsten avbröt ordern. Om ordern inte kan uppfyllas inom 90 dagar annulleras den också och du får ett meddelande.     |
|Rensa | Data på enhetsdiskarna raderas. Enhetsrensningen anses slutförd när orderloggrapporten är tillgänglig i Azure-portalen.|



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker problem med Data Box](data-box-faq.md).
