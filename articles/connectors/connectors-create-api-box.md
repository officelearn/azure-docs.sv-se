---
title: Anslut till Box
description: 'Skapa och hantera filer med REST-API: er för Box och Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 38e061e918d445de07961af1789891f44c59090a
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789876"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Skapa och hantera filer i box med Azure Logic Apps

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