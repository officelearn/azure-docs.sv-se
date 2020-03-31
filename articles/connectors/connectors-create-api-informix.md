---
title: Ansluta till IBM Informix-databasen
description: Automatisera uppgifter och arbetsflöden som hanterar resurser som lagras i IBM Informix med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757976"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Hantera IBM Informix-databasresurser med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Informix-anslutningsappen](/connectors/informix/)kan du skapa automatiserade uppgifter och arbetsflöden som hanterar resurser i en IBM Informix-databas. Den här anslutningen innehåller en Microsoft-klient som kommunicerar med fjärr-Informix-serverdatorer över ett TCP/IP-nätverk, inklusive molnbaserade databaser som IBM Informix för Windows som körs i Azure-virtualisering och lokala databaser när du använder den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). Du kan ansluta till dessa Informix-plattformar och -versioner om de är konfigurerade för att stödja DRDA-klientanslutningar (Distributed Relational Database Architecture):

* IBM Informix 12,1
* IBM Informix 11,7

Det här avsnittet visar hur du använder kopplingen i en logikapp för att bearbeta databasåtgärder.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* För lokala databaser [hämtar och installerar du den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en lokal dator och skapar sedan en [Azure-datagatewayresurs i Azure-portalen](../logic-apps/logic-apps-gateway-connection.md).

* Logikappen där du behöver åtkomst till Din Informix-databas. Den här kopplingen innehåller endast åtgärder, så logikappen måste redan börja med en utlösare, till exempel [utlösaren Återkommande](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Lägga till en Informix-åtgärd

1. Öppna logikappen i Logic App Designer i [Azure-portalen,](https://portal.azure.com)om den inte redan är öppen.

1. Välj Nytt **steg**under det steg där du vill lägga till åtgärden Informix .

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över anslutningspilen. Markera plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange som filter `informix` i sökrutan. I åtgärdslistan väljer du den åtgärd du vill använda, till exempel:

   ![Välj den Informix-åtgärd som ska köras](./media/connectors-create-api-informix/select-informix-connector-action.png)

   Kopplingen innehåller dessa åtgärder som kör motsvarande databasåtgärder:

   * Hämta tabeller - Lista databastabeller med hjälp av ett `CALL` utdrag
   * Hämta rader - Läs alla rader `SELECT *` med hjälp av en sats
   * Hämta rad - Läs en `SELECT WHERE` rad med hjälp av ett uttryck
   * Lägga till en `INSERT` rad med hjälp av ett uttryck
   * Redigera en rad `UPDATE` med hjälp av ett uttryck
   * Ta bort en `DELETE` rad med hjälp av en sats

1. Om du uppmanas att ange anslutningsinformation för Informix-databasen följer du [stegen för att skapa anslutningen](#create-connection)och fortsätter sedan med nästa steg.

1. Ange information för den valda åtgärden:

   | Åtgärd | Beskrivning | Egenskaper och beskrivningar |
   |--------|-------------|-----------------------------|
   | **Hämta tabeller** | Lista databastabeller genom att köra en Informix CALL-sats. | Inget |
   | **Hämta rader** | Hämta alla rader i den angivna tabellen genom `SELECT *` att köra en Informix-sats. | **Tabellnamn**: Namnet på den Informix-tabell som du vill använda <p><p>Om du vill lägga till andra egenskaper i den här åtgärden markerar du dem i listan **Lägg till ny parameter.** Mer information finns i [kopplingens referensavsnitt](/connectors/informix/). |
   | **Hämta rad** | Hämta en rad från den angivna tabellen `SELECT WHERE` genom att köra en Informix-sats. | - **Tabellnamn**: Namnet på den Informix-tabell som du vill använda <br>- **Rad-ID:** Det unika ID:t för raden, till exempel`9999` |
   | **Infoga rad** | Lägg till en rad i den angivna Informix-tabellen genom att köra en Informix-sats. `INSERT` | - **Tabellnamn**: Namnet på den Informix-tabell som du vill använda <br>- **objekt**: Raden med de värden som ska läggas till |
   | **Uppdatera rad** | Ändra en rad i den angivna Informix-tabellen genom att köra en Informix-sats. `UPDATE` | - **Tabellnamn**: Namnet på den Informix-tabell som du vill använda <br>- **Rad-ID:** Det unika ID:t för raden som ska uppdateras, till exempel`9999` <br>- **Rad**: Raden med uppdaterade värden, till exempel`102` |
   | **Ta bort rad** | Ta bort en rad från den angivna Informix-tabellen genom att köra en Informix-sats. `DELETE` | - **Tabellnamn**: Namnet på den Informix-tabell som du vill använda <br>- **Rad-ID:** Det unika ID:t för raden som ska tas bort, till exempel`9999` |
   ||||

1. Spara din logikapp. Testa [logikappen](#test-logic-app) eller fortsätt att bygga logikappen.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Anslut till Informix

1. Om logikappen ansluter till en lokal databas väljer du **Anslut via lokal datagateway**.

1. Ange den här anslutningsinformationen och välj sedan **Skapa**.

   | Egenskap | JSON-egenskap | Krävs | Exempelvärde | Beskrivning |
   |----------|---------------|----------|---------------|-------------|
   | Anslutningsnamn | `name` | Ja | `informix-demo-connection` | Namnet som ska användas för anslutningen till Informix-databasen |
   | Server | `server` | Ja | - Moln:`informixdemo.cloudapp.net:9089` <br>- Lokalt:`informixdemo:9089` | TCP/IP-adressen eller aliaset som finns i IPv4- eller IPv6-format, följt av ett kolon och ett TCP/IP-portnummer |
   | Databas | `database` | Ja | `nwind` | DRDA-relationsdatabasnamnet (RDBNAM) eller Informix-databasnamnet (dbname). Informix accepterar en sträng på 128 byte. |
   | Autentisering | `authentication` | Endast lokalt | **Grundläggande** eller **Windows** (kerberos) | Den autentiseringstyp som krävs av Informix-databasen. Den här egenskapen visas bara när du väljer **Anslut via lokal datagateway**. |
   | Användarnamn | `username` | Inga | <*databas-användarnamn*> | Ett användarnamn för databasen |
   | lösenord | `password` | Inga | <*databas-lösenord*> | Ett lösenord för databasen |
   | Gateway | `gateway` | Endast lokalt | - <*Azure-prenumeration*> <br>- <*Azure-on-premises-data-gateway-resource*> | Azure-prenumerationen och Azure-resursnamnet för den lokala datagatewayen som du skapade i Azure-portalen. Egenskapen **Gateway** och underegenskaper visas bara när du väljer **Anslut via lokal datagateway**. |
   ||||||

   Ett exempel:

   * **Molndatabas**

     ![Anslutningsinformation för molndatabas](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Lokal databas**

     ![Lokal databasanslutningsinformation](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Spara din logikapp.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Testa logikappen

1. Välj **Kör**i verktygsfältet Logikappdesigner . När logikappen har körts kan du visa utdata från den körningen.

1. Välj **Översikt**på logikappens meny . Välj den senaste körningen under > **Sammanfattningskörningshistorik**i översiktsfönstret. **Summary**

1. Under **Logikappkörning**väljer du **Kör information**.

1. I åtgärdslistan väljer du åtgärden med de utdata som du vill visa, till exempel **Get_tables**.

   Om åtgärden lyckades markeras egenskapen **Status** som **Lyckades**.

1. Om du vill visa indata väljer du URL-länken under **Indatalänk.** Om du vill visa utdata väljer du URL-länken under länken **Utdatalänk.** Här är några exempel på utdata:

   * **Get_tables** visar en lista med tabeller:

     ![Utdata från åtgärden "Hämta tabeller"](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** visar en lista med rader:

     ![Utdata från åtgärden Hämta rader](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** visar den angivna raden:

     ![Utdata från åtgärden "Hämta rad"](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** visar den nya raden:

     ![Utdata från åtgärden Infoga rad](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** visar den uppdaterade raden:

     ![Utdata från åtgärden "Uppdatera rad"](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** visar den borttagna raden:

     ![Utdata från åtgärden "Ta bort rad"](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Anslutningsspecifik information

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens Swagger-beskrivning, läser du [kopplingens referenssida](/connectors/informix/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](apis-list.md)
