---
title: Problem med att spara administratörsautentiseringsuppgifter konfigurera Azure AD Gallery-appen
description: Lär dig hur du felsöker problem med ansikte när du sparar administratörsautentiseringsuppgifter när du konfigurerar användar etablering till ett Azure Active Directory Galleri program.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/21/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 7a3340e72499087dce7773264272601dfce8a50f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91266790"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problem med att spara administratörsautentiseringsuppgifter när du konfigurerar användar etablering till ett Azure Active Directory Galleri program 

När du använder Azure Portal för att konfigurera [Automatisk användar etablering](user-provisioning.md) för ett företags program kan det uppstå en situation där:

* **Admin-autentiseringsuppgifterna** som har angetts för programmet är giltiga och knappen **Testa anslutning** fungerar. Men det går inte att spara autentiseringsuppgifterna och Azure Portal returnerar ett allmänt fel meddelande.

Om SAML-baserad enkel inloggning också har kon figurer ATS för samma program är den mest sannolika orsaken till felet att Azure AD: s interna lagrings gräns per program för certifikat och autentiseringsuppgifter har överskridits.

Azure AD har för närvarande en maximal lagrings kapacitet på 1024 byte för alla certifikat, hemliga token, autentiseringsuppgifter och relaterade konfigurations data som är kopplade till en enda instans av ett program (kallas även för en tjänst huvud post i Azure AD).

När SAML-baserad enkel inloggning har kon figurer ATS lagras certifikatet som används för att signera SAML-token här, och använder ofta över 50% procent av utrymmet.

Eventuella hemliga token, URI: er, e-postadresser för meddelanden, användar namn och lösen ord som anges under installationen av användar etablering kan orsaka att lagrings gränsen överskrids.

## <a name="how-to-work-around-this-issue"></a>Så här löser du det här problemet 

Det finns två sätt att komma runt det här problemet idag:

1. **Använd två Gallery-programinstanser, ett för enkel inloggning och ett för användar etablering** – med galleriet till galleriet, kan du lägga till LinkedIn [-höjning från](../saas-apps/linkedinelevate-tutorial.md) galleriet och konfigurera det för enkel inloggning. För etablering, Lägg till en annan instans av LinkedIn från Azure AD App-galleriet och ge den namnet "LinkedIn-höjning (etablering)". För den andra instansen konfigurerar du [etablering](../saas-apps/linkedinelevate-provisioning-tutorial.md), men inte enkel inloggning. När du använder den här lösningen måste samma användare och grupper [tilldelas](../manage-apps/assign-user-or-group-access-portal.md) till båda programmen. 

2. **Minska mängden konfigurations data som lagras** – alla data som anges i avsnittet [admin-autentiseringsuppgifter](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) på fliken etablering lagras på samma plats som SAML-certifikatet. Det är möjligt att det inte går att minska längden på alla dessa data, några valfria konfigurations fält som **e-postaviseringen** kan tas bort.

## <a name="next-steps"></a>Nästa steg
[Konfigurera användar etablering och avetablering för SaaS-program](user-provisioning.md)
