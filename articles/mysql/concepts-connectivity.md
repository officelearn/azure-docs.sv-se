---
title: Tillfälliga anslutningsfel – Azure Database for MySQL
description: Lär dig hur du hanterar tillfälliga anslutningsfel och ansluter effektivt till Azure Database för MySQL.
keywords: mysql-anslutning,anslutningssträng,anslutningsproblem,tillfälligt fel,anslutningsfel,anslut effektivt
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 79c5c7e485cc9cb03757b8a981cef92d79b81c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537184"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Hantera tillfälliga fel och ansluta effektivt till Azure Database for MySQL

I den här artikeln beskrivs hur du hanterar tillfälliga fel och ansluter effektivt till Azure Database for MySQL.

## <a name="transient-errors"></a>Tillfälliga fel

Ett tillfälligt fel, även kallat transientfel, är ett fel som löser sig själv. Oftast visas dessa fel som en anslutning till databasservern som tas bort. Det går inte heller att öppna nya anslutningar till en server. Tillfälliga fel kan uppstå till exempel när maskinvaru- eller nätverksfel inträffar. En annan orsak kan vara en ny version av en PaaS-tjänst som håller på att rullas ut. De flesta av dessa händelser mildras automatiskt av systemet på mindre än 60 sekunder. En bästa praxis för att utforma och utveckla program i molnet är att förvänta sig tillfälliga fel. Anta att de kan hända i någon komponent när som helst och att ha lämplig logik på plats för att hantera dessa situationer.

## <a name="handling-transient-errors"></a>Hantera tillfälliga fel

Tillfälliga fel bör hanteras med hjälp av logik för återförsök. Situationer som måste beaktas:

* Ett fel uppstår när du försöker öppna en anslutning
* En inaktiv anslutning tas bort på serversidan. När du försöker utfärda ett kommando kan det inte köras
* En aktiv anslutning som för närvarande kör ett kommando tas bort.

Det första och andra fallet är ganska rakt fram för att hantera. Försök öppna anslutningen igen. När du lyckas har det tillfälliga felet mildrats av systemet. Du kan använda din Azure-databas för MySQL igen. Vi rekommenderar att du väntar innan du försöker ansluta igen. Backa om de första försöken misslyckas. På så sätt kan systemet använda alla tillgängliga resurser för att lösa felsituationen. Ett bra mönster att följa är:

* Vänta i 5 sekunder innan du försöker igen.
* För varje efterföljande återförsök ökar väntetiden exponentiellt, upp till 60 sekunder.
* Ange ett maximalt antal försök vid vilken tidpunkt ditt program anser att åtgärden misslyckades.

När en anslutning till en aktiv transaktion misslyckas är det svårare att hantera återställningen korrekt. Det finns två fall: Om transaktionen var skrivskyddad till sin natur är det säkert att öppna anslutningen igen och att försöka igen transaktionen. Om transaktionen emellertid också skrev till databasen måste du ta reda på om transaktionen återställdes eller om den lyckades innan det tillfälliga felet inträffade. I så fall kanske du bara inte har fått bekräftelse från databasservern.

Ett sätt att göra detta är att generera ett unikt ID på klienten som används för alla försök. Du skickar det här unika ID:t som en del av transaktionen till servern och lagrar det i en kolumn med en unik begränsning. På så sätt kan du säkert försöka transaktionen igen. Det lyckas om den tidigare transaktionen återställdes och klientgenererat unikt ID ännu inte finns i systemet. Det misslyckas med att ange en dubblettnyckelfel om det unika ID:t tidigare lagrats eftersom den tidigare transaktionen slutfördes.

När ditt program kommunicerar med Azure Database for MySQL via mellanprogram från tredje part frågar du leverantören om mellanmaterialet innehåller logik för återförsök för tillfälliga fel.

Se till att testa att du försöker logiken igen. Försök till exempel att köra koden samtidigt skala upp eller ned beräkningsresurserna för din Azure-databas för MySQL-server. Ditt program bör hantera den korta driftstopp som påträffas under den här åtgärden utan problem.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Ansluta effektivt till Azure Database för MySQL

Databasanslutningar är en begränsad resurs, så att effektivt använda anslutningspooler för att komma åt Azure Database for MySQL optimerar prestanda. I avsnittet nedan beskrivs hur du använder anslutningspoolning eller beständiga anslutningar för att mer effektivt komma åt Azure Database för MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Komma åt databaser med hjälp av anslutningspooler (rekommenderas)

Hantering av databasanslutningar kan ha en betydande inverkan på programmets prestanda som helhet. För att optimera programmets prestanda bör målet vara att minska antalet gånger anslutningar upprättas och tid för att upprätta anslutningar i nyckelkodsökvägar. Vi rekommenderar starkt att du använder databasanslutningspooler eller beständiga anslutningar för att ansluta till Azure Database for MySQL. Databasanslutningspooler hanterar skapande, hantering och allokering av databasanslutningar. När ett program begär en databasanslutning prioriteras allokeringen av befintliga inaktiva databasanslutningar i stället för att skapa en ny anslutning. När programmet har slutförts med hjälp av databasanslutningen återställs anslutningen som förberedelse för vidare användning, i stället för att bara stängas.

För bättre illustration innehåller den här artikeln [en bit exempelkod](./sample-scripts-java-connection-pooling.md) som använder JAVA som exempel. Mer information finns i [Apache common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> Servern konfigurerar en timeout-mekanism för att stänga en anslutning som har varit inaktiv under en längre tid för att frigöra resurser. Var noga med att ställa in verifieringssystemet för att säkerställa effektiviteten av beständiga anslutningar när du använder dem. Mer information finns i [Konfigurera verifieringssystem på klientsidan för att säkerställa hur effektiva beständiga anslutningar är](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Komma åt databaser med hjälp av beständiga anslutningar (rekommenderas)

Begreppet beständiga anslutningar liknar anslutningspoolningen. Att ersätta korta anslutningar med beständiga anslutningar kräver endast mindre ändringar i koden, men det har en viktig effekt när det gäller att förbättra prestanda i många typiska programscenarier.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Komma åt databaser med hjälp av mekanismen för vänte- och återförsök med korta anslutningar

Om du har resursbegränsningar rekommenderar vi starkt att du använder databaspoolning eller beständiga anslutningar för att komma åt databaser. Om ditt program använder korta anslutningar och upplever anslutningsfel när du närmar dig den övre gränsen för antalet samtidiga anslutningar kan du prova mekanismen för att vänta och försöka igen. Du kan ställa in en lämplig väntetid med kortare väntetid efter det första försöket. Därefter kan du prova att vänta på händelser flera gånger.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Konfigurera verifieringsmekanismer i klienter för att bekräfta hur effektiva beständiga anslutningar är

Servern konfigurerar en timeout-mekanism för att stänga en anslutning som har varit inaktiv under en längre tid för att frigöra resurser. När klienten kommer åt databasen igen motsvarar den att skapa en ny anslutningsbegäran mellan klienten och servern. Konfigurera en verifieringsmekanism på klienten för att säkerställa hur effektiva anslutningarna är under processen att använda dem. Som visas i följande exempel kan du använda Tomcat JDBC-anslutningspoolning för att konfigurera den här verifieringsmekanismen.

Genom att ställa in parametern TestOnBorrow, när det finns en ny begäran, verifierar anslutningspoolen automatiskt effektiviteten av alla tillgängliga inaktiva anslutningar. Om en sådan anslutning är effektiv, dess direkt returnerade annars anslutningspoolen drar tillbaka anslutningen. Anslutningspoolen skapar sedan en ny effektiv anslutning och returnerar den. Den här processen säkerställer att databasen används effektivt. 

Information om de specifika inställningarna finns i [JDBC:s officiella introduktionsdokument för anslutningspoolen](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). Du måste främst ställa in följande tre parametrar: TestOnBorrow (inställt på true), ValidationQuery (inställt på SELECT 1) och ValidationQueryTimeout (inställd på 1). Den specifika exempelkoden visas nedan:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka anslutningsproblem till Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)
