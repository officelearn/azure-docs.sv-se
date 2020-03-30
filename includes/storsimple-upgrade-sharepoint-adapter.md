---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187386"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Uppgradera SharePoint 2010 till SharePoint 2013 och installera sedan StorSomple-kortet för SharePoint
> [!IMPORTANT]
> Filer som tidigare flyttats till extern lagring via RBS kommer inte att vara tillgängliga förrän uppgraderingen är klar och RBS-funktionen är aktiverad igen. Om du vill begränsa användarnas påverkan utför du en uppgradering eller ominstallation under ett planerat underhållsfönster.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Så här uppgraderar du SharePoint 2010 till SharePoint 2013 och installerar sedan kortet
1. I SharePoint 2010-servergruppen noterar du BLOB-arkivet för de externa BLOB:erna och de innehållsdatabaser som RBS är aktiverat för. 
2. Installera och konfigurera den nya SharePoint 2013-servergruppen. 
3. Flytta databaser, program och webbplatssamlingar från SharePoint 2010-servergruppen till den nya SharePoint 2013-servergruppen. Instruktioner finns i [Översikt över uppgraderingsprocessen till SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Installera StorSimple-kortet för SharePoint på den nya servergruppen. Gå till [Installera StorSimple-kortet för SharePoint](#install-the-storsimple-adapter-for-sharepoint) för procedurer.
5. Med hjälp av den information som du noterade i steg 1 aktiverar du RBS för samma uppsättning innehållsdatabaser och tillhandahåller samma sökväg för BLOB-arkiv som användes i SharePoint 2010-installationen. Gå till [Konfigurera RBS](#configure-rbs) för procedurer. När du har slutfört det här steget bör tidigare externa filer vara tillgängliga från den nya servergruppen. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Uppgradera StorSimple-kortet för SharePoint
> [!IMPORTANT]
> Du bör schemalägga den här uppgraderingen så att den sker under ett planerat underhållsfönster av följande skäl:
> 
> * Tidigare externt innehåll kommer inte att vara tillgängligt förrän kortet installeras om.
> * Allt innehåll som laddas upp till webbplatsen efter att du har avinstallerat den tidigare versionen av StorSimple-kortet för SharePoint, men innan du installerar den nya versionen, lagras i innehållsdatabasen. Du måste flytta innehållet till StorSimple-enheten när du har installerat det nya kortet. Du kan använda `RBS Migrate()` den Microsoft PowerShell-cmdlet som medföljer SharePoint för att migrera innehållet. Mer information finns i [Migrera innehåll till eller från RBS](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Så här uppgraderar du StorSimple-kortet för SharePoint
1. Avinstallera den tidigare versionen av StorSimple-kortet för SharePoint.
   
   > [!NOTE]
   > Detta inaktiverar automatiskt RBS i innehållsdatabaserna. Befintliga BLOBs kommer dock att finnas kvar på StorSimple-enheten. Eftersom RBS är inaktiverat och BLOB:erna inte har migrerats tillbaka till innehållsdatabaserna misslyckas alla begäranden för dessa BLOB:er. 
   > 
   > 
2. Installera det nya StorSimple-kortet för SharePoint. Det nya kortet känner automatiskt igen de innehållsdatabaser som tidigare aktiverats eller inaktiverats för RBS och använder de tidigare inställningarna.

