---
title: Hantera inställningarna för ditt konto i LUIS | Microsoft Docs
description: Använda LUIS webbplats för att hantera inställningarna för ditt konto.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 1b5caf199cd5bfe0fa462950cc1f7beb3f2872b2
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887614"
---
# <a name="manage-your-luis-account"></a>Hantera LUIS-konto
De två viktig information för ett konto med LUIS är användarkontot och nyckeln för redigering. Din inloggningsinformation hanteras på [account.microsoft.com](https://account.microsoft.com). Redigering nyckeln hanteras från den [LUIS](luis-reference-regions.md) webbplats **inställningar** sidan. 

## <a name="authoring-key"></a>Redigera nyckel

Den här enda, regionspecifika redigering nyckeln på den **inställningar** sidan kan du skapa alla dina appar från den [LUIS](luis-reference-regions.md) webbplats samt de [redigera API: er](https://aka.ms/luis-authoring-api). Förenkla processen redigering nyckeln kan göra en [begränsad](luis-boundaries.md) antalet endpoint frågar varje månad. 

![LUIS inställningssidan](./media/luis-how-to-account-settings/account-settings.png)

Redigering nyckeln används för alla appar som du äger, samt alla appar som du står som medarbetare.

## <a name="authoring-key-regions"></a>Redigera viktiga regioner
Redigering nyckeln är specifika för den [redigering region](luis-reference-regions.md#publishing-regions). Nyckeln fungerar inte i en annan region. 

## <a name="reset-authoring-key"></a>Återställ redigering nyckel
Om redigering nyckeln äventyras, kan du återställa nyckeln. Nyckeln har återställts på alla dina appar på den [LUIS](luis-reference-regions.md) webbplats. Om du skapar dina appar authoring-API: er, måste du ändra värdet för `Ocp-Apim-Subscription-Key` till den nya nyckeln. 

## <a name="delete-account"></a>Ta bort konto
Se [datalagring och borttagning av](luis-concept-data-storage.md#accounts) information om vilka data tas bort när du tar bort ditt konto. 

## <a name="azure-active-directory-tenant-user"></a>Användare med Azure Active Directory-klient
LUIS använder standard medgivande flödet för Azure Active Directory (AD Azure). 

Innehavaradministration bör arbeta direkt med användare som behöver åtkomst till använda LUIS i Azure AD. 

Först användaren loggar in på LUIS och ser popup-fönstret behöva godkännande av administratören. Användaren kontaktar administratör innan du fortsätter. 

Därefter innehavaradministration loggar in på LUIS och ser en dialogruta för medgivande-flödet. Det här är i dialogrutan som administratören behöver ge tillstånd för användaren. När administratören godkänner behörigheten, kan användaren fortsätta med LUIS.

Om innehavaradministration inte kommer att logga in på LUIS måste administratören kan komma åt [medgivande](https://account.activedirectory.windowsazure.com/r#/applications) för LUIS. 

![Azure active directory-behörighet genom appens webbplats](./media/luis-how-to-account-settings/tenant-permissions.png)

Om klienten administratören vill bara vissa användare att använda LUIS, [identitetsblogg](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Användarkonton med flera e-postmeddelanden för medarbetare
Om du lägger till medarbetare till en LUIS-app, anger du den exakta e-postadress som en medarbetare som behöver använda LUIS som medarbetare. Med Azure Active Directory (Azure AD) kan en enskild användare till flera e-postkonto som är utbytbara, kräver att användaren loggar in med den e-postadress som angetts i listan över den medarbetarens LUIS. 


## <a name="next-steps"></a>Nästa steg

Läs mer om din [redigering nyckeln](luis-concept-keys.md#authoring-key). 

