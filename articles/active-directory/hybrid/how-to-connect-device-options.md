---
title: 'Azure AD Connect: Enhetsalternativ | Microsoft Docs'
description: Det här dokumentet beskriver Enhetsalternativ som är tillgängliga i Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c21b4b8996d7dae62c2aa1937c2876a66f6f82bd
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320568"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: Enhetsalternativ

Följande dokumentation innehåller information om de olika enhet alternativen som är tillgängliga i Azure AD Connect. Du kan använda Azure AD Connect för att konfigurera följande två åtgärder: 
* **Hybrid Azure AD-anslutning**: om din miljö har en lokal AD-fotavtryck och du vill att fördelarna med Azure AD, du kan implementera hybrid Azure AD-anslutna enheter. Dessa enheter är anslutna både till din lokala Active Directory och Azure Active Directory.
* **Tillbakaskrivning av enhet**: tillbakaskrivning av enhet som används för att aktivera villkorlig åtkomst baserat på enheter till AD FS (2012 R2 eller senare) skyddade enheter

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfigurera Enhetsalternativ i Azure AD Connect

1.  Kör Azure AD Connect. I den **ytterligare uppgifter** väljer **konfigurera Enhetsalternativ**.  Klicka på **Nästa**.
    ![Konfigurera Enhetsalternativ](./media/how-to-connect-device-options/deviceoptions.png) 

    Den **översikt** sidan visar information.
    ![Översikt](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > De nya konfigurera Enhetsalternativ är endast tillgängliga i version 1.1.819.0 och nyare.

2.  Du kan välja åtgärden som ska utföras på alternativsidan för enheten när du har angett autentiseringsuppgifterna för Azure AD.
    ![Åtgärder](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Hybrid Azure AD-anslutning](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Konfigurera / inaktivera tillbakaskrivning av enhet](how-to-connect-device-writeback.md)

