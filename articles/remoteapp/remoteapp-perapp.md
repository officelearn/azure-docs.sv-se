---
title: "Publicera program till enskilda användare i en Azure RemoteApp-samling (förhandsgranskning) | Microsoft Docs"
description: "Lär dig hur du kan publicera appar till enskilda användare, i stället för att vara beroende av grupper i Azure RemoteApp."
services: remoteapp-preview
documentationcenter: 
author: piotrci
manager: mbaldwin
editor: 
ms.assetid: 1fd0539d-fa65-4ea5-a98e-0be0cf580690
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: piotrci
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 20f9e9b31498f20c2a27f0d82d434abad73d2454
ms.lasthandoff: 03/31/2017


---
# <a name="publish-applications-to-individual-users-in-an-azure-remoteapp-collection-preview"></a>Publicera program till enskilda användare i en Azure RemoteApp-samling (förhandsgranskning)
> [!IMPORTANT]
> Azure RemoteApp upphör att gälla den 31 augusti 2017. Läs [meddelandet](https://go.microsoft.com/fwlink/?linkid=821148) för mer information.
> 
> 

Den här artikeln beskriver hur du publicerar program till enskilda användare i en Azure RemoteApp-samling. Det här är en ny funktion i Azure RemoteApp. För tillfället är den endast tillgänglig för tidiga användare som en ”privat förhandsversion” för utvärderingsändamål.

Från början gick det bara att publicera program på ett sätt med Azure RemoteApp: Administratören publicerade appar från avbildningen som sedan var tillgängliga för alla användare i samlingen.

Ett vanligt scenario är att inkludera många program i en enda avbildning och distribuera en samling. På så sätt kan man minska hanteringskostnaderna. Ofta är inte alla program relevanta för alla användare – administratörer skulle föredra att publicera appar till enskilda användare så att de inte ser onödiga program i sin program-feed.

Det är nu möjligt att göra i Azure RemoteApp – för tillfället är det en begränsad funktion i förhandsgranskningen . Här är en kort sammanfattning av de nya funktionerna:

1. En samling kan anges till ett av två lägen:
   
   * Det ursprungliga ”samlingsläget” där alla användare i en samling kan se alla publicerade program. Det är standardläget.
   * Det nya ”programläget” där användarna bara ser program som uttryckligen har tilldelats dem.
2. Just nu kan programläget bara aktiveras med Azure RemoteApp PowerShell-cmdlets.
   
   * I programläget går det inte att hantera användartilldelning i samlingen via Azure-portalen. Användartilldelning måste hanteras via PowerShell-cmdlets.
3. Användarna ser bara de program som publicerats direkt till dem. Men det kan fortfarande vara möjligt för användaren att starta andra program som är tillgängliga på bilden genom att öppna dem direkt i operativsystemet.
   
   * Den här funktionen ger inte en säker låsning av program, den begränsar bara synligheten i denna program-feed.
   * Om du behöver isolera användare från program måste du använda separata samlingar för det.

## <a name="how-to-get-azure-remoteapp-powershell-cmdlets"></a>Hur du hämtar Azure RemoteApp PowerShell-cmdlets
Om du vill prova den nya funktionen för förhandsgranskning behöver du använda Azure PowerShell-cmdlets. För tillfället går det inte att aktivera det nya läget för programpublicering via Azure-hanteringsportalen.

Kontrollera först att du har [Azure PowerShell-modulen](/powershell/azureps-cmdlets-docs) installerad.

Starta sedan PowerShell-konsolen i administratörsläge och kör följande cmdlet:

        Add-AzureAccount

Du uppmanas att ange användarnamn och lösenord för Azure. När du har loggat in kommer du att kunna köra Azure RemoteApp-cmdlets mot dina Azure-prenumerationer.

## <a name="how-to-check-which-mode-a-collection-is-in"></a>Kontrollera vilket läge en samling är i
Kör följande cmdlet:

        Get-AzureRemoteAppCollection <collectionName>

![Kontrollera samlingsläget](./media/remoteapp-perapp/araacllelvel.png)

AclLevel-egenskapen kan ha följande värden:

* Samling: det ursprungliga publiceringsläget. Alla användare ser alla publicerade appar.
* Program: det nya publiceringsläget. Användarna ser bara de appar som publiceras direkt till dem.

## <a name="how-to-switch-to-application-publishing-mode"></a>Växla till läget för programpublicering
Kör följande cmdlet:

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

Programmets publiceringstillstånd bevaras: Till en början ser alla användare alla appar som ursprungligen har publicerats.

## <a name="how-to-list-users-who-can-see-a-specific-application"></a>Visa en lista över användare som kan se ett visst program
Kör följande cmdlet:

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

Detta visar alla användare som kan se programmet.

Obs! Du kan visa programalias (kallas ”app-alias” i ovanstående syntax) genom att köra Get-AzureRemoteAppProgram – Samlingsnamn <collectionName>.

## <a name="how-to-assign-an-application-to-a-user"></a>Tilldela ett program till en användare
Kör följande cmdlet:

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

Användaren ser nu programmet i Azure RemoteApp-klienten och kan ansluta till det.

## <a name="how-to-remove-an-application-from-a-user"></a>Ta bort ett program från en användare
Kör följande cmdlet:

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## <a name="providing-feedback"></a>Skicka feedback
Vi uppskattar din feedback och dina förslag om den här funktionen för förhandsgranskning. Fyll i vår [undersökning](http://www.instant.ly/s/FDdrb) och berätta vad du tycker.

## <a name="havent-had-a-chance-to-try-the-preview-feature"></a>Har inte haft möjlighet att prova förhandsgranskningen?
Om du ännu inte har deltagit i förhandsgranskningen använder du denna [undersökning](http://www.instant.ly/s/AY83p) om du vill begära åtkomst.


