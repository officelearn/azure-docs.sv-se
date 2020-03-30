---
title: Begär ett åtkomstpaket – hantering av Azure AD-berättigande
description: Lär dig hur du använder My Access-portalen för att begära åtkomst till ett åtkomstpaket i Azure Active Directory-berättigandehantering.
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
ms.openlocfilehash: 4b1ccde2f1f92237978ab4e68acaa26393bbb9d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261754"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Begär åtkomst till ett åtkomstpaket i Azure AD-berättigandehantering

Med Azure AD-berättigandehantering möjliggör ett åtkomstpaket en engångsinställning av resurser och principer som automatiskt administrerar åtkomst under åtkomstpaketets livstid. 

En åtkomstpakethanterare kan konfigurera principer för att kräva godkännande för användare att ha åtkomst till åtkomstpaket. En användare som behöver åtkomst till ett åtkomstpaket kan skicka en begäran om att få åtkomst. I den här artikeln beskrivs hur du skickar en åtkomstbegäran.

## <a name="sign-in-to-the-my-access-portal"></a>Logga in på My Access-portalen

Det första steget är att logga in på My Access-portalen där du kan begära åtkomst till ett åtkomstpaket.

**Viktig roll:** Beställare

1. Leta efter ett e-postmeddelande eller ett meddelande från projektet eller affärschefen som du arbetar med. E-postmeddelandet ska innehålla en länk till det åtkomstpaket som du behöver tillgång till. Länken börjar `myaccess`med , innehåller en katalog ledtråd och slutar med ett åtkomstpaket-ID.  (För den amerikanska regeringen `https://myaccess.microsoft.us` kan domänen vara i stället.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Öppna länken.

1. Logga in på My Access-portalen.

    Se till att du använder ditt organisationskonto (arbete eller skola). Om du är osäker kontaktar du projektet eller affärschefen.

## <a name="request-an-access-package"></a>Begär ett åtkomstpaket

När du har hittat åtkomstpaketet i My Access-portalen kan du skicka en begäran.

**Viktig roll:** Beställare

1. Hitta åtkomstpaketet i listan.  Om det behövs kan du söka genom att skriva en söksträng och sedan välja **filtret Namn,** **katalog**eller **Resurser.**

    ![My Access-portal - Resurssökning](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Markera åtkomstpaketet genom att klicka på bocken.

1. Klicka på **Begär åtkomst** för att öppna fönstret Begär åtkomst.

    ![My Access-portal - Access-paket](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Om rutan **Affärsmotivering** visas skriver du en motivering för att behöva åtkomst.

1. Om **Begäran för viss period?** **Yes** **No**

1. Om det behövs anger du startdatum och slutdatum.

    ![My Access-portal - Åtkomst för begäran](./media/entitlement-management-shared/my-access-request-access.png)

1. När du är klar klickar du på **Skicka** för att skicka din begäran.

1. Klicka på **Begär historik** om du vill visa en lista över dina begäranden och status.

    Om åtkomstpaketet kräver godkännande är begäran nu i ett väntande godkännandetillstånd.

### <a name="select-a-policy"></a>Välj en princip

Om du begär åtkomst till ett åtkomstpaket som har flera principer som gäller kan du bli ombedd att välja en princip. En åtkomstpakethanterare kan till exempel konfigurera ett åtkomstpaket med två principer för två grupper av interna medarbetare. Den första principen kan ge åtkomst i 60 dagar och kräva godkännande. Den andra principen kan ge åtkomst i 2 dagar och inte kräver godkännande. Om du stöter på det här scenariot måste du välja den princip som du vill använda.

![My Access-portal - Begärandeåtkomst - flera principer](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>Skicka en begäran igen

När du begär åtkomst till ett åtkomstpaket kan din begäran nekas eller din begäran upphör att gälla om godkännare inte svarar i tid. Om du behöver åtkomst kan du försöka igen och skicka in din begäran igen. I följande procedur beskrivs hur du skickar en begäran om åtkomst igen:

**Viktig roll:** Beställare

1. Logga in på **My Access-portalen.**

1. Klicka på **Begär historik** från navigeringsmenyn till vänster.

1. Leta reda på det åtkomstpaket som du skickar en begäran till igen.

1. Markera åtkomstpaketet genom att klicka på bocken.

1. Klicka på den blå **visa** länken till höger om det valda åtkomstpaketet.
    
    ![Välj åtkomstpaket och visa länk](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    En ruta öppnas till höger med åtkomstpaketets begäranhistorik.
    
    ![Knappen Välj skicka igen](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Klicka på knappen **Skicka igen** längst ned i fönstret.

## <a name="cancel-a-request"></a>Avbryta en begäran

Om du skickar en åtkomstbegäran och begäran fortfarande är i **det väntande godkännandetillståndet** kan du avbryta begäran.

**Viktig roll:** Beställare

1. Klicka på **Begär historik** till vänster i Portalen Min Åtkomst för att se en lista över dina begäranden och status.

1. Klicka på länken **Visa** för den begäran som du vill avbryta.

1. Om begäran fortfarande är i **väntande godkännandeläge** kan du klicka på **Avbryt begäran om** att avbryta begäran.

    ![My Access-portal - Avbryt begäran](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Klicka på **Begär historik** för att bekräfta att begäran har avbrutits.

## <a name="next-steps"></a>Nästa steg

- [Godkänna eller neka åtkomstbegäranden](entitlement-management-request-approve.md)
- [Begär process- och e-postaviseringar](entitlement-management-process.md)
