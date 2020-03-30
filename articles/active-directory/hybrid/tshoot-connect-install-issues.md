---
title: Felsöka installationsproblem för Azure AD Connect | Microsoft Dokument"
description: Det här avsnittet innehåller steg för felsÃ¶kning av problem med att installera Azure AD Connect.
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
ms.openlocfilehash: e75ad29c5b4a76de5317991995f132c6cb53bbe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70211837"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Felsöka: Problem med installation av Azure AD Connect

## <a name="recommended-steps"></a>**Rekommenderade åtgärder**
Kontrollera vilken [Azure AD Connect-installationstyp](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) som passar dig. Om du uppfyller kriterierna för expressinstallation rekommenderar vi starkt att du går med expressinstallationen. Expressinstallationen ger dig minimala alternativ som behövs för att slutföra installationen, därför är sannolikheten mindre för problem. 

Men om du inte uppfyller kriterierna för expressinstallation och måste göra den anpassade installationen så här är några metodtips som du kan följa för att undvika vanliga problem. För enkelhetens skull nämns endast selektiva alternativ här:

* Kontrollera att du är administratör på den dator där du installerar AAD Connect. Logga in på datorn med samma administratörsautentiseringsuppgifter.

* Låt alla alternativ vara standard på följande sida, förutom "Använd en befintlig SQL Server", om du vill använda befintlig SQL Server. Här finns [mer information](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) om hur du använder anpassade installationsalternativ. 

    ![Använda befintlig SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* På följande sida väljer du alternativet "Skapa nytt AD-konto" för att undvika behörighetsproblem med befintligt konto.

    ![AD-skogskonto](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Vanliga frågor**

* [Anslutningsproblem med lokal Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Anslutningsproblem med Azure Active Directory online](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Behörighetsproblem med lokal Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Rekommenderade dokument**
* [Förhandskrav för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Välj vilken installationstyp du vill använda för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Komma igång med Azure AD Connect med standardinställningar](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Anpassad installation av Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Uppgradera från en tidigare version till den senaste](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Vad är mellanlagringsserver?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Vad är PowerShell-modulen ADConnectivityTools?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Nästa steg
- [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md).
- [Vad är hybrididentitet?](whatis-hybrid-identity.md)





