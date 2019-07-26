---
title: Returnera eller Ersätt Azure Data Box Edge-enheten | Microsoft Docs
description: Beskriver hur du returnerar eller ersätter den Azure Data Box Edge enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/19/2019
ms.author: alkohli
ms.openlocfilehash: a5fee604a529e9ca6153f6c189f199577ae65426
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356160"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Returnera eller Ersätt din Azure Data Box Edge-enhet

I den här artikeln beskrivs hur du rensar data och sedan returnerar Azure Data Box Edge enheten. När du har returnerat enheten kan du också ta bort resursen som är kopplad till enheten eller beställa en ersättnings enhet.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Rensa data från data diskarna på enheten
> * Öppna ett support ärende för att returnera din enhet
> * Packa upp enheten och Schemalägg en upphämtning
> * Ta bort resursen i Azure Portal
> * Skaffa en ersättnings enhet

## <a name="erase-data-from-the-device"></a>Radera data från enheten

Om du vill rensa bort data från enhetens data diskar måste du återställa enheten. Du kan återställa enheten med hjälp av det lokala webb gränssnittet eller PowerShell-gränssnittet.

Innan du återställer skapar du en kopia av lokala data på enheten om det behövs. Du kan kopiera data från enheten till en Azure Storage behållare.

Utför följande steg för att återställa enheten med det lokala webb gränssnittet.

1. I det lokala webb gränssnittet går du till **underhåll > enhets återställning**.
2. Välj **Återställ enhet**.

    ![Återställ enhet](media/data-box-edge-return-device/device-reset-1.png)

3. När du uppmanas att bekräfta kontrollerar du varningen och väljer **Ja** för att fortsätta.

    ![Bekräfta återställning](media/data-box-edge-return-device/device-reset-2.png)  

Vid återställning raderas data från enhetens data diskar. Den här processen tar cirka 30-40 minuter beroende på hur mycket data som finns på enheten.

Du kan också ansluta till PowerShell-gränssnittet på enheten och använda `Reset-HcsAppliance` cmdleten för att radera data från data diskarna. Mer information finns i [återställa din enhet](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Om du utbyter eller uppgraderar till en ny enhet rekommenderar vi att du återställer enheten först när du har fått den nya enheten.
> - Enhets återställningen tar bara bort alla lokala data från enheten. De data som finns i molnet tas inte bort och [kostnader](https://azure.microsoft.com/pricing/details/storage/)samlas in. Dessa data måste tas bort separat med ett hanterings verktyg för moln lagring som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Öppna ett support ärende

Utför följande steg för att påbörja retur processen.

1. Öppna ett support ärende med Microsoft Support som anger att du vill returnera enheten. Välj problem typen som **data Box Edge maskin vara**.

    ![Öppna support ärende](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. En Microsoft Support-tekniker kommer att kontakta dig. Ange leverans information.
3. Om du behöver en retur frakt Box kan du begära den. Svara **Ja** på frågan **behöver en tom ruta för att returnera**.


## <a name="schedule-a-pickup"></a>Schemalägg hämtning

1. Stäng av enheten. I det lokala webb gränssnittet går du till **underhåll > energi inställningar**.
2. Välj **Stäng av**. När du uppmanas att bekräfta klickar du på **Ja** för att fortsätta. Mer information finns i [hantera energi](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Koppla bort ström kablar och ta bort alla nätverks kablar från enheten.
4. Förbered försändelse paketet genom att använda din egen ruta eller den tomma box som du fick från Azure. Placera enheten och de ström sladdar som levererades med enheten i rutan.
5. Fäst frakt sedeln som du fick från Azure på paketet.
6. Schemalägg en upphämtning med din regionala operatör. Om du returnerar enheten i USA är operatören UPS-enhet. Så här schemalägger du en upphämtning:

    1. Ring den lokala UPS-enheten (landsspecifika avgiftsfritt nummer).
    2. I ditt samtal offerterar du det omvända försändelse spårnings numret som det visas på den utskrivna etiketten.
    3. Om spårnings numret inte är citerat, kräver UPS att du betalar ytterligare en kostnad under upphämtningen.

    I stället för att schemalägga upphämtningen kan du också ta bort Data Box Edge på den närmaste List rutan.

## <a name="delete-the-resource"></a>Ta bort resursen

När enheten har tagits emot i Azure-datacentret, inspekteras enheten efter skada eller tecken på manipulation.

- Om enheten kommer intakt och i en stark form stoppas fakturerings mätaren för resursen. Microsoft Support kommer att kontakta dig för att bekräfta att enheten returnerades. Du kan sedan ta bort resursen som är kopplad till enheten i Azure Portal.
- Om enheten kommer kraftigt skadas kan böter gälla. Mer information finns i [vanliga frågor och svar om förlorade eller skadade enhets](https://azure.microsoft.com/pricing/details/databox/edge/) [-och produkt villkor](https://www.microsoft.com/licensing/product-licensing/products).  


Du kan ta bort enheten i Azure Portal:
-   När du har placerat beställningen och innan enheten förbereds av Microsoft.
-   När du har returnerat enheten till Microsoft, skickar den fysisk inspektion i Azure-datacentret och Microsoft Support anrop för att bekräfta att enheten har returnerats.

Om du har aktiverat enheten mot en annan prenumeration eller plats flyttar Microsoft din beställning till den nya prenumerationen eller platsen inom en arbets dag. När ordningen har flyttats kan du ta bort den här resursen.


Utför följande steg för att ta bort enheten och resursen i Azure Portal.

1. I Azure Portal går du till din resurs och sedan till **Översikt**. I kommando fältet väljer du **ta bort**.

    ![Välj Ta bort](media/data-box-edge-return-device/delete-resource-1.png)

2. I bladet **ta bort enhet** skriver du namnet på den enhet som du vill ta bort och väljer **ta bort**.

    ![Bekräfta borttagning](media/data-box-edge-return-device/delete-resource-2.png)

Du får ett meddelande när enheten och den tillhör ande resursen har tagits bort.

## <a name="get-a-replacement-device"></a>Skaffa en ersättnings enhet

En ersättnings enhet krävs när den befintliga enheten har ett maskin varu problem eller behöver uppgraderas. Utför följande steg när det finns ett maskin varu problem i enheten:

1. [Öppna ett support ärende för maskin varu problem](#open-a-support-ticket). Microsoft Support kommer att avgöra om en FRU-enhet (Field Replacement Unit) inte är tillgänglig för den här instansen eller om enheten behöver en maskin varu uppgradering. I båda fallen beställer support en ersättnings enhet.
2. [Skapa en ny resurs](data-box-edge-deploy-prep.md#create-a-new-resource) för ersättnings enheten. Se till att markera kryss rutan mot att **Jag har en data Box Edge enhet**. 
3. När du har fått en ersättnings enhet [installerar](data-box-edge-deploy-install.md) och [aktiverar](data-box-edge-deploy-connect-setup-activate.md) du ersättnings enheten mot den nya resursen.
4. Följ alla steg för att returnera den ursprungliga enheten:
    1. Öppna en annan biljett för att returnera den ursprungliga enheten.
    2. [Radera data på enheten](#erase-data-from-the-device).
    3. [Schemalägg hämtningen](#schedule-a-pickup).
    5. [Ta bort resursen](#delete-the-resource) som är kopplad till den returnerade enheten.



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar bandbredd](data-box-edge-manage-bandwidth-schedules.md).
