---
title: Begär åtkomst till ett Access-paket i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig hur du använder min åtkomst Portal för att begära åtkomst till ett Access-paket i Azure Active Directory rättighets hantering.
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddc0a3788075701fb4633895e7b22fff2c15f60b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173699"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Begär åtkomst till ett Access-paket i Azure AD-hantering av rättigheter

Med hantering av Azure AD-behörighet kan du använda ett Access-paket för att konfigurera resurser och principer som automatiskt administrerar åtkomst för åtkomst paketets livs längd. 

En Access Package Manager kan konfigurera principer för att kräva att användare får åtkomst till paket. En användare som behöver åtkomst till ett Access-paket kan skicka en begäran om att få åtkomst. Den här artikeln beskriver hur du skickar en åtkomstbegäran.

## <a name="sign-in-to-the-my-access-portal"></a>Logga in på portalen för åtkomst

Det första steget är att logga in på min åtkomst portal där du kan begära åtkomst till ett Access-paket.

**Nödvändig roll:** Begär Ande

1. Sök efter ett e-postmeddelande eller ett meddelande från projektet eller Business Manager som du arbetar med. E-postmeddelandet ska innehålla en länk till åtkomst paketet som du behöver åtkomst till. Länken börjar med `myaccess`, innehåller ett katalog tips och slutar med ett Access-paket-ID.
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Öppna länken.

1. Logga in på portalen för åtkomst.

    Se till att du använder ditt organisations konto (arbets-eller skol konto). Om du är osäker kan du kontakta projektet eller Business Manager.

## <a name="request-an-access-package"></a>Begär ett Access-paket

När du har hittat Access-paketet i My Access-portalen kan du skicka en begäran.

**Nödvändig roll:** Begär Ande

1. Hitta Access-paketet i listan.  Om det behövs kan du söka genom att skriva en Sök sträng och sedan välja filtret **namn**, **katalog**eller **resurser** .

    ![Min åtkomst Portal – resurs sökning](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Klicka på bock markeringen för att välja Access-paketet.

1. Klicka på **begär åtkomst** för att öppna fönstret begär åtkomst.

    ![Åtkomst till Portal – åtkomst paket](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Om rutan **affärs justering** visas anger du en motivering för att ha åtkomst.

1. Välj **Ja** eller **Nej**om **begäran om angiven period?** är aktive rad.

1. Ange vid behov start datum och slutdatum.

    ![Min åtkomst Portal – begär åtkomst](./media/entitlement-management-shared/my-access-request-access.png)

1. När du är färdig klickar du på **Skicka** för att skicka in din begäran.

1. Klicka på **begär ande historik** om du vill se en lista över dina begär Anden och status.

    Om åtkomst paketet kräver godkännande är begäran nu i ett tillstånd som väntar på godkännande.

### <a name="select-a-policy"></a>Välj en princip

Om du begär åtkomst till ett Access-paket som har flera principer som gäller kan du bli ombedd att välja en princip. Till exempel kan en Access Package Manager Konfigurera ett Access-paket med två principer för två grupper av interna anställda. Den första principen kan ge åtkomst i 60 dagar och kräver godkännande. Den andra principen kan tillåta åtkomst i 2 dagar och kräver inte godkännande. Om du stöter på det här scenariot måste du välja den princip som du vill använda.

![Min åtkomst Portal – begär åtkomst – flera principer](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="cancel-a-request"></a>Avbryt en begäran

Om du skickar en åtkomstbegäran och begäran fortfarande har statusen **väntar på godkännande** kan du avbryta begäran.

**Nödvändig roll:** Begär Ande

1. I portalen My Access klickar du på **begär ande historik** för att se en lista över dina begär Anden och status.

1. Klicka på länken **Visa** för den begäran som du vill avbryta.

1. Om begäran fortfarande har statusen väntar på **godkännande** kan du klicka på **Avbryt begäran** om du vill avbryta begäran.

    ![Min åtkomst Portal-Avbryt förfrågan](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Klicka på **begär ande historik** för att bekräfta att begäran avbröts.

## <a name="next-steps"></a>Nästa steg

- [Godkänn eller neka åtkomst begär Anden](entitlement-management-request-approve.md)
- [Begär process och e-postmeddelanden](entitlement-management-process.md)
