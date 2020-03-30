---
title: Problem med att spara administratörsuppgifter konfigurera Azure AD-galleriapp
description: Felsöka vanliga problem när du konfigurerar användaretablering till ett program som redan finns i Azure AD Application Gallery
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ff80224037001e41daf49cd6fc21439b2cc5cff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522874"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problem med att spara administratörsuppgifter när du konfigurerar användaretablering till ett Azure Active Directory Gallery-program 

När du använder Azure-portalen för att konfigurera [automatisk användaretablering](user-provisioning.md) för ett företagsprogram kan du stöta på en situation där:

* De **administratörsautentiseringsuppgifter** som angetts för programmet är giltiga och knappen **Testa anslutning** fungerar. Autentiseringsuppgifterna kan dock inte sparas och Azure-portalen returnerar ett allmänt felmeddelande.

Om SAML-baserad enkel inloggning också är konfigurerad för samma program, är den mest troliga orsaken till felet att Azure AD:s interna lagringsgräns per program för certifikat och autentiseringsuppgifter har överskridits.

Azure AD har för närvarande en maximal lagringskapacitet på 1024 byte för alla certifikat, hemliga token, autentiseringsuppgifter och relaterade konfigurationsdata som är associerade med en enda instans av ett program (kallas även en tjänsthuvudpost i Azure AD).

När SAML-baserad enkel inloggning är konfigurerad lagras certifikatet som används för att signera SAML-token här och förbrukar ofta över 50 % procent av utrymmet.

Alla hemliga token, URI:er, e-postadresser för meddelanden, användarnamn och lösenord som anges under installationen av användaretablering kan leda till att lagringsgränsen överskrids.

## <a name="how-to-work-around-this-issue"></a>Så här kan du komma runt det här problemet 

Det finns två möjliga sätt att komma runt det här problemet idag:

1. **Använd två galleriprograminstanser, en för enkel inloggning och en för användaretablering** - Ta galleriprogrammet [LinkedIn Elevate](../saas-apps/linkedinelevate-tutorial.md) som exempel kan du lägga till LinkedIn Elevate från galleriet och konfigurera det för enkel inloggning. För etablering lägger du till en annan instans av LinkedIn Elevate från Azure AD-appgalleriet och namnger den "LinkedIn Elevate (Etablering)." I den här andra instansen konfigurerar du [etablering](../saas-apps/linkedinelevate-provisioning-tutorial.md), men inte enkel inloggning. När du använder den här lösningen måste samma användare och grupper [tilldelas](../manage-apps/assign-user-or-group-access-portal.md) båda programmen. 

2. **Minska mängden konfigurationsdata som lagras** – Alla data som anges i avsnittet [Administratörsautentiseringsuppgifter](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) på etableringsfliken lagras på samma plats som SAML-certifikatet. Även om det kanske inte är möjligt att minska längden på alla dessa data, kan vissa valfria konfigurationsfält som **e-postmeddelandet** tas bort.

## <a name="next-steps"></a>Nästa steg
[Konfigurera etablering och avetablering av användare till SaaS-program](user-provisioning.md)
