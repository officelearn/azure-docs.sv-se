---
title: Tillämpa uppdateringar i Azure Stack | Microsoft Docs
description: Lär dig hur du importerar och installera Microsoft-uppdateringspaket för ett integrerat Azure Stack-system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 0c3f52c78bbfd3094324b74f3b66610fcebfa2f4
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099300"
---
# <a name="apply-updates-in-azure-stack"></a>Tillämpa uppdateringar i Azure Stack

*Gäller för: Integrerade Azure Stack-system*

Du kan använda den **uppdatera** panelen i administrationsportalen för att tillämpa Microsoft- eller OEM-uppdateringspaket för Azure Stack.

Om du använder en integrerade system version 1807 eller tidigare, måste du ladda ned uppdateringspaketet, importera paketfilerna till Azure Stack och sedan installera uppdateringspaketet. Anvisningar finns i [Update Azure Stack genom att ladda ned paketet](#update-azure-stack-by-downloading-the-package)

Dessa uppgradera instruktioner fungerar med integrerade Azure Stack-systemen. Om du använder Azure Stack Development System, måste du hämta installationspaketet för den aktuella versionen. Anvisningar finns i [installera Azure Stack Development Kit](.\asdk\asdk-install.md)

## <a name="update-azure-stack"></a>Uppdatera Azure Stack

### <a name="select-and-apply-an-update-package"></a>Välj och tillämpa ett uppdateringspaket

1. Öppna administrationsportalen.

2. Välj **instrumentpanelen**. Välj den **uppdatering** panelen.

    ![Azure Stack-uppdatering tillgänglig](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. Anteckna den aktuella versionen av Azure Stack. Du kan uppdatera till den nästa fullständiga versionen. Till exempel om du kör Azure Stack 1811 nästa publicerat version är 1901.

    ![Använd Azure Stack-uppdatering](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. Välj den nästa tillgängliga versionen i listan med uppdateringar. Du kan välja **visa** i version anmärkningar för att öppna avsnittet viktig information för versionen om vill granska versionsändringarna.

5. Välj uppdatering nu. Uppdateringen startar.

### <a name="review-update-history"></a>Granska uppdateringshistorik

1. Öppna administrationsportalen.

2. Välj **instrumentpanelen**. Välj den **uppdatering** panelen.

3. Välj **uppdateringshistorik**.

![Uppdateringshistorik för Azure Stack](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>Uppdatera Azure Stack genom att ladda ned paketet

Om du använder en integrerade system version 1807 eller tidigare, måste du ladda ned uppdateringspaketet, importera paketfilerna till Azure Stack och sedan installera uppdateringspaketet.

## <a name="download-the-update-package"></a>Ladda ned uppdateringspaketet

När ett Microsoft- eller OEM-uppdateringspaket för Azure Stack är tillgänglig, ladda ned paketet till en plats som kan nås från Azure Stack och granska paketinnehållet. Ett uppdateringspaket som vanligtvis består av följande filer:

- En självextraherande `<PackageName>.exe` fil. Den här filen innehåller nyttolasten för uppdateringen, till exempel den senaste kumulativa uppdateringen för Windows Server.

- Motsvarande `<PackageName>.bin` filer. Dessa filer innehåller komprimering för nyttolasten som är associerad med den *PackageName*.exe-fil.

- En `Metadata.xml` fil. Den här filen innehåller viktig information om uppdateringen, till exempel utgivare, namn, krav, storlek och support sökväg URL.

> [!IMPORTANT]  
> När uppdateringspaketet Azure Stack 1901 används, flyttas förpackningsformat för Azure Stack uppdatering pacakges från .exe, .bin(s), och XML-format till en .zip(s) och XML-format. Azure Stack-operatörer som har anslutit stämplar påverkas inte. Azure Stack-operatörer som inte är anslutna kommer helt enkelt importera XML och ZIP-filer genom att använda samma process som beskrivs nedan.

## <a name="import-and-install-updates"></a>Importera och installera uppdateringar

Följande procedur visar hur du importerar och installera uppdateringspaket i administratörsportalen.

> [!IMPORTANT]  
> Vi rekommenderar starkt att du meddela användare om eventuella underhållsåtgärder, och att du schemalägger normala underhållsfönster under tider utanför kontorstid så mycket som möjligt. Underhåll kan påverka både användaren arbetsbelastningar och åtgärder.

1. I administratörsportalen kan väljer **alla tjänster**. Sedan, under den **DATA + lagring** kategori, väljer **lagringskonton**. (Eller börja skriva i filterrutan **lagringskonton**, och välj det.)

    ![Visar var du hittar storage-konton i portalen](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Skriv i rutan filtrera **uppdatera**, och välj den **updateadminaccount** storage-konto.

3. I storage-konto information under **Services**väljer **Blobar**.
 
    ![Visar hur du kommer till BLOB-objekt för storage-konto](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. Under **Blobtjänst**väljer **+ behållare** att skapa en behållare. Ange ett namn (till exempel *uppdatering 1811*), och välj sedan **OK**.
 
     ![Visar hur du lägger till en behållare i lagringskontot](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. När behållaren har skapats klickar du på behållarens namn och klicka sedan på **överför** att ladda upp paketfilerna till behållaren.
 
    ![Visar hur du överför paketfilerna](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. Under **ladda upp blob**, klicka på mappikonen, bläddra till den uppdateringspaketet .exe-fil och klicka sedan på **öppna** filen i Utforskaren.
  
7. Under **ladda upp blob**, klickar du på **överför**.
  
    ![Visar var du vill ladda upp varje fil för registreringspaketet](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Upprepa steg 6 och 7 för de *PackageName*.bin och Metadata.xml-filer. Importera inte filen kompletterande Notice.txt om ingår.
9. När du är klar kan du granska meddelanden (klockikonen i det övre högra hörnet i portalen). Meddelanden ska indikera att överföringen är klar.
10. Gå tillbaka till Update-panelen på instrumentpanelen. Panelen ska indikera att en uppdatering är tillgänglig. Klicka på panelen om du vill granska nyligen tillagda uppdateringspaketet.
11. Om du vill installera uppdateringen, Välj det paket som har markerats som **redo** och antingen högerklicka på paketet och välj **Uppdatera nu**, eller klicka på den **Uppdatera nu** åtgärd i den övre delen .
12. När du klickar på installera uppdateringspaketet du kan visa statusen i den **uppdatering körningsinformation** området. Härifrån kan du också klicka på **fullständig Hämtningsloggar** att ladda ned filerna.
13. När uppdateringen är klar visas panelen Update den uppdaterade versionen av Azure Stack.

Du kan manuellt ta bort uppdateringar från storage-konto när de har installerats på Azure Stack. Azure Stack med jämna mellanrum söker efter äldre uppdateringspaket och tar bort dem från lagring. Det kan ta Azure Stack två veckor för att ta bort de gamla paket.

## <a name="next-steps"></a>Nästa steg

- [Hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md)
- [Azure Stack som hanteringsprincip](azure-stack-servicing-policy.md)
