---
title: Uppgradera från DirSync och Azure AD Sync | Microsoft-dokument
description: Beskriver hur du uppgraderar från DirSync och Azure AD Sync till Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 803fcc0161f2a092006e60db5a98f5bf18dce1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381186"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Uppgradera Windows Azure Active Directory Sync och Azure Active Directory Sync
Azure AD Connect är det bästa sättet att ansluta din lokala katalog till Azure AD och Office 365. Det här är ett bra tillfälle att uppgradera till Azure AD Connect från Windows Azure Active Directory Sync (DirSync) eller Azure AD Sync eftersom dessa verktyg nu är inaktuella och inte längre stöds från och med den 13 april 2017.

De två identitetssynkroniseringsverktyg som är inaktuella erbjöds för enskilda skogskunder (DirSync) och för flera skogar och andra avancerade kunder (Azure AD Sync). Dessa äldre verktyg har ersatts med en enda lösning som är tillgänglig för alla scenarier: Azure AD Connect. Det erbjuder nya funktioner, funktionsförbättringar och stöd för nya scenarier. För att kunna fortsätta synkronisera dina lokala identitetsdata till Azure AD och Office 365 rekommenderar vi starkt att du uppgraderar till Azure AD Connect. Microsoft garanterar inte att dessa äldre versioner fungerar efter den 31 december 2017.

Den senaste versionen av DirSync släpptes i juli 2014 och den senaste versionen av Azure AD Sync släpptes i maj 2015.

## <a name="what-is-azure-ad-connect"></a>Vad är Azure AD Connect?
Azure AD Connect är efterföljaren till DirSync och Azure AD Sync. Den kombinerar alla scenarier dessa två stöds. Du kan läsa mer om det när [du integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Schema för utfasning
| Datum | Kommentar |
| --- | --- |
| den 13 april 2016 |Windows Azure Active Directory Sync ("DirSync") och Microsoft Azure Active Directory Sync ("Azure AD Sync") meddelas som inaktuella. |
| den 13 april 2017 |Supporten upphör. Kunderna kan inte längre öppna ett supportärende utan att uppgradera till Azure AD Connect först. |
|den 31 december 2017|Azure AD kanske inte längre accepterar kommunikation från Windows Azure Active Directory Sync ("DirSync") och Microsoft Azure Active Directory Sync ("Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Så här övergår du till Azure AD Connect
Om du kör DirSync finns det två sätt att uppgradera: Uppgradering på plats och parallelldistribution. En uppgradering på plats rekommenderas för de flesta kunder och om du har ett nytt operativsystem och färre än 50 000 objekt. I andra fall rekommenderas att du gör en parallell distribution där din DirSync-konfiguration flyttas till en ny server som kör Azure AD Connect.

| Lösning | Scenario |
| --- | --- |
| [Uppgradera från DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Om du har en befintlig DirSync-server som redan körs.</li> |
| [Uppgradera från Azure AD Sync](how-to-upgrade-previous-version.md) |<li>Om du flyttar från Azure AD Sync.</li> |

Om du vill se hur du gör en uppgradering på plats från DirSync till Azure AD Connect kan du se den här channel 9-videon:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
**F: Jag har fått ett e-postmeddelande från Azure-teamet och/eller ett meddelande från Meddelandecentret för Office 365, men jag använder Connect.**  
Meddelandet skickades också till kunder som använder Azure AD Connect med ett byggnummer 1.0. \*0,0 (med en pre-1.1 release). Microsoft rekommenderar kunder att hålla sig aktuella med Azure AD Connect-versioner. Den [automatiska uppgraderingsfunktionen](how-to-connect-install-automatic-upgrade.md) som introducerades i 1.1 gör det enkelt att alltid ha en ny version av Azure AD Connect installerad.

**F: Slutar DirSync/Azure AD Sync att fungera den 13 april 2017?**  
DirSync/Azure AD Sync fortsätter att fungera den 13 april 2017.  Azure AD kan dock inte längre acceptera kommunikation från DirSync/Azure AD Sync efter den 31 december 2017.

**F: Vilka DirSync-versioner kan jag uppgradera från?**  
Det stöds för att uppgradera från alla DirSync-versionen som för närvarande används. 

**F: Hur är det med Azure AD Connector för FIM/MIM?**  
Azure AD Connector för FIM/MIM har **inte** meddelats som inaktuell. Det är på **funktionen frysa**; inga nya funktioner läggs till och den får inga buggfixar. Microsoft rekommenderar kunder som använder den för att planera att flytta från den till Azure AD Connect. Vi rekommenderar starkt att du inte startar några nya distributioner med den. Denna koppling kommer att meddelas föråldrad i framtiden.

## <a name="additional-resources"></a>Ytterligare resurser
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
