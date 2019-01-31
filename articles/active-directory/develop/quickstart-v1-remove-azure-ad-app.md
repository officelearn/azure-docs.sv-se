---
title: Ta bort ett program från Azure Active Directory
description: Lär dig hur du tar bort ett program från Azure Active Directory (AD Azure).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 805a917cbb4d867ee866ec99c8e90ea883527856
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100962"
---
# <a name="quickstart-remove-an-application-from-azure-active-directory"></a>Snabbstart: Ta bort ett program från Azure Active Directory

Företagsutvecklare och SaaS-leverantörer (Software as a Service) som har registrerat program med Azure Active Directory (Azure AD) kan behöva ta bort registreringen av ett program från sin Azure AD-klientorganisation.

I den här snabbstarten lär du dig att:

* [Ta bort ett program som skapats av din organisation](#removing-an-application-authored-by-your-organization)
* [Ta bort ett program med flera klientorganisationer som skapats av en annan organisation](#removing-a-multi-tenant-application-authorized-by-another-organization)

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du en Azure AD-klientorganisation som har program som är registrerade till den.

* Om du inte redan har en klientorganisation kan du [läsa om hur du skaffar en](quickstart-create-new-tenant.md).
* Information om hur du lägger till och registrera appar till din klientorganisation finns [Lägga till ett program till Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="removing-an-application-authored-by-your-organization"></a>Ta bort ett program som skapats av din organisation

Program som din organisation har registrerat visas under filtret **Mina appar** på din klientorganisations huvudsida för **appregistreringar**. Dessa program är de som du registrerade manuellt via Azure-portalen eller programmatiskt via PowerShell eller Microsoft Graph API. Mer specifikt representeras dessa program av både ett program- och ett tjänsthuvudnamnsobjekt i din klientorganisation. Mer information om de här objekten finns i [Programobjekt och tjänsthuvudnamnsobjekt](app-objects-and-service-principals.md).

### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Så tar du bort ett program med en enskild klientorganisation från din katalog

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Om ditt konto ger dig tillgång till fler än en väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten, väljer **Appregistreringar** och letar upp och väljer det program som du vill konfigurera.
    Detta tar dig till programmets huvudsakliga registreringssida, vilket öppnar sidan **Inställningar** för programmet.
1. På programmets huvudsakliga registreringssida väljer du **Ta bort**.
1. Välj **Ja** för att bekräfta att du vill ta bort programmet.

### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Ta bort ett program med flera klientorganisationer från dess hemkatalog

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Om ditt konto ger dig tillgång till fler än en väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten, väljer **Appregistreringar** och letar upp och väljer det program som du vill konfigurera.
    Detta tar dig till programmets huvudsakliga registreringssida, vilket öppnar sidan **Inställningar** för programmet.
1. Från sidan **Inställningar** väljer du **egenskaper** och ändrar växeln **Multi-tenanted** (Med flera klientorganisationer) till **Nej** för att först ändra programmet till en enskild klientorganisation och väljer sedan **Spara**.
    Programmets tjänsthuvudnamnsobjekt finns kvar i klientorganisationerna för alla organisationer som redan har samtyckt till det.
1. Välj **Ta bort** på programmets huvudsakliga registreringssida.
1. Välj **Ja** för att bekräfta att du vill ta bort programmet.

## <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Ta bort ett program med flera klientorganisationer som skapats av en annan organisation

En delmängd av de program som visas under filtret **Alla appar** (exklusive registreringarna för **Mina appar**) på din klientorganisations huvudsida för **appregistreringar** sidan är program för flera klientorganisationer.

Tekniskt sett kommer dessa program för flera klientorganisationer från en annan klientorganisation och registrerades i din klientorganisation under medgivandeprocessen. Mer specifikt representeras de endast av en tjänsthuvudnamnsobjekt i din klientorganisation utan motsvarande programobjekt. Mer information om skillnaderna mellan program- och tjänsthuvudnamnsobjekt finns i [Program och tjänsthuvudnamnsobjekt i Azure AD](app-objects-and-service-principals.md).

För att åtkomsten för ett program för flera klientorganisationer till din katalog ska tas bort (efter att ha godkänts) måste företagets administratör ta bort programmets tjänsthuvudnamn. Administratören måste ha behörighet som global administratör och kan ta bort den via Azure-portalen eller använda [Azure AD PowerShell-cmdletarna](https://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Nästa steg

Läs mer om dessa andra relaterade snabbstarter för apphantering för appar som använder Azure AD v1.0-slutpunkten:

- [Lägga till ett program till Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Uppdatera ett program i Azure AD](quickstart-v1-update-azure-ad-app.md)
