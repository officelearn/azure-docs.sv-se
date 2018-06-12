---
title: Ansluta till Office 365 användare – Azure Logikappar | Microsoft Docs
description: 'Hantera användarprofiler med Office 365-användare REST API: er och Azure Logic Apps'
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: f030ac07dc1615c435c1a110836d7a03ab8a8546
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295616"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Kom igång med användare av Office 365-kopplingen
Ansluta till Office 365-användare få profiler och Sök efter användare. Med Office 365-användare kan du:

* Skapa ditt företag flödet som baseras på de data som du får från Office 365-användare. 
* Använd åtgärder som hämta direktrapporter, hämta en chef användarprofil och mycket mer. De här åtgärderna få svar och utdata gör tillgängligt för andra åtgärder. Till exempel få en persons direktrapporter ta den här informationen och uppdatera SQL Azure-databasen. 

Du kan komma igång genom att skapa en logikapp nu, se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Skapa en anslutning till Office 365-användare
När du lägger till den här anslutningen dina logic apps kan måste du logga in på ditt konto för Office 365-användare och Tillåt logikappar att ansluta till ditt konto.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

När du skapar anslutningen kan ange du egenskaper för Office 365-användare, t.ex. användar-ID. Den **REST API-referensen** i den här artikeln beskriver dessa egenskaper.

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/officeusers/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).