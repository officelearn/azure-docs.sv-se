---
title: Skapa prenumerationer i Azure API Management | Microsoft-dokument
description: Lär dig hur du skapar prenumerationer i Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073528"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Skapa prenumerationer i Azure API Management

När du publicerar API:er via Azure API Management är det enkelt och vanligt att skydda åtkomsten till dessa API:er med hjälp av prenumerationsnycklar. Klientprogram som behöver använda de publicerade API:erna måste innehålla en giltig prenumerationsnyckel i HTTP-begäranden när de ringer anrop till dessa API:er. För att få en prenumerationsnyckel för åtkomst till API:er krävs en prenumeration. Mer information om prenumerationer finns [i Prenumerationer i Azure API Management](api-management-subscriptions.md).

Den här artikeln går igenom stegen för att skapa prenumerationer i Azure-portalen.

## <a name="prerequisites"></a>Krav

För att göra stegen i den här artikeln är förutsättningarna följande:

+ [Skapa en API Management-instans](get-started-create-service-instance.md).
+ Förstå [prenumerationer i API Management](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Skapa en ny prenumeration

1. Välj **Prenumerationer** på menyn till vänster.
2. Välj **Lägg till prenumeration**.
3. Ange ett namn på prenumerationen och välj scope.
4. Du kan också välja om prenumerationen ska associeras med en användare.
5. Välj **Spara**.

![Flexibla abonnemang](./media/api-management-subscriptions/flexible-subscription.png)

När du har skapat prenumerationen tillhandahålls två API-nycklar för att komma åt API:erna. En nyckel är primär och en är sekundär. 

## <a name="next-steps"></a>Nästa steg
Få mer information om API Management:

+ Lär dig andra [begrepp](api-management-terminology.md) i API Management.
+ Följ våra [tutorials](import-and-publish.md) för att lära dig mer om API Management.
+ Se vår [FAQ-sida](api-management-faq.md) för vanliga frågor.