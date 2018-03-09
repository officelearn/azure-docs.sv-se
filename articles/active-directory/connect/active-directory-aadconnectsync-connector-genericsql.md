---
title: "Allmän SQL Connector | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar Microsofts allmänna SQL-anslutningen."
services: active-directory
documentationcenter: 
author: billmath
manager: bhu
editor: 
ms.assetid: fd8ccef3-6605-47ba-9219-e0c74ffc0ec9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: billmath
ms.openlocfilehash: 5ac8aff7e302a255efb86606416131ecf8088137
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="generic-sql-connector-technical-reference"></a>Teknisk referens för allmän SQL Connector
Den här artikeln beskriver allmänna SQL-anslutningen. Artikeln gäller för följande produkter:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Måste använda snabbkorrigering 4.1.3671.0 eller senare [KB3092178](https://support.microsoft.com/kb/3092178).

För MIM2016 och FIM2010R2 kopplingen är tillgänglig för hämtning från den [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

Den här kopplingen i åtgärden finns i [allmänna SQL Connector stegvisa](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) artikel.

## <a name="overview-of-the-generic-sql-connector"></a>Översikt över den allmänna SQL-anslutningen
Den allmänna SQL-anslutningen kan du integrera synkroniseringstjänsten med ett databassystem som erbjuder ODBC-anslutning.  

Följande funktioner stöds av den aktuella versionen av kopplingen ur på hög nivå:

| Funktion | Support |
| --- | --- |
| Anslutna datakällan |Anslutningen stöds med alla 64-bitars ODBC-drivrutiner. Den har testats med följande: <li>Microsoft SQL Server & SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 11 &10;g</li><li>MySQL 5.x</li> |
| Scenarier |<li>Livscykelhantering för objektet</li><li>Lösenordshantering</li> |
| Åtgärder |<li>Fullständig Import och Deltaimport, Export</li><li>För Export: Lägga till, ta bort uppdateringen, och Ersätt</li><li>Ange lösenord, ändra lösenord</li> |
| Schema |<li>Dynamisk identifiering av objekt och attribut</li> |

### <a name="prerequisites"></a>Förutsättningar
Innan du använder anslutningen kan du kontrollera att du har följande på synkroniseringsservern:

* 4.5.2 för Microsoft .NET Framework eller senare
* 64-bitars ODBC-drivrutiner för klienten

### <a name="permissions-in-connected-data-source"></a>Behörigheter i anslutna datakällan
För att skapa eller utför någon av uppgifterna som stöds i generiska SQL-koppling, måste du ha:

* db_datareader
* db_datawriter

### <a name="ports-and-protocols"></a>Portar och protokoll
De portar som krävs för ODBC-drivrutinen ska fungera dokumentationen databasleverantörens.

## <a name="create-a-new-connector"></a>Skapa en ny koppling
Skapa en allmän SQL-anslutning i **synkroniseringstjänsten** Välj **hanteringsagenten** och **skapa**. Välj den **generiskt SQL (Microsoft)** koppling.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Anslutning
Anslutningen används ett ODBC DSN-fil för anslutningen. Skapa filen DSN med **ODBC-datakällor** hittades i start-menyn under **Administrationsverktyg**. I det administrativa verktyget skapar en **fil-DSN** så kan få tillgång till anslutningstjänsten.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

Skärmen anslutning är först när du skapar en ny koppling för allmän SQL. Först måste du ange följande information:

* DSN filsökväg
* Autentisering
  * Användarnamn
  * Lösenord

Databasen ska ha stöd för en av dessa autentiseringsmetoder:

* **Windows-autentisering**: autentiserande databasen använder Windows-autentiseringsuppgifter för att verifiera användaren. Det användarnamn/lösenord anges används för att autentisera med databasen. Det här kontot behöver behörigheter till databasen.
* **SQL-autentisering**: de autentiserande databasen använder det användarnamn/lösenordet definieras en anslutning skärmen för att ansluta till databasen. Om du lagrar användarens namn/lösenord i filen DSN har autentiseringsuppgifterna på anslutningen skärmen högre prioritet.
* **Azure SQL Database authentication**: Mer information finns i [Anslut till SQL-databas med hjälp av Azure Active Directory Authentication](../../sql-database/sql-database-aad-authentication.md).

**DN är fästpunkt**: Om du väljer det här alternativet används också DN som fästpunktsattributet. Den kan användas för en enkel implementering men även har följande begränsningar:

* Anslutningen stöder endast en objekttyp. Därför kan referensattribut bara referera samma objekttyp.

**Exporttyp av: Objekt-Ersätt**: under exporten när endast vissa attribut har ändrats, hela objektet med alla attribut som exporteras och ersätter det befintliga objektet.

### <a name="schema-1-detect-object-types"></a>Schemat 1 (identifiera objekttyper)
På den här sidan kan ska du konfigurera hur anslutningen ska hitta olika objekttyper i databasen.

Varje objekttyp visas som en partition och konfigurerat ytterligare på **konfigurera partitioner och hierarkier**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Objekt av typen identifieringsmetod**: kopplingen stöder dessa identifieringsmetoder för typ av objekt.

* **Fast värde**: du anger en lista över typer av objekt med en kommaavgränsad lista. Till exempel: `User,Group,Department`.  
  ![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
* **Tabellen/vyn/lagrad procedur**: Ange namnet på den tabell/vy/lagrade proceduren och kolumnnamnet som innehåller en lista över objekttyper. Om du använder en lagrad procedur kan också ange parametrar för den i formatet **[Name]: [riktning]: [värde]**. Ange varje parameter på en separat rad (Använd Ctrl + Retur för att hämta en ny rad).  
  ![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
* **SQL-frågan**: det här alternativet kan du ange en SQL-fråga som returnerar en kolumn med objekttyper, till exempel `SELECT [Column Name] FROM TABLENAME`. Den returnerade kolumnen måste vara av typen sträng (varchar).

### <a name="schema-2-detect-attribute-types"></a>Schemat 2 (identifiera attributtyper)
På den här sidan kan ska du konfigurera hur attributnamn och typer ska identifieras. Konfigurationsalternativen visas för varje objekttyp som identifierats på föregående sida.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Attributet typen identifieringsmetod**: kopplingen stöder dessa identifieringsmetoder för attributet typ med de identifierade objekt i schemat 1.

* **Tabellen/vyn/lagrad procedur**: Ange namnet på den tabell/vy/lagrade proceduren som ska användas för att hitta attributnamnen. Om du använder en lagrad procedur kan också ange parametrar för den i formatet **[Name]: [riktning]: [värde]**. Ange varje parameter på en separat rad (Använd Ctrl + Retur för att hämta en ny rad). Ange en kommaavgränsad lista med tabeller eller vyer för att identifiera attributnamnen i ett flervärdesattribut. Flervärdesattribut scenarier stöds inte när överordnade och underordnade tabellen har samma kolumnnamn.
* **SQL-frågan**: det här alternativet kan du ange en SQL-fråga som returnerar en kolumn med attributnamn, till exempel `SELECT [Column Name] FROM TABLENAME`. Den returnerade kolumnen måste vara av typen sträng (varchar).

### <a name="schema-3-define-anchor-and-dn"></a>Schemat 3 (definiera fästpunkt och DN)
Den här sidan kan du konfigurera ankare och DN-attribut för varje identifierad objekttyp. Du kan välja flera attribut som gör det unikt ankaret.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

* Flera värden och booleskt attribut visas inte.
* Samma attribut kan inte använda för DN och ankaret, såvida inte **DN är fästpunkt** är markerat på sidan anslutningar.
* Om **DN är fästpunkt** är markerat på sidan anslutningar den här sidan kräver endast DN-attribut. Det här attributet används också som fästpunkt-attribut.

  ![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Schemat 4 (definiera attributtyp, referens och riktning)
Den här sidan kan du konfigurera attributtyp, till exempel heltal, binary eller booleskt värde och riktning för varje attribut. Alla attribut från sidan **schema 2** visas inklusive attribut med flera värden.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

* **DataType**: används för att mappa attributtypen för de typer som är känd av Synkroniseringsmotorn. Standardinställningen är att använda samma typ som identifierats i SQL-schemat, men DateTime och referens är inte enkelt kan upptäckas. För de, måste du ange **DateTime** eller **referens**.
* **Riktning**: du kan ange attributet riktning för Import, Export eller ImportExport. ImportExport är som standard.

![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Information:

* Om en attributtyp inte kan upptäckas av anslutningen använder datatypen String.
* **Kapslade tabeller** kan betraktas som en kolumn databastabeller. Oracle lagrar raderna i en kapslad tabell i bokstavsordning. När du hämtar den kapslade tabellen till en variabel för PL/SQL har raderna angetts i följd nedsänkt text som börjar på 1. Som ger matrisliknande åtkomst till enskilda rader.
* **VARRYS** stöds inte i kopplingen.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Schemat 5 (definiera partition för referensattribut)
På den här sidan kan konfigurera du för alla referensattribut vilken partition (objekttyp) ett attribut refererar till.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Om du använder **DN är fästpunkt**, måste du använda samma objekttyp som du refererar från. Du kan inte referera till en annan objekttyp.

>[!NOTE]
Från och med mars 2017 uppdateringen är nu ett alternativ för ”*” när det här alternativet är valt och sedan alla möjliga medlemstyper kommer att importeras.

![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/any-option.png)

>[!IMPORTANT]
 Från och med kan 2017 den ”\*” aka **något alternativ** har ändrats för att stödja importera och exportera flödet. Om du vill använda det här alternativet ska med flera värden tabellen/vyn ha ett attribut som innehåller objekttypen av.

![](./media/active-directory-aadconnectsync-connector-genericsql/any-02.png)

 </br> Om ”*” är markerad och sedan måste du även ange namnet på kolumnen med objekttypen.</br> ![](./media/active-directory-aadconnectsync-connector-genericsql/any-03.png)

Efter importen visas något som liknar bilden nedan:

  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/after-import.png)



### <a name="global-parameters"></a>Globala parametrar
Sidan globala parametrar används för att konfigurera Deltaimport, datum/tid-format och lösenordsmetod.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)



Allmän SQL Connector stöder följande metoder för Deltaimport:

* **Utlösaren**: se [genererar Delta vyer med hjälp av utlösare](https://technet.microsoft.com/library/cc708665.aspx).
* **Vattenstämpel**: en generisk metod som kan användas med en databas. Vattenstämpel frågan är förifyllda baserat på databasleverantören. En kolumn för vattenstämpeln måste finnas på varje tabell/vy som används. Den här kolumnen måste följa infogningar och uppdateringar av tabeller som och dess beroende (med flera värden eller underordnad) tabeller. Klockorna synkroniseringstjänsten och databasservern måste synkroniseras. Om inte, vissa poster i Deltaimport kan utelämnas.  
  Begränsning:
  * Vattenstämpel strategi har inte för ta bort stödobjekt.
* **Ögonblicksbild**: (fungerar endast med Microsoft SQL Server) [genererar Delta vyer med hjälp av ögonblicksbilder](https://technet.microsoft.com/library/cc720640.aspx)
* **Ändringsspårning**: (fungerar endast med Microsoft SQL Server) [om ändringsspårning](https://msdn.microsoft.com/library/bb933875.aspx)  
  Begränsningar:
  * Fästpunkten & DN-attribut måste vara en del av primärnyckeln för det valda objektet i tabellen.
  * SQL-fråga stöds inte under Import och Export med ändringsspårning.

**Ytterligare parametrar**: Ange databas-serverns tidszon som anger där databasservern finns. Det här värdet används för att stödja olika format för datum och tid attribut.

Kopplingen lagrar alltid datum och tid i UTC-format. Om du vill konvertera rätt datum och tid, tidszon databasservern och det format som används måste anges. Formatet som anges i .net-format.

Varje gång Datumattribut måste anges till kopplingen i UTC-tidsformat under exporten.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Lösenord Configuration**: anslutningen ger funktioner för synkronisering av lösenord och stöder och ändra lösenord.

Kopplingen erbjuder två metoder för att stödja synkronisering av lösenord:

* **Lagrade proceduren**: den här metoden kräver två lagrade procedurer för att stödja a & nge ändra lösenordet. Ange alla parametrar för Lägg till och ändra lösenord igen i **ange lösenord SP** och **ändra lösenord SP** parametrar som respektive per exemplet nedan.
  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
* **Lösenord tillägget**: den här metoden kräver att lösenord tilläggs-DLL (måste du ange det DLL-namn som implementerar det [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) interface). Lösenordet tilläggsammanfattningen måste placeras i tilläggsmappen så att anslutningen kan läsa in DLL-filen vid körning.
  ![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Du måste även aktivera hantering för lösenord på den **konfigurera tillägget** sidan.
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Konfigurera partitioner och hierarkier
På sidan partitioner och hierarkier väljer du alla typer av objekt. Varje objekt är en egen partition.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Du kan också åsidosätta de värden som definieras på den **anslutning** eller **globala parametrar** sidan.

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Konfigurera ankare
Den här sidan är skrivskyddat eftersom ankaret har redan definierats. Den valda fästpunktsattributet läggs alltid till objekttypen så förblir den unika över objekttyper.

![ankare](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Konfigurera kör steg-Parameter
De här stegen är konfigurerade för körning av profiler på kopplingen. De här konfigurationerna gör det faktiska arbetet med att importera och exportera data.

### <a name="full-and-delta-import"></a>Fullständig och Deltaimport
Generisk SQL Connector-stöd fullständig och Deltaimport på följande sätt:

* Tabell
* Visa
* Lagrad procedur
* SQL-fråga

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tabellen/vyn**  
Om du vill importera flera värden attribut för ett objekt som du måste ange namnet på tabellen/vyn i **namn multivärdes tabellvyer** och respektive kopplingsvillkor i den **kopplingsvillkor** med den överordnade tabellen . Om det finns flera med flera värden tabeller i datakällan, kan du använda union till en enda vy.

>[!IMPORTANT]
Hanteringsagenten generiskt SQL fungerar bara med en tabell i flera värden. Placera inte till namnet på flera värden tabellvyer mer än ett namn för tabellen. Det är en begränsning av generisk SQL.


Exempel: Du vill importera medarbetare objektet och alla dess med flera värden attribut. Det finns två tabeller, namngivna medarbetare (huvudtabell) och avdelning (med flera värden).
Gör följande:

* Typen **medarbetare** i **SP-tabell/vy**.
* Avdelning i **namn multivärdes tabellvyer**.
* Skriv kopplingsvillkor mellan anställda och avdelning i **kopplingsvillkor**, till exempel `Employee.DEPTID=Department.DepartmentID`.
  ![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Lagrade procedurer**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

* Om du har mycket data, rekommenderas det att implementera sidbrytning med din lagrade procedurer.
* För den lagrade proceduren att stödja sidbrytning, måste du ange starta Index och End Index. Se: [effektivt växling till stora mängder Data](https://msdn.microsoft.com/library/bb445504.aspx).
* @StartIndex och @EndIndex ersätts under körning med respektive värdet för sidstorlek konfigurerats på **konfigurera steg** sidan. Till exempel, när kopplingen hämtar första sidan och sidstorleken anges 500, i en sådan situation @StartIndex skulle värdet vara 1 och @EndIndex 500. Dessa värden ökar när koppling hämtar de efterföljande sidorna och ändra den @StartIndex & @EndIndex värde.
* Om du vill köra parametriserade lagrad procedur ange parametrar i `[Name]:[Direction]:[Value]` format. Ange varje parameter på en separat rad (tryck på Ctrl + Retur att hämta en ny rad).
* Allmän SQL kopplingen stödjer också importen från länkade servrar i Microsoft SQL Server. Om information ska hämtas från en tabell i länkade servern bör tabell anges i formatet: `[ServerName].[Database].[Schema].[TableName]`
* Allmän SQL Connector stöder endast de objekt som har liknande struktur (både alias namn och datatyp) mellan köra steg information och schema-identifiering. Om det markerade objektet från schemat och informationen i kör steget skiljer sig, kan SQL-anslutningen inte stöder den här typen av scenarier.

**SQL-fråga**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

* Flera resultatmängder frågor som inte stöds.
* SQL-frågan har stöd för sidbrytningen och ange start Index och End Index som en variabel för att stödja sidbrytning.

### <a name="delta-import"></a>Deltaimport
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Delta importera konfigurationen kräver vissa ytterligare konfiguration jämfört med fullständig Import.

* Om du väljer det utlösare eller ögonblicksbild sättet att spåra deltaändringar och ange sedan Historiktabellen eller en ögonblicksbild av databasen i **Historiktabellen eller ögonblicksbild databasnamnet** rutan.
* Du måste också ange kopplingsvillkor mellan historiktabellen och den överordnade tabellen, till exempel `Employee.ID=History.EmployeeID`
* För att spåra transaktionen på den överordnade tabellen från tabellen tidigare, måste du ange kolumnnamnet som innehåller informationen för åtgärden (Lägg till/Uppdatera/ta bort).
* Om du väljer vattenstämpel att spåra deltaändringar Ange kolumnnamnet som innehåller informationen igen i **vattenstämplar markera kolumnnamnet**.
* Den **ändra Typattributet** krävs för ändringstyp. Den här kolumnen mappar en ändring som uppstår i den primära tabellen eller Flervärde tabell till en ändring i delta-vyn. Den här kolumnen kan innehålla Modify_Attribute ändra typen för attributet nivå ändra eller lägga till, ändra, eller ta bort ändra typ av en ändring på objektnivå. Om det är något annat än standardvärdet Lägg till, kan ändra eller ta bort och du definiera dessa värden med hjälp av det här alternativet.

### <a name="export"></a>Exportera
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Stöd för allmän SQL Connector Export med fyra metoder som stöds, till exempel:

* Tabell
* Visa
* Lagrad procedur
* SQL-fråga

**Tabellen/vyn**  
Om du väljer alternativet tabellen/vyn genererar kopplingen respektive frågor för att utföra exporten.

**Lagrade procedurer**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Om du väljer alternativet lagrade proceduren kräver Export tre olika lagrade procedurer att utföra Insert/Update/Delete-åtgärder.

* **Lägg till SP namnet**: den här SP körs om alla objekt som kommer till anslutningstjänsten för infogning i respektive tabell.
* **Uppdatera SP namnet**: den här SP körs om alla objekt som kommer till anslutningstjänsten för uppdatering i respektive tabell.
* **Ta bort SP namn**: den här SP körs om alla objekt som kommer att anslutningen ska tas bort i tabellen respektive.
* Filattributet från schemat som används som ett parametervärde till den lagrade proceduren. Till exempel `@EmployeeName: INPUT: EmployeeName` (EmployeeName väljs i connector-schemat och kopplingen ersätter värdet för respektive medan export)
* För att köra lagrade procedurer som parametrar, ange parametrar i `[Name]:[Direction]:[Value]` format. Ange varje parameter på en separat rad (tryck på Ctrl + Retur att hämta en ny rad).

**SQL-fråga**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Om du väljer SQL frågealternativet kräver Export tre olika frågor för att utföra Insert/Update/Delete-åtgärder.

* **Infoga frågan**: den här frågan körs om alla objekt som kommer till anslutningstjänsten för infogning i respektive tabell.
* **Uppdatera fråga**: den här frågan körs om alla objekt som kommer till anslutningstjänsten för uppdatering i respektive tabell.
* **Ta bort frågan**: den här frågan körs om alla objekt som kommer att anslutningen ska tas bort i tabellen respektive.
* Filattributet från schemat som används som ett parametervärde till i frågan, t ex `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Felsökning
* Information om hur du aktiverar loggning för att felsöka anslutningen finns i [hur du aktiverar ETW-spårning för kopplingar](http://go.microsoft.com/fwlink/?LinkId=335731).
