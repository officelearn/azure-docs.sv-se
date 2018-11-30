---
title: Hur du skapar prenumerationer i Azure API Management | Microsoft Docs
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
ms.openlocfilehash: 3f4e113125ec9644aac974e47996afe290e57cee
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621822"
---
# <a name="how-to-create-subscriptions-in-azure-api-management"></a>Hur du skapar prenumerationer i Azure API Management

När du publicerar API: er via Azure API Management (APIM), är det enklaste och vanligaste sättet att skydda åtkomsten till dessa API: er med hjälp av Prenumerationsnycklar. Med andra ord måste klientprogram som kan behöver använda den publicerade API: er innehålla en giltig prenumeration-nyckel i HTTP-begäranden när anrop till dessa API: er. Om du vill ha en Prenumerationsnyckel för åtkomst till API: er, krävs en prenumeration. Mer information om prenumerationer finns i [prenumerationer i Azure API Management](api-management-subscriptions.md)

Den här artikeln beskriver stegen för att skapa prenumerationer i Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln, måste du:

+ [Skapa en APIM-instans](get-started-create-service-instance.md)
+ Förstå [prenumerationer i APIM](api-management-subscriptions.md)

## <a name="create-a-new-subscription"></a>Skapa en ny prenumeration

1. Klicka på **prenumerationer** i menyn till vänster
2. Klicka på **Lägg till prenumeration**
3. Ange ett namn för prenumerationen och välj omfånget
4. Klicka på **Spara**

![Flexibla prenumerationer](./media/api-management-subscriptions/flexible-subscription.png)

När prenumerationen har skapats kan etableras ett par med API-nycklar (primära och sekundära) för att komma åt API: erna.

## <a name="next-steps"></a>Nästa steg
Mer information om API Management:

+ Läs andra [begrepp](api-management-terminology.md) i API Management
+ Följ våra [självstudier](import-and-publish.md) mer information om API Management
+ Kontrollera våra [FAQ-sida](api-management-faq.md) för vanliga frågor