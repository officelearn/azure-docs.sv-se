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
ms.openlocfilehash: 25989d07b7d879ac68283ee56a7ccb0c07e09623
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "35356062"
---
# <a name="manage-your-luis-account"></a>Hantera LUIS-konto
De två viktig information för ett konto med LUIS är användarkontot och nyckeln för redigering. Inloggningsinformationen hanteras på [account.microsoft.com](https://account.microsoft.com). Din nyckel för redigering hanteras från den [LUIS][LUIS] webbplats **inställningar** sidan. 

## <a name="authoring-key"></a>Redigera nyckel

Den här enda, regionspecifika redigering nyckel på den **inställningar** sidan kan du skapa dina appar från den [LUIS][LUIS] webbplats samt de [ redigering av API: er](https://aka.ms/luis-authoring-api). Som en Service nyckeln redigering tillåts att göra en [begränsad](luis-boundaries.md) antalet endpoint frågar varje månad. 

![LUIS inställningssidan](./media/luis-how-to-account-settings/account-settings.png)

Redigering nyckeln används för alla appar som du äger, samt alla appar som du har listats som en samarbetspartner.

## <a name="authoring-key-regions"></a>Redigera nyckel regioner
Nyckeln för redigering är specifik för den [redigering region](luis-reference-regions.md#publishing-regions). Nyckeln fungerar inte i en annan region. 

## <a name="reset-authoring-key"></a>Återställa authoring-nyckeln
Om din redigering nyckeln äventyras, kan du återställa nyckeln. Nyckeln har återställts på alla dina appar på den [LUIS] webbplats. Om du skapar dina appar via redigering API: er, måste du ändra värdet för `Ocp-Apim-Subscription-Key` till den nya nyckeln. 

## <a name="delete-account"></a>Ta bort konto
Se [datalagring och borttagning av](luis-concept-data-storage.md#accounts) information om vilka data tas bort när du tar bort ditt konto. 

## <a name="azure-active-directory-tenant-user"></a>Användare med Azure Active Directory-klient
LUIS använder standard medgivande flödet för Azure Active Directory (AD Azure). 

Innehavaradministration bör arbeta direkt med användare som behöver åtkomst till använda LUIS i Azure AD. 

Först användaren loggar in på LUIS och ser popup-fönstret behöva godkännande av administratören. Användaren kontaktar innehavaradministration innan du fortsätter. 

Därefter innehavaradministration loggar in på LUIS och ser en dialogruta för medgivande-flödet. Detta är dialogrutan administratören behöver ge behörighet för användaren. När administratören godkänner behörigheten, kan användaren fortsätta med LUIS.

Om innehavaradministration inte kommer att logga in på LUIS måste administratören kan komma åt [medgivande](https://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=65920ba3-ab61-4a9b-9b10-505e5ce61b58) för LUIS. 

Om klienten administratören vill bara vissa användare att använda LUIS, [identitetsblogg](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om din [redigering nyckeln](luis-concept-keys.md#authoring-key). 

[LUIS]: luis-reference-regions.md
