---
title: ta med fil
description: ta med fil
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: ae8eebf9667f2bc03fdc1082fb38c19c5c645c10
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060770"
---
**Namn** | **Extern URL**  | **URL: en för myndigheter** | **Beskrivning** |
--- | --- | --- | ---
Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Används för access control och Identitetshantering med AAD 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Används för överföring av replikeringsdata och koordination 
Replikering | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Används för replikeringshantering och koordination 
Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Används för åtkomst till lagringskontot som lagrar replikerade data 
Telemetri (valfritt) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Används för telemetri 
Tidssynkronisering | ``time.windows.com`` | ``time.nist.gov`` | Ssed att kontrollera tidssynkronisering mellan system och global tid i alla distributioner.


