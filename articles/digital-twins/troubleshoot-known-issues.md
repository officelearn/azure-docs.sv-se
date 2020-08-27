---
title: Kända problem – Azure Digitals, dubbla
description: Få hjälp med att identifiera och åtgärda kända problem med Azure Digitals.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 01d962db45a58781ca5f2ba494de16ad420b0807
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921077"
---
# <a name="known-issues-in-azure-digital-twins"></a>Kända problem i Azure Digitals, dubbla

Den här artikeln innehåller information om kända problem som är associerade med Azure Digitals.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400-klient fel: felaktig begäran" i Cloud Shell

Kommandon i Cloud Shell kan ibland Miss lyckas med fel meddelandet "400-klient fel: felaktig begäran för URL: http://localhost:50342/oauth2/token " följt av fullständig stack spårning.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Detta kan lösas genom att köra kommandot igen `az login` och slutföra efterföljande inloggnings steg.

Sedan bör du kunna köra kommandot igen.

### <a name="possible-causes"></a>Möjliga orsaker

Detta är resultatet av ett känt problem i Cloud Shell: [*hämtning av token från Cloud Shell Miss lyckas tillfälligt med 400-klient fel: felaktig begäran*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="missing-role-assignment-after-scripted-setup"></a>Roll tilldelning saknas efter skriptad installation

Vissa användare kan uppleva problem med roll tilldelnings delen i [*anvisningar: Konfigurera en instans och autentisering (skript)*](how-to-set-up-instance-scripted.md). Skriptet indikerar inte fel, men rollen *Azure Digitals-ägare (förhands granskning)* har inte tilldelats till användaren, och detta påverkar möjligheten att skapa andra resurser på vägen.

Följ anvisningarna i avsnittet [*Verifiera användar roll tilldelning*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) i installations artikeln för att avgöra om din roll tilldelning har kon figurer ATS när skriptet har körts. Om användaren inte visas med den här rollen påverkar det här problemet.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

För att lösa problemet kan du konfigurera roll tilldelningen manuellt med hjälp av CLI-eller Azure Portal. 

Följ dessa anvisningar:
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Möjliga orsaker

För användare som är inloggade med en personlig [Microsoft-konto (MSA)](https://account.microsoft.com/account)kan användarens huvud-ID som identifierar dig i kommandon som det kan skilja sig från användarens inloggnings-e-post, vilket gör det svårt för skriptet att identifiera och använda för att tilldela rollen korrekt.

## <a name="issue-with-interactive-browser-authentication"></a>Problem med interaktiv webb läsar autentisering

När du skriver authentication code i dina Azure Digital-program med version **1.2.0** av ** [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) -biblioteket**kan det uppstå problem med [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) -metoden.

Detta är inte den senaste versionen av biblioteket. Den senaste versionen är **1.2.2**.

Den berörda metoden används i följande artiklar: 
* [*Självstudie: koda en klient app*](tutorial-code.md)
* [*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)
* [*Anvisningar: använda Azures digitala dubbla API: er och SDK: er*](how-to-use-apis-sdks.md)

Problemet innehåller ett felsvar för "Azure. Identity. AuthenticationFailedException" när du försöker autentisera i ett webbläsarfönster. Det kan hända att det inte går att starta webbläsarfönstret helt eller visas för att autentisera användaren, medan klient programmet fortfarande Miss lyckas med felet.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Du löser problemet genom att uppdatera dina program så att de använder Azure. Identity version **1.2.2**. I den här versionen av biblioteket bör webbläsaren läsa in och autentiseras som förväntat.

### <a name="possible-causes"></a>Möjliga orsaker

Detta är relaterat till ett öppet problem med den senaste versionen av Azure. Identity Library (version **1.2.0**): [*kan inte autentiseras när du använder InteractiveBrowserCredential*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

Du ser det här problemet om du använder version **1.2.0** i ditt Azure Digitals dubbla program, eller om du lägger till biblioteket i projektet utan att ange en version (som även som standard till den senaste versionen).

## <a name="next-steps"></a>Nästa steg

Läs mer om säkerhet och behörigheter på digitala Azure-objekt:
* [*Koncept: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md)