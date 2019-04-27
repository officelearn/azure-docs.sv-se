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
ms.openlocfilehash: bc791fea1dfd184749e84cb7b7a912972c6a9f12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657622"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Skapa prenumerationer i Azure API Management

När du publicera API: er via Azure API Management är det enkelt och gemensamma för säker åtkomst till dessa API: er med hjälp av prenumerationsnycklar. Klientprogram som kan behöver använda den publicerade API: er måste innehålla en giltig prenumerationsnyckel i HTTP-begäranden när de göra anrop till dessa API: er. Om du vill ha en prenumerationsnyckel för åtkomst till API: er, krävs en prenumeration. Mer information om prenumerationer finns i [prenumerationer i Azure API Management](api-management-subscriptions.md).

Den här artikeln beskriver stegen för att skapa prenumerationer i Azure-portalen.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill vidta åtgärder i den här artikeln är kraven på följande sätt:

+ [Skapa en API Management-instans](get-started-create-service-instance.md).
+ Förstå [prenumerationer i API Management](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Skapa en ny prenumeration

1. Välj **prenumerationer** i menyn till vänster.
2. Välj **Lägg till prenumeration**.
3. Ange ett namn för prenumerationen och välj omfattningen.
4. Du kan också välja om prenumerationen som ska associeras med en användare.
5. Välj **Spara**.

![Flexibla prenumerationer](./media/api-management-subscriptions/flexible-subscription.png)

När du har skapat prenumerationen tillhandahålls två API-nycklar för att få åtkomst till API: erna. En nyckeln är primär och en är sekundär. 

## <a name="next-steps"></a>Nästa steg
Få mer information om API Management:

+ Läs andra [begrepp](api-management-terminology.md) i API Management.
+ Följ våra [självstudier](import-and-publish.md) mer information om API Management.
+ Kontrollera våra [FAQ-sida](api-management-faq.md) för vanliga frågor.