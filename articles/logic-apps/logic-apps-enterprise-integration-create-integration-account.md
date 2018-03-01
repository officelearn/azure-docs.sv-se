---
title: "Skapa länkar, ta bort eller flytta integration konton i Azure logikappar | Microsoft Docs"
description: "Hur du skapar ett konto för integrering och länka det till dina logic apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: LADocs; mandia
ms.openlocfilehash: b238ef8cf9d1328913334a92c042584334d81e3c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="what-is-an-integration-account"></a>Vad är en integrering konto?

Ett konto för integration gör det möjligt för ditt företag integrationsappar, särskilt logikappar, komma åt och hantera B2B-artefakter, till exempel samarbetspartner, avtal, kartor, scheman, certifikat och så vidare. För att ge åtkomst, att länka ditt integration konto till din logikapp efter att se till att båda integration konto och logik appen har den *samma Azure-plats*.

## <a name="create-an-integration-account"></a>Skapa ett integrationskonto

1. Logga in på [Azure Portal](http://portal.azure.com "Azure Portal"). 

2. Azure Huvudmeny, Välj **alla tjänster**. I sökrutan anger du ”integration” och välj sedan **integrationskonton**.

   ![Skapa integration konto](./media/logic-apps-enterprise-integration-accounts/account-1.png)

3. Överst på sidan Välj **Lägg till**.

   ![Välj Lägg till](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Kontot för integrering och välj den Azure-prenumeration som du vill använda. Du kan antingen skapa en ny **resursgruppen** eller välj en befintlig resursgrupp. Välj en **plats** som värd för ditt konto för integrering och ett **prisnivån**. När du är klar kan du välja **skapa**.

   ![Ange information för ditt konto för integrering](./media/logic-apps-enterprise-integration-accounts/account-4.png)

   Azure tillhandahåller integration kontot på den valda platsen bör vara klart inom en minut.

5. Uppdatera sidan. Du ser ditt nya konto för integrering i listan.

   ![Det nya kontot på integrering visas](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

Därefter länka integration-konto som du har skapat i din logikapp. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Länka ett integration konto till en logikapp

Om du vill ge dina logic apps åtkomst till B2B-artefakter, till exempel samarbetspartner, avtal, kartor och scheman i kontot för integrering länka integration konto till din logikapp. 

1. Välj logikappen i Azure-portalen och kontrollera platsen för din logikapp.

   ![Välj din logikapp, Kontrollera platsen](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. Under **inställningar**väljer **integrering konto**.

   ![Välj ”Integration konto”](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Från den **välja ett konto för integrering** väljer integration-konto som du vill länka till din logikapp. Slutför Länkning väljer **spara**.

   ![Välj kontot integrering](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

   Du får ett meddelande som visar din integrering kontot är länkat till logikappen och alla artefakter i ditt konto för integrering är nu tillgängliga för din logikapp.

   ![Din logikapp är kopplad till ditt konto för integrering](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Nu när ditt konto integration är länkad till din logikapp, kan du använda B2B-kopplingar i din logikapp. Några vanliga B2B-kopplingar innehåller XML-verifiering och koda/avkoda flat-fil.  

## <a name="delete-your-integration-account"></a>Ta bort ditt konto för integrering

1. Azure Huvudmeny, Välj **alla tjänster**. I sökrutan anger du ”integration” och välj sedan **integrationskonton**.

   ![Hitta integration-konto](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Välj integration-konto som du vill ta bort.

    ![Välj integration konto ska tas bort](./media/logic-apps-enterprise-integration-accounts/account-5.png)

3. Välj på menyn **ta bort**.

    ![Välj ”Ta bort”](./media/logic-apps-enterprise-integration-accounts/delete.png)

4. Bekräfta valet att ta bort kontot integrering.

## <a name="move-your-integration-account"></a>Flytta integration-kontot

Följ dessa steg om du vill flytta ett integration konto till en annan Azure-prenumerationen eller resursen. Kontrollera att du uppdaterar alla skript om du vill använda den nya resursen-ID när du flyttar integration-konto.

1. Azure Huvudmeny, Välj **alla tjänster**. I sökrutan anger du ”integration” och välj sedan **integrationskonton**.

   ![Hitta integration-konto](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Välj integration-konto som du vill flytta. Under **inställningar**, Välj **egenskaper**.

   ![Välj integration konto att flytta. Under inställningar, välj Egenskaper](./media/logic-apps-enterprise-integration-accounts/move.png)

3. Ändra resursgrupp eller Azure-prenumeration som är kopplad till ditt konto för integrering.

   ![Välj Ändra resursgrupp eller ändra prenumerationen](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>Nästa steg

* [Mer information om avtalen](../logic-apps/logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")  

