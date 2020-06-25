---
title: Felsöka Azure AD Connect installations problem | Microsoft Docs
description: Det här avsnittet innehåller anvisningar för hur du felsöker problem med att installera Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6da21e9aa3b3b4cafec71a4d1881b9eb32b4dedc
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85356227"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Felsöka: Azure AD Connect installations problem

## <a name="recommended-steps"></a>**Rekommenderade åtgärder**
Kontrol lera vilken [Azure AD Connect Installations typ](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) som passar dig bäst. Om du uppfyller villkoren för Express installation rekommenderar vi starkt att du fortsätter med Express installationen. Express installationen ger dig minimala alternativ som krävs för att slutföra installationen, vilket innebär att det är mindre troligt att det uppstår problem. 

Om du inte uppfyller villkoren för Express installation och måste göra den anpassade installationen, är det dock några metod tips som du kan följa för att undvika vanliga problem. För enkelhetens skull är endast selektiva alternativ angivna här:

* Se till att du är administratör på datorn där du installerar AAD Connect. Logga in på datorn med samma administratörs behörighet.

* Låt alla alternativ vara standard på följande sida, förutom "Använd en befintlig SQL Server" om du vill använda befintliga SQL Server. Här är [Mer information](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) om hur du använder anpassade installations alternativ. 

    ![Använd befintlig SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* På följande sida väljer du alternativet "Skapa nytt AD-konto" för att undvika eventuella behörighets problem med det befintliga kontot.

    ![AD-skogs konto](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Vanliga problem**

* [Anslutnings problem med lokala Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Anslutnings problem med online-Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Behörighets problem med lokala Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Rekommenderade dokument**
* [Förhandskrav för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Välj vilken installationstyp du vill använda för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Komma igång med Azure AD Connect med standardinställningar](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Anpassad installation av Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Uppgradera från en tidigare version till den senaste](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Vad är en mellanlagrings Server?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Vad är PowerShell-modulen ADConnectivityTools?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Nästa steg
- [Azure AD Connect synkronisering](how-to-connect-sync-whatis.md).
- [Vad är hybrididentitet?](whatis-hybrid-identity.md)





