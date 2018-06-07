---
title: Installera Azure AD Connect med hjälp av en befintlig databas ADSync | Microsoft Docs
description: Det här avsnittet beskriver hur du använder en befintlig ADSync-databas.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b28d4cc6b10b0fd47a67cfefa7cf1022921a5d96
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591777"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Installera Azure AD Connect med en befintlig ADSync-databas
Azure AD Connect kräver en SQL Server-databas för att lagra data. Du kan använda standardvärdet SQL Server 2012 Express LocalDB installerad med Azure AD Connect, eller så kan du använda din egen fullständig version av SQL Server. Tidigare skapades alltid en ny databas med namnet ADSync när du har installerat Azure AD Connect. Med Azure AD Connect version 1.1.613.0 (eller efter) har du möjlighet att installera Azure AD Connect genom att peka till en befintlig ADSync-databas.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Fördelarna med att använda en befintlig ADSync-databas
Genom att peka till en befintlig databas ADSync:

- Förutom inloggningsuppgifterna, synkroniseringskonfigurationen lagras i databasen ADSync (inklusive anpassade Synkroniseringsregler, kopplingar, filtrering och konfiguration för valfria funktioner) automatiskt återställas och används under installationen . Autentiseringsuppgifter som används av Azure AD Connect för att synkronisera ändringar med lokala AD och Azure AD är krypterade och kan bara användas av den föregående Azure AD Connect-servern.
- Alla identitetsdata (associerad med kopplingens utrymmen och metaversum) och synkronisering cookies lagras i databasen ADSync återställas. Den nyligen installerade Azure AD Connect som servern kan fortsätta att synkronisera från där den tidigare Azure AD Connect-servern slutade, i stället för att du behöver utföra en fullständig synkronisering.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scenarier där med en befintlig databas ADSync är bra
Dessa fördelar är användbart i följande scenarier:


- Du har en befintlig Azure AD Connect-distribution. Den befintliga Azure AD Connect-servern fungerar inte längre men SQLServer som innehåller ADSync databasen fortfarande fungerar. Du kan installera en ny Azure AD Connect-server och peka på den befintliga ADSync-databasen. 
- Du har en befintlig Azure AD Connect-distribution. SQLServer som innehåller ADSync databasen fungerar inte längre. Du har dock en senaste säkerhetskopiering av databasen. Du kan återställa ADSync databasen till en ny SQLServer först. Efter som du kan installera en ny Azure AD Connect-server och peka på den återställda databasen ADSync.
- Du har en befintlig Azure AD Connect-distribution som använder LocalDB. På grund av 10 GB-gräns som angetts av LocalDB, vill du migrera till en fullständig SQL. Du kan säkerhetskopiera databasen ADSync från LocalDB och återställa den till en SQLServer. Efter som du kan installera en ny Azure AD Connect-server och peka på den återställda databasen ADSync.
- Du försöker installera en fristående server och vill kontrollera att konfigurationen matchar den aktuella aktiva servern. Du kan säkerhetskopiera databasen ADSync och återställa den till en annan SQLServer. Efter som du kan installera en ny Azure AD Connect-server och peka på den återställda databasen ADSync.

## <a name="prerequisite-information"></a>Nödvändig information

Viktigt att vidta tänka på innan du fortsätter:

- Se till att granska kraven för att installera Azure AD Connect på maskinvara och krav, och kontot och behörigheter som krävs för att installera Azure AD Connect. De behörigheter som krävs för att installera Azure AD Connect med ”Använd befintlig databas” läge är detsamma som ”anpassad” installation.
- Distribuera Azure AD Connect mot en befintlig ADSync kan databas bara användas med fullständig SQL. Den stöds inte med SQL Express LocalDB. Om du har en befintlig databas ADSync i LocalDB som du vill använda måste du först ADSync-databasen (LocalDB) och återställa den till fullständig SQL. Efter som du kan distribuera Azure AD Connect mot den återställda databasen med den här metoden.
- Versionen av Azure AD Connect som används för installation måste uppfylla följande kriterier:
    - 1.1.613.0 eller senare, och
    - Samma eller högre än versionen av Azure AD Connect som senast användes med ADSync-databasen. Om Azure AD Connect-version som används för installation är högre än den version som senast användes med ADSync databasen kan sedan en fullständig synkronisering krävas.  Fullständig synkronisering krävs om schemat eller synkronisering regeln har ändrats mellan de två versionerna. 
- ADSync databasen som används ska innehålla en synkroniseringstillstånd är relativt nya. Senaste synkronisering aktiviteten med den befintliga databasen ADSync ska inom de senaste tre veckorna.
- När du installerar Azure AD Connect med ”Använd befintlig databas” metod, bevaras inte inloggningsmetod konfigurerats på den föregående Azure AD Connect-servern. Du kan dessutom konfigurera inloggningsmetod under installationen. Du kan bara konfigurera inloggningsmetod när installationen är klar.
- Du kan inte ha flera Azure AD Connect-servrar som delar samma ADSync databas. Metoden ”Använd befintlig databas” kan du använda en befintlig ADSync databas med en ny Azure AD Connect-server. Det har inte stöd för delning.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Steg för att installera Azure AD Connect med ”Använd befintlig databas”-läge
1.  Hämta Azure AD Connect-installationsprogrammet (AzureADConnect.MSI) till Windows server. Dubbelklicka på Azure AD Connect-installationsprogrammet för att starta installationen av Azure AD Connect.
2.  När MSI-installationen är klar startar Azure AD Connect-guiden med installationen av Express-läge. Stäng fönstret genom att klicka på ikonen avsluta.
![Välkommen](media/active-directory-aadconnect-existing-database/db1.png)
3.  Starta en ny kommandotolk eller PowerShell-session. Navigera till mappen <drive>\Program\Microsoft Azure AD Connect. Kör kommandot.\AzureADConnect.exe /useexistingdatabase för att starta Azure AD Connect-guiden i ”Använd befintlig databas” läge.
![PowerShell](media/active-directory-aadconnect-existing-database/db2.png)
4.  Du greeted med Välkommen till Azure AD Connect-skärmen. När du godkänner licensvillkoren och sekretesspolicyn klickar du på **Fortsätt**.
![Välkommen](media/active-directory-aadconnect-existing-database/db3.png)
5.  På den **installera nödvändiga komponenter** skärmen den **använder en befintlig SQL Server** är aktiverat. Ange namnet på den SQLServer som är värd för databasen ADSync. Om SQL-engine-instansen som används som värd för ADSync databasen inte är standardinstansen på SQLServer, måste du ange instansnamnet för SQL-motorn. Om SQL-surfning inte är aktiverat, måste du dessutom också ange portnumret för SQL engine-instansen. Exempel:         
![Välkommen](media/active-directory-aadconnect-existing-database/db4.png)           

6.  På den **Anslut till Azure AD** skärmen, måste du ange autentiseringsuppgifter för en global administratör i Azure AD-katalogen. Det rekommenderas att använda ett konto i standarddomänen onmicrosoft.com. Det här kontot används bara för att skapa ett tjänstkonto i Azure AD och används inte när guiden har slutförts.
![Anslut](media/active-directory-aadconnect-existing-database/db5.png)
 
7.  På den **Anslut dina kataloger** skärmen den befintliga AD-skogen har konfigurerats för katalogsynkronisering anges med ett rött kryss ikonen bredvid den. Om du vill synkronisera ändringar från en lokal krävs AD-skog, en AD DS-konto. Azure AD Connect-guiden kan inte hämta autentiseringsuppgifter för AD DS-konto som lagras i databasen ADSync eftersom autentiseringsuppgifterna krypteras och endast kan dekrypteras av tidigare Azure AD Connect-servern. Klicka på **ändra autentiseringsuppgifter** att ange AD DS-konto för AD-skog.
![Kataloger](media/active-directory-aadconnect-existing-database/db6.png)
 
 
8.  I popup-fönstret kan du antingen (i) ange autentiseringsuppgifter för en företagsadministratör och låta Azure AD Connect skapa AD DS-konto för dig, eller (ii) skapar AD DS-konto och ange sina autentiseringsuppgifter till Azure AD Connect. När du har valt ett alternativ och ange nödvändiga autentiseringsuppgifter klickar du på **OK** att stänga popup-fönstret.
![Välkommen](media/active-directory-aadconnect-existing-database/db7.png)
 
 
9.  När autentiseringsuppgifterna har angetts ersätts ikonen rött kryss med en grön skalstreck ikon. Klicka på **Nästa**.
![Välkommen](media/active-directory-aadconnect-existing-database/db8.png)
 
 
10. På den **redo att konfigurera** klickar du på **installera**.
![Välkommen](media/active-directory-aadconnect-existing-database/db9.png)
 
 
11. När installationen är klar aktiveras automatiskt Azure AD Connect-servern för Mellanlagringsläge. Vi rekommenderar att du läser igenom serverkonfigurationen och väntande exporter för oväntade ändringar innan du inaktiverar Mellanlagringsläge. 

## <a name="next-steps"></a>Nästa steg

- Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](active-directory-aadconnect-whats-next.md).
- Läs mer om dessa funktioner, som aktiverades med installationen: [Förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).
- Läs mer om [schemaläggaren och hur du utlöser synkronisering](active-directory-aadconnectsync-feature-scheduler.md).
- Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
