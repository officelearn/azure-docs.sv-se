---
title: Flytta Azure AD Connect-databasen från SQL Server Express till SQL Server. | Microsoft Docs
description: Det här dokumentet beskriver hur du flyttar Azure AD Connect-databasen från den lokala SQL Server Express-servern till en fjärransluten SQL Server.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ae0e87fddabee9f42cbb5506dce4cd7a5f4f082
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918842"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>Flytta Azure AD Connect-databasen från SQL Server Express till SQL Server 

Det här dokumentet beskriver hur du flyttar Azure AD Connect-databasen från den lokala SQL Server Express-servern till en fjärransluten SQL Server.  Du kan använda följande procedurer nedan för att utföra den här uppgiften.

## <a name="about-this-scenario"></a>Om det här scenariot
Följande är lite kortfattad information om det här scenariot.  I det här scenariot är Azure AD Connect-version (1.1.819.0) installerad på en enda domänkontrollant för Windows Server 2016.  Den använder den inbyggda SQL Server 2012 Express Edition för databasen.  Databasen kommer att flyttas till en server med SQL Server 2017.

![Scenariots arkitektur](media/how-to-connect-install-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>Flytta databasen Azure AD Connect
Använd följande steg för att flytta Azure AD Connect-databasen till en fjärransluten SQL Server.

1. På Azure AD Connect-servern går du till **Tjänster** och stoppar tjänsten **Microsoft Azure AD Sync**.
2. Leta upp mappen **%Program Files%\Microsoft Azure AD Sync/Data/** och kopiera filerna **ADSync.mdf** and **ADSync_log.ldf** till den fjärrstyrda SQL-servern.
3. Starta om **Microsoft Azure AD Sync** på Azure AD Connect-servern.
4. Avinstallera Azure AD Connect genom att gå till Kontrollpanelen – program – program och funktioner.  Välj Microsoft Azure AD Connect och klicka på Avinstallera längst upp.
5. Öppna SQL Server Management Studio på den fjärranslutna SQL-servern.
6. På databaser, högerklicka och välj Bifoga.
7. På skärmen **Bifoga databaser** klickar du på **Lägg till** och navigerar till filen ADSync.mdf.  Klicka på **OK**.
   ![koppla databasen](media/how-to-connect-install-move-db/move2.png)

8. När databasen är bifogad, gå tillbaka till Azure AD Connect-servern och installera Azure AD Connect.
9. När MSI-installationen är klar startar Azure AD Connect-guiden med installationsläget Express. Stäng fönstret genom att klicka på ikonen Avsluta.
   ![Välkommen](./media/how-to-connect-install-move-db/db1.png)
10. Starta en ny kommandotolk eller PowerShell-session. Navigera till mappen \<enhet > \Program\Microsoft Azure AD Connect. Kör kommandot .\AzureADConnect.exe /useexistingdatabase för att starta Azure AD Connect-guiden i läget ”Använd befintlig databas”.
    ![PowerShell](./media/how-to-connect-install-move-db/db2.png)
11. Du välkomnas med Välkommen till Azure AD Connect-skärmen. När du godkänt licensvillkoren och sekretesspolicyn klickar du på **Fortsätt**.
    ![Välkommen](./media/how-to-connect-install-move-db/db3.png)
12. På skärmen **Installera nödvändiga komponenter** är alternativet **Använd en befintlig SQL-server** aktiverat. Ange namnet på den SQL-server som är värd för ADSync-databasen. Om SQL-motorinstansen som används som värd för ADSync-databasen inte är standardinstansen på SQL-servern, måste du ange instansnamnet för SQL-motorn. Om SQL-surfning inte är aktiverat, måste du dessutom också ange portnumret för SQL-motorinstansen. Exempel:         
    ![Välkommen](./media/how-to-connect-install-move-db/db4.png)           

13. På skärmen **Anslut till Azure AD**, måste du ange autentiseringsuppgifter för en global administratör i Azure AD-katalogen. Det rekommenderas att använda ett konto i standarddomänen onmicrosoft.com. Det här kontot används bara för att skapa ett tjänstkonto i Azure AD och används inte när guiden har slutförts.
    ![Anslut](./media/how-to-connect-install-move-db/db5.png)
 
14. På skärmen **Anslut dina kataloger** har den befintliga AD-skog som konfigurerats för katalogsynkronisering angetts med en röd kryssikon bredvid sig. Om du vill synkronisera ändringar från en lokal AD-skog krävs ett AD DS-konto. Azure AD Connect-guiden kan inte hämta autentiseringsuppgifter för det AD DS-konto som lagrats i ADSync-databasen eftersom autentiseringsuppgifterna krypterats och endast kan dekrypteras av den tidigare Azure AD Connect-servern. Klicka på **Ändra autentiseringsuppgifter** för att ange AD DS-kontot för AD-skogen.
    ![Kataloger](./media/how-to-connect-install-move-db/db6.png)
 
 
15. I popup-fönstret kan du antingen (i) ange autentiseringsuppgifter för en företagsadministratör och låta Azure AD Connect skapa AD DS-kontot åt dig, eller (ii) skapa AD DS-kontot och ange dess autentiseringsuppgifter till Azure AD Connect. När du har valt ett alternativ och angett nödvändiga autentiseringsuppgifter klickar du på **OK** för att stänga popup-fönstret.
    ![Välkommen](./media/how-to-connect-install-move-db/db7.png)
 
 
16. När autentiseringsuppgifterna har angetts ersätts den röda kryssikonen med en grön bockikon. Klicka på **Nästa**.
    ![Välkommen](./media/how-to-connect-install-move-db/db8.png)
 
 
17. Klicka på **Installera** på skärmen **Klart att konfigurera**.
    ![Välkommen](./media/how-to-connect-install-move-db/db9.png)
 
 
18. När installationen är klar aktiveras automatiskt Azure AD Connect-servern för mellanlagringsläge. Vi rekommenderar att du läser igenom serverkonfigurationen och väntande exporter för oväntade ändringar innan du inaktiverar mellanlagringsläget. 

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
- [Installera Azure AD Connect med en befintlig ADSync-databas](how-to-connect-install-existing-database.md)
- [Installerar Azure AD Connect med SQL-delegerade administratörsbehörigheter](how-to-connect-install-sql-delegation.md)

