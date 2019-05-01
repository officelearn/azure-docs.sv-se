---
title: Hämta mer data, objekt eller poster med sidbrytning – Azure Logic Apps
description: Konfigurera sidbrytning överskrider storleksgränsen för standard-sidan för anslutningsappsåtgärder i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 2d1bcf2cf83fab106f79120c3caacc424f839836
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476548"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Få mer data, objekt eller poster med hjälp av sidbrytning i Azure Logic Apps

När du hämtar data, objekt eller poster med hjälp av en anslutningstjänsten åtgärd i [Azure Logic Apps](../logic-apps/logic-apps-overview.md), du kan få resultatmängder så stor att åtgärden inte returnera alla resultat på samma gång. Med vissa åtgärder kanske antalet resultat som överskrider dess Standardsidstorleken. I det här fallet returnerar åtgärden bara den första sidan i resultaten. Till exempel Standardsidstorleken för SQL Server-anslutningen **hämta rader** åtgärd är 2048, men kan variera beroende på andra inställningar.

Vissa åtgärder kan du aktivera en *sidbrytning* så att logikappen kan hämta fler resultat upp till gränsen för sidbrytning, men returnerar de resultat som ett enda meddelande när åtgärden är klar. När du använder sidbrytning, måste du ange en *tröskelvärdet* värde, som är för målantalet resultat som du vill att åtgärden ska returnera. Åtgärden hämtar resultat tills du når dina angivet tröskelvärde. När det totala antalet objekt som är mindre än angivet tröskelvärde, hämtar åtgärden alla resultat.

Aktivera sidnumrering inställningen hämtar sidor i resultatet baserat på sidstorlek för en koppling. Det här innebär att ibland kan du få fler resultat än den angivna tröskeln. Till exempel när du använder SQL Server **hämta rader** åtgärd som har stöd för sidbrytning inställning:

* Åtgärdens Standardsidstorleken har 2048 poster per sida.
* Anta att du har 10 000 poster och ange 5 000 poster som minimum.
* Sidbrytning hämtar sidor med poster för att komma till minst den angivna lägsta värdet, åtgärden returnerar 6144 poster (3 sidor x 2048 poster), inte 5 000 poster.

Här följer en lista med bara några av de kopplingar som där du kan överskrida Standardsidstorleken för specifika åtgärder:

* [Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har en Azure-prenumeration än, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).

* Logikappen och åtgärden där du vill aktivera sidbrytning. Om du inte har en logikapp kan se [snabbstarten: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Aktivera sidnumrering

För att avgöra om en åtgärd har stöd för sidbrytning i Logic App Designer, kontrollera åtgärdens inställningarna för den **sidbrytning** inställningen. Det här exemplet visar hur du aktiverar sidbrytning i SQL Server **hämta rader** åtgärd.

1. I åtgärdens övre högra hörnet väljer du ellipserna (**...** ) och välj **inställningar**.

   ![Öppna den åtgärdsinställningar](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Om åtgärden har stöd för sidbrytning, åtgärden visas den **sidbrytning** inställningen.

1. Ändra den **sidbrytning** från **av** till **på**. I den **tröskelvärdet** egenskapen, ange ett heltalsvärde för antalet resultat som du vill att åtgärden ska returneras.

   ![Ange minsta antal resultat som ska returneras](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. När du är klar kan du välja **klar**.

## <a name="workflow-definition---pagination"></a>Arbetsflödesdefinitionen - sidbrytning

När du aktiverar sidbrytning för en åtgärd som har stöd för den här funktionen kan din logikapp arbetsflödesdefinitionen innehåller den `"paginationPolicy"` egenskapen tillsammans med den `"minimumItemCount"` -egenskapen i den åtgärden `"runtimeConfiguration"` egenskap, till exempel:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Få support

Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
