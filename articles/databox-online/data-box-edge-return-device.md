---
title: Returnera din Azure Data Box Edge-enhet | Microsoft Docs
description: Beskriver hur du returnerar Azure Data Box Edge-enhet och ta bort beställningen för enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468611"
---
# <a name="return-your-azure-data-box-edge-device"></a>Returnera din Azure Data Box Edge-enhet

Den här artikeln beskriver hur du rensar data och returnerar sedan din Azure Data Box Edge-enhet. När du har kommit tillbaka enheten, kan du också ta bort resursen som hör till enheten.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Rensa data från datadiskarna på enheten
> * Öppna ett supportärende för att returnera din enhet
> * Packa upp enheten och schemalägga en upphämtning
> * Ta bort resursen i Azure-portalen

## <a name="erase-data-from-the-device"></a>Radera data från enheten

Om du vill rensa data från datadiskarna för din enhet, måste du återställa din enhet. Du kan återställa enheten med hjälp av det lokala webbgränssnittet eller PowerShell-gränssnittet.

Innan du återställer måste du skapa en kopia av lokala data på enheten om det behövs. Du kan kopiera data från enheten till en Azure Storage-behållare.

Gör följande om du vill återställa enheten med det lokala webbgränssnittet.

1. I det lokala webbgränssnittet går du till **Underhåll > enheten återställa**.
2. Välj **Återställ enhet**.

    ![Återställ enhet](media/data-box-edge-return-device/device-reset-1.png)

3. När du uppmanas att bekräfta granska varningen och välj **Ja** att fortsätta.

    ![Bekräfta återställning](media/data-box-edge-return-device/device-reset-2.png)  

Återställningen Raderar data från enheten datadiskarna. Den här processen tar cirka 30 – 40 minuter beroende på mängden data på enheten.

Du kan också ansluta till PowerShell-gränssnittet för enheten och använder den `Reset-HcsAppliance` cmdlet för att radera data från datadiskarna. Mer information finns i [återställer enheten](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Om du byta ut eller uppgradera till en ny enhet, rekommenderar vi att du återställer en enhet förrän du har fått den nya enheten.
> - Enheten återställa endast tar bort alla lokala data från enheten. De data som finns i molnet tas inte bort och samlar in [avgifter](https://azure.microsoft.com/pricing/details/storage/). Dessa data måste tas bort separat med hjälp av ett cloud storage hanteringsverktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Öppna ett supportärende

Vidta följande steg för att påbörja processen.

1. Öppna ett supportärende med Microsoft Support som anger att du vill gå tillbaka enheten. Välj problemtypen som **Data Box Edge maskinvara**.

    ![Öppna supportärende](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. En Microsoft Support-tekniker att kontakta dig. Ange leveransinformationen.
3. Om du behöver en return endash där kan du begära den. Svar **Ja** på frågan **behöver en tom ruta att returnera**.


## <a name="schedule-a-pickup"></a>Schemalägga en upphämtning

1. Stäng av enheten. I det lokala webbgränssnittet går du till **Underhåll > energiinställningar**.
2. Välj **stänga**. När du uppmanas att bekräfta klickar du på **Ja** att fortsätta. Mer information finns i [hantera power](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Koppla från strömkablarna och ta bort alla nätverkskablarna från enheten.
4. Förbereda leveransen paketet genom att använda din egen box-enhet eller den tomma rutan som du fick från Azure. Placera enheten och strömkablar som levererades med enheten i rutan.
5. Fästa adressetikett som du fick från Azure-paketet.
6. Schemalägga en upphämtning med din regionala operatör. Om returnera enheten i USA, är din operatör UPS. Så här schemalägger en hämtning:

    1. Anropa den lokala UPS (landsspecifika kostnadsfritt nummer).
    2. Citera omvänd leveransen spårnings-ID som visas på din utskrivna etikett i dina anrop.
    3. Om Spårningsnumret inte anges kräver UPS att betala en extra avgift under hämtning.

    I stället för schemaläggning för upphämtningen kan du också släppa utanför rutan Data i närmaste samlingsplats.

## <a name="delete-the-resource"></a>Ta bort resursen

När enheten har tagits emot vid Azure-datacentret, kontrolleras enheten för skador eller några tecken på manipulation.

- Om enheten anländer intakta och leveransprocessen stoppas faktureringsmätaren för den resursen. Microsoft Support kontaktar dig för att bekräfta att enheten har returnerats. Du kan sedan ta bort resursen som hör till enheten i Azure-portalen.
- Om enheten anländer avsevärt skadad kan böter tillkomma. Mer information finns i [vanliga frågor och svar på förlorad eller skadad enhet](https://azure.microsoft.com/pricing/details/databox/edge/) och [produkten användarvillkoren](https://www.microsoft.com/licensing/product-licensing/products).  


Du kan ta bort enheten i Azure portal:
-   När du har placerat ordningen och innan enheten förbereds av Microsoft.
-   När du har kommit tillbaka enheten till Microsoft, överförs de fysiska kontrollerna vid Azure-datacentret och Microsoft Support ringer för att bekräfta att enheten har returnerats.

Om du har aktiverat enheten mot en annan prenumeration eller plats, flyttar Microsoft din beställning till ny prenumeration eller plats inom en arbetsdag. När ordningen flyttas kan du ta bort den här resursen.


Vidta följande steg för att ta bort enheten och resursen i Azure-portalen.

1. I Azure-portalen går du till resursen och sedan till **översikt**. Välj kommandofältet **ta bort**.

    ![Välj Ta bort](media/data-box-edge-return-device/delete-resource-1.png)

2. I den **ta bort enhet** bladet anger du namnet på den enhet du vill ta bort och välj **ta bort**.

    ![Bekräfta borttagning](media/data-box-edge-return-device/delete-resource-2.png)

Du meddelas när enheten och den associerade resursen har tagits bort.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar bandbredd](data-box-edge-manage-bandwidth-schedules.md).
