---
title: Anslut till Box
description: Automatisera aktiviteter och arbets flöden som skapar och hanterar filer i Box genom att använda Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75666779"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Skapa och hantera filer i Box genom att använda Azure Logic Apps

I den här artikeln visas hur du kan skapa och hantera dina filer i Box från en Logic-app med Box Connector. På så sätt kan du skapa Logi Kap par som automatiserar uppgifter och arbets flöden för att hantera filer och andra åtgärder, till exempel:

* Skapa ditt affärs flöde baserat på de data du får från Box.

* Utlös automatiserade uppgifter och arbets flöde när en fil skapas eller uppdateras.

* Kör en åtgärd som kopierar en fil eller tar bort en fil.

  När de här åtgärderna får svar gör de utdata tillgängliga för andra åtgärder. 
  När en fil ändras i rutan kan du till exempel skicka filen i ett e-postmeddelande med Office 365.

## <a name="prerequisites"></a>Krav

* Ett [Box-konto](https://www.box.com/home)

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Den Logic app där du vill komma åt ditt Box-konto. Om du vill starta din Logic app med en box-utlösare behöver du en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Om du inte har använt Logic Apps igen kan du läsa mer [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och gränser, som beskrivs i filens OpenAPI-fil (tidigare Swagger), finns på [kopplingens referens sida](/connectors/box/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)