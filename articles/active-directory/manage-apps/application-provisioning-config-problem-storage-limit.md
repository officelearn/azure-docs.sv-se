---
title: Problem med att spara autentiseringsuppgifter för administratörer när du konfigurerar användaretablering för ett Azure AD-galleriprogram | Microsoft Docs
description: Så här felsöker du vanliga problem som kan stöta på när Konfigurera användaretablering för ett program redan visas i Azure AD-Programgalleriet
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74519bf2eeb31a700c2fb02cbf3b94702449427
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443154"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problem med att spara autentiseringsuppgifter när du konfigurerar användaretablering för ett program för Azure Active Directory-galleri 

När du använder Azure-portalen för att konfigurera [automatisk användaretablering](user-provisioning.md) för ett företagsprogram som kan uppstå en situation där:

* Den **administratörsautentiseringsuppgifter** har angetts för programmet är giltiga, och **Testanslutningen** knappen fungerar. Men det går inte att spara autentiseringsuppgifterna och Azure-portalen returnerar ett allmänt felmeddelande.

Om SAML-baserad enkel inloggning har konfigurerats för samma program, den mest troliga orsaken till felet är att Azure AD internt, programspecifika lagringsgränsen för certifikat och autentiseringsuppgifter har överskridits.

Azure AD för närvarande har en maximal lagringskapacitet på 1 024 byte för alla certifikat, hemlig token, autentiseringsuppgifter och tillhörande konfigurationsdata som är associerade med en enda instans av ett program (kallas även en service principal-post i Azure AD).

När SAML-baserad enkel inloggning konfigureras certifikatet som används för att signera SAML-token lagras här och ofta förbrukar över 50 procent av utrymmet.

Alla hemlig token, URI: er, meddelande e-postadresser, användarnamn och lösenord som får anges under installationen av etableringen av användare kan orsaka lagringsgränsen överskrids.

## <a name="how-to-work-around-this-issue"></a>Hur du löser problemet 

Det finns två möjliga sätt att undvika det här problemet i dag:

1. **Använda galleriet för två instanser av programmet, en för enkel inloggning och en för användaretablering** -tar galleriprogram [LinkedIn höjer](../saas-apps/linkedinelevate-tutorial.md) exempelvis kan du lägga till LinkedIn höjer från galleriet och konfigurera den för enkel inloggning. Lägg till en annan instans av LinkedIn höjer från Azure AD-appgalleri för etablering, och ge den namnet ”LinkedIn utöka (etablering)”. Konfigurera för den här andra instansen [etablering](../saas-apps/linkedinelevate-provisioning-tutorial.md), men inte enkel inloggning. När du använder den här lösningen kan samma användare och grupper måste vara [tilldelade](assign-user-or-group-access-portal.md) till båda programmen. 

2. **Minska mängden lagras** -alla data som angetts i den [administratörsautentiseringsuppgifter](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) på fliken etablering lagras på samma plats som SAML-certifikat. Även om det inte möjligt att minska längden på alla dessa data, vissa fält i valfri konfiguration som den **e-postmeddelande** kan tas bort.

## <a name="next-steps"></a>Nästa steg
[Konfigurera användare etablering och avetablering för SaaS-program](user-provisioning.md)
