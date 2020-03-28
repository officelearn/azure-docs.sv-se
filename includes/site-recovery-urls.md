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
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187868"
---
Namn | Kommersiell WEBBADRESS | Url till myndigheter | Beskrivning
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Används för åtkomstkontroll och identitetshantering med hjälp av Azure Active Directory. 
Säkerhetskopiering | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Används för överföring av replikeringsdata och koordination.
Replikering | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Används för åtgärder för replikeringshantering och koordination.
Lagring | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Används för åtkomst till lagringskontot som lagrar replikerade data.
Telemetri (valfritt) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Används för telemetri.
Tidssynkronisering | ``time.windows.com`` | ``time.nist.gov`` | Används för att kontrollera tidssynkronisering mellan system och global tid i alla distributioner.


