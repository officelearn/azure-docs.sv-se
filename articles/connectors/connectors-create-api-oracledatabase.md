---
title: Ansluta till Oracle-databasen - Azure Logikappar | Microsoft Docs
description: 'Infoga och hantera poster med Oracle Database REST API: er och Azure Logic Apps'
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 03/29/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: be099cb8c2a36fa8bc09a5a30fca1785dd3933db
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609564"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Kom igång med kopplingen Oracle-databas

Med kopplingen Oracle-databas kan skapa du organisationens arbetsflöden som använder data i den befintliga databasen. Den här anslutningen kan ansluta till en lokal Oracle-databas eller en virtuell Azure-dator med Oracle-databas som är installerad. Med den här anslutningen kan du:

* Skapa ditt arbetsflöde genom att lägga till en ny kund till en databas för kunder eller uppdatera en ordning i en order-databas.
* Använd åtgärder för att hämta en rad med data, infoga en ny rad och även ta bort. Till exempel när en post har skapats i Dynamics CRM Online (en utlösare), sedan infoga en rad i en Oracle-databas (en åtgärd). 

Den här artikeln visar hur du använder kopplingen Oracle-databas i en logikapp.

## <a name="prerequisites"></a>Förutsättningar

* Oracle-versioner stöds: 
    * Oracle 9 och senare
    * Oracle-klientprogramvaran 8.1.7 eller senare

* Installera den lokala datagatewayen. [Ansluta till lokala data från logikappar](../logic-apps/logic-apps-gateway-connection.md) innehåller stegen. En gateway krävs för att ansluta till en lokal Oracle-databas eller en virtuell Azure-dator med Oracle DB installerat. 

    > [!NOTE]
    > Lokala datagateway fungerar som en brygga och ger en säker dataöverföring mellan lokala data (data som inte är i molnet) och dina logic apps. Samma gateway kan användas med flera tjänster och flera datakällor. Så kan du bara behöver installera gatewayen en gång.

* Installera Oracle-klienten på datorn där du installerade lokala datagateway. Glöm inte att installera 64-bitars Oracle Data Provider för .NET från Oracle:  

  [64-bitars ODAC 12c version 4 (12.1.0.2.4) för Windows x64](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Om Oracle-klienten inte är installerad, inträffar ett fel vid försök att skapa eller använda anslutningen. Finns de vanliga fel i den här artikeln.


## <a name="add-the-connector"></a>Lägg till anslutningen

> [!IMPORTANT]
> Den här anslutningen har inte utlösare. Det finns endast åtgärder. Så när du skapar din logikapp, lägga till en annan utlösare för att starta din logikapp, till exempel **schema - upprepning**, eller **begäranden / svar - svar**. 

1. I den [Azure-portalen](https://portal.azure.com), skapa en tom logikapp.

2. I början av din logikapp, Välj den **begäranden / svar - begäran** utlösare: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Välj **Spara**. När du sparar genereras automatiskt en begärd URL. 

4. Välj **nytt steg**, och välj **lägga till en åtgärd**. Skriv i `oracle` att se tillgängliga åtgärder: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Detta är det snabbaste sättet att se utlösare och åtgärder som är tillgängliga för alla anslutningar. Ange i en del av namnet på anslutningen, till exempel `oracle`. Designern listar alla utlösare och åtgärder. 

5. Välj en av åtgärderna som **Oracle-databasen - Get-raden**. Välj **Anslut via lokala datagateway**. Ange servernamnet för Oracle, autentiseringsmetod, användarnamn, lösenord och välj gatewayen:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. När du är ansluten, markera en tabell från listan och ange rad-ID för tabellen. Du behöver veta identifierare i tabellen. Om du inte vet administratören Oracle-databas och hämta utdata från `select * from yourTableName`. Detta ger dig identifierbar information du behöver för att fortsätta.

    I följande exempel returneras jobbdata från en databas för personal: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Du kan använda någon av de andra kopplingarna för att skapa arbetsflödet i den här nästa steg. Om du vill testa hämta data från Oracle sedan skicka dig ett e-postmeddelande med Oracle data med hjälp av en skicka e-kopplingar, sådan Office 365 eller Gmail. Använd dynamiska token från Oracle-tabell för att skapa den `Subject` och `Body` av din e-post:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Spara** din logikapp och välj sedan **kör**. Stäng designern och titta på körs historiken för status. Välj raden meddelande om det misslyckas. Designer öppnas och visar vilka steg misslyckades och visar även information om felet. Om det lyckas, bör du få ett e-postmeddelande med information som du har lagt till.


### <a name="workflow-ideas"></a>Arbetsflöde för uppslag

* Du vill övervaka #oracle hashtaggar och placera tweets i en databas så att de kan efterfrågas och används i andra program. Lägg till i en logikapp den `Twitter - When a new tweet is posted` utlösa och ange den **#oracle** hashtaggar. Lägg sedan till den `Oracle Database - Insert row` åtgärder, och välj din tabell:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Meddelanden skickas till en Service Bus-kö. Du vill hämta dessa meddelanden och placera dem i en databas. Lägg till i en logikapp den `Service Bus - when a message is received in a queue` utlösa och Välj kön. Lägg sedan till den `Oracle Database - Insert row` åtgärder, och välj din tabell:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Vanliga fel

#### <a name="error-cannot-reach-the-gateway"></a>**Fel**: Det går inte att nå gatewayen

**Orsak**: lokala datagateway kan inte ansluta till molnet. 

**Minskning**: Kontrollera att din gateway körs på den lokala datorn där du installerade dem och att den kan ansluta till internet.  Vi rekommenderar att du inte installerar gateway på en dator som kan stängas av eller i viloläge. Du kan också starta om tjänsten lokala data gateway (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Fel**: den leverantör som används är föråldrad: ' System.Data.OracleClient kräver Oracle klientprogramversionen 8.1.7 eller senare.'. Se [ https://go.microsoft.com/fwlink/p/?LinkID=272376 ](https://go.microsoft.com/fwlink/p/?LinkID=272376) att installera den officiella leverantören.

**Orsak**: Oracle-klient-SDK inte är installerad på datorn där lokala datagateway körs.  

**Lösning**: hämta och installera Oracle klient-SDK på samma dator som lokala datagateway.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Fel**: tabellen [tabellnamn] definierar inte några viktiga kolumner

**Orsak**: tabellen inte har någon primärnyckel.  

**Lösning**: connector i Oracle-databasen kräver att en tabell med en primärnyckelkolumn används.

#### <a name="currently-not-supported"></a>För närvarande stöds inte

* Vyer och lagrade procedurer 
* En tabell med sammansatta nycklar
* Kapslade objekttyper i tabeller
 
## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/oracle/). 

## <a name="get-some-help"></a>Få hjälp

Den [Logikappar i Azure-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) är det bra att ställa frågor, besvara frågor och se vad andra användare med Logic Apps gör. 

Du kan förbättra Logic Apps och kopplingar genom röstning och skicka dina idéer på [ http://aka.ms/logicapps-wish ](http://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md), och utforska de tillgängliga kopplingarna i Logic Apps på [API: er listan](apis-list.md).
