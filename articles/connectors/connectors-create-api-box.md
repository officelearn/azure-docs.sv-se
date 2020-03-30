---
title: Anslut till ruta
description: Automatisera uppgifter och arbetsflöden som skapar och hanterar filer i Box med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666779"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Skapa och hantera filer i Box med hjälp av Azure Logic Apps

Den här artikeln visar hur du kan skapa och hantera dina filer i Box inifrån en logikapp med Box-kopplingen. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för att hantera dina filer och andra åtgärder, till exempel:

* Bygg upp ditt affärsflöde baserat på de data du får från Box.

* Utlösa automatiserade uppgifter och arbetsflöde när en fil skapas eller uppdateras.

* Kör en åtgärd som kopierar en fil eller tar bort en fil.

  När dessa åtgärder får ett svar gör de utdata tillgängliga för andra åtgärder. 
  När en fil till exempel ändras i Box kan du skicka filen i e-post med Office 365.

## <a name="prerequisites"></a>Krav

* Ett [Box-konto](https://www.box.com/home)

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Logikappen där du vill komma åt ditt Box-konto. Om du vill starta logikappen med en Box-utlösare behöver du en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och begränsningar, enligt beskrivningen i kopplingens OpenAPI-fil (tidigare Swagger) finns på [kopplingens referenssida](/connectors/box/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)