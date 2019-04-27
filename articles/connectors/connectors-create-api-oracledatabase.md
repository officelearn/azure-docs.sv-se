---
title: Ansluta till Oracle Database – Azure Logic Apps | Microsoft Docs
description: 'Infoga och hanterar journaler med hjälp av Oracle Database REST API: er och Azure Logic Apps'
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/29/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 06f65aef203b4f0d765f21b9d17b90081de85c94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453647"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Kom igång med Oracle Database-anslutning

Med Oracle Database-anslutningen kan skapa du organisationens arbetsflöden som använder data i den befintliga databasen. Den här anslutningen kan ansluta till en lokal Oracle-databas eller Azure-datorer med Oracle-databas som är installerad. Med den här anslutningen kan du:

* Skapa ditt arbetsflöde genom att lägga till en ny kund en kunder-databas eller uppdaterar en order i en order-databas.
* Använda åtgärder för att hämta en rad med data, infoga en ny rad och även ta bort. Till exempel när en post skapas i Dynamics CRM Online (en utlösare), sedan infoga en rad i en Oracle-databas (en åtgärd). 

Den här artikeln visar hur du använder Oracle Database-anslutningen i en logikapp.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Oracle-versioner som stöds: 
    * Oracle 9 och senare
    * Oracle klientprogramvara 8.1.7 och senare

* Installera den lokala datagatewayen. [Ansluta till lokala data från logikappar](../logic-apps/logic-apps-gateway-connection.md) innehåller stegen. Gatewayen krävs för att ansluta till en lokal Oracle-databas eller en virtuell Azure-dator med Oracle DB har installerats. 

    > [!NOTE]
    > Den lokala datagatewayen fungerar som en brygga och ger en säker dataöverföring mellan lokala data (data som inte är i molnet) och dina logic apps. Samma gateway kan användas med flera tjänster och flera datakällor. Därför kan du bara behöva installera gatewayen på en gång.

* Installera Oracle-klienten på datorn där du har installerat den lokala datagatewayen. Tänk på att installera 64-bitars Oracle Data Provider för .NET från Oracle:  

  [64-bitars ODAC 12c version 4 (12.1.0.2.4) för Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Om Oracle-klienten inte är installerat, uppstår ett fel vid försök att skapa eller använda anslutningen. Se vanliga fel i den här artikeln.


## <a name="add-the-connector"></a>Lägg till anslutningsappen

> [!IMPORTANT]
> Den här anslutningen har inte utlösare. Den har endast åtgärder. Så när du skapar din logikapp kan lägga till en annan utlösare för att starta logikappen, till exempel **schema – återkommande**, eller **begäran / svar - svar**. 

1. I den [Azure-portalen](https://portal.azure.com), skapa en tom logikapp.

2. I början av din logikapp, Välj den **begäran / svar - begäran** utlösare: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Välj **Spara**. När du sparar genereras automatiskt en begärd URL. 

4. Välj **nytt steg**, och välj **Lägg till en åtgärd**. Skriv i `oracle` att se tillgängliga åtgärder: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Det här är det snabbaste sättet att se utlösare och åtgärder som är tillgängliga för alla anslutningar. Ange i en del av kopplingsnamnet `oracle`. Designern visar en lista över alla utlösare och åtgärder. 

5. Välj en av åtgärderna som **Oracle Database - Get-raden**. Välj **Anslut via lokal datagateway**. Ange Oracle-servernamnet, autentiseringsmetod, användarnamn, lösenord och välj gatewayen:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. När du är ansluten, markera en tabell i listan och ange rad-ID i tabellen. Du behöver veta identifierare i tabellen. Om du inte vet, kontakta administratören Oracle DB och hämta utdata från `select * from yourTableName`. Detta ger dig identifierbar information du behöver för att fortsätta.

    I följande exempel returneras jobbdata från en databas för personalfrågor: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. I nästa steg kan du använda någon av de andra anslutningsapparna för att bygga ditt arbetsflöde. Om du vill testa hämta data från Oracle, skicka sedan dig själv ett e-postmeddelande med Oracle-data med någon av skicka e-anslutningsappar, sådana Office 365- eller Gmail. Använd dynamisk token från Oracle-tabell för att skapa den `Subject` och `Body` av din e-post:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Spara** din logikapp och välj sedan **kör**. Stäng designern och titta på körningshistorik för status. Välj raden misslyckade meddelande om det misslyckas. Designer öppnas och visar vilka steg misslyckades och visar även information om felet. Om det lyckas, bör du få ett e-postmeddelande med information som du har lagt till.


### <a name="workflow-ideas"></a>Arbetsflödet idéer

* Du vill övervaka #oracle hashtaggen och placerar tweets i en databas så att de kan efterfrågas och användas i andra program. I en logikapp, lägger du till den `Twitter - When a new tweet is posted` utlösa och ange den **#oracle** hashtagg. Lägg sedan till den `Oracle Database - Insert row` åtgärd, och välj din tabell:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Meddelanden skickas till en Service Bus-kö. Du vill få dessa meddelanden och placerar dem i en databas. I en logikapp, lägger du till den `Service Bus - when a message is received in a queue` utlösa och väljer du kö. Lägg sedan till den `Oracle Database - Insert row` åtgärd, och välj din tabell:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Vanliga fel

#### <a name="error-cannot-reach-the-gateway"></a>**Fel**: Det går inte att nå en Gateway

**Orsak**: Den lokala datagatewayen är inte kan ansluta till molnet. 

**Minskning**: Kontrollera att din gateway körs på den lokala datorn där du har installerat och att den kan ansluta till internet.  Vi rekommenderar att du inte installerar gatewayen på en dator som kan stängas av eller strömsparläge. Du kan också starta om den lokala data gateway-tjänsten (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Fel**: Providern som används är föråldrad: ' System.Data.OracleClient kräver Oracle klientprogramversionen 8.1.7 eller senare ”. Se [ https://go.microsoft.com/fwlink/p/?LinkID=272376 ](https://go.microsoft.com/fwlink/p/?LinkID=272376) att installera den officiella providern.

**Orsak**: Oracle-klientens SDK är inte installerad på datorn där den lokala datagatewayen körs.  

**Upplösning**: Ladda ned och installera Oracle-klientens SDK på samma dator som den lokala datagatewayen.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Fel**: Tabellen ”[tabellnamn]” definierar inga nyckelkolumner

**Orsak**: Tabellen har inte någon primärnyckel.  

**Upplösning**: Oracle Database-anslutningen kräver att en tabell med en primärnyckelkolumn.

#### <a name="currently-not-supported"></a>För närvarande inte

* Vyer 
* Valfri tabell med sammansatta nycklar
* Kapslade objekttyper i tabeller
 
## <a name="connector-specific-details"></a>Information om specifika

Visa alla utlösare och åtgärder som definierats i swagger och får även eventuella gränser i den [anslutningsinformationen](/connectors/oracle/). 

## <a name="get-some-help"></a>Få hjälp

Den [Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) är ett bra ställe att ställa frågor, besvara frågor och se vad andra Logic Apps-användare gör. 

Du kan hjälpa att förbättra Logic Apps och anslutningsapparna genom att rösta och skicka dina idéer på [ https://aka.ms/logicapps-wish ](https://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md), och utforska tillgängliga anslutningsappar i Logic Apps på [API: er lista](apis-list.md).
