---
title: Hämta fler objekt eller poster med sidnumrering
description: Konfigurera sidnumrering så att den överskrider standardgränsen för sidstorlek för anslutningsåtgärder i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792120"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Hämta mer data, objekt eller poster med hjälp av sidnumrering i Azure Logic Apps

När du hämtar data, objekt eller poster med hjälp av en anslutningsåtgärd i [Azure Logic Apps](../logic-apps/logic-apps-overview.md)kan du få resultatuppsättningar så stora att åtgärden inte returnerar alla resultat samtidigt. Med vissa åtgärder kan antalet resultat överstiga kopplingens standardsidastorlek. I det här fallet returnerar åtgärden bara den första resultatsidan. Till exempel är standard sidstorleken för SQL Server-anslutningens **hämta rader** 2048, men kan variera beroende på andra inställningar.

Med vissa åtgärder kan du aktivera en *sidnumreringsinställning* så att logikappen kan hämta fler resultat upp till sidnumreringsgränsen, men returnera dessa resultat som ett enda meddelande när åtgärden är klar. När du använder sidnumrering måste du ange ett *tröskelvärde,* vilket är det målantal resultat som du vill att åtgärden ska returneras. Åtgärden hämtar resultat tills du når det angivna tröskelvärdet. När det totala antalet objekt är mindre än det angivna tröskelvärdet hämtar åtgärden alla resultat.

Om du aktiverar sidnumreringsinställningen hämtas resultatsidor baserat på en kopplings sidstorlek. Detta innebär att du ibland kan få fler resultat än den angivna tröskelvärdet. Till exempel när du använder SQL Server **Hämta rader** åtgärd, som stöder sidnumrering inställning:

* Åtgärdens standardsidastorlek är 2048 poster per sida.
* Anta att du har 10 000 poster och anger 5 000 poster som minimum.
* Sidnumrering får sidor med poster, så för att få minst det angivna minimumet returnerar åtgärden 6144 poster (3 sidor x 2048 poster), inte 5000 poster.

Här är en lista med bara några av kopplingarna där du kan överskrida standardsidans storlek för specifika åtgärder:

* [Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft-team](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration ännu [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Logikappen och den åtgärd där du vill aktivera sidnumrering. Om du inte har någon logikapp läser du [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Aktivera sidnumrering

Om du vill ta reda på om en åtgärd stöder sidnumrering i Logic App Designer kontrollerar du åtgärdens inställningar för **sidnumreringen.** Det här exemplet visar hur du aktiverar sidnumrering i sql server-åtgärden **Hämta rader.**

1. I åtgärdens övre högra hörn väljer du ellipserna (**...**) och väljer **Inställningar**.

   ![Öppna åtgärdens inställningar](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Om åtgärden stöder sidnumrering visas **sidgineringsinställningen.**

1. Ändra **sidnumreringsinställningen** från **Av** till **På**. I egenskapen **Tröskel anger** du ett heltalsvärde för det målantal resultat som du vill att åtgärden ska returnera.

   ![Ange minsta antal resultat som ska returneras](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. När du är redo väljer du **Klar**.

## <a name="workflow-definition---pagination"></a>Arbetsflödesdefinition - sidnumrering

När du aktiverar sidnumrering för en åtgärd som stöder den här `"paginationPolicy"` funktionen innehåller `"minimumItemCount"` logikappens `"runtimeConfiguration"` arbetsflödesdefinition egenskapen tillsammans med egenskapen i åtgärdens egenskap, till exempel:

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
