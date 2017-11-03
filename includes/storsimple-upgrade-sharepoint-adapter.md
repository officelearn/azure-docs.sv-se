<!--author=SharS last changed: 9/17/15-->

### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Uppgradera SharePoint 2010 SharePoint 2013 och installera sedan StorSomple-Adapter för SharePoint
> [!IMPORTANT]
> Alla filer som tidigare har flyttats till extern lagring via RBS blir inte tillgängliga förrän uppgraderingen är klar och RBS funktionen aktiveras igen. Om du vill begränsa effekten av användaren utför en uppgradering eller ominstallation under ett planerat underhåll.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Uppgradera SharePoint 2010 till SharePoint 2013 och sedan installera kortet
1. Anteckna BLOB store sökvägen för externalized Blobbar och innehållsdatabaserna som RBS är aktiverad i SharePoint 2010-servergruppen. 
2. Installera och konfigurera nya SharePoint 2013-grupp. 
3. Flytta databaser, program och webbplatssamlingar från SharePoint 2010-servergruppen till den nya SharePoint 2013-gruppen. Mer information går du till [översikt över uppgraderingen till SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Installera StorSimple-kortet för SharePoint på den nya gruppen. Gå till [installerar StorSimple-kort för SharePoint](#install-the-storsimple-adapter-for-sharepoint) för procedurer.
5. Med hjälp av information som du antecknade i steg 1, aktivera RBS för samma uppsättning innehållsdatabaser och ger samma BLOB store-sökväg som användes i SharePoint 2010-installationen. Gå till [konfigurera RBS](#configure-rbs) för procedurer. När du har slutfört det här steget ska tidigare externalized filer vara tillgänglig från den nya gruppen. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Uppgradera StorSimple-kortet för SharePoint
> [!IMPORTANT]
> Du bör planera uppgraderingen ska ske under planerat underhåll av följande skäl:
> 
> * Tidigare blir externalized innehåll inte tillgängliga förrän kortet installeras.
> * Allt innehåll som överförs till platsen när du avinstallerar den tidigare versionen av StorSimple-kortet för SharePoint, men innan du installerar den nya versionen kommer att lagras i innehållsdatabasen. Du behöver flytta innehållet till StorSimple-enhet när du installerar det nya kortet. Du kan använda Microsoft` RBS Migrate()` PowerShell-cmdlet som ingår i SharePoint för att överföra innehållet. Mer information finns i [migrerar innehåll till eller från RBS](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Så här uppgraderar du StorSimple-kortet för SharePoint
1. Avinstallera den tidigare versionen av StorSimple-kortet för SharePoint.
   
   > [!NOTE]
   > Detta inaktiverar automatiskt RBS på innehållsdatabaserna. Befintlig BLOB förblir dock på StorSimple-enheten. Eftersom RBS är inaktiverad och Blobar inte har migrerats till innehållsdatabaser, misslyckas alla förfrågningar för dessa BLOB. 
   > 
   > 
2. Installera nya StorSimple-kortet för SharePoint. Det nya kortet identifieras innehållsdatabaser som tidigare aktiveras eller inaktiveras för RBS automatiskt och använder de tidigare inställningarna.

