---
title: "Azure AD Connect Health - hälsotjänstinformationen är inte upp till datum aviseringen | Microsoft Docs"
description: "Det här dokumentet beskriver orsaken till ”hälsotjänstinformationen är inte uppdaterad” aviseringen och felsökning av den."
services: active-directory
documentationcenter: 
author: zhiweiw
manager: maheshu
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 45e88320a64770239c8f322212e12ca7826cd0da
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Hälsotjänstinformationen är inte uppdaterad avisering

## <a name="overview"></a>Översikt
<li>Azure AD Connect Health genererar data ny avisering när den inte får alla datapunkter från servern i två timmar. Aviseringen rubriken är **hälsotjänstinformationen är inte uppdaterad**. </li>
<li>Den **varning** varning utlöses om Connect Health inte får partiella dataelement som skickats från servern i två timmar. Status för en varning utlöses inte e-postmeddelanden till klient-administratören. </li>
<li>Den **fel** varning utlöses om Connect Health inte får några dataelement som skickats från servern i två timmar. Fel status avisering utlösare e-postmeddelanden till klient-administratören. </li>

>[!IMPORTANT] 
> Den här aviseringen följer Connect Health [policy för datalagring](active-directory-aadconnect-health-gdpr.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Felsökningssteg 
* Se till att gå igenom och uppfylla de [avsnittet krav](active-directory-aadconnect-health-agent-install.md#requirements).
* Använd [test anslutningsverktyget](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) att identifiera problem med nätverksanslutningen.


## <a name="next-steps"></a>Nästa steg
* [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
