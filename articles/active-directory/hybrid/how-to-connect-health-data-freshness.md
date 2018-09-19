---
title: Azure AD Connect Health - hälsotjänstinformationen är inte upp till datum aviseringen | Microsoft Docs
description: Det här dokumentet beskriver orsaken till ”hälsotjänstinformationen är inte uppdaterad” aviseringen och felsökning av den.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 430ea5f0a6f737d7632a4352c24d893368b80558
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46315100"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Hälsotjänstinformationen är inte uppdaterad avisering

## <a name="overview"></a>Översikt
<li>Azure AD Connect Health genererar data färska avisering när den inte tar emot alla datapunkter från servern i två timmar. Aviseringens namn är **hälsotjänstinformationen är inte uppdaterad**. </li>
<li>Den **varning** status aviseringen utlöses om Connect Health inte tar emot partiella dataelement som skickats från servern i två timmar. Status för en varning utlöses inte e-postmeddelanden till Innehavaradministratör. </li>
<li>Den **fel** status aviseringen utlöses om Connect Health inte får några dataelement som skickats från servern i två timmar. Fel status avisering utlösare e-postmeddelanden till Innehavaradministratör. </li>

>[!IMPORTANT] 
> Den här aviseringen följer Connect Health [policy för datalagring](reference-connect-health-user-privacy.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar 
* Se till att gå igenom och uppfylla de [kravavsnitt](how-to-connect-health-agent-install.md#requirements).
* Använd [test anslutningsverktyget](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) att identifiera problem med nätverksanslutningen.


## <a name="next-steps"></a>Nästa steg
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
