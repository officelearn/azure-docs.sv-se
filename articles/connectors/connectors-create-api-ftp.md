---
title: Ansluter till FTP-server - Azure Logic Apps | Microsoft Docs
description: Skapa, övervaka och hantera filer på en FTP-server med Azure Logikappar
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 07/22/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 983e8f84e6e44bc9e5de5f4e7fff361b92b316c9
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295701"
---
# <a name="get-started-with-the-ftp-connector"></a>Kom igång med FTP-anslutningen
Använd FTP-anslutningen för att övervaka, hantera och skapa filer på en FTP-server. 

Att använda [alla anslutningar](apis-list.md), måste du först skapa en logikapp. Du kan komma igång med [att skapa en logikapp nu](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-ftp"></a>Anslut till FTP
Innan din logikapp kan komma åt någon tjänst, måste du först skapa en *anslutning* till tjänsten. En [anslutning](connectors-overview.md) tillhandahåller anslutningen mellan en logikapp och en annan tjänst.  

### <a name="create-a-connection-to-ftp"></a>Skapa en anslutning till FTP
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>Använda en FTP-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. [Mer information om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!IMPORTANT]
> FTP-anslutningen kräver en FTP-server som är tillgänglig från Internet och är konfigurerad för att fungera med PASSIVT läge. FTP-anslutningen är också **inte kompatibel med implicit FTPS (FTP över SSL)**. FTP-anslutningen har endast stöd för explicit FTPS (FTP över SSL).  
> 
> 

I det här exemplet jag visar hur du använder den **FTP - när en fil har lagts till eller ändrats** utlöser ett arbetsflöde för logik app att starta när en fil har lagts till eller ändrats på en FTP-server. Du kan använda den här utlösaren för att övervaka en FTP-mapp för nya filer som representerar order från kunder i ett enterprise-exempel.  Du kan sedan använda en åtgärd för FTP-anslutningen som **hämta filinnehåll** att hämta innehållet i ordningen för vidare bearbetning och lagring i order-databasen.

1. Ange *ftp* i sökrutan på logic apps designer väljer den **FTP - när en fil har lagts till eller ändrats** utlösare   
   ![FTP-utlösarbild 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   Den **när en fil har lagts till eller ändrats** kontrollen öppnas  
   ![Bild 2 till FTP-utlösare](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Välj den **...**  finns på höger sida av kontrollen. Då öppnas väljarkontrollen mapp  
   ![Bild 3 till FTP-utlösare](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Välj den **>** (HÖGERPIL) och bläddra till mappen som du vill övervaka för nya eller ändrade filer. Välj mapp och mappen visas nu i meddelande i **mappen** kontroll.  
   ![Bild 4 till FTP-utlösare](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

Din logikapp har nu konfigurerats med en utlösare som börjar den andra utlösare och åtgärder i arbetsflödet körs när en fil ändras eller skapas i mappen specifika FTP. 

> [!NOTE]
> Det måste innehålla minst en utlösare och en åtgärd för en logikapp ska fungera. Följ stegen i nästa avsnitt för att lägga till en åtgärd.  
> 
> 

## <a name="use-a-ftp-action"></a>Använda en FTP-åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Mer information om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Nu när du har lagt till en utlösare, Följ dessa steg för att lägga till en åtgärd som kommer att hämta innehållet i den nya eller ändrade filen som hittas av utlösaren.    

1. Välj **+ nytt steg** att lägga till den åtgärden för att hämta innehållet i filen på FTP-servern  
2. Välj den **lägga till en åtgärd** länk.  
   ![Bild 1 till FTP-åtgärd](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Ange *FTP* att söka efter alla åtgärder som rör FTP.
4. Välj **FTP - hämta filinnehåll** som åtgärden som ska vidtas när en nya eller ändrade filen finns i FTP-mappen.      
   ![Bild 2 till FTP-åtgärd](./media/connectors-create-api-ftp/ftp-action-2.png)  
   Den **hämta filinnehåll** styra öppnas. **Obs**: uppmanas du att godkänna din logikapp för att komma åt ditt konto för FTP-servern om du inte har gjort det tidigare.  
   ![Bild 3 till FTP-åtgärd](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Välj den **filen** kontrollen (det tomma utrymmet som finns under ** filen ***). Här kan använda du någon av de olika egenskaperna från den nya eller ändrade filen som hittas på FTP-servern.  
6. Välj den **filen innehåll** alternativet.  
   ![Bild 4 till FTP-åtgärd](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. Kontrollen har uppdaterats, vilket betyder att den **FTP - hämta filinnehåll** åtgärd får den *filen innehåll* av nya eller ändrade filen på FTP-servern.      
   ![Bild 5 till FTP-åtgärd](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Spara ditt arbete och sedan lägga till en fil i mappen FTP för att testa ditt arbetsflöde.    

Logikappen har nu konfigurerats med en utlösare för att övervaka en mapp på en FTP-server och starta arbetsflödet när den hittar en ny fil eller en fil på FTP-servern. 

Logikappen har också konfigurerats med en åtgärd för att hämta innehållet i den nya eller ändrade filen.

Du kan nu lägga till en annan åtgärd som den [SQL Server - infogningsraden](connectors-create-api-sqlazure.md) åtgärder för att infoga innehållet i den nya eller ändrade filen i en SQL-databastabell.  

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/ftpconnector/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)

