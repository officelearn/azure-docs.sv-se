---
title: Vägledning för SQL Database app för flera klienter exempel – Wingtip SaaS | Microsoft Docs
description: Innehåller anvisningar och riktlinjer för att installera och kör programmet på flera klienter som använder Azure SQL Database, Wingtip biljetter SaaS-exemplet.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: da8814cfd8fca8da061c27d9c5b69af15bff5007
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054412"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Allmänna riktlinjer för att arbeta med Wingtip biljetter SaaS exempelappar

Den här artikeln innehåller allmänna riktlinjer för att köra de Wingtip biljetter SaaS exempelprogram som använder Azure SQL Database. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Ladda ned och avblockera Wingtip biljetter SaaS-skript

Körbart innehåll (skript, DLL-filer) kan blockeras av Windows när zip-filer laddas ned från en extern källa och extraheras. Vid extrahering av skripten från en zip-fil **följer du stegen nedan för att avblockera .zip-filen innan du extrahera**. Detta säkerställer att skripten ska tillåtas att köras.

1. Bläddra till Wingtip biljetter SaaS GitHub-lagringsplatsen för det mönster för innehavare av databasen som du vill utforska: 
    - [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klicka på **klona eller ladda ned**.
3. Klicka på **ladda ned zip** och spara filen.
4. Högerklicka på zipfilen och välj **egenskaper**. Namnet på zip-filen motsvarar namnet på lagringsplatsen. (ex.) _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. På den **Allmänt** fliken **avblockera**.
6. Klicka på **OK**.
7. Extrahera filerna.

Skript finns i den *... \\Inlärningsmoduler* mapp.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Arbeta med Wingtip biljetter PowerShell-skript

Du måste sätta igång skripten som tillhandahålls för att få ut mest av exemplet. Använd brytpunkter och stega igenom skripten som de köra och granska hur de olika SaaS-mönstren implementeras. För att enkelt gå igenom de tillhandahållna skripten och modulerna för att bäst förstå, bör du använda den [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Uppdatera konfigurationsfilen för din distribution

Redigera den **UserConfig.psm1** fil med resurs- och användarnamn värdet som du anger under distributionen:

1. Öppna den *PowerShell ISE* och läsa in... \\Inlärningsmoduler\\*UserConfig.psm1* 
2. Uppdatera *ResourceGroupName* och *namn* med specifika värden för din distribution (på raderna 10 och 11 endast).
3. Spara ändringarna!

Om dessa värden här bara ser du inte behöva uppdatera de här distributionsspecifika värdena i varje skript.

### <a name="execute-the-scripts-by-pressing-f5"></a>Köra skripten genom att trycka på F5

Flera skript använder *$PSScriptRoot* att navigera mappar, och *$PSScriptRoot* utvärderas bara när skript körs genom att trycka på **F5**.  Om du markerar och kör en markering (**F8**) kan resultera i fel, trycker du på **F5** när du kör skript.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Stega igenom skripten för att undersöka implementeringen

Det bästa sättet att förstå skripten är genom att stega dig igenom dem för att se vad de gör. Kolla in den medföljande **Demo -** skript som presenterar ett enkelt sätt att följa arbetsflöde på hög nivå. Den **Demo -** skript visar de steg som krävs för att utföra varje uppgift, så ange brytpunkter och gå djupare i de olika anropen för att se implementeringsdetaljer för de olika SaaS-mönstren.

Tips för att utforska och stega dig igenom PowerShell-skript:

- Öppna **Demo -** skript i PowerShell ISE.
- Kör eller Fortsätt med **F5** (med hjälp av **F8** rekommenderas inte eftersom *$PSScriptRoot* utvärderas inte när du kör val av ett skript).
- Placera brytpunkter genom att klicka, eller välja en rad och trycka på **F9**.
- Stega över en funktion eller skriptanrop med **F10**.
- Stega in i en funktion eller skriptanrop med **F11**.
- Stega ut ur den aktuella funktionen eller skriptanropet med **Skift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Utforska databasschemat och kör SQL-frågor med SSMS

Använd [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) att ansluta och bläddra programservrar och databaser.

Distributionen har ursprungligen klienter och catalog SQL Database-servrar för att ansluta till. Namngivning av servrarna beror på databasen innehavare mönstret (se nedan för information). 

   - **Fristående program:** servrar för varje klient (ex.) *contosoconcerthall -&lt;användaren&gt;*  server) och *catalog-sa -&lt;användare&gt;*
   - **Databas per klient:** *tenants1-dpt -&lt;användaren&gt;*  och *catalog-dpt -&lt;användaren&gt;*  servrar
   - **Databas för flera innehavare:** *tenants1-mt -&lt;användaren&gt;*  och *catalog-mt -&lt;användaren&gt;*  servrar

För att säkerställa en lyckad demoanslutning, alla servrar har en [brandväggsregel](sql-database-firewall-configure.md) så att alla IP-adresser via.


1. Öppna *SSMS* och Anslut till klienterna. Servernamnet är beroende av databasen innehavare mönster som du har valt (se nedan för information):
    - **Fristående program:** servrar med enskilda klienter (ex.) *contosoconcerthall -&lt;användaren&gt;. database.windows.net*) 
    - **Databas per klient:** *tenants1-dpt -&lt;användaren&gt;. database.windows.net*
    - **Databas för flera innehavare:** *tenants1-mt -&lt;användaren&gt;. database.windows.net* 
2. Klicka på **anslut** > **databasmotor...** :

   ![katalogserver](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Autentiseringsuppgifterna för demot är: inloggningsnamn = *developer*, lösenord = *P@ssword1*

    Bilden nedan visar inloggningen för den *databas per klient* mönster. 
    ![anslutning](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Upprepa steg 2 – 3 och Anslut till katalogservern (se nedan för specifik servernamn baserat på databas från en innehavare valt)
    - **Fristående program:** *catalog-sa -&lt;användaren&gt;. database.windows.net*
    - **Databas per klient:** *catalog-dpt -&lt;användaren&gt;. database.windows.net*
    - **Databas för flera innehavare:** *catalog-mt -&lt;användaren&gt;. database.windows.net*


När du har anslutit bör du se alla servrar. Listan över databaser kan vara olika, beroende på de innehavare som du har etablerat.

Bilden nedan visar inloggningen för den *databas per klient* mönster.

![objektutforskaren](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Nästa steg
- [Distribuera Wingtip biljetter SaaS fristående program](saas-standaloneapp-get-started-deploy.md)
- [Distribuera Wingtip biljetter SaaS-databas per klient program](saas-dbpertenant-get-started-deploy.md)
- [Distribuera databas för flera klienter i Wingtip biljetter SaaS-program](saas-multitenantdb-get-started-deploy.md)

