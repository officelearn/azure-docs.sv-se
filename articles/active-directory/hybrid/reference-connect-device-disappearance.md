---
title: Förstå Azure AD Connect 1.4.xx.x och försvinnande av enheter | Microsoft-dokument
description: I det här dokumentet beskrivs ett problem som uppstår med version 1.4.xx.x av Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176022"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Förstå Azure AD Connect 1.4.xx.x och försvinnande av enheter
Med version 1.4.xx.x av Azure AD Connect kan vissa kunder se att vissa eller alla sina Windows-enheter försvinner från Azure AD. Detta är inte en anledning till oro, eftersom dessa enhetsidentiteter inte används av Azure AD under godkännande för villkorlig åtkomst. Den här ändringen tar inte bort alla Windows-enheter som har registrerats korrekt med Azure AD för Hybrid Azure AD Join.

Om du ser borttagningen av enhetsobjekt i Azure AD som överskrider tröskelvärdet för exportborttagning, rekommenderas att kunden tillåter att borttagningarna går igenom. [Så här: tillåt borttagningar att flöda när de överskrider borttagningströskeln](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Bakgrund
Windows-enheter som registrerats som Hybrid Azure AD-anslutna representeras i Azure AD som enhetsobjekt. Dessa enhetsobjekt kan användas för villkorlig åtkomst. Windows 10-enheter synkroniseras till molnet via Azure AD Connect, nednivå Windows-enheter registreras direkt med antingen AD FS eller sömlös enkel inloggning.

## <a name="windows-10-devices"></a>Windows 10-enheter
Endast Windows 10-enheter med ett specifikt userCertificate-attributvärde som konfigurerats av Hybrid Azure AD Join ska synkroniseras till molnet av Azure AD Connect. I tidigare versioner av Azure AD Connect har det här kravet inte tillämpats strikt, vilket resulterade i onödiga enhetsobjekt i Azure AD. Sådana enheter i Azure AD stannade alltid i "väntande" tillstånd eftersom dessa enheter inte var avsedda att registreras med Azure AD. 

Den här versionen av Azure AD Connect synkroniserar endast Windows 10-enheter som är korrekt konfigurerade för att vara Hybrid Azure AD-anslutna. Windows 10-enhetsobjekt utan Azure AD-anslutningsspecifika userCertificate tas bort från Azure AD.

## <a name="down-level-windows-devices"></a>Windows-enheter på lägre nivå
Azure AD Connect ska aldrig synkronisera [Windows-enheter på lägre nivå](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Alla enheter i Azure AD som tidigare synkroniserats felaktigt kommer nu att tas bort från Azure AD. Om Azure AD Connect försöker ta bort [Windows-enheter på lägre nivå](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)är enheten inte den som skapades av [Microsoft Workplace Join för icke-Windows 10-datorer MSI](https://www.microsoft.com/download/details.aspx?id=53554) och den kan inte förbrukas av någon annan Azure AD-funktion.

Vissa kunder kan behöva gå tillbaka till [How To: Planera implementeringen av hybrid-Azure Active Directory-anslutning](../devices/hybrid-azuread-join-plan.md) för att få sina Windows-enheter registrerade korrekt och se till att sådana enheter kan delta fullt ut i enhetsbaserad villkorlig åtkomst. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Hur kan jag verifiera vilka enheter som tas bort med den här uppdateringen?

Om du vill kontrollera vilka enheter som tas bort kan du använda det här PowerShell-skriptet:https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Det här skriptet genererar en rapport om certifikat som lagras i Active Directory-datorobjekt, särskilt certifikat som utfärdats av hybrid-Azure AD-kopplingsfunktionen.
Den kontrollerar certifikaten som finns i egenskapen UserCertificate för ett datorobjekt i AD och validerar för varje certifikat som inte har upphört att gälla om certifikatet har utfärdats för hybrid Azure AD-kopplingsfunktionen (dvs. ämnesnamn matchar CN={ObjectGUID}).
Innan azure AD Connect skulle synkronisera till Azure AD alla datorer som innehöll minst ett giltigt certifikat men med början på Azure AD Connect version 1.4 kan synkroniseringsmotorn identifiera Hybrid Azure AD-kopplingscertifikat och "cloudfilter" datorobjekt från synkronisering till Azure AD om det inte finns ett giltigt Hybrid Azure AD-anslutningscertifikat.
Azure AD-enheter som redan har synkroniserats med AD men inte har ett giltigt Hybrid Azure AD-anslutningscertifikat tas bort (CloudFiltered=TRUE) av synkroniseringsmotorn.

## <a name="next-steps"></a>Efterföljande moment
- [Versionshistorik för Azure AD Connect](reference-connect-version-history.md)
