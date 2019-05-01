---
title: Godkänn eller avvisa förfrågningar i Azure AD-rättigheten hantering (förhandsversion) – Azure Active Directory
description: Lär dig hur du använder min åtkomst-portalen för att godkänna eller neka begäranden till en åtkomst-paketet i Azure Active Directory rättigheten hantering (förhandsversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541532"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Godkänn eller avvisa förfrågningar i Azure AD rättigheten hantering (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azure AD rättigheten management kan du konfigurera principer för att kräva godkännande av åtkomst-paket och väljer en eller flera godkännare. Den här artikeln beskrivs hur utnämnda godkännare kan godkänna eller neka förfrågningar för åtkomst-paket.

## <a name="open-request"></a>Open-förfrågan

Det första steget att godkänna eller neka förfrågningar om är att leta upp och öppna förfrågan väntar på godkännande. Det finns två sätt att öppna förfrågan.

**Nödvändiga roll:** Godkännare

1. Leta efter ett e-postmeddelande från Microsoft Azure där du uppmanas att godkänna eller avvisa en begäran. Här är ett exempel e-postmeddelande:

    ![Godkänn begäran om åtkomst till e-post för paketet](./media/entitlement-management-shared/email-approve-request.png)

1. Klicka på den **Godkänn eller Avvisa begäran** länk för att öppna förfrågan.

1. Logga in på portalen Mina åtkomst.

Om du inte har e-postmeddelandet hittar du förfrågningar väntar på ditt godkännande genom att följa dessa steg.

1. Logga in på min åtkomst Portal på [ https://myaccess.microsoft.com ](https://myaccess.microsoft.com).

1. I den vänstra menyn klickar du på **godkännanden** att se en lista över förfrågningar om godkännande.

1. På den **väntande** fliken, hitta begäran.

## <a name="approve-or-deny-request"></a>Godkänn eller Avvisa begäran

Du kan se information som hjälper dig att göra en Godkänn eller neka beslut när du har öppnat en åtkomstbegäran väntar på godkännande.

**Nödvändiga roll:** Godkännare

1. Klicka på den **visa** länk för att öppna fönstret för åtkomst-begäran.

1. Klicka på **information** att se information om förfrågan.

    Informationen omfattar användarens namn, organisation, få åtkomst till start- och datum om affärsrelaterad motivering när begäran har skickats och när begäran att förfalla.

1. Klicka på **godkänna** eller **neka**.

1. Om det behövs kan du ange en orsak.

    ![Min åtkomstportalen - förfrågan](./media/entitlement-management-shared/my-access-approve-request.png)

1. Klicka på **skicka** att skicka ditt beslut.

    Om en princip är konfigurerad med flera godkännare, måste endast en godkännare fatta ett beslut om väntar på godkännande. När en godkännare har skickat sina beslutet att förfrågan, begäran har slutförts och är inte längre tillgänglig för andra granskare att godkänna eller neka begäran. De andra godkännarna kan se begäran beslutet och beslutsfattare i den här åtkomst-portalen. För närvarande stöds endast enskild steg godkännande.

    Om ingen av konfigurerade godkännarna kan godkänna eller neka förfrågan begäran går ut efter varaktigheten för konfigurerade begäran. Användaren får ett meddelande att deras förfrågan har upphört att gälla och att de behöver för att skicka förfrågan.

## <a name="next-steps"></a>Nästa steg

- [Begär åtkomst till ett åtkomst-paket](entitlement-management-request-access.md)
- [Begär processen och e-postaviseringar](entitlement-management-process.md)
