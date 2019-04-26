---
title: Uppgradera från DirSync och Azure AD Sync | Microsoft Docs
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381186"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Uppgradera Windows Azure Active Directory Sync och Azure Active Directory Sync
Azure AD Connect är det bästa sättet att ansluta din lokala katalog till Azure AD och Office 365. Det här är det hög tid att uppgradera till Azure AD Connect från Windows Azure Active Directory Sync (DirSync) eller Azure AD Sync eftersom dessa verktyg nu är föråldrade och stöds inte längre från och med 13 April 2017.

Synkroniseringsverktyg för två identitet är föråldrade var erbjuds för en enkel skog kunder (DirSync) och för flera skogar och andra avancerade användare (Azure AD Sync). Dessa äldre verktyg har ersatts med en enda lösning som är tillgänglig för alla scenarier: Azure AD Connect. Den erbjuder nya funktioner och funktionsförbättringar av stöd för nya scenarier. För att kunna fortsätta att synkronisera dina lokala identitetsdata till Azure AD och Office 365, vi rekommenderar att du uppgraderar till Azure AD Connect. Microsoft garanterar inte dessa äldre versioner att fungera efter 31 December 2017.

Den senaste versionen av DirSync gavs ut i juli 2014 och den senaste versionen av Azure AD Sync som gavs ut i maj 2015.

## <a name="what-is-azure-ad-connect"></a>Vad är Azure AD Connect?
Azure AD Connect är efterföljaren till DirSync och Azure AD Sync. Alla scenarier kombinerar dessa två som stöds. Du kan läsa mer om det i [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Utfasningsschemat
| Date | Kommentar |
| --- | --- |
| Den 13 april 2016 |Windows Azure Active Directory Sync (”DirSync”) och Microsoft Azure Active Directory Sync (”Azure AD Sync”) tillkännages som föråldrade. |
| Den 13 april 2017 |Support upphör. Kunder kommer inte längre att kunna öppna ett supportärende utan att först uppgradera till Azure AD Connect. |
|Den 31 december 2017|Azure AD kan inte längre accepterar kommunikation från Windows Azure Active Directory Sync (”DirSync”) och Microsoft Azure Active Directory Sync (”Azure AD Sync”).

## <a name="how-to-transition-to-azure-ad-connect"></a>Hur du övergår till Azure AD Connect
Om du kör DirSync, finns det två sätt som du kan uppgradera: Plats-uppgradering, parallell distribution. En uppgradering på plats rekommenderas för de flesta kunder och om du har en nyligen genomförd operativsystem och mindre än 50 000 objekt. I annat fall rekommenderas att du gör en parallell distribution där dina DirSync-konfigurationen flyttas till en ny server som kör Azure AD Connect.

| Lösning | Scenario |
| --- | --- |
| [Uppgradera från DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Om du har en befintlig DirSync-server som redan körs.</li> |
| [Uppgradera från Azure AD-synkronisering](how-to-upgrade-previous-version.md) |<li>Om du flyttar från Azure AD Sync.</li> |

Om du vill se hur du gör en uppgradering från DirSync till Azure AD Connect läser den här videon på Channel 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
**F: Jag har fått ett e-postmeddelande från Azure-teamet och/eller ett meddelande från meddelandecenter för Office 365, men jag använder Connect.**  
Meddelandet har också skickas till kunder som använder Azure AD Connect med ett build-nummer 1.0. \*.0 (med en i förväg 1.1-version). Microsoft rekommenderar att kunder kan hålla dig uppdaterad med Azure AD Connect-versioner. Den [automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) funktionen som infördes i 1.1 gör det enkelt att alltid har den senaste versionen av Azure AD Connect är installerat.

**F: DirSync/Azure AD Sync slutar att fungera på den 13 April 2017?**  
DirSync/Azure AD Sync fortsätter att fungera på den 13 April 2017.  Azure AD kan dock inte längre accepterar kommunikation från DirSync/Azure AD Sync 31 December 2017.

**F: Vilka DirSync-versioner kan jag uppgradera från?**  
Det går för att uppgradera från någon DirSync-version som används för tillfället. 

**F: Vad är Azure AD Connector för FIM/MIM?**  
Azure AD Connector för FIM/MIM har **inte** har meddelats som föråldrade. Det är på **funktionen låsning**; inga nya funktioner har lagts till och den får inga felkorrigeringar. Microsoft rekommenderar att kunder som använder du planerar att flytta från den till Azure AD Connect. Vi rekommenderar starkt att inte starta alla nya distributioner som använder den. Den här anslutningen kommer att tillkännages inaktuellt i framtiden.

## <a name="additional-resources"></a>Ytterligare resurser
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
