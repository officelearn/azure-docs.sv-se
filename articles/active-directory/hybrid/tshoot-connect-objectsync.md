---
title: 'Azure AD Connect: Felsöka objektsynkronisering | Microsoft-dokument'
description: Det här avsnittet innehåller steg för felsÃ¶kning av problem med objektsynkronisering med hjälp av felsÃ¶kningsuppgiften.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e56d4d94e38e5095ef2223d0cc2875cbf1dcd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64919123"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Felsöka objektsynkronisering med Azure AD Connect-synkronisering
Den här artikeln innehåller anvisningar för felsökning av problem med objektsynkronisering med hjälp av felsökningsuppgiften. Om du vill se hur felsökning fungerar i Azure Active Directory (Azure AD) Connect kan du titta på [den här korta videon](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Felsöka uppgift
För Azure AD Connect-distribution med version 1.1.749.0 eller högreanvänder du felsökningsaktiviteten i guiden  för att felsöka problem med synkronisering av objekt. För tidigare versioner, felsöka manuellt enligt beskrivningen [här](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Köra felsökningsuppgiften i guiden
Så här kör du felsökningsuppgiften i guiden:

1.  Öppna en ny Windows PowerShell-session på Din Azure AD Connect-server med alternativet Kör som administratör.
2.  Kör `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`eller .
3.  Starta Azure AD Connect-guiden.
4.  Navigera till sidan Ytterligare uppgifter, välj Felsöka och klicka på Nästa.
5.  På sidan Felsökning klickar du på Starta för att starta felsökningsmenyn i PowerShell.
6.  Välj Felsöka objektsynkronisering på huvudmenyn.
![Felsöka objektsynkronisering](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Felsöka indataparametrar
Följande indataparametrar behövs av felsökningsaktiviteten:
1.  **Objektsärende namn** – Det här är det unika namnet på objektet som behöver felsökas
2.  **AD-kopplingsnamn** – Det här är namnet på AD-skogen där objektet ovan finns.
3.  Globala administratörsautentiseringsuppgifter ![för Azure AD-klienten](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultatet av felsökningsuppgiften
Felsökningsuppgiften utför följande kontroller:

1.  Identifiera UPN-felmatchning om objektet synkroniseras med Azure Active Directory
2.  Kontrollera om objektet filtreras på grund av domänfiltrering
3.  Kontrollera om objektet filtreras på grund av filtrering av organisationsenhet
4.  Kontrollera om objektsynkronisering är blockerad på grund av en länkad postlåda
5. Kontrollera om objektet är dynamisk distributionsgrupp som inte ska synkroniseras

I resten av det här avsnittet beskrivs specifika resultat som returneras av aktiviteten. I varje enskilt fall ger aktiviteten en analys följt av rekommenderade åtgärder för att lösa problemet.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Identifiera UPN-felmatchning om objektet synkroniseras med Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>UPN Suffix verifieras INTE med Azure AD-klient
När USERPrincipalName (UPN)/Alternate Login ID-suffix inte verifieras med Azure AD-klienten ersätter Azure Active Directory UPN-suffixen med standarddomännamnet "onmicrosoft.com".

![Azure AD ersätter UPN](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Ändra UPN Suffix från en federerad domän till en annan federerad domän
Azure Active Directory tillåter inte synkronisering av BYTE AV USERPrincipalName (UPN)/Alternate Login ID-suffix från en federerad domän till en annan federerad domän. Detta gäller för domäner som verifieras med Azure AD-klienten och har autentiseringstypen som Federerad.

![Ingen UPN-synkronisering från en federerad domän till en annan](media/tshoot-connect-objectsync/objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Azure AD Tenant DirSync-funktionen "SynchronizeUpnForManagedUsers" är inaktiverad
När Azure AD Tenant DirSync-funktionen "SynchronizeUpnForManagedUsers" är inaktiverad tillåter Azure Active Directory inte synkroniseringsuppdateringar till UserPrincipalName/Alternate Login ID för licensierade användarkonton med hanterad autentisering.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objektet filtreras på grund av domänfiltrering
### <a name="domain-is-not-configured-to-sync"></a>Domänen är inte konfigurerad för synkronisering
Objektet är utanför omfånget på grund av att domänen inte har konfigurerats. I exemplet nedan är objektet inte synkroniserat omfång eftersom den domän som det tillhör filtreras från synkroniseringen.

![Domänen är inte konfigurerad för synkronisering](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Domänen är konfigurerad för synkronisering men saknas körprofiler/körningssteg
Objektet är utanför omfånget eftersom domänen saknar körprofiler/körsteg. I exemplet nedan är objektet inte synkroniserat omfång eftersom den domän som det tillhör saknas körsteg för profilen för fullständig importkörning.
![saknade körningsprofiler](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Objektet filtreras på grund av ou-filtrering
Objektet är inte synkroniserat på grund av ou-filtreringskonfiguration. I exemplet nedan tillhör objektet OU=NoSync,DC=bvtadwbackdc,DC=com.  Den här organisationsenheten ingår inte i synkroniseringsomfånget.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problem med länkad postlåda
En länkad postlåda ska associeras med ett externt huvudkonto i en annan betrodd kontoskog. Om det inte finns något sådant externt huvudkonto synkroniseras inte användarkontot med den länkade postlådan i Exchange-skogen till Azure AD-klienten.</br>
![Länkad postlåda](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Dynamisk distributionsgrupp fråga
På grund av olika skillnader mellan lokal Active Directory och Azure Active Directory synkroniserar Azure AD Connect inte dynamiska distributionsgrupper till Azure AD-klienten.

![Dynamisk distributionsgrupp](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>HTML-rapport
Förutom att analysera objektet genererar felsökningsuppgiften också en HTML-rapport som har allt känt om objektet. Den här HTML-rapporten kan delas med supportteamet för att göra ytterligare felsökning om det behövs.

![HTML-rapport](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
