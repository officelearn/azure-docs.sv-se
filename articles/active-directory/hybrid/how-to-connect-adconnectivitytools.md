---
title: 'Azure AD Connect: Vad är PowerShell-modulen ADConnectivityTool | Microsoft Docs'
description: Det här dokumentet introducerar den nya ADConnectivity PowerShell-modulen och hur den kan användas för att felsöka.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5340cd8c802df4ffbe0207b5401d2fee4e207e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571128"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Felsökning av Azure AD-anslutningar med ADConnectivityTool PowerShell-modulen

Verktyget ADConnectivity är en PowerShell-modul som används i någon av följande:

- Under installationen när ett problem med nätverksanslutningen förhindrar lyckad validering av Active Directory-autentiseringsuppgifter användaren som angavs i guiden.
- Efter installation av en användare som anropar funktionerna från en PowerShell-session.

Verktyget finns i: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool under installationen

På den **Anslut dina kataloger** sidan i den Azure AD Connect-guiden, om ett problem inträffar, ADConnectivityTool som automatiskt använda någon av dess funktioner för att fastställa vad som händer.  Nätverksproblem kan betraktas som något av följande:

- Namnet på skogen den angivna användaren angav du felaktigt eller nämnda skogen finns inte 
- UDP-port 389 stängs i domänkontrollanter som är associerade med den skogen som användaren har angett
- Autentiseringsuppgifterna som du angav i fönstret ”AD-skogskonto' har inte behörighet att hämta de domänkontrollanter som är associerade med målet skog
- Någon TCP-port 53, 88 eller 389 stängs i domänkontrollanter som är associerade med den skogen som användaren har angett 
- Både 389 UDP och TCP-port (eller portar) stängs
- DNS inte kunde matchas för den angivna skogen och/eller dess associerade domänkontrollanter

När någon av de här problemen finns visas ett felmeddelande för relaterade i AADConnect-guiden:


![Fel](media/how-to-connect-adconnectivitytools/error1.png)

Till exempel när vi försöker lägga till en katalog på den **Anslut dina kataloger** skärmen, Azure AD Connect måste bekräfta det och förväntar sig att kunna kommunicera med en domänkontrollant via port 389.  Om den inte ser vi felet som visas i skärmbilden ovan.  

Vad som faktiskt händer i bakgrunden är att Azure AD Connect anropar den `Start-NetworkConnectivityDiagnosisTools` funktion.  Den här funktionen anropas när valideringen av autentiseringsuppgifter misslyckas på grund av ett problem med nätverksanslutningen.

Slutligen genereras en detaljerad loggfil när verktyget anropas från guiden. Loggen finns i **C:\ProgramData\AADConnect\ADConnectivityTool-\<datum >-\<tid > .log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools efter installation
När Azure AD Connect har installerats, kan alla funktioner i ADConnectivityTools PowerShell-modulen användas.  

Du kan hitta information om funktionerna i den [ADConnectivityTools referens](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Vi kommer att anropa den här funktionen eftersom den kan **endast** anropas manuellt när ADConnectivityTool.psm1 har importerats till PowerShell. 

Den här funktionen utför samma logik som i Azure AD Connect-guiden körs för att verifiera de angivna autentiseringsuppgifterna för AD.  Men det ger en mycket mer utförlig förklaring om problemet och en föreslagen lösning. 

Valideringen av anslutning består av följande steg:
-   Hämta objektet för domännamn FQDN (fullständigt kvalificerade domännamn)
-   Verifiera att, om användaren har valt Skapa nytt AD-konto, autentiseringsuppgifterna tillhör gruppen Företagsadministratörer
-   Get-skog FQDN-objekt
-   Bekräfta att minst en domän som är associerade med objektet tidigare hämtades skog FQDN kan nås
-   Kontrollera att funktionsnivån för skogen är Windows Server 2003 eller senare.

Användaren kommer att kunna lägga till en katalog om alla dessa åtgärder har körts.

Om användaren som kör den här funktionen när ett problem kan lösas (eller om inga problem finns på alla) visar utdata för användaren att gå tillbaka till den Azure AD Connect-guiden och försök att infoga autentiseringsuppgifterna igen.



## <a name="next-steps"></a>Nästa steg
- [Azure AD Connect: Konton och behörigheter](reference-connect-accounts-permissions.md)
- [Snabbinstallation](how-to-connect-install-express.md)
- [Anpassad Installation](how-to-connect-install-custom.md)
- [ADConnectivityTools referens](reference-connect-adconnectivitytools.md)

