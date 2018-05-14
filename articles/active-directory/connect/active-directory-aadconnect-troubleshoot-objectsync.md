---
title: 'Azure AD Connect: Felsöka objekt synkronisering | Microsoft Docs'
description: Det här avsnittet innehåller anvisningar att felsöka problem med synkronisering av objektet med hjälp av aktiviteten felsökning.
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
ms.date: 05/1/2018
ms.author: billmath
ms.openlocfilehash: a28a377ec3872fad0121636070b6604eaa415b30
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Felsöka objekt synkronisering med Azure AD Connect-synkronisering
Det här dokumentet innehåller anvisningar att felsöka problem med synkronisering av objektet med hjälp av aktiviteten felsökning.

## <a name="troubleshooting-task"></a>Felsökning av aktivitet
För Azure Active Directory (AAD) ansluta distribution med version 1.1.749.0 eller högre, använda aktiviteten felsökning i guiden för att felsöka objekt synkroniseringsproblem. För tidigare versioner måste du felsöka manuellt enligt [här](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Kör aktiviteten felsökning i guiden
Utför följande steg om du vill köra aktiviteten felsökning i guiden:

1.  Öppna en ny Windows PowerShell-session på Azure AD Connect-servern med Kör som administratör.
2.  Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted`.
3.  Starta Azure AD Connect-guiden.
4.  Gå till sidan ytterligare aktiviteter, Välj felsökning och klicka på Nästa.
5.  På sidan felsökning klickar du på Starta om du vill starta menyn felsökning i PowerShell.
6.  Välj felsöka objekt synkronisering på huvudmenyn.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Felsöka indataparametrar
Följande indataparametrarna behövs för felsökning uppgiften:
1.  **Objekt-huvudnamnet** – detta är det unika namnet på det objekt som måste felsökning
2.  **Namn på AD-koppling** – det här är namnet på den AD-skog där objektet ovan finns.
3.  Global administratörsautentiseringsuppgifter för Azure AD-klient ![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultatet av aktiviteten felsökning
Felsökning aktiviteten utför följande kontroller:

1.  Identifiera UPN-matchningsfel om objektet är synkroniserad med Azure Active Directory
2.  Kontrollera om objektet är filtrerad på grund av domän filtrering
3.  Kontrollera om objektet är filtrerad på grund av att organisationsenhetsfiltrering
4.  Kontrollera om objektet synkroniseringen har blockerats på grund av en länkad postlåda
5. Kontrollera om objektet är dynamisk distributionsgrupp som inte ska synkroniseras

Resten av det här avsnittet beskriver specifika resultat som returneras av aktiviteten. I varje fall ger aktiviteten en analys följt av rekommenderade åtgärder för att lösa problemet.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Identifiera UPN-matchningsfel om objektet är synkroniserad med Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>UPN-suffixet verifieras inte med Azure AD-klient
När UserPrincipalName (UPN) / alternativt inloggnings-ID suffix verifieras inte med Azure AD-klient och Azure Active Directory ersätter UPN-suffix med standard domain name ”onmicrosoft.com”.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Om du ändrar UPN-Suffix från en federerad domän till en annan federerad domän
Azure Active Directory inte tillåter synkronisering av UserPrincipalName (UPN) / alternativt inloggnings-ID suffix ändring från en federerad domän till en annan federerad domän. Detta gäller för domäner som har verifierats med Azure AD-klient och har den autentiseringstyp som federerat.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Azure AD-klient DirSync-funktionen 'SynchronizeUpnForManagedUsers' är inaktiverad
När Azure AD-klient DirSync 'SynchronizeUpnForManagedUsers' är inaktiverat, tillåter inte synkronisering uppdateringar till UserPrincipalName/alternativa inloggnings-ID för licensierade användarkonton med hanterade autentisering i Azure Active Directory.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objekt filtreras på grund av domän filtrering
### <a name="domain-is-not-configured-to-sync"></a>Domänen är inte konfigurerad för synkronisering
Objektet ligger utanför omfånget på grund av domän inte konfigurerats. I exemplet nedan är synkroniserad omfattning i objektet som den domän som den tillhör filtreras från synkronisering.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Domänen är konfigurerad för att synkronisera men saknar kör profiler/köra steg
Objektet är utanför omfånget som saknas i domänen kör profiler/köra steg. Objektet är synkroniserad omfång som den domän som den tillhör saknas kör steg för den fullständiga importen kör profil i exemplet nedan.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Objekt filtreras på grund av att organisationsenhetsfiltrering
Objektet är inte synkroniserat scope på grund av OU-filtrering konfiguration. I exemplet nedan objektet tillhör OU = NoSync, DC = bvtadwbackdc, DC = com.  Organisationsenheten ingår inte i omfånget för synkronisering.</br>

![ORGANISATIONSENHET](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch7.png)

## <a name="linked-mailbox-issue"></a>Länkad postlåda problemet
En länkad postlåda ska associeras med ett externt master konto finns i en annan betrodd kontoskog. Om det finns ingen sådan externa master konto, så Azure AD Connect synkroniserar inte användaren konto motsvarar länkad postlåda i Exchange-skogen till Azure AD-klient.</br>
![Länkad postlåda](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Dynamisk distributionsgrupp problemet
På grund av olika skillnader mellan lokala synkroniserar Active Directory och Azure Active Directory, Azure AD Connect inte dynamiska distributionsgrupper till Azure AD-klient.

## <a name="html-report"></a>HTML-rapport
Förutom att analysera objektet genererar felsökning uppgiften också en HTML-rapport som har allt kända om objektet. HTML-rapport kan delas med supportteamet att göra ytterligare felsökning, om det behövs.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch8.png)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
