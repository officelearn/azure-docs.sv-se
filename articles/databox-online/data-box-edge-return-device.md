---
title: Returnera eller ersätta din Azure Data Box Edge-enhet | Microsoft-dokument
description: Beskriver hur du returnerar eller ersätter Azure Data Box Edge-enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 521277b2eed7edfba016f6a80e8f877decfb0ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651108"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Returnera eller ersätta din Azure Data Box Edge-enhet

I den här artikeln beskrivs hur du rensar data och returnerar sedan din Azure Data Box Edge-enhet. När du har returnerat enheten kan du också ta bort resursen som är kopplad till enheten eller beställa en ersättningsenhet.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Rensa data från datadiskarna på enheten
> * Öppna en supportbiljett för att returnera din enhet
> * Packa enheten och schemalägg en upphämtning
> * Ta bort resursen i Azure Portal
> * Skaffa en ersättningsenhet

## <a name="erase-data-from-the-device"></a>Radera data från enheten

Om du vill rensa bort data från enhetens datadiskar måste du återställa enheten. Du kan återställa enheten med det lokala webbgränssnittet eller PowerShell-gränssnittet.

Innan du återställer skapar du en kopia av de lokala data på enheten om det behövs. Du kan kopiera data från enheten till en Azure Storage-behållare.

Så här återställer du enheten med det lokala webbgränssnittet.

1. Gå till **Underhåll > Enhetsåterställning i**det lokala webbgränssnittet .
2. Välj **Återställ enhet**.

    ![Återställa enhet](media/data-box-edge-return-device/device-reset-1.png)

3. När du uppmanas att bekräfta, granska varningen och välj **Ja** för att fortsätta.

    ![Bekräfta återställning](media/data-box-edge-return-device/device-reset-2.png)  

Återställningen raderar data från enhetens datadiskar. Beroende på mängden data på enheten tar den här processen cirka 30-40 minuter.

Du kan också ansluta till enhetens PowerShell-gränssnitt och använda cmdleten `Reset-HcsAppliance` för att radera data från datadiskarna. Mer information finns i [Återställa enheten](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Om du byter om eller uppgraderar till en ny enhet rekommenderar vi att du återställer enheten först när du har tagit emot den nya enheten.
> - Enhetsåterställningen tar bara bort alla lokala data från enheten. Data som finns i molnet tas inte bort och samlar in [avgifter](https://azure.microsoft.com/pricing/details/storage/). Dessa data måste tas bort separat med hjälp av ett molnlagringshanteringsverktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Öppna en supportbiljett

För att påbörja returprocessen, gör följande.

1. Öppna en supportbiljett med Microsoft Support som anger att du vill returnera enheten. Välj problemtyp som **Data Box Edge Hardware**.

    ![Öppna supportbegäran](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. En Microsoft-supporttekniker kontaktar dig. Ange leveransinformation.
3. Om du behöver en returbox kan du begära den. Svara **Ja** på frågan **Behöver du en tom ruta för att returnera**.


## <a name="schedule-a-pickup"></a>Schemalägg en upphämtning

1. Stäng av enheten. Gå till **Underhåll > Energiinställningar i**det lokala webbgränssnittet .
2. Välj **Stäng av**. När du uppmanas att bekräfta klickar du på **Ja** för att fortsätta. Mer information finns i [Hantera ström](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Koppla bort strömkablarna och ta bort alla nätverkskablar från enheten.
4. Förbered leveranspaketet med hjälp av din egen ruta eller den tomma rutan som du fick från Azure. Placera enheten och nätsladdarna som levererades med enheten i kartongen.
5. Fäst leveransetiketten som du fick från Azure på paketet.
6. Schemalägg en upphämtning med ditt regionala transportföretag. Om du returnerar enheten i USA kan din operatör vara UPS eller FedEx. Så här schemalägger du en upphämtning med UPS:

    1. Ring den lokala UPS (landsspecifika avgiftsfritt nummer).
    2. I samtalet anger du det omvända sändningsspårningsnumret som visas på den utskrivna etiketten.
    3. Om spårningsnumret inte anges kräver UPS att du betalar en extra avgift under upphämtningen.

    I stället för att schemalägga hämtningen kan du också släppa av Data Box Edge på närmaste avlämningsplats.

## <a name="delete-the-resource"></a>Ta bort resursen

När enheten har tagits emot på Azure-datacentret inspekteras enheten för skador eller tecken på manipulering.

- Om enheten anländer intakt och i god form stannar faktureringsmätaren för den resursen. Microsoft Support kontaktar dig för att bekräfta att enheten returnerades. Du kan sedan ta bort resursen som är associerad med enheten i Azure-portalen.
- Om enheten anländer avsevärt skadad, kan böter tillkomma. Mer information finns i [vanliga frågor om förlorad eller skadad enhet](https://azure.microsoft.com/pricing/details/databox/edge/) och produkt [användarvillkor](https://www.microsoft.com/licensing/product-licensing/products).  


Du kan ta bort enheten i Azure-portalen:
-   När du har gjort beställningen och innan enheten förbereds av Microsoft.
-   När du har returnerat enheten till Microsoft godkänns den fysiska inspektionen i Azure-datacentret och Microsoft Support-anrop för att bekräfta att enheten returnerades.

Om du har aktiverat enheten mot en annan prenumeration eller plats flyttar Microsoft din beställning till den nya prenumerationen eller platsen inom en arbetsdag. När ordern har flyttats kan du ta bort den här resursen.


Gör följande för att ta bort enheten och resursen i Azure-portalen.

1. Gå till din resurs i Azure-portalen och sedan till **Översikt**. Välj **Ta bort**i kommandofältet .

    ![Välj ta bort](media/data-box-edge-return-device/delete-resource-1.png)

2. Skriv namnet på den enhet som du vill ta bort i bladet **Ta bort enhet** och välj Ta **bort**.

    ![Bekräfta borttagning](media/data-box-edge-return-device/delete-resource-2.png)

Du meddelas när enheten och den associerade resursen har tagits bort.

## <a name="get-a-replacement-device"></a>Skaffa en ersättningsenhet

En ersättningsenhet behövs när den befintliga enheten har ett maskinvarufel eller behöver en uppgradering. Gör så här när enheten har ett maskinvaruproblem:

1. [Öppna en supportbiljett för maskinvaruproblem](#open-a-support-ticket). Microsoft Support kommer att fastställa att en ERSÄTTNINGsenhet för fält inte är tillgänglig för den här instansen eller så behöver enheten en maskinvaruuppgradering. I båda fallen beställer supporten en ersättningsenhet.
2. [Skapa en ny resurs](data-box-edge-deploy-prep.md#create-a-new-resource) för ersättningsenheten. Markera kryssrutan mot Jag **har en Data Box Edge-enhet**. 
3. När du har fått en ersättningsenhet [installerar](data-box-edge-deploy-install.md) och [aktiverar](data-box-edge-deploy-connect-setup-activate.md) du ersättningsenheten mot den nya resursen.
4. Följ alla steg för att returnera den ursprungliga enheten:
    1. Öppna en annan biljett för att returnera den ursprungliga enheten.
    2. [Radera data på enheten](#erase-data-from-the-device).
    3. [Schemalägg en upphämtning](#schedule-a-pickup).
    5. [Ta bort resursen som](#delete-the-resource) är associerad med den returnerade enheten.



## <a name="next-steps"></a>Nästa steg

- Lär dig att [hantera bandbredd](data-box-edge-manage-bandwidth-schedules.md).
