---
title: Skapa prenumerationer i Azure API Management | Microsoft Docs
description: Lär dig hur du skapar prenumerationer i Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 1393e548c46c23f6b50c1b18a274febb74914ae8
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054519"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Skapa prenumerationer i Azure API Management

När du publicera API: er via Azure API Management är det enkelt och gemensamma för säker åtkomst till dessa API: er med hjälp av prenumerationsnycklar. Klientprogram som kan behöver använda den publicerade API: er måste innehålla en giltig prenumerationsnyckel i HTTP-begäranden när de göra anrop till dessa API: er. Om du vill ha en prenumerationsnyckel för åtkomst till API: er, krävs en prenumeration. Mer information om prenumerationer finns i [prenumerationer i Azure API Management](api-management-subscriptions.md).

Den här artikeln beskriver stegen för att skapa prenumerationer i Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

Om du vill vidta åtgärder i den här artikeln är kraven på följande sätt:

+ [Skapa en API Management-instans](get-started-create-service-instance.md).
+ Förstå [prenumerationer i API Management](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Skapa en ny prenumeration

1. Välj **prenumerationer** i menyn till vänster.
2. Välj **Lägg till prenumeration**.
3. Ange ett namn för prenumerationen och välj omfattningen.
4. Välj **Spara**.

![Flexibla prenumerationer](./media/api-management-subscriptions/flexible-subscription.png)

När du har skapat prenumerationen tillhandahålls två API-nycklar för att få åtkomst till API: erna. En nyckeln är primär och en är sekundär. 

## <a name="next-steps"></a>Nästa steg
Få mer information om API Management:

+ Läs andra [begrepp](api-management-terminology.md) i API Management.
+ Följ våra [självstudier](import-and-publish.md) mer information om API Management.
+ Kontrollera våra [FAQ-sida](api-management-faq.md) för vanliga frågor.