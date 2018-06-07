---
title: Riktlinjer för SQL-databas med flera innehavare app exempel – Wingtip SaaS | Microsoft Docs
description: Innehåller steg och riktlinjer för att installera och köra exempelprogram för flera innehavare som använder Azure SQL Database, Wingtip biljetter SaaS-exempel.
keywords: sql database tutorial
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 6c352298c701c827cd01c0ed7f427b7ed6015e29
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646685"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Allmänna riktlinjer för att arbeta med Wingtip biljetter exempel SaaS-appar

Den här artikeln innehåller allmänna riktlinjer för att köra de Wingtip biljetter exempel SaaS-program som använder Azure SQL Database. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Hämta och avblockera Wingtip biljetter SaaS-skript

Körbara innehållet (skript, DLL-filer) kan blockeras av Windows när zip-filer laddas ned från en extern källa och extraheras. När skript från en zip-fil, **Följ stegen nedan för att låsa upp ZIP-filen innan du extraherar**. Detta säkerställer att skript tillåts köra.

1. Bläddra till Wingtip biljetter SaaS GitHub-lagringsplatsen för databasen innehavare mönstret som du vill utforska: 
    - [WingtipTicketsSaaS StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klicka på **kloning eller hämta**.
3. Klicka på **hämta zip** och spara filen.
4. Högerklicka på zip-filen och välj **egenskaper**. Namnet på zip-filen motsvarar namnet på lagringsplatsen. (t.ex. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. På den **allmänna** väljer **avblockera**.
6. Klicka på **OK**.
7. Extrahera filerna.

Skript finns i den *... \\Learning moduler* mapp.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Arbeta med Wingtip biljetter PowerShell-skript

Om du vill få ut mesta av exemplet behöver du fördjupa dig i de angivna skript. Använd brytpunkter och gå igenom skript som de köra och granska hur de olika SaaS-mönster implementeras. För att enkelt gå igenom angivna skript och moduler för bästa förstå, bör du använda den [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Uppdatera konfigurationsfilen för distributionen

Redigera den **UserConfig.psm1** fil med resurs-grupp- och värdet som anges under distributionen:

1. Öppna den *PowerShell ISE* och läsa in... \\Learning moduler\\*UserConfig.psm1* 
2. Uppdatera *ResourceGroupName* och *namn* med specifika värden för din distribution (på rader 10 och 11 endast).
3. Spara ändringarna!

Om dessa värden anges här helt enkelt att du inte behöver uppdatera dessa distribution-specifika värden i alla skript.

### <a name="execute-the-scripts-by-pressing-f5"></a>Kör skript genom att trycka på F5

Använda flera skript *$PSScriptRoot* att navigera mappar, och *$PSScriptRoot* utvärderas bara när skript körs genom att trycka på **F5**.  Markera och kör en markering (**F8**) kan leda till fel, trycker du på **F5** när du kör skript.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Stega igenom skripten för att undersöka implementeringen

Det bästa sättet att förstå skripten är genom att gå igenom dem och se vad de gör. Kolla in de medföljande **Demo -** skript som finns ett lättöverskådligt arbetsflöde på hög nivå. Den **Demo -** skript visar de steg som krävs för att utföra varje aktivitet, så ange brytpunkter och öka detaljnivån djupare i enskilda anrop för att se implementeringsdetaljerna för de olika SaaS-mönster.

Tips för att utforska och stega igenom PowerShell-skript:

- Öppna **Demo -** skript i PowerShell ISE.
- Köra eller fortsätta med **F5** (med hjälp av **F8** rekommenderas inte eftersom *$PSScriptRoot* utvärderas inte när du kör val av ett skript).
- Placera brytpunkter genom att klicka, eller välja en rad och trycka på **F9**.
- Stega över en funktion eller skriptanrop med **F10**.
- Stega in i en funktion eller skriptanrop med **F11**.
- Stega ut ur den aktuella funktionen eller skriptanropet med **Skift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Utforska databasschemat och kör SQL-frågor med SSMS

Använd [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) att ansluta och bläddra programservrar och databaser.

Distributionen har ursprungligen klienter och katalogservrar för SQL-databas för att ansluta till. Namngivning av servrarna beror på databasen innehavare mönstret (se nedan för information). 

   - **Fristående program:** servrar för varje klient (t.ex. *contosoconcerthall -&lt;användare&gt;*  server) och *katalog-sa -&lt;användare&gt;*
   - **Databasen per klient:** *tenants1-dpt -&lt;användare&gt;*  och *katalog-dpt -&lt;användare&gt;*  servrar
   - **Flera innehavare databasen:** *tenants1-huvudmålservern -&lt;användare&gt;*  och *katalog-huvudmålservern -&lt;användare&gt;*  servrar

För att säkerställa en lyckad demo-anslutning, alla servrar som har en [brandväggsregel](sql-database-firewall-configure.md) så att alla IP-adresser via.


1. Öppna *SSMS* och ansluta till klienterna. Servernamnet är beroende av databasen innehavare mönstret som du har valt (se nedan för information):
    - **Fristående program:** servrar för enskilda klienter (t.ex. *contosoconcerthall -&lt;användare&gt;. database.windows.net*) 
    - **Databasen per klient:** *tenants1-dpt -&lt;användaren&gt;. database.windows.net*
    - **Flera innehavare databasen:** *tenants1-huvudmålservern -&lt;användaren&gt;. database.windows.net* 
2. Klicka på **anslut** > **databasmotor...** :

   ![katalogserver](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Autentiseringsuppgifterna för demot är: inloggningsnamn = *developer*, lösenord = *P@ssword1*

    Bilden nedan visar inloggningen för den *databas per klient* mönster. 
    ![Anslutning](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Upprepa steg 2 – 3 och ansluta till katalogserver (se nedan för specifika servernamn baserat på databasen innehavare mönster som valts)
    - **Fristående program:** *katalog-sa -&lt;användaren&gt;. database.windows.net*
    - **Databasen per klient:** *katalog-dpt -&lt;användaren&gt;. database.windows.net*
    - **Flera innehavare databasen:** *katalog-huvudmålservern -&lt;användaren&gt;. database.windows.net*


Du bör se alla servrar när anslutningen lyckas. I listan över databaser kan vara olika beroende på de klienter du har etablerat.

Bilden nedan visar inloggningen för den *databas per klient* mönster.

![objektutforskaren](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Nästa steg
- [Distribuera Wingtip biljetter SaaS fristående program](saas-standaloneapp-get-started-deploy.md)
- [Distribuera Wingtip biljetter SaaS databasen per klient program](saas-dbpertenant-get-started-deploy.md)
- [Distribuera databasprogram Wingtip biljetter SaaS flera innehavare](saas-multitenantdb-get-started-deploy.md)

