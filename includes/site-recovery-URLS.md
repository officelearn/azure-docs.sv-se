---
title: ta med fil
description: ta med fil
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/28/2018
ms.author: raynew
ms.openlocfilehash: f7d6c3f68618fec839ccff06b73ba44d106999d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38765546"
---
| Namn | Extern URL | URL: en för myndigheter | Beskrivning |
|---|---|---|---|
| Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Används för access control och Identitetshantering med AAD |
| Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Används för överföring av replikeringsdata och koordination |
| Replikering | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Används för replikeringshantering och koordination |
| Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Används för åtkomst till lagringskontot som lagrar replikerade data |
| Telemetri (valfritt) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Används för telemetri |

``time.nist.gov`` och ``time.windows.com`` används för att kontrollera tidssynkronisering mellan system och global tid i alla distributioner.


