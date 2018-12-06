---
title: 'Azure AD Connect: Felsöka objektsynkronisering | Microsoft Docs'
description: Det här avsnittet innehåller anvisningar att felsöka problem med synkronisering av objektet med hjälp av felsökningsaktiviteten.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: cfd963ae38d42d245f2eec1ddb76c7af316039ba
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961135"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Felsöka objektsynkronisering med Azure AD Connect-synkronisering
Den här artikeln innehåller steg för felsökning av problem med synkronisering av objektet med hjälp av felsökningsaktiviteten. Om du vill se hur felsökning fungerar i Azure Active Directory (Azure AD) Connect, se [den här korta videon](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Felsökningsaktiviteten
För Azure AD ansluta distribution med version 1.1.749.0 eller högre, använda av felsökningsaktiviteten i guiden för att felsöka problem med synkronisering av objektet. För tidigare versioner måste du felsöka manuellt enligt [här](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Köra aktiviteten felsökning i guiden
Om du vill köra aktiviteten felsökning i guiden, utför du följande steg:

1.  Öppna en ny Windows PowerShell-session på din Azure AD Connect-server med alternativet Kör som administratör.
2.  Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted`.
3.  Starta Azure AD Connect-guiden.
4.  Gå till sidan ytterligare aktiviteter, avancerade och klicka på Nästa.
5.  På sidan om felsökning, klickar du på Starta till startmenyn felsökning i PowerShell.
6.  Välj Felsök synkronisering av objektet i huvudmenyn.
![](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Felsöka indataparametrar
Följande indataparametrar krävs av av felsökningsaktiviteten:
1.  **Objekt-huvudnamnet** – detta är det unika namnet på det objekt som du behöver felsöka
2.  **AD-Kopplingsnamn** – det här är namnet på den AD-skog där objektet ovan finns.
3.  Autentiseringsuppgifter som global Azure AD-klient ![](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultatet av av felsökningsaktiviteten
Av felsökningsaktiviteten utför följande kontroller:

1.  Identifiera UPN matchningsfel om objektet har synkroniserats till Azure Active Directory
2.  Kontrollera om objektet är filtrerad på grund av filtrering av domän
3.  Kontrollera om objektet är filtrerad på grund av att OU-filtrering
4.  Kontrollera om synkronisering av objektet har blockerats på grund av en länkad postlåda
5. Kontrollera om objektet är dynamiskt distributionsgrupp som inte ska synkroniseras

Resten av det här avsnittet beskriver specifika resultat som returneras av aktiviteten. I båda fallen ger uppgiften en analys som följt av rekommenderade åtgärder för att lösa problemet.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Identifiera UPN matchningsfel om objektet har synkroniserats till Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>UPN-suffixet verifieras inte med Azure AD-klient
När UserPrincipalName (UPN) / suffix för alternativt inloggnings-ID är inte verifierad med Azure AD-klient och Azure Active Directory ersätter de UPN-suffix med standard domain name ”onmicrosoft.com”.

![](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Om du ändrar UPN-Suffix från en federerad domän till en annan federerad domän
Azure Active Directory tillåter inte att synkroniseringen av UserPrincipalName (UPN) / alternativt inloggnings-ID suffix ändring från en federerad domän till en annan federerad domän. Detta gäller för domäner som har verifierats med Azure AD-klient och har den autentiseringstyp som federerade.

![](media/tshoot-connect-objectsync/objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Azure AD-klient DirSync-funktion ”SynchronizeUpnForManagedUsers” är inaktiverad
När Azure AD-klient DirSync-funktion ”SynchronizeUpnForManagedUsers” är inaktiverad, tillåter inte synkronisering uppdateringar till UserPrincipalName/alternativa inloggnings-ID för licensierade användarkonton med hanterad autentisering i Azure Active Directory.

![](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objekt filtreras på grund av filtrering av domän
### <a name="domain-is-not-configured-to-sync"></a>Domänen har inte konfigurerats för synkronisering
Objektet är utanför på grund av domänen som har inte konfigurerats. I exemplet nedan är synkroniserad omfattning i objektet som den domän som den tillhör filtreras från synkronisering.

![](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Domänen är konfigurerad för att synkronisera men saknar kör profiler/köra steg
Objektet är utanför omfånget eftersom domänen saknas kör profiler/köra steg. Objektet är osynkroniserad omfång som den domän som den tillhör saknar kör stegen för den fullständiga importen körningsprofil i exemplet nedan.
![](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Objekt filtreras på grund av att OU-filtrering
Objektet är inte synkroniserade omfång på grund av OU-filtrering konfiguration. I exemplet nedan objektet tillhör OU = NoSync, DC = bvtadwbackdc, DC = com.  Den här Organisationsenheten ingår inte i omfånget för synkronisering.</br>

![ORGANISATIONSENHET](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Länkad postlåda problemet
En länkad postlåda ska associeras med en extern huvudkonto som finns i en annan betrodd kontoskog. Om det finns ingen sådan externa huvudkonto kommer Azure AD Connect synkroniserar inte användaren konto motsvarar länkad postlåda i Exchange-skogen till Azure AD-klient.</br>
![Länkad postlåda](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Dynamisk distributionsgrupp problemet
På grund av olika skillnader mellan lokala synkroniserar Active Directory och Azure Active Directory, Azure AD Connect inte dynamisk distributionsgrupper till Azure AD-klient.

![Dynamisk distributionsgrupp](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>HTML-rapport
Förutom att analysera objektet genererar av felsökningsaktiviteten även en HTML-rapport som har allt känt objektet. Den här HTML-rapport kan delas med supportteamet att göra ytterligare felsökning, om det behövs.

![](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
