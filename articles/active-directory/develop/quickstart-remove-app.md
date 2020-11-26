---
title: 'Snabb start: ta bort en registrerad app från Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur du tar bort ett program som är registrerat hos Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: aa0662ae0b1caa63938f5e07aea10b9af60661d3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173268"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Snabb start: ta bort ett program som är registrerat med Microsoft Identity Platform

Företagsutvecklare och SaaS-leverantörer (Software as a Service) som har registrerat program med Microsoft Identity Platform kan behöva ta bort en programregistrering.

I den här snabbstarten lär du dig att:

* Ta bort ett program som skapats av dig eller din organisation
* Ta bort ett program som skapats av en annan organisation

## <a name="prerequisites"></a>Förutsättningar

* Slut för ande av [snabb start: registrera ett program med Microsoft Identity Platform](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Ta bort ett program som skapats av dig eller din organisation

Program som du eller din organisation har registrerat representeras av både ett programobjekt och ett tjänsthuvudnamnsobjekt i din klientorganisation. Mer information finns i [Programobjekt och tjänsthuvudnamnsobjekt](./app-objects-and-service-principals.md).

Om du vill ta bort ett program måste vara angiven som ägare av programmet eller ha administratörsbehörighet.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** och väljer sedan **Appregistreringar**. Leta upp och välj det program som du vill konfigurera. När du har valt appen ser du programmets **översiktssida**.
1. På sidan **Översikt** väljer du **Ta bort**.
1. Välj **Ja** för att bekräfta att du vill ta bort appen.

## <a name="remove-an-application-authored-by-another-organization"></a>Ta bort ett program som skapats av en annan organisation

Om du visar **Appregistreringar** i kontexten för en klientorganisation kommer en delmängd av de program som visas under fliken **Alla appar** från en annan klientorganisation och registrerades i din klientorganisation under medgivandeprocessen. Mer specifikt representeras de endast av en tjänsthuvudnamnsobjekt i din klientorganisation utan motsvarande programobjekt. Mer information om skillnaderna mellan program- och tjänsthuvudnamnsobjekt finns i [Programobjekt och tjänsthuvudnamnsobjekt i Azure AD](./app-objects-and-service-principals.md).

För att kunna ta bort åtkomsten för ett program till din katalog (efter att medgivande har givits) måste företagets administratör ta bort dess tjänsthuvudnamn. Administratören måste ha behörighet som global administratör och kan ta bort programmet via Azure-portalen eller använda [Azure AD PowerShell-cmdletarna](/previous-versions/azure/jj151815(v=azure.100)) för att ta bort åtkomst.

## <a name="next-steps"></a>Nästa steg

Läs mer om program-och tjänst huvud objekt i Microsoft Identity Platform:

> [!div class="nextstepaction"]
> [Objekt för program och tjänstens huvudnamn i Azure Active Directory](app-objects-and-service-principals.md)