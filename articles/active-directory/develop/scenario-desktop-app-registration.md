---
title: 'Skrivbordsappen att anrop webb-API: er (appregistrering) - Microsoft identity-plattformen'
description: 'Lär dig att skapa en skrivbordsapp att anrop webb-API: er (appregistrering)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5da934709274d90668d94dfea3a9c223e191d032
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076067"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Skrivbordsappen att anrop webb-API: er – appregistrering

Den här artikeln innehåller särdrag för app-registrering för ett skrivbordsprogram.

## <a name="supported-accounts-types"></a>Typer av konton som stöds

Kontotyper stöds i skrivbordsprogram är beroende av den funktion du söker ljus upp, och därför på flöden som du vill använda.

### <a name="audience-for-interactive-token-acquisition"></a>Målgruppen för interaktiva tokenförvärv

Om ditt program använder interaktiv autentisering kan du logga in användare från någon [kontotyp](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>Målgruppen för skrivbordsapp tyst flöden

- Om du planerar att använda integrerad Windows-autentisering eller användarnamn/lösenord behöver programmet för att logga in användare i din egen klientorganisation (LOB-utvecklare) eller i Azure Active directory organisationer (ISV-scenario). Dessa autentiseringsflöden stöds inte för personliga Microsoft-konton
- Om du vill använda kodflöde för enheten kan logga du inte in användare med sina personliga Microsoft-konton ännu
- Om du loggar in användare med sociala identiteter som passerar en B2C-auktoritet och principen kan använda du bara interaktiva och användarnamnet autentisering.

## <a name="redirect-uris"></a>Omdirigerings-URI:er

Igen beror omdirigerings-URI: er använder i skrivbordsprogram på det flöde som du vill använda.

- Om du använder interaktiv autentisering, ska du använda `https://login.microsoftonline.com/common/oauth2/nativeclient`. Du ska få den här konfigurationen genom att klicka på motsvarande URL-Adressen i den **autentisering** för ditt program
  
  > [!IMPORTANT]
  > Idag MSAL.NET använder en annan omdirigerings-URI som standard i program som körs på Windows (`urn:ietf:wg:oauth:2.0:oob`). I framtiden vi vill ändra denna standardinställning och därför rekommenderar vi att du använder `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Om din app bara använder integrerad Windows-autentisering, användarnamn/lösenord eller enhet kod Flow, behöver du inte registrera en omdirigerings-URI för ditt program. Faktiskt flödena göra en tur och RETUR till Microsoft identity-plattformen v2.0-slutpunkten och ditt program ringas inte upp på någon specifik URI. För att skilja dem från en konfidentiell klient programflödet som inte har omdirigerings-URI: er antingen (klientens autentiseringsuppgifter flödet som används i daemon-program), du behöver att uttrycka att ditt program är ett offentligt klientprogram. Den här konfigurationen uppnås genom att gå till den **autentisering** för ditt program och i den **avancerade inställningar** underavsnitt, Välj **Ja**, på frågan **Hantera program som en offentlig klient** (i den **standard klienttyp** punkt)

  ![Tillåt offentlig klient](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-behörigheter

Skrivbordsprogram anropa API: er åt den inloggade användaren. De måste begära delegerade behörigheter. De kan inte begära programbehörigheter (hanteras endast i [daemon program](scenario-daemon-overview.md))

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skrivbordsappen - appkonfiguration](scenario-desktop-app-configuration.md)
