---
title: Felsöka problem med Azure AD Connect-installation | Microsoft Docs
description: Det här avsnittet innehåller anvisningar att felsöka problem med att installera Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e077127681f8bd7b650ab22f2d036efd7f9733ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60454803"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Felsökning: Installationsproblem med Azure AD Connect

## <a name="recommended-steps"></a>**Rekommenderade åtgärder**
Kontrollera vilket [installationstyp för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) är lämplig för dig. Om du uppfyller villkoren för expressinstallationsfiler sedan vi rekommenderar starkt att du går med snabbinstallation. Snabbinstallation ger minimal alternativ som behövs för att slutföra installationen, det finns därför mindre sannolikheten för eventuella problem. 

Om du inte uppfyller villkoren för expressinstallationsfiler och göra den anpassade installationen och sedan här följer några rekommendationer kan du följa för att undvika vanliga problem. För enkelhetens skull anges endast selektiv alternativ här:

* Se till att du är administratör på datorn där du installerar AAD Connect. Logga in datorn med samma autentiseringsuppgifter.

* Låt alla alternativ som standard på följande sida, förutom ”Använd en befintlig SQLServer” om du vill använda befintliga SQL Server. Här följer [mer](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) om hur du använder anpassade installationsalternativen. 

    ![Använda befintliga SQLServer](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* På sidan, välja alternativet ”Skapa ny AD-konto”, att undvika vilken behörighet som utfärdar med befintligt konto.

    ![AD-skogen konto](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Vanliga problem**

* [Anslutningsproblem med lokalt Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Anslutningsproblem med online Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Behörighetsproblem med lokala Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Rekommenderade dokument**
* [Förutsättningar för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Välj vilken installationstyp du vill använda för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Komma igång med Azure AD Connect med standardinställningar](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Anpassad installation av Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Uppgradera från en tidigare version till den senaste](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Vad är mellanlagringsserver?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Vad är PowerShell-modulen ADConnectivityTools?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Nästa steg
- [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md).
- [Vad är hybrididentitet? ](whatis-hybrid-identity.md).





