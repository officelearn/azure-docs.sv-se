---
title: Förstå Azure AD Connect 1.4. xx. x och enhetens avvisade | Microsoft Docs
description: Det här dokumentet beskriver ett problem som uppstår i version 1.4. xx. x Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 1ba17feea880bb55c3b4a14a06b3d803dba2350a
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316959"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Förstå Azure AD Connect 1.4. xx. x och enheten försvinner
Med version 1.4. xx. x Azure AD Connect kan vissa kunder se vissa eller alla sina Windows-enheter försvinner från Azure AD. Detta är inte en orsak till betänkligheter eftersom dessa enhets identiteter inte används av Azure AD under auktoriseringen för villkorlig åtkomst. Den här ändringen tar inte bort några Windows-enheter som har registrerats korrekt med Azure AD för Hybrid Azure AD-anslutning.

Om du ser borttagningen av enhets objekt i Azure AD som överskrider tröskelvärdet för export borttagning, rekommenderar vi att kunden tillåter att borttagningarna går igenom. [Gör så här: Tillåt borttagningar som flödar när de överstiger tröskelvärdet för borttagning](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Bakgrund
Windows-enheter som har registrerats som hybrid Azure AD-anslutna representeras i Azure AD som enhets objekt. Dessa enhets objekt kan användas för villkorlig åtkomst. Windows 10-enheter synkroniseras till molnet via Azure AD Connect och Windows-enheter på nivån i nivå registreras direkt med antingen AD FS eller sömlös enkel inloggning.

## <a name="windows-10-devices"></a>Windows 10-enheter
Endast Windows 10-enheter med ett speciellt userCertificate som kon figurer ATS av hybrid Azure AD Join kommer att synkroniseras med molnet genom att Azure AD Connect. I tidigare versioner av Azure AD Connect det här kravet inte rigoröst tvingande, vilket resulterar i onödiga enhets objekt i Azure AD. Sådana enheter i Azure AD är alltid i vänte läge eftersom enheterna inte var avsedda att registreras med Azure AD. 

Den här versionen av Azure AD Connect synkroniserar bara Windows 10-enheter som är korrekt konfigurerade för att vara hybrid Azure AD-anslutna. Windows 10 enhets objekt utan Azure AD Join-userCertificate kommer att tas bort från Azure AD.

## <a name="down-level-windows-devices"></a>Windows-enheter med äldre nivåer
Azure AD Connect bör aldrig synkronisera [Windows-enheter på detalj nivå](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Alla enheter i Azure AD som synkroniserades tidigare kommer nu att tas bort från Azure AD. Om Azure AD Connect försöker ta bort [Windows-enheter på äldre nivå](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)är enheten inte den som skapades av [Microsoft-Workplace Join för MSI-versioner för icke-Windows 10-datorer](https://www.microsoft.com/download/details.aspx?id=53554) och den kan inte användas av någon annan Azure AD-funktion.

Vissa kunder kan behöva [gå tillbaka till: Planera implementeringen](../devices/hybrid-azuread-join-plan.md) av hybrid Azure Active Directory för att få sina Windows-enheter registrerade korrekt och se till att sådana enheter helt kan delta i enhets-baserad villkorlig åtkomst. 

## <a name="next-steps"></a>Nästa steg
- [Azure AD Connect versions historik](reference-connect-version-history.md)
