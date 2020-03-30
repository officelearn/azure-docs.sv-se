---
title: Godkänna eller neka åtkomstbegäranden - Hantering av Azure AD-berättigande
description: Lär dig hur du använder My Access-portalen för att godkänna eller neka begäranden till ett åtkomstpaket i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/27/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec63efe16f1120ca6e7d07c8917d8ad2b3a0e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261728"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Godkänna eller neka åtkomstbegäranden i Azure AD-rättighetshantering

Med Azure AD-berättigandehantering kan du konfigurera principer för godkännande för åtkomstpaket och välja en eller flera godkännare. I den här artikeln beskrivs hur utsedda godkännare kan godkänna eller neka begäranden om åtkomstpaket.

## <a name="open-request"></a>Öppna begäran

Det första steget för att godkänna eller neka åtkomstbegäranden är att hitta och öppna åtkomstbegäran som väntar på godkännande. Det finns två sätt att öppna åtkomstbegäran.

**Viktig roll:** Godkännare

1. Leta efter ett e-postmeddelande från Microsoft Azure där du uppmanas att godkänna eller neka en begäran. Här är ett exempel e-post:

    ![Godkänna begäran om åtkomst till paket-e-post](./media/entitlement-management-shared/approver-request-email.png)

1. Klicka på länken **Godkänn eller neka begäran för** att öppna åtkomstbegäran.

1. Logga in på My Access-portalen.

Om du inte har e-postmeddelandet kan du hitta de åtkomstbegäranden som väntar på ditt godkännande genom att följa dessa steg.

1. Logga in på My [https://myaccess.microsoft.com](https://myaccess.microsoft.com)Access-portalen på .  (För den amerikanska regeringen kommer domänen i `myaccess.microsoft.us`länken Min Åtkomst-portal att vara .)

1. Klicka på **Godkännanden** på den vänstra menyn om du vill visa en lista över åtkomstbegäranden som väntar på godkännande.

1. Leta reda på begäran på fliken **Väntande.**

## <a name="approve-or-deny-request"></a>Godkänna eller neka begäran

När du har öppnat en åtkomstbegäran som väntar på godkännande kan du se information som hjälper dig att godkänna eller neka beslut.

**Viktig roll:** Godkännare

1. Klicka på länken **Visa** för att öppna fönstret Begäran om Access.

1. Klicka på **Information** om du vill se information om åtkomstbegäran.

    Informationen inkluderar användarens namn, organisation, start- och slutdatum om sådan anges, affärsmotivering, när begäran skickades och när begäran upphör att gälla.

1. Klicka på **Godkänn** eller **Neka**.

1. Om det behövs anger du en orsak.

    ![My Access-portal - Åtkomstbegäran](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Klicka på **Skicka** för att skicka in ditt beslut.

    Om en princip har konfigurerats med flera godkännare behöver bara en godkännare fatta ett beslut om det väntande godkännandet. När en godkännare har skickat sitt beslut till åtkomstbegäran slutförs begäran och är inte längre tillgänglig för de andra godkännare att godkänna eller neka begäran. De andra godkännare kan se beslutet om begäran och beslutsfattaren i sin My Access-portal. För närvarande stöds endast godkännande i ett steg.

    Om ingen av de konfigurerade godkännarna kan godkänna eller neka åtkomstbegäran upphör begäran att gälla efter den konfigurerade begäran. Användaren får ett meddelande om att deras åtkomstbegäran har upphört att gälla och att de måste skicka åtkomstbegäran igen.

## <a name="next-steps"></a>Nästa steg

- [Begär åtkomst till ett åtkomstpaket](entitlement-management-request-access.md)
- [Begär process- och e-postaviseringar](entitlement-management-process.md)
