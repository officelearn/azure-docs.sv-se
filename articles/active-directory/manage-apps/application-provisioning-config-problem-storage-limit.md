---
title: Problem med att spara admin-autentiseringsuppgifter konfigurera Azure AD Gallery-appen | Microsoft Docs
description: Så här felsöker du vanliga problem med att konfigurera användar etablering till ett program som redan finns med i Azure AD-programgalleriet
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 152b704ca597fb473a820124ee6147d6d9bc7845
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381475"
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

1. **Använd två Gallery-programinstanser, ett för enkel inloggning och ett för användar etablering** – med galleriet till galleriet [](../saas-apps/linkedinelevate-tutorial.md) , kan du lägga till LinkedIn-höjning från galleriet och konfigurera det för enkel inloggning. För etablering, Lägg till en annan instans av LinkedIn från Azure AD App-galleriet och ge den namnet "LinkedIn-höjning (etablering)". För den andra instansen konfigurerar du [etablering](../saas-apps/linkedinelevate-provisioning-tutorial.md), men inte enkel inloggning. När du använder den här lösningen måste samma användare och grupper [tilldelas](assign-user-or-group-access-portal.md) till båda programmen. 

2. **Minska mängden konfigurations data som lagras** – alla data som anges i avsnittet [admin-autentiseringsuppgifter](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) på fliken etablering lagras på samma plats som SAML-certifikatet. Det är möjligt att det inte går att minska längden på alla dessa data, några valfria konfigurations fält som **e-** postaviseringen kan tas bort.

## <a name="next-steps"></a>Nästa steg
[Konfigurera användar etablering och avetablering för SaaS-program](user-provisioning.md)
