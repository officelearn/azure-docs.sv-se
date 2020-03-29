---
title: Ansluta till Oracle-databasen
description: Infoga och hantera poster med REST-API:er för Oracle Database och Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/29/2017
tags: connectors
ms.openlocfilehash: 99abd48bde97c2a2e085688cdfbb365e5e4cfd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789434"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Komma igång med Oracle Database-anslutningen

Med Oracle Database-anslutningsappen skapar du organisationsarbetsflöden som använder data i den befintliga databasen. Den här anslutningen kan ansluta till en lokal Oracle-databas eller en virtuell Azure-dator med Oracle-databas installerad. Med den här kopplingen kan du:

* Skapa arbetsflödet genom att lägga till en ny kund i en kunddatabas eller uppdatera en order i en orderdatabas.
* Använd åtgärder för att hämta en rad med data, infoga en ny rad och till och med ta bort. När en post till exempel skapas i Dynamics CRM Online (en utlösare) infogar du sedan en rad i en Oracle-databas (en åtgärd). 

Den här artikeln visar hur du använder Oracle Database-anslutningen i en logikapp.

## <a name="prerequisites"></a>Krav

* Oracle-versioner som stöds: 
    * Oracle 9 och senare
    * Oracle klientprogramvara 8.1.7 och senare

* Installera den lokala datagatewayen. [I anslutning till lokala data från logikappar](../logic-apps/logic-apps-gateway-connection.md) visas stegen. Gatewayen krävs för att ansluta till en lokal Oracle-databas eller en Azure VM med Oracle DB installerat. 

    > [!NOTE]
    > Den lokala datagatewayen fungerar som en brygga och tillhandahåller en säker dataöverföring mellan lokala data (data som inte finns i molnet) och dina logikappar. Samma gateway kan användas med flera tjänster och flera datakällor.Därför kanske du bara behöver installera gatewayen en gång.

* Installera Oracle-klienten på datorn där du installerade den lokala datagatewayen.Var noga med att installera 64-bitars Oracle-dataprovidern för .NET från Oracle:  

  [64-bitars ODAC 12c version 4 (12.1.0.2.4) för Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Om Oracle-klienten inte är installerad uppstår ett fel när du försöker skapa eller använda anslutningen. Se de vanligaste felen i den här artikeln.


## <a name="add-the-connector"></a>Lägg till kopplingen

> [!IMPORTANT]
> Den här kopplingen har inga utlösare. Den har bara handlingar. Så när du skapar din logikapp, lägga till en annan utlösare för att starta din logikapp, till exempel **Schema - Återkommande**eller Begär / Svar - **Svar**. 

1. Skapa en tom logikapp i [Azure-portalen.](https://portal.azure.com)

2. I början av logikappen väljer du **utlösaren Begär/svar - Begäran:** 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Välj **Spara**. När du sparar genereras en url för begäran automatiskt. 

4. Välj **Nytt steg** och välj sedan **Lägg till en åtgärd**. Skriv `oracle` in för att se tillgängliga åtgärder: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Detta är också det snabbaste sättet att se utlösare och åtgärder som är tillgängliga för alla kopplingar. Skriv in en del av `oracle`kopplingsnamnet, till exempel . Designern listar eventuella utlösare och eventuella åtgärder. 

5. Välj en av åtgärderna, till exempel **Oracle Database - Get row**. Välj **Anslut via lokal datagateway**. Ange Oracle-servernamn, autentiseringsmetod, användarnamn, lösenord och välj gateway:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. När du har anslutit väljer du en tabell i listan och anger rad-ID:et till tabellen. Du måste känna till identifieraren i tabellen. Om du inte vet kontaktar du Oracle DB-administratören `select * from yourTableName`och hämtar utdata från . Detta ger dig den identifierbara information du behöver för att gå vidare.

    I följande exempel returneras jobbdata från en personaldatabas: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. I nästa steg kan du använda någon av de andra kopplingarna för att skapa arbetsflödet. Om du vill testa att hämta data från Oracle skickar du ett e-postmeddelande med Oracle-data med hjälp av en av skicka e-postanslutningar, till exempel Office 365 eller Gmail. Använd de dynamiska tokens från Oracle-tabellen för att skapa `Subject` och `Body` för din e-post:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Spara** logikappen och välj sedan **Kör**. Stäng designern och titta på körningarnas historik för statusen. Om det misslyckas markerar du den misslyckade meddelanderaden. Designern öppnas och visar vilket steg som misslyckades och visar även felinformationen. Om det lyckas bör du få ett e-postmeddelande med den information du lagt till.


### <a name="workflow-ideas"></a>Idéer om arbetsflöde

* Du vill övervaka hashtaggen #oracle och placera tweetsen i en databas så att de kan frågas och användas i andra program. I en logikapp `Twitter - When a new tweet is posted` lägger du till utlösaren och anger **hashtaggen #oracle.** Lägg sedan `Oracle Database - Insert row` till åtgärden och välj tabellen:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Meddelanden skickas till en servicebusskö. Du vill hämta dessa meddelanden och placera dem i en databas. Lägg till utlösaren `Service Bus - when a message is received in a queue` i en logikapp och välj kön. Lägg sedan `Oracle Database - Insert row` till åtgärden och välj tabellen:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Vanliga fel

#### <a name="error-cannot-reach-the-gateway"></a>**Fel:** Det går inte att nå gatewayen

**Orsak**: Den lokala datagatewayen kan inte ansluta till molnet. 

**Begränsning:** Kontrollera att din gateway körs på den lokala datorn där du installerade den och att den kan ansluta till internet.Vi rekommenderar att du inte installerar gatewayen på en dator som kan vara avstängd eller strömsparläge.Du kan också starta om den lokala datagatewaytjänsten (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Fel:** Leverantören som används är föråldrad: "System.Data.OracleClient kräver Oracle-klientprogram version 8.1.7 eller senare.". Se [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) till att installera den officiella leverantören.

**Orsak**: Oracle-klienten SDK är inte installerad på datorn där den lokala datagatewayen körs.  

**Lösning**: Hämta och installera Oracle-klienten SDK på samma dator som den lokala datagatewayen.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Fel:** Tabellen "[Tabellnamn]" definierar inga nyckelkolumner

**Orsak**: Tabellen har ingen primärnyckel.  

**Lösning**: Oracle Database-kopplingen kräver att en tabell med en primärnyckelkolumn används.

#### <a name="currently-not-supported"></a>Stöds för närvarande inte

* Vyer 
* Alla tabeller med sammansatta nycklar
* Kapslade objekttyper i tabeller
 
## <a name="connector-specific-details"></a>Anslutningsspecifik information

Visa alla utlösare och åtgärder som definierats i skryt, och se även eventuella gränser i [anslutningsinformationen](/connectors/oracle/). 

## <a name="get-some-help"></a>Få lite hjälp

[Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) är ett bra ställe att ställa frågor, svara på frågor och se vad andra Logic Apps-användare gör. 

Du kan hjälpa till att förbättra Logic Apps och [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish)connectors genom att rösta och skicka in dina idéer på . 


## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)och utforska tillgängliga kopplingar i Logic Apps at [API:er list](apis-list.md).
