---
title: 'Azure AD Connect: Vad är ADConnectivityTool PowerShell-modulen | Microsoft-dokument'
description: Det här dokumentet introducerar den nya ADConnectivity PowerShell-modulen och hur den kan användas för felsökning.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64571128"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Felsöka Azure AD-anslutning med ADConnectivityTool PowerShell-modulen

ADConnectivity-verktyget är en PowerShell-modul som används i något av följande:

- Under installationen när ett nätverksanslutningsproblem förhindrar att Active Directory-autentiseringsuppgifterna autentiseringsuppgifterna i guiden har validerats.
- Bokför installation av en användare som anropar funktionerna från en PowerShell-session.

Verktyget finns i: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool under installationen

På sidan **Anslut dina kataloger,** i Azure AD Connect-guiden, om ett nätverksproblem uppstår, använder ADConnectivityTool automatiskt en av sina funktioner för att avgöra vad som händer.  Något av följande kan betraktas som nätverksproblem:

- Namnet på skogen som användaren angav skrevs felaktigt, eller sa att Forest inte finns 
- UDP-port 389 är stängd i de domänkontrollanter som är associerade med skogen som användaren angav
- Autentiseringsuppgifterna i fönstret AD-skogskonto har inte behörighet att hämta domänkontrollanterna som är associerade med målskogen
- Alla TCP-portar 53, 88 eller 389 är stängda i domänkontrollanterna som är associerade med skogen som användaren tillhandahöll 
- Både UDP 389 och en TCP-port (eller portar) är stängda
- DNS kunde inte lösas för den angivna skogen och\eller dess associerade domänkontrollanter

När något av dessa problem hittas visas ett relaterat felmeddelande i AADConnect-guiden:


![Fel](media/how-to-connect-adconnectivitytools/error1.png)

När vi till exempel försöker lägga till en katalog på skärmen **Anslut dina kataloger** måste Azure AD Connect verifiera detta och förväntar sig att kunna kommunicera med en domänkontrollant via port 389.  Om det inte kan, kommer vi att se felet som visas i skärmdumpen ovan.  

Vad som faktiskt händer bakom kulisserna, är att `Start-NetworkConnectivityDiagnosisTools` Azure AD Connect anropar funktionen.  Den här funktionen anropas när valideringen av autentiseringsuppgifter misslyckas på grund av ett problem med nätverksanslutningen.

Slutligen genereras en detaljerad loggfil när verktyget anropas från guiden. Loggen finns i **C:\ProgramData\AADConnect\ADConnectivityTool-\<datum>-\<tid>.log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools efter installation
När Azure AD Connect har installerats kan någon av funktionerna i ADConnectivityTools PowerShell-modulen användas.  

Du hittar referensinformation om funktionerna i [ADConnectivityTools Reference](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-anslutningValidation

Vi kommer att ropa ut den här funktionen eftersom den **bara** kan anropas manuellt när ADConnectivityTool.psm1 har importerats till PowerShell. 

Den här funktionen kör samma logik som Azure AD Connect-guiden körs för att validera de angivna AD-autentiseringsuppgifterna.  Men det ger en mycket mer utförlig förklaring om problemet och en föreslagen lösning. 

Anslutningsvalideringen består av följande steg:
-   Hämta Domain FQDN-objekt (fullständigt kvalificerat domännamn)
-   Verifiera att om användaren valde "Skapa nytt AD-konto" tillhör dessa autentiseringsuppgifter gruppen Företagsadministratörer
-   Hämta Forest FQDN-objekt
-   Bekräfta att minst en domän som är associerad med det tidigare erhållna Forest FQDN-objektet kan nås
-   Kontrollera att skogens funktionsnivå är Windows Server 2003 eller senare.

Användaren kan lägga till en katalog om alla dessa åtgärder har utförts.

Om användaren kör den här funktionen när ett problem har lösts (eller om det inte finns några problem alls) kommer utdata att indikera att användaren går tillbaka till Azure AD Connect-guiden och försöker infoga autentiseringsuppgifterna igen.



## <a name="next-steps"></a>Efterföljande moment
- [Azure AD Connect: Konton och behörigheter](reference-connect-accounts-permissions.md)
- [Snabb installation](how-to-connect-install-express.md)
- [Anpassad installation](how-to-connect-install-custom.md)
- [Referens för ADConnectivityTools](reference-connect-adconnectivitytools.md)

