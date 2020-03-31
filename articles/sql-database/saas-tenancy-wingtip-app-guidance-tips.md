---
title: Exempel på app för flera innehavare - Wingtip SaaS
description: Innehåller steg och vägledning för att installera och köra exempelprogrammet för flera innehavare som använder Azure SQL Database, Wingtip Tickets SaaS-exemplet.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 162d1f269c65ad98afa30e8e96370bbdceca99bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132291"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Allmän vägledning för att arbeta med Wingtip Tickets exempel SaaS apps

Den här artikeln innehåller allmän vägledning för att köra exempel på SaaS-program för Wingtip-biljetter som använder Azure SQL Database.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Ladda ner och låsa upp Wingtip Biljetter SaaS skript

Körbart innehåll (skript, dlls) kan blockeras av Windows när zip-filer hämtas från en extern källa och extraheras. När du extraherar skripten från en zip-fil **följer du stegen nedan för att låsa upp ZIP-filen innan du extraherar**. Detta säkerställer att skripten tillåts köras.

1. Bläddra till Wingtip Tickets SaaS GitHub repo för databasen hyresmönster du vill utforska:
    - [WingtipTicketsSSaaS-FriståendeApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klicka på **Klona eller hämta**.
3. Klicka på **Hämta zip** och spara filen.
4. Högerklicka på zip-filen och välj **Egenskaper**. Zip-filnamnet motsvarar repponamnet. (ex. _WingtipTicketsSSaaS-DbPerTenant-master.zip_)
5. Välj Ta bort **blockering**på fliken **Allmänt** .
6. Klicka på **OK**.
7. Extrahera filerna.

Skript finns i *.. Mappen \\Utbildningsmoduler.*


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Arbeta med Wingtip Tickets PowerShell skript

För att få ut det mesta av provet måste du dyka in i de medföljande skripten. Använd brytpunkter och gå igenom skripten när de körs och undersök hur de olika SaaS-mönstren implementeras. För att enkelt gå igenom de medföljande skripten och modulerna för bästa förståelse rekommenderar vi att du använder [PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Uppdatera konfigurationsfilen för distributionen

Redigera **filen UserConfig.psm1** med resursgruppen och användarvärdet som du anger under distributionen:

1. Öppna *PowerShell ISE* och ladda ... \\Utbildningsmoduler\\*UserConfig.psm1*
2. Uppdatera *ResourceGroupName* och *Namn* med specifika värden för distributionen (endast på raderna 10 och 11).
3. Spara ändringarna!

Om du anger dessa värden här kan du helt enkelt inte behöva uppdatera dessa distributionsspecifika värden i varje skript.

### <a name="execute-the-scripts-by-pressing-f5"></a>Köra skripten genom att trycka på F5

Flera skript använder *$PSScriptRoot* för att navigera i mappar, och *$PSScriptRoot* utvärderas endast när skript körs genom att trycka på **F5**.Om du markerar och kör en markering (**F8**) kan det leda till fel, så tryck på **F5** när du kör skript.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Stega igenom skripten för att undersöka implementeringen

Det bästa sättet att förstå skripten är genom att gå igenom dem för att se vad de gör. Kolla in de medföljande **Demo-skript** som presenterar en lätt att följa hög nivå arbetsflöde. **Demoskripten** visar de steg som krävs för att utföra varje uppgift, så ange brytpunkter och detaljgranskning i de enskilda anropen för att se implementeringsinformation för de olika SaaS-mönstren.

Tips för att utforska och gå igenom PowerShell-skript:

- Öppna **demoskript** i PowerShell ISE.
- Kör eller fortsätt med **F5** (med **F8** rekommenderas inte eftersom *$PSScriptRoot* inte utvärderas när du kör val av ett skript).
- Placera brytpunkter genom att klicka, eller välja en rad och trycka på **F9**.
- Stega över en funktion eller skriptanrop med **F10**.
- Stega in i en funktion eller skriptanrop med **F11**.
- Stega ut ur den aktuella funktionen eller skriptanropet med **Skift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Utforska databasschemat och kör SQL-frågor med SSMS

Använd [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) för att ansluta och bläddra bland programservrar och databaser.

Distributionen har ursprungligen klienter och katalog SQL Database-servrar att ansluta till. Namngivningen av servrarna beror på databasreningsmönstret (se nedan för detaljer).

   - **Fristående program:** servrar för varje klient (t.ex. *contosoconcerthall-&lt;&gt; Användarserver)* och *katalog-sa-&lt;Användare&gt; *
   - **Databas per klient:** *tenants1-dpt-&lt;Användare&gt; * och *katalog-dpt-&lt;Användarservrar&gt; *
   - Databas med **flera innehavare:** *tenants1-mt-&lt;Användare&gt; * och *katalog-mt-&lt;Användarservrar&gt; *

För att säkerställa en lyckad demoanslutning har alla servrar en [brandväggsregel](sql-database-firewall-configure.md) som tillåter alla IPs igenom.


1. Öppna *SSMS* och anslut till klienterna. Servernamnet beror på vilket databasarreningsmönster du har valt (se nedan för detaljer):
    - **Fristående program:** servrar för enskilda klienter (t.ex. *contosoconcerthall-&lt;&gt;Användare .database.windows.net*)
    - **Databas per klient:** *&lt;tenants1-dpt- Användare&gt;.database.windows.net*
    - **Databas med flera innehavare:** *tenants1-mt-&lt;Användare&gt;.database.windows.net*
2. Klicka på **Anslut** > **databasmotor...**:

   ![katalogserver](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Demo autentiseringsuppgifter är: Login = *utvecklare*, Lösenord = *P\@ssword1*

    Bilden nedan visar inloggningen för *databasen per klientmönster.*
    ![Anslutning](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Upprepa steg 2-3 och anslut till katalogservern (se nedan för specifika servernamn baserat på det valda databasarreningsmönstret)
    - **Fristående program:** *katalog-sa-&lt;&gt;Användare .database.windows.net*
    - **Databas per klient:** *&lt;katalog-dpt- Användare&gt;.database.windows.net*
    - **Databas med flera innehavare:** *&lt;katalog-mt- Användare&gt;.database.windows.net*


När du har anslutit bör du se alla servrar. Listan över databaser kan vara olika beroende på vilka klienter du har etablerat.

Bilden nedan visar inloggningen för *databasen per klientmönster.*

![objektutforskaren](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Nästa steg
- [Distribuera Wingtip Biljetter SaaS fristående program](saas-standaloneapp-get-started-deploy.md)
- [Distribuera SaaS-databasen för Wingtip-biljetter per klientprogram](saas-dbpertenant-get-started-deploy.md)
- [Distribuera Wingtip Tickets SaaS-databasprogrammet för flera innehavare](saas-multitenantdb-get-started-deploy.md)

