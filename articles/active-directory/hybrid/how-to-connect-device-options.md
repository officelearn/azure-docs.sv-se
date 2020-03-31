---
title: 'Azure AD Connect: Enhetsalternativ | Microsoft-dokument'
description: Det här dokumentet innehåller enhetsalternativ som är tillgängliga i Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ddcdb67ef079cfa23902a1dcb03b0ec61077fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109534"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: Enhetsalternativ

Följande dokumentation innehåller information om de olika enhetsalternativ som finns i Azure AD Connect. Du kan använda Azure AD Connect för att konfigurera följande två åtgärder: 
* **Hybrid Azure AD-koppling:** Om din miljö har ett lokalt AD-fotavtryck och du vill ha fördelarna med Azure AD kan du implementera hybrid-Azure AD-anslutna enheter. Dessa enheter är anslutna både till din lokala Active Directory och din Azure Active Directory.
* **Enhetsreskript tillbaka:** Enhetsreskript tillbaka används för att aktivera villkorlig åtkomst baserat på enheter till AD FS (2012 R2 eller högre) skyddade enheter

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfigurera enhetsalternativ i Azure AD Connect

1.  Kör Azure AD Connect. På sidan **Ytterligare uppgifter** väljer du **Konfigurera enhetsalternativ**.  Klicka på **Nästa**.
    ![Konfigurera enhetsalternativ](./media/how-to-connect-device-options/deviceoptions.png) 

    På sidan **Översikt** visas information.
    ![Översikt](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > De nya konfigurera enhetsalternativen är endast tillgängliga i version 1.1.819.0 och nyare.

2.  När du har angett autentiseringsuppgifterna för Azure AD kan du välja vilken åtgärd som ska utföras på sidan Enhetsalternativ.
    ![Enhetsåtgärder](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Hybrid Azure AD-koppling](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Konfigurera/ inaktivera tillbakaskrivning av enheten](how-to-connect-device-writeback.md)

