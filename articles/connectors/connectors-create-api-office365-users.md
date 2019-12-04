---
title: Anslut till Office 365-användare
description: 'Hantera användar profiler med Office 365-användare REST-API: er och Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: addb64a9b43c51af8363caa6f0fb3261a618e893
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789535"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Kom igång med Office 365-användarens koppling
Anslut till Office 365-användare för att hämta profiler, Sök efter användare med mera. Med Office 365-användare kan du:

* Skapa ditt affärs flöde baserat på de data du får från Office 365-användare. 
* Använd åtgärder som hämtar direkt rapporter, hämta en chefs användar profil med mera. De här åtgärderna får ett svar och gör sedan resultatet tillgängligt för andra åtgärder. Du kan till exempel hämta en persons direkt rapporter och sedan ta den här informationen och uppdatera en SQL Azure databas. 

Du kan komma igång genom att skapa en Logic app nu, se [skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Skapa en anslutning till Office 365-användare
När du lägger till den här anslutningen i dina Logi Kap par måste du logga in på ditt konto för Office 365-användare och tillåta att Logi Kap par ansluter till ditt konto.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

När du har skapat anslutningen anger du Office 365-användarens egenskaper, t. ex. användar-ID. **REST API-referensen** i den här artikeln beskriver dessa egenskaper.

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Visa eventuella utlösare och åtgärder som definierats i Swagger och se även eventuella begränsningar i [anslutnings informationen](/connectors/officeusers/).

## <a name="more-connectors"></a>Fler anslutningar
Gå tillbaka till [listan med API: er](apis-list.md).