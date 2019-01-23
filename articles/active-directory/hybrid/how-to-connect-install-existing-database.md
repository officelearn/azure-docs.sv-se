---
title: Installera Azure AD Connect genom att använda en befintlig ADSync-databas | Microsoft Docs
description: Det här avsnittet beskriver hur du använder en befintlig ADSync-databas.
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
ms.topic: article
ms.date: 08/30/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a03e04b9ab249b5bb8ed43eecbc18d3a24374659
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473143"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Installera Azure AD Connect med en befintlig ADSync-databas
Azure AD Connect kräver en SQL Server-databas för att lagra data. Du kan använda standard SQL Server 2012 Express LocalDB som installerats med Azure AD Connect, eller så kan du använda din egen fullständiga versionen av SQL. Tidigare, när du har installerat Azure AD Connect, en ny databas med namnet ADSync alltid skapades. Med Azure AD Connect version 1.1.613.0 (eller efter) har du möjlighet att installera Azure AD Connect genom att peka till en befintlig ADSync-databas.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Fördelarna med att använda en befintlig ADSync-databas
Genom att peka på en befintlig ADSync-databas:

- Förutom information om säkerhetsreferenser, synkroniseringskonfigurationen som lagras i ADSync-databasen (inklusive anpassade Synkroniseringsregler, anslutningar, filtrering och konfiguration för valfria funktioner) automatiskt återställas och används under installationen . Autentiseringsuppgifter som används av Azure AD Connect för att synkronisera ändringar med lokala AD och Azure AD är krypterade och kan bara användas av tidigare Azure AD Connect-servern.
- Alla identitet (associerade med kopplingens utrymmen och metaversum) och synkronisering cookies som lagras i ADSync-databas kan också återställas. Nyligen installerade Azure AD Connect-servern kan fortsätta att synkronisera från där tidigare Azure AD Connect-servern slutade, i stället för att behöva utföra en fullständig synkronisering.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scenarier där med en befintlig ADSync-databas kan vara bra
Dessa fördelar är användbart i följande scenarier:


- Du har en befintlig Azure AD Connect-distribution. Din befintliga Azure AD Connect-servern längre arbetar inte men SQLServer som innehåller ADSync-databas fortfarande fungerar. Du kan installera en ny Azure AD Connect-servern och peka den till en befintlig ADSync-databas. 
- Du har en befintlig Azure AD Connect-distribution. Din SQL-server som innehåller ADSync-databas fungerar inte längre. Du kan dock en nyligen genomförd säkerhetskopiering av databasen. Du kan återställa ADSync-databas till en ny SQLServer först. Då och du kan installera en ny Azure AD Connect-servern och peka på den återställda ADSync-databasen.
- Du har en befintlig Azure AD Connect-distribution som använder LocalDB. På grund av den gräns på 10 GB som LocalDB, vill du migrera till fullständig SQL. Du kan säkerhetskopiera ADSync-databas från LocalDB och återställer dem till en SQLServer. Då och du kan installera om en ny Azure AD Connect-servern och peka på den återställda ADSync-databasen.
- Du försöker ställa in en mellanlagringsserver och vill kontrollera att konfigurationen överensstämmer med den aktuella aktiva servern. Du kan säkerhetskopiera ADSync-databas och återställa den till en annan SQLServer. Då och du kan installera om en ny Azure AD Connect-servern och peka på den återställda ADSync-databasen.

## <a name="prerequisite-information"></a>Information om nödvändiga komponenter

Viktig information att ta Observera av innan du fortsätter:

- Se till att granska kraven för att installera Azure AD Connect på maskinvara och krav, och kontot och behörigheter som krävs för att installera Azure AD Connect. De behörigheter som krävs för att installera Azure AD Connect med ”Använd befintlig databas” läge är samma som ”anpassad” installation.
- Distribuera Azure AD Connect mot en befintlig ADSync stöds database endast med fullständig SQL. Det stöds inte med SQL Express LocalDB. Om du har en befintlig ADSync-databas i LocalDB som du vill använda, måste du först säkerhetskopiera ADSync-databas (LocalDB) och återställa den till fullständig SQL. Då och du kan distribuera Azure AD Connect mot den återställda databasen med den här metoden.
- Versionen av Azure AD Connect som används för installation måste uppfylla följande kriterier:
    - 1.1.613.0 eller senare, och
    - Samma eller högre än versionen av Azure AD Connect senast användes med ADSync-databas. Om den Azure AD Connect-version som används för installation är högre än den version som senast användes med ADSync-databas, kan sedan en fullständig synkronisering krävas.  Fullständig synkronisering krävs om det finns schema eller synkronisera ändras mellan de två versionerna. 
- ADSync-databas som används bör innehålla en synkroniseringstillstånd som är relativt nytt. Senaste synkronisering aktivitet med befintlig ADSync-databas ska vara inom de senaste tre veckorna.
- När du installerar Azure AD Connect med ”Använd befintlig databas” metod, bevaras inte inloggningsmetod konfigurerats på den tidigare Azure AD Connect-servern. Dessutom kan konfigurera du inte inloggningsmetod under installationen. Du kan bara konfigurera inloggningsmetod när installationen är klar.
- Du kan inte ha flera Azure AD Connect-servrar som delar samma ADSync-databas. ”Använd befintlig databas”-metoden kan du återanvända en befintlig ADSync-databas med en ny Azure AD Connect-server. Det har inte stöd för delning.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Steg för att installera Azure AD Connect med ”Använd befintlig databas”-läge
1.  Ladda ned Azure AD Connect-installationsprogrammet (AzureADConnect.MSI) till Windows server. Dubbelklicka på Azure AD Connect-installationsprogrammet för att starta installationen av Azure AD Connect.
2.  När MSI-installationen är klar startar Azure AD Connect-guiden med installationsläget Express. Stäng fönstret genom att klicka på ikonen Avsluta.
![Välkommen](./media/how-to-connect-install-existing-database/db1.png)
3.  Starta en ny kommandotolk eller PowerShell-session. Navigera till mappen <drive>\Program\Microsoft Azure AD Connect. Kör kommandot .\AzureADConnect.exe /useexistingdatabase för att starta Azure AD Connect-guiden i läget ”Använd befintlig databas”.
![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
4.  Du välkomnas med Välkommen till Azure AD Connect-skärmen. När du godkänt licensvillkoren och sekretesspolicyn klickar du på **Fortsätt**.
![Välkommen](./media/how-to-connect-install-existing-database/db3.png)
5.  På skärmen **Installera nödvändiga komponenter** är alternativet **Använd en befintlig SQL-server** aktiverat. Ange namnet på den SQL-server som är värd för ADSync-databasen. Om SQL-motorinstansen som används som värd för ADSync-databasen inte är standardinstansen på SQL-servern, måste du ange instansnamnet för SQL-motorn. Om SQL-surfning inte är aktiverat, måste du dessutom också ange portnumret för SQL-motorinstansen. Exempel:         
![Välkommen](./media/how-to-connect-install-existing-database/db4.png)           

6.  På skärmen **Anslut till Azure AD**, måste du ange autentiseringsuppgifter för en global administratör i Azure AD-katalogen. Det rekommenderas att använda ett konto i standarddomänen onmicrosoft.com. Det här kontot används bara för att skapa ett tjänstkonto i Azure AD och används inte när guiden har slutförts.
![Anslut](./media/how-to-connect-install-existing-database/db5.png)
 
7.  På skärmen **Anslut dina kataloger** har den befintliga AD-skog som konfigurerats för katalogsynkronisering angetts med en röd kryssikon bredvid sig. Om du vill synkronisera ändringar från en lokal AD-skog krävs ett AD DS-konto. Azure AD Connect-guiden kan inte hämta autentiseringsuppgifter för det AD DS-konto som lagrats i ADSync-databasen eftersom autentiseringsuppgifterna krypterats och endast kan dekrypteras av den tidigare Azure AD Connect-servern. Klicka på **Ändra autentiseringsuppgifter** för att ange AD DS-kontot för AD-skogen.
![Kataloger](./media/how-to-connect-install-existing-database/db6.png)
 
 
8.  I popup-fönstret kan du antingen (i) ange autentiseringsuppgifter för en företagsadministratör och låta Azure AD Connect skapa AD DS-kontot åt dig, eller (ii) skapa AD DS-kontot och ange dess autentiseringsuppgifter till Azure AD Connect. När du har valt ett alternativ och angett nödvändiga autentiseringsuppgifter klickar du på **OK** för att stänga popup-fönstret.
![Välkommen](./media/how-to-connect-install-existing-database/db7.png)
 
 
9.  När autentiseringsuppgifterna har angetts ersätts den röda kryssikonen med en grön bockikon. Klicka på **Nästa**.
![Välkommen](./media/how-to-connect-install-existing-database/db8.png)
 
 
10. Klicka på **Installera** på skärmen **Klart att konfigurera**.
![Välkommen](./media/how-to-connect-install-existing-database/db9.png)
 
 
11. När installationen är klar aktiveras automatiskt Azure AD Connect-servern för mellanlagringsläge. Vi rekommenderar att du läser igenom serverkonfigurationen och väntande exporter för oväntade ändringar innan du inaktiverar mellanlagringsläget. 

## <a name="post-installation-tasks"></a>Uppgifter efter installation
När du återställer en säkerhetskopia av databasen som skapats i en version av Azure AD Connect innan 1.2.65.0 testservern väljer en metod för att automatiskt **inte konfigurerar**. När dina lösenordets hash-synkronisering och tillbakaskrivning av lösenordsinställningar återställs, måste du ändra inloggningsmetod så att den matchar andra principer som gäller för din aktiva synkroniseringsserver senare.  Det gick inte att slutföra de här stegen kan förhindra att användare registrerar i bör den här servern blir aktiv.  

Använd tabellen nedan för att kontrollera eventuella ytterligare steg som krävs.

|Funktion|Steg|
|-----|-----|
|Synkronisering av lösenordshash| Synkronisering av Lösenordshash och inställningar för tillbakaskrivning av lösenord är helt återställas för versioner av Azure AD Connect från och med 1.2.65.0.  Om återställning använder en äldre version av Azure AD Connect, granska alternativet synkroniseringsinställningar för dessa funktioner för att säkerställa att de matchar din aktiva synkroniseringsserver.  Några andra konfigurationssteg bör vara nödvändigt.|
|Federation med AD FS|Azure-autentiseringar kommer att fortsätta att använda AD FS-princip som konfigurerats för din aktiva synkroniseringsserver.  Om du använder Azure AD Connect för att hantera din AD FS-servergrupp, kan du om du vill ändra metoden logga in till AD FS-federation inför din standby-server blir den aktiva synkronisering-instansen.   Om Enhetsalternativ är aktiverade på den aktiva synkronisering-servern kan du konfigurera dessa alternativ på den här servern genom att köra uppgiften ”konfigurera enhetsalternativ”.|
|Direktautentisering och Desktop enkel inloggning|Uppdatera inloggningen metod så att den matchar konfigurationen på servern för aktiva synkroniseringen.  Om detta inte följs innan uppgradera servern till primär, direkt autentisering tillsammans med sömlös enkelinloggning på kommer att inaktiveras och din klient låsas ute om du inte har lösenordshashsynkronisering som säkerhetskopiera loggar du in alternativet. Observera att när du aktiverar direktautentisering i mellanlagringsläge kan en ny autentiseringsagent kommer att installeras, registrerat och körs som en agent för hög tillgänglighet som ska ta emot begäranden inloggning.|
|Federation med PingFederate|Azure-autentiseringar kommer fortsätta att använda PingFederate-principen som konfigurerats för din aktiva synkroniseringsserver.  Du kan också ändra inloggningsmetoden till PingFederate inför din standby-server blir den aktiva synkronisering-instansen.  Det här steget kan fördröjas tills du behöver federera ytterligare domäner med PingFederate.|
## <a name="next-steps"></a>Nästa steg

- Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](how-to-connect-post-installation.md).
- Läs mer om dessa funktioner, som aktiverades med installationen: [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](how-to-connect-health-sync.md).
- Läs mer om [schemaläggaren och hur du utlöser synkronisering](how-to-connect-sync-feature-scheduler.md).
- Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
