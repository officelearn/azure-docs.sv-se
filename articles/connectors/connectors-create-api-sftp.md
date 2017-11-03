---
title: "Lär dig hur du använder SFTP-anslutningen i dina logic apps | Microsoft Docs"
description: "Skapa logikappar med Azure App service. Anslut till SFTP API för att skicka och ta emot. Du kan utföra olika åtgärder så som att skapa, uppdatera, hämta eller ta bort filer."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 31253d8daee1581167a96a20ba8ad529a04b3e92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-sftp-connector"></a>Kom igång med SFTP-koppling
Använda SFTP-anslutningen för att komma åt en SFTP-konto för att skicka och ta emot filer. Du kan utföra olika åtgärder så som att skapa, uppdatera, hämta eller ta bort filer.  

Att använda [alla anslutningar](apis-list.md), måste du först skapa en logikapp. Du kan komma igång med [att skapa en logikapp nu](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sftp"></a>Anslut till SFTP
Innan din logikapp kan komma åt någon tjänst, måste du först skapa en *anslutning* till tjänsten. En [anslutning](connectors-overview.md) tillhandahåller anslutningen mellan en logikapp och en annan tjänst.  

### <a name="create-a-connection-to-sftp"></a>Skapa en anslutning till SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Använd en SFTP-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. [Mer information om utlösare](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

I det här exemplet i **SFTP - när en fil har lagts till eller ändrats** utlösare används för att initiera en logik app arbetsflödet när en fil har lagts till eller ändras på en SFTP-server. Du också lägga till ett villkor som kontrollerar du innehållet i den nya eller ändrade filen och gör ett beslut att extrahera filen om innehållet visar ska extraheras innan du använder innehållet. Slutligen lägger du till en åtgärd för att extrahera innehållet i en fil och placerar du extraherade innehållet i en mapp på SFTP-servern. 

Du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar order i ett enterprise-exempel.  Du kan sedan använda åtgärden SFTP-koppling som **hämta filinnehåll**, för att hämta innehållet i ordningen för vidare bearbetning och lagring i en order-databas.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Lägg till ett villkor
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Använd en SFTP-åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Mer information om åtgärder](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/sftpconnector/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).