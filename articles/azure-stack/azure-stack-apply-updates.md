---
title: "Tillämpa uppdateringar i Azure-stacken | Microsoft Docs"
description: "Lär dig hur du importerar och installera Microsoft-uppdateringspaket för ett Azure-stacken integrerat system."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: twooley
ms.openlocfilehash: b00bd606faaffaad30ff6cea3bcf47dc85282f69
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
---
# <a name="apply-updates-in-azure-stack"></a>Tillämpa uppdateringar i Azure-stacken

*Gäller för: Azure Stack integrerat system*

Du kan använda Microsoft update-paket för Azure-stacken genom att använda uppdateringen panelen i administratörsportalen som operatör Azure stacken. Du måste ladda ned uppdateringspaketet Microsoft, importera paketfilerna till Azure-stacken och sedan installera uppdateringspaketet. 

## <a name="download-the-update-package"></a>Ladda ned uppdateringspaketet

När ett Microsoft-uppdateringspaket för Azure-stacken är tillgänglig kan hämta paketet till en plats som kan nås från Azure-stacken och granska paketinnehållet. Ett uppdateringspaket består normalt av följande filer:

- En självextraherande *PackageName*.exe-fil. Den här filen innehåller nyttolasten för uppdateringen, till exempel den senaste kumulativa uppdateringen för Windows Server.   
- Motsvarande *PackageName*.bin-filer. Dessa filer innehåller komprimering för nyttolasten som är associerad med den *PackageName*.exe-fil. 
- Någon Metadata.xml-fil. Den här filen innehåller viktig information om uppdateringen, till exempel utgivare, namn, nödvändiga, storlek och support-URL-sökväg.

## <a name="import-and-install-updates"></a>Importera och installera uppdateringar

Följande procedur visar hur du importerar och installera uppdateringspaket i administratörsportalen.

> [!IMPORTANT]
> Vi rekommenderar starkt att du meddela användare om eventuella underhållsåtgärder och att du schemalägger normal underhållsfönster under icke kontorstid så mycket som möjligt. Underhåll kan påverka både användaren arbetsbelastningar och portalen åtgärder.

1. Markera i administratörsportalen, **fler tjänster**. Sedan, under den **Data + lagring** kategori, Välj **lagringskonton**. (Eller i filterrutan börja skriva **lagringskonton**, och markera den.)

    ![Visar var du hittar storage-konton i portalen](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Skriv i filterrutan **uppdatera**, och välj den **updateadminaccount** storage-konto.

    ![Visar hur du söker efter updateadminaccount](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. I lagringen konto information under **Services**väljer **Blobbar**.
 
    ![Visar hur du kommer till BLOB storage-konto](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. Under **Blob-tjänst**väljer **+ behållare** att skapa en behållare. Ange ett namn (till exempel *uppdatering 1709*), och välj sedan **OK**.
 
     ![Visar hur du lägger till en behållare i storage-konto](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. När behållaren har skapats klickar du på behållarnamnet och klicka sedan på **överför** att överföra paketfilerna till behållaren.
 
    ![Visar hur du överför filerna i paketet](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. Under **överför blob**, klicka på mappikonen, bläddra till den uppdateringspaketet .exe-fil och klicka sedan på **öppna** filen i Utforskaren.
  
7. Under **överför blob**, klickar du på **överför**. 
 
    ![Visar var du ska överföra varje paketfil](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Upprepa steg 6 och 7 för de *PackageName*.bin och Metadata.xml-filer. 
9. När du är klar kan du granska meddelanden (i det övre högra hörnet av portal klockikonen). Meddelanden bör ange att överföringen är klar. 
10. Gå tillbaka till Update-ikonen på instrumentpanelen. Panelen ska ange att en uppdatering är tillgänglig. Klicka på panelen om du vill granska nyligen tillagda uppdateringspaketet.
11. Välj det paket som har markerats som om du vill installera uppdateringen **redo** och högerklicka på paketet och välj **Uppdatera nu**, eller klicka på den **Uppdatera nu** åtgärd längst upp .
12. När du klickar på installera uppdateringspaketet kan du visa status i den **uppdateringskörningen information** område. Härifrån kan du också klicka på **hämta fullständig loggarna** att hämta loggfilerna.
13. När uppdateringen är klar visas den uppdaterade versionen av Azure-stacken Update-ikonen.

## <a name="next-steps"></a>Nästa steg

- [Hantera uppdateringar i Azure Stack-översikt](azure-stack-updates.md)
- [Azure-stacken behandling av princip](azure-stack-servicing-policy.md)
