---
title: 'Azure AD Connect: Enhetsalternativ | Microsoft Docs'
description: Det här dokumentet beskriver Enhetsalternativ som är tillgängliga i Azure AD Connect
services: active-directory
documentationcenter: ''
author: anandy
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.component: hybrid
ms.author: anandy
ms.openlocfilehash: 0eb3a33ee030dcda6a811a771578c9e976e36619
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593290"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: Enhetsalternativ

Följande dokumentation innehåller information om de olika alternativen för enheten i Azure AD Connect. Du kan använda Azure AD Connect för att konfigurera följande två åtgärder: 
* **Ansluta till Azure AD-hybridlösning**: om din miljö har en lokal AD storleken och du även vill utnyttja funktionerna i Azure Active Directory, du kan implementera hybrid Azure AD anslutna enheter. Dessa är enheter som är både, ansluten till din lokala Active Directory och Azure Active Directory.
* **Tillbakaskrivning av enheter**: tillbakaskrivning av enheter som används för att aktivera villkorlig åtkomst baserat på enheter med AD FS (2012 R2 eller högre) skyddade enheter

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfigurera alternativ för enheten i Azure AD Connect

1.  Kör Azure AD Connect. I den **ytterligare uppgifter** väljer **konfigurera Enhetsalternativ**.
    ![Konfigurera alternativ för enhet](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    Om du klickar på nästa, en **översikt** visas, vilket beskrivs de åtgärder som kan utföras.
    ![Översikt](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > De nya konfigurera Enhetsalternativ är bara tillgängliga i version 1.1.819.0 och nyare.

2.  När du anger autentiseringsuppgifter för Azure AD kan du har valt åtgärden som ska utföras på alternativsidan för enheten.
    ![Åtgärder för enhet](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Azure AD-hybridlösning koppling](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Konfigurera / inaktivera tillbakaskrivning av enhet.](./active-directory-aadconnect-feature-device-writeback.md)

