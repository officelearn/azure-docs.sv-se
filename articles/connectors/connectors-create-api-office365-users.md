---
title: Ansluta till Office 365-användare
description: Automatisera uppgifter och arbetsflöden som hämtar och hanterar profiler i Office 365-användares profiler med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666864"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Hämta och hantera profiler i Office 365-användare med hjälp av Azure Logic Apps

Anslut till Office 365-användare för att få profiler, söka användare med mera. Med Office 365-användare kan du:

* Skapa ditt affärsflöde baserat på de data du får från Office 365-användare. 
* Använd åtgärder som hämtar direktrapporter, hämta en chefs användarprofil med mera. Dessa åtgärder får ett svar och gör sedan utdata tillgängliga för andra åtgärder. Hämta till exempel en persons direktrapporter och ta sedan den här informationen och uppdatera en SQL Azure-databas. 

Du kan komma igång genom att skapa en logikapp nu, se [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Skapa en anslutning till Office 365-användare

När du lägger till den här anslutningsappen i logikapparna måste du logga in på ditt Office 365-användarkonto så att Azure Logic Apps kan ansluta till ditt konto.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

När du har skapat anslutningen anger du egenskaperna för Office 365-användare, till exempel användar-ID. **REST API-referensen** i den här artikeln beskriver dessa egenskaper.

## <a name="connector-specific-details"></a>Anslutningsspecifik information

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens Swagger-beskrivning, läser du [kopplingens referenssida](/connectors/officeusers/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](apis-list.md)