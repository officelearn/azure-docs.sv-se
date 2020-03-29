---
title: Installera Azure AD Connect med hjälp av en befintlig ADSync-databas | Microsoft-dokument
description: I det här avsnittet beskrivs hur du använder en befintlig ADSync-databas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dc6993586063c9c99a287c51d799b44f921768d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245193"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Installera Azure AD Connect med en befintlig ADSync-databas
Azure AD Connect kräver en SQL Server-databas för att lagra data. Du kan antingen använda standard-SQL Server 2012 Express LocalDB som är installerad med Azure AD Connect eller använda din egen fullständiga version av SQL. Tidigare, när du installerade Azure AD Connect, skapades alltid en ny databas med namnet ADSync. Med Azure AD Connect version 1.1.613.0 (eller efter) har du möjlighet att installera Azure AD Connect genom att peka den på en befintlig ADSync-databas.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Fördelar med att använda en befintlig ADSync-databas
Genom att peka på en befintlig ADSync-databas:

- Med undantag för autentiseringsuppgifter, återställs och används synkroniseringskonfiguration som lagras i ADSync-databasen (inklusive anpassade synkroniseringsregler, kopplingar, filtrering och valfri funktioners konfiguration) automatiskt under installationen . Autentiseringsuppgifter som används av Azure AD Connect för att synkronisera ändringar med lokala AD och Azure AD krypteras och kan endast nås av den tidigare Azure AD Connect-servern.
- Alla identitetsdata (som är associerade med anslutningsutrymmen och metaversum) och synkroniseringscookies som lagras i ADSync-databasen återställs också. Den nyligen installerade Azure AD Connect-servern kan fortsätta att synkronisera från där den tidigare Azure AD Connect-servern slutade, i stället för att behöva utföra en fullständig synkronisering.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scenarier där det är fördelaktigt att använda en befintlig ADSync-databas
Dessa fördelar är användbara i följande scenarier:


- Du har en befintlig Azure AD Connect-distribution. Din befintliga Azure AD Connect-server fungerar inte längre, men SQL-servern som innehåller ADSync-databasen fungerar fortfarande. Du kan installera en ny Azure AD Connect-server och peka den på den befintliga ADSync-databasen. 
- Du har en befintlig Azure AD Connect-distribution. SQL-servern som innehåller ADSync-databasen fungerar inte längre. Du har dock en säkerhetskopierad databas nyligen. Du kan återställa ADSync-databasen till en ny SQL-server först. Därefter kan du installera en ny Azure AD Connect-server och peka den på den återställda ADSync-databasen.
- Du har en befintlig Azure AD Connect-distribution som använder LocalDB. På grund av den gräns på 10 GB som anges av LocalDB vill du migrera till full SQL. Du kan säkerhetskopiera ADSync-databasen från LocalDB och återställa den till en SQL-server. Därefter kan du installera om en ny Azure AD Connect-server och peka den på den återställda ADSync-databasen.
- Du försöker konfigurera en mellanlagringsserver och vill se till att konfigurationen matchar konfigurationen för den aktuella aktiva servern. Du kan säkerhetskopiera ADSync-databasen och återställa den till en annan SQL-server. Därefter kan du installera om en ny Azure AD Connect-server och peka den på den återställda ADSync-databasen.

## <a name="prerequisite-information"></a>Nödvändig information

Viktiga anteckningar att ta del av innan du fortsätter:

- Se till att granska förutsättningarna för installation av Azure AD Connect vid maskinvara och förutsättningar samt konto och behörigheter som krävs för att installera Azure AD Connect. De behörigheter som krävs för att installera Azure AD Connect med "använd befintlig databas"-läge är samma som "anpassad" installation.
- Distribuera Azure AD Connect mot en befintlig ADSync-databas stöds endast med fullständig SQL. Det stöds inte med SQL Express LocalDB. Om du har en befintlig ADSync-databas i LocalDB som du vill använda måste du först säkerhetskopiera ADSync-databasen (LocalDB) och återställa den till full SQL. Därefter kan du distribuera Azure AD Connect mot den återställda databasen med den här metoden.
- Den version av Azure AD Connect som används för installationen måste uppfylla följande kriterier:
    - 1.1.613.0 eller högre, OCH
    - Samma eller högre än den version av Azure AD Connect som senast användes med ADSync-databasen. Om Azure AD Connect-versionen som används för installationen är högre än den version som senast användes med ADSync-databasen, kan en fullständig synkronisering krävas.  Fullständig synkronisering krävs om det finns schema- eller synkroniseringsregeländringar mellan de två versionerna. 
- ADSync-databasen som används bör innehålla ett synkroniseringstillstånd som är relativt nytt. Den senaste synkroniseringsaktiviteten med den befintliga ADSync-databasen bör vara inom de senaste tre veckorna.
- När du installerar Azure AD Connect med metoden "använd befintlig databas" bevaras inte inloggningsmetoden som konfigurerats på den tidigare Azure AD Connect-servern. Vidare kan du inte konfigurera inloggningsmetoden under installationen. Du kan bara konfigurera inloggningsmetoden när installationen är klar.
- Du kan inte ha flera Azure AD Connect-servrar som delar samma ADSync-databas. Med metoden "använd befintlig databas" kan du återanvända en befintlig ADSync-databas med en ny Azure AD Connect-server. Det stöder inte delning.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Steg för att installera Azure AD Connect med "använd befintlig databas"-läge
1.  Hämta Azure AD Connect-installationsprogrammet (AzureADConnect.MSI) till Windows-servern. Dubbelklicka på Azure AD Connect-installationsprogrammet för att börja installera Azure AD Connect.
2.  När MSI-installationen är klar startar Azure AD Connect-guiden med installationsläget Express. Stäng fönstret genom att klicka på ikonen Avsluta.
![Välkommen](./media/how-to-connect-install-existing-database/db1.png)
3.  Starta en ny kommandotolk eller PowerShell-session. Navigera till mappen "C:\Program Files\Microsoft Azure Active Directory Connect". Kör kommandot .\AzureADConnect.exe /useexistingdatabase för att starta Azure AD Connect-guiden i läget ”Använd befintlig databas”.

> [!NOTE]
> Använd växeln **/UseExistingDatabase** endast när databasen redan innehåller data från en tidigare Azure AD Connect-installation. Till exempel när du flyttar från en lokal databas till en fullständig SQL Server-databas eller när Azure AD Connect-servern har återskapats och du återställde en SQL-säkerhetskopia av ADSync-databasen från en tidigare installation av Azure AD Connect. Om databasen är tom, det vill än, den innehåller inga data från en tidigare Azure AD Connect-installation, hoppa över det här steget.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. Du välkomnas med Välkommen till Azure AD Connect-skärmen. När du godkänt licensvillkoren och sekretesspolicyn klickar du på **Fortsätt**.
   ![Välkommen](./media/how-to-connect-install-existing-database/db3.png)
1. På skärmen **Installera nödvändiga komponenter** är alternativet **Använd en befintlig SQL-server** aktiverat. Ange namnet på den SQL-server som är värd för ADSync-databasen. Om SQL-motorinstansen som används som värd för ADSync-databasen inte är standardinstansen på SQL-servern, måste du ange instansnamnet för SQL-motorn. Om SQL-surfning inte är aktiverat, måste du dessutom också ange portnumret för SQL-motorinstansen. Ett exempel:         
   ![Välkommen](./media/how-to-connect-install-existing-database/db4.png)           

1. På skärmen **Anslut till Azure AD**, måste du ange autentiseringsuppgifter för en global administratör i Azure AD-katalogen. Det rekommenderas att använda ett konto i standarddomänen onmicrosoft.com. Det här kontot används bara för att skapa ett tjänstkonto i Azure AD och används inte när guiden har slutförts.
   ![Anslut](./media/how-to-connect-install-existing-database/db5.png)
 
1. På skärmen **Anslut dina kataloger** har den befintliga AD-skog som konfigurerats för katalogsynkronisering angetts med en röd kryssikon bredvid sig. Om du vill synkronisera ändringar från en lokal AD-skog krävs ett AD DS-konto. Azure AD Connect-guiden kan inte hämta autentiseringsuppgifter för det AD DS-konto som lagrats i ADSync-databasen eftersom autentiseringsuppgifterna krypterats och endast kan dekrypteras av den tidigare Azure AD Connect-servern. Klicka på **Ändra autentiseringsuppgifter** för att ange AD DS-kontot för AD-skogen.
   ![Kataloger](./media/how-to-connect-install-existing-database/db6.png)
 
 
1. I popup-fönstret kan du antingen (i) ange autentiseringsuppgifter för en företagsadministratör och låta Azure AD Connect skapa AD DS-kontot åt dig, eller (ii) skapa AD DS-kontot och ange dess autentiseringsuppgifter till Azure AD Connect. När du har valt ett alternativ och angett nödvändiga autentiseringsuppgifter klickar du på **OK** för att stänga popup-fönstret.
   ![Välkommen](./media/how-to-connect-install-existing-database/db7.png)
 
 
1. När autentiseringsuppgifterna har angetts ersätts den röda kryssikonen med en grön bockikon. Klicka på **Nästa**.
   ![Välkommen](./media/how-to-connect-install-existing-database/db8.png)
 
 
1. Klicka på **Installera**på skärmen **Klar att konfigurera** .
   ![Välkommen](./media/how-to-connect-install-existing-database/db9.png)
 
 
1. När installationen är klar aktiveras automatiskt Azure AD Connect-servern för mellanlagringsläge. Vi rekommenderar att du läser igenom serverkonfigurationen och väntande exporter för oväntade ändringar innan du inaktiverar mellanlagringsläget. 

## <a name="post-installation-tasks"></a>Uppgifter efter installation
När du återställer en databassäkerhetskopiering som skapats av en version av Azure AD Connect före 1.2.65.0, väljer mellanlagringsservern automatiskt en inloggningsmetod för **Konfigurera inte**. Medan inställningarna för återställning av lösenord hash-synkronisering och lösenord återställs måste du sedan ändra inloggningsmetoden så att den matchar de andra principer som gäller för den aktiva synkroniseringsservern.  Om du inte slutför dessa steg kan användare inte logga in om den här servern blir aktiv.  

Använd tabellen nedan för att kontrollera eventuella ytterligare steg som krävs.

|Funktion|Steg|
|-----|-----|
|Synkronisering av lösenord hash-synkronisering| Inställningarna för återställning av lösenord hash-synkronisering och lösenord återställs helt för versioner av Azure AD Connect från och med 1.2.65.0.  Om du återställer med en äldre version av Azure AD Connect läser du synkroniseringsalternativinställningarna för dessa funktioner för att säkerställa att de matchar din aktiva synkroniseringsserver.  Inga andra konfigurationssteg bör behövas.|
|Federation med AD FS|Azure-autentiseringar fortsätter att använda AD FS-principen som konfigurerats för din aktiva synkroniseringsserver.  Om du använder Azure AD Connect för att hantera din AD FS-servergrupp kan du eventuellt ändra inloggningsmetoden till AD FS-federationen som förberedelse för att din standby-server ska bli aktiv synkroniseringsinstans.   Om enhetsalternativ är aktiverade på den aktiva synkroniseringsservern konfigurerar du dessa alternativ på den här servern genom att köra aktiviteten Konfigurera enhetsalternativ.|
|Direktautentisering och enkel inloggning för stationär dator|Uppdatera inloggningsmetoden så att den matchar konfigurationen på den aktiva synkroniseringsservern.  Om detta inte följs innan du marknadsför servern till primär inaktiveras direktautentisering tillsammans med Seamless Single Sign on och din klient kan vara utelåst om du inte har synkronisering av lösenord hash-funktioner som säkerhetskopieringsskyltningsalternativ. Observera också att när du aktiverar direktautentisering i mellanlagringsläge installeras, registreras en ny autentiseringsagent och körs som en agent med hög tillgänglighet som accepterar inloggningsbegäranden.|
|Federation med PingFederate|Azure-autentiseringar fortsätter att använda pingfederate-principen som konfigurerats för din aktiva synkroniseringsserver.  Du kan också ändra inloggningsmetoden till PingFederate som förberedelse för att standby-servern ska bli den aktiva synkroniseringsinstansen.  Det här steget kan skjutas upp tills du behöver federera ytterligare domäner med PingFederate.|

## <a name="next-steps"></a>Nästa steg

- Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](how-to-connect-post-installation.md).
- Läs mer om dessa funktioner, som aktiverades med installationen: [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](how-to-connect-health-sync.md).
- Läs mer om [schemaläggaren och hur du utlöser synkronisering](how-to-connect-sync-feature-scheduler.md).
- Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
