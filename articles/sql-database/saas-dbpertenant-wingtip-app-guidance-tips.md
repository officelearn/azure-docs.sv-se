---
title: "Riktlinjer för SQL-databas med flera innehavare app exempel – Wingtip SaaS | Microsoft Docs"
description: "Innehåller steg och riktlinjer för att installera och köra exempelprogram för flera innehavare som använder Azure SQL Database, Wingtip SaaS-exempel."
keywords: sql database tutorial
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: genemi
ms.openlocfilehash: 4c90d70bb3b043ef81a224f0f69107eaa6eb0547
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="guidance-and-tips-for-azure-sql-database-multi-tenant-saas-app-example"></a>Vägledning och tips för Azure SQL Database med flera innehavare SaaS app exempel


## <a name="download-and-unblock-the-wingtip-tickets-saas-database-per-tenant-scripts"></a>Hämta och avblockera Wingtip biljetter SaaS databas per klient skript

Körbara innehållet (skript, DLL-filer) kan blockeras av Windows när zip-filer laddas ned från en extern källa och extraheras. När skript från en zip-fil, ***Följ stegen nedan för att låsa upp ZIP-filen innan du extraherar***. Detta säkerställer att skript tillåts köra.

1. Bläddra till [Wingtip biljetter SaaS databas per klient GitHub-repo-](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
2. Klicka på **kloning eller hämta**.
3. Klicka på **hämta ZIP** och spara filen.
4. Högerklicka på den **WingtipTicketsSaaS-DbPerTenant-master.zip** fil och markera **egenskaper**.
5. På den **allmänna** väljer **avblockera**.
6. Klicka på **OK**.
7. Extrahera filerna.

Skript finns i den *... \\Learning moduler* mapp.


## <a name="working-with-the-wingtip-tickets-saas-database-per-tenant-powershell-scripts"></a>Arbeta med Wingtip biljetter SaaS databas per klient PowerShell-skript

Om du vill få ut mesta av exemplet behöver du fördjupa dig i de angivna skript. Använd brytpunkter och gå igenom de skript som undersöker information om hur de olika SaaS-mönster implementeras. För att enkelt gå igenom angivna skript och moduler för bästa förstå, bör du använda den [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Uppdatera konfigurationsfilen för distributionen

Redigera den **UserConfig.psm1** fil med resurs-grupp- och värdet som anges under distributionen:

1. Öppna den *PowerShell ISE* och läsa in... \\Learning moduler\\*UserConfig.psm1* 
2. Uppdatera *ResourceGroupName* och *namn* med specifika värden för din distribution (på rader 10 och 11 endast).
3. Spara ändringarna!

Om dessa värden anges här helt enkelt att du inte behöver uppdatera dessa distribution-specifika värden i alla skript.

### <a name="execute-scripts-by-pressing-f5"></a>Kör skript genom att trycka på F5

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

Ursprungligen har två SQL Database-servrar för att ansluta till - den *tenants1-dpt -&lt;användare&gt;*  server, och *katalog-dpt -&lt;användare&gt;* server. För att säkerställa en lyckad demo-anslutning, båda servrarna har en [brandväggsregel](sql-database-firewall-configure.md) så att alla IP-adresser via.


1. Öppna *SSMS* och ansluta till den *tenants1-dpt -&lt;användare&gt;. database.windows.net* server.
2. Klicka på **anslut** > **databasmotor...** :

   ![katalogserver](media/saas-dbpertenant-wingtip-app-guidance-tips/connect.png)

3. Autentiseringsuppgifterna för demot är: inloggningsnamn = *developer*, lösenord = *P@ssword1*

   ![anslutning](media/saas-dbpertenant-wingtip-app-guidance-tips/tenants1-connect.png)

4. Upprepa steg 2 – 3 och ansluta till den *katalog-dpt -&lt;användare&gt;. database.windows.net* server.


När du har anslutit, borde du se bägge servrarna. I listan över databaser kan vara olika beroende på de klienter du har etablerat.

![objektutforskaren](media/saas-dbpertenant-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Nästa steg

[Distribuera Wingtip biljetter SaaS databasen per klient program](saas-dbpertenant-get-started-deploy.md)

