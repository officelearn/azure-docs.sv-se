---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59805208"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Uppgradera SharePoint 2010 till SharePoint 2013 och sedan installerar StorSomple Adapter för SharePoint
> [!IMPORTANT]
> Alla filer som tidigare har flyttats till extern lagring via RBS kan inte tillgängliga förrän uppgraderingen är klar och funktionen RBS aktiveras igen. Utföra en uppgradering eller ominstallation ett planerat underhållsfönster för att begränsa användarna påverkas.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Uppgradera SharePoint 2010 till SharePoint 2013 och sedan installera adaptern
1. Observera BLOB store-sökväg för externalized Blobar och innehållsdatabaserna som RBS är aktiverat i SharePoint 2010-servergruppen. 
2. Installera och konfigurera den nya SharePoint 2013-servergruppen. 
3. Flytta databaser, program och webbplatssamlingar från SharePoint 2010-servergruppen till den nya SharePoint 2013-servergruppen. Mer information går du till [översikt över uppgraderingen till SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Installera StorSimple Adapter för SharePoint på den nya gruppen. Gå till [installera StorSimple Adapter för SharePoint](#install-the-storsimple-adapter-for-sharepoint) tillvägagångssätt.
5. Med hjälp av informationen som du antecknade i steg 1, aktivera RBS för samma uppsättning innehållsdatabaser och ange samma BLOB store-sökväg som användes i SharePoint 2010-installation. Gå till [konfigurera RBS](#configure-rbs) tillvägagångssätt. När du har slutfört det här steget ska tidigare externalized filer vara tillgänglig från den nya gruppen. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Uppgradera StorSimple Adapter för SharePoint
> [!IMPORTANT]
> Du bör schemalägga den här uppgraderingen ska ske under ett planerat underhåll av följande skäl:
> 
> * Tidigare är externalized innehåll inte tillgängliga förrän adaptern har installerats om.
> * Allt innehåll som överförs till platsen när du har avinstallerat den tidigare versionen av StorSimple Adapter för SharePoint, men innan du installerar den nya versionen, kommer att lagras i innehållsdatabasen. Du behöver att flytta innehållet till StorSimple-enheten när du installerar det nya kortet. Du kan använda Microsofts `RBS Migrate()` PowerShell-cmdlet som ingår i SharePoint för att migrera om innehållet. Mer information finns i [migrerar du innehåll till eller från RBS](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Så här uppgraderar du StorSimple Adapter för SharePoint
1. Avinstallera den tidigare versionen av StorSimple Adapter för SharePoint.
   
   > [!NOTE]
   > Detta kommer automatiskt att inaktivera RBS på innehållsdatabaserna. Befintliga Blobar finns dock kvar på StorSimple-enheten. Eftersom RBS inaktiveras och Blobarna som inte har migrerats till innehållsdatabaser, misslyckas alla begäranden för dessa Blobar. 
   > 
   > 
2. Installera nya StorSimple Adapter för SharePoint. Det nya kortet att automatiskt identifiera innehållsdatabaser som tidigare har aktiverat eller inaktiverat för RBS och använder de tidigare inställningarna.

