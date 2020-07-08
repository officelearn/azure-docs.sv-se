---
title: Anslut till Office 365-användare
description: Automatisera uppgifter och arbets flöden som hämtar och hanterar profiler i Office 365-användare profiler med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 0361d42ea614b394142e32a9193ab1d48d1a4a6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84194239"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Hämta och hantera profiler i Office 365-användare med hjälp av Azure Logic Apps

Anslut till Office 365-användare för att hämta profiler, Sök efter användare med mera. Med Office 365-användare kan du:

* Skapa ditt affärs flöde baserat på de data du får från Office 365-användare. 
* Använd åtgärder som hämtar direkt rapporter, hämta en chefs användar profil med mera. De här åtgärderna får ett svar och gör sedan resultatet tillgängligt för andra åtgärder. Du kan till exempel hämta en persons direkt rapporter och sedan ta den här informationen och uppdatera en databas i Azure SQL Database. 

Du kan komma igång genom att skapa en Logic app nu, se [skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Skapa en anslutning till Office 365-användare

När du lägger till den här anslutningen i Logi Kap par måste du logga in på ditt konto för Office 365-användare så att Azure Logic Apps kan ansluta till ditt konto.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

När du har skapat anslutningen anger du Office 365-användarens egenskaper, t. ex. användar-ID. **REST API-referensen** i den här artikeln beskriver dessa egenskaper.

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens Swagger beskrivning, finns på [kopplingens referens sida](/connectors/officeusers/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](apis-list.md)