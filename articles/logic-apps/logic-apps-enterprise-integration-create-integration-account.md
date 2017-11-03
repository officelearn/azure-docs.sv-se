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
ms.openlocfilehash: 716e7b5bab8725dea0fd2b760d0e46e8e892c5b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-an-integration-account"></a>Vad är en integrering konto?

Ett konto för integrering tillåter enterprise integration apparna att hantera artefakter, inklusive scheman, kartor, certifikat, partners och avtal. Alla integration appar som du skapar använder ett konto för integrering för att komma åt dessa scheman, kartor, certifikat och så vidare.

## <a name="create-an-integration-account"></a>Skapa ett konto för integrering

1.  Logga in på [Azure Portal](http://portal.azure.com "Azure Portal"). I den vänstra menyn, Välj **fler tjänster**.

    ![Välj ”fler tjänster”](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. I sökrutan skriver du ”integration” för filtret. Välj i resultatlistan **Integrationskonton**.

    ![Filtrera efter ”integration”, Välj ”Integrationskonton”](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. Överst på sidan Välj **Lägg till**.

    ![Välj Lägg till](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Kontot för integrering och välj den Azure-prenumeration som du vill använda. Du kan antingen skapa en ny **resursgruppen** eller välj en befintlig resursgrupp. Välj en **plats** som värd för ditt konto för integrering och ett **prisnivån**. 

    När du är klar kan du välja **skapa**.

    ![Ange information för ditt konto för integrering](./media/logic-apps-enterprise-integration-accounts/account-4.png)

    Azure tillhandahåller integration kontot på den valda platsen bör vara klart inom 1 minut.

5. Uppdatera sidan. Du ser ditt nya konto för integrering i listan.

    ![Det nya kontot på integrering visas](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

Därefter länka integration-konto som du har skapat i din logikapp. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Länka ett integration konto till en logikapp

Om du vill ge dina logic länka appar åtkomst till maps, scheman, avtal och andra artefakter i kontot integration integration-konto till din logikapp.

### <a name="prerequisites"></a>Krav

* Ett konto för integrering
* En logikapp

> [!NOTE] 
> Se till att appen integration konto och logik finns i den *samma Azure-plats* innan du börjar.


1. Välj logikappen i Azure-portalen och kontrollera platsen för din logikapp.

    ![Välj din logikapp, Kontrollera platsen](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. Under **inställningar**väljer **integrering konto**.

    ![Välj ”Integration konto”](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Från den **välja ett konto för integrering** väljer integration-konto som du vill länka till din logikapp. Slutför Länkning väljer **spara**.

    ![Välj kontot integrering](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

    Du får ett meddelande som visar din integrering kontot är länkat till logikappen och alla artefakter i ditt konto för integrering är nu tillgängliga för din logikapp.

    ![Din logikapp är kopplad till ditt konto för integrering](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Nu när ditt konto integration är länkad till din logikapp, kan du använda B2B-kopplingar i dina logic apps. Några vanliga B2B-kopplingar innehåller XML-verifiering och koda/avkoda flat-fil.  

## <a name="delete-your-integration-account"></a>Ta bort ditt konto för integrering

1. Välj **fler tjänster**.

    ![Välj ”fler tjänster”](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. I sökrutan skriver du ”integration” för filtret. Välj i resultatlistan **Integrationskonton**.

    ![Filtrera efter ”integration”, Välj ”Integrationskonton”](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. Välj integration-konto som du vill ta bort.

    ![Välj integration konto ska tas bort](./media/logic-apps-enterprise-integration-accounts/account-5.png)

4. Välj på menyn **ta bort**.

    ![Välj ”Ta bort”](./media/logic-apps-enterprise-integration-accounts/delete.png)

5. Bekräfta valet att ta bort kontot integrering.

## <a name="move-your-integration-account"></a>Flytta integration-kontot

Följ dessa steg om du vill flytta ett integration konto till en annan Azure-prenumerationen eller resursen.

> [!IMPORTANT]
> Du måste uppdatera alla skript om du vill använda den nya resursen-ID när du flyttar ett konto för integrering.

1. Välj **fler tjänster**.

    ![Välj ”fler tjänster”](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. I sökrutan skriver du ”integration” för filtret. Välj i resultatlistan **Integrationskonton**.

    ![Filtrera efter ”integration”, Välj ”Integrationskonton”](./media/logic-apps-enterprise-integration-accounts/account-2.png)

3. Välj integration-konto som du vill flytta. Under **inställningar**, Välj **egenskaper**.

    ![Välj integration konto att flytta. Under inställningar, välj Egenskaper](./media/logic-apps-enterprise-integration-accounts/move.png)

5. Ändra resursgrupp eller Azure-prenumeration som är kopplad till ditt konto för integrering.

    ![Välj Ändra resursgrupp eller ändra prenumerationen](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>Nästa steg
* [Mer information om avtalen](../logic-apps/logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")  

