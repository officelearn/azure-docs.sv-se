---
title: Problem med att skapa ett programproxyprogram | Microsoft-dokument
description: Felsöka problem med att skapa programproxyprogram i Azure AD Admin-portalen
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104b98cba0948ec5d0896877e54eab1e7cd4049f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825807"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problem med att skapa ett programproxyprogram 

Nedan följer några av de vanligaste problemen som människor står inför när de skapar ett nytt programproxyprogram.

## <a name="recommended-documents"></a>Rekommenderade dokument 

Mer information om hur du skapar ett programproxyprogram via administratörsportalen finns i [Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md).

Om du följer stegen i dokumentet och får ett felmeddelande om hur du skapar programmet läser du felinformationen för information och förslag på hur du åtgärdar programmet. De flesta felmeddelanden innehåller en föreslagen korrigering. 

## <a name="specific-things-to-check"></a>Specifika saker att kontrollera

För att undvika vanliga fel, kontrollera:

-   Du är administratör med behörighet att skapa ett programproxyprogram

-   Den interna webbadressen är unik

-   Den externa webbadressen är unik

-   Webbadresserna börjar med http eller https och slutar med ett "/"

-   Webbadressen ska vara ett domännamn och inte en IP-adress

Felmeddelandet ska visas i det övre högra hörnet när du skapar programmet. Du kan också välja meddelandeikonen för att se felmeddelandena.

   ![Meddelandefråga](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Nästa steg
[Aktivera Application Proxy på Azure-portalen](application-proxy-add-on-premises-application.md)
