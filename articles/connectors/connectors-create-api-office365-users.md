---
title: Anslut till Office 365-användare
description: Automatisera uppgifter och arbets flöden som hämtar och hanterar profiler i Office 365-användare profiler med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666864"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Hämta och hantera profiler i Office 365-användare med hjälp av Azure Logic Apps

Anslut till Office 365-användare för att hämta profiler, Sök efter användare med mera. Med Office 365-användare kan du:

* Skapa ditt affärs flöde baserat på de data du får från Office 365-användare. 
* Använd åtgärder som hämtar direkt rapporter, hämta en chefs användar profil med mera. De här åtgärderna får ett svar och gör sedan resultatet tillgängligt för andra åtgärder. Du kan till exempel hämta en persons direkt rapporter och sedan ta den här informationen och uppdatera en SQL Azure databas. 

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