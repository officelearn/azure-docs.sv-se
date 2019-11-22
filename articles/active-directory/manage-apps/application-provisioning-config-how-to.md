---
title: Konfigurera användar etablering i en Azure AD-Galleri-app
description: Hur du snabbt kan konfigurera omfattande användar konto etablering och avetablering för program som redan finns med i Azure AD-programgalleriet
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a2955a418aa0ce43c030afc588a2840bbb9b50f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275815"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Konfigurera användar etablering för ett Azure AD Gallery-program

*Etablering av användar konto* är en handling som används för att skapa, uppdatera och/eller inaktivera användar konto poster i ett programs lokala användar profil arkiv. De flesta moln-och SaaS-program lagrar användarens roll och behörigheter i användarens egna lokala användar profil Arkiv och förekomst av en sådan användar post i användarens lokala arkiv *krävs* för enkel inloggning och åtkomst till arbete.

I Azure Portal visar fliken **etablering** i det vänstra navigerings fönstret för en företags app vilka etablerings lägen som stöds för den appen. Detta kan vara ett av två värden:

## <a name="configuring-an-application-for-manual-provisioning"></a>Konfigurera ett program för manuell etablering

*Manuell* etablering innebär att användar konton måste skapas manuellt med hjälp av de metoder som tillhandahålls av appen. Detta kan betyda att du loggar in på en administrations Portal för appen och lägger till användare med hjälp av ett webbaserat användar gränssnitt. Du kan också ladda upp ett kalkyl blad med information om användar kontot med hjälp av en mekanism som tillhandahålls av programmet. Läs dokumentationen från appen eller kontakta appens utvecklare för att ta reda på vilka mekanismer som är tillgängliga.

Om *manuell* är det enda läget som visas för ett angivet program innebär det att det inte finns någon automatisk Azure AD-etablerings anslutning för appen än. Det innebär också att appen inte stöder de krav som gäller för Microsofts API för användar hantering, som används för att bygga en automatiserad etablerings anslutning.

Om du vill begära stöd för automatisk etablering för en specifik app kan du fylla i en begäran med hjälp av [Azure Active Directory program begär Anden](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Konfigurera ett program för automatisk etablering

*Automatisk* innebär att en Azure AD-etablerings anslutning har utvecklats för det här programmet. Mer information om Azure AD Provisioning-tjänsten och hur den fungerar finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Mer information om hur du etablerar specifika användare och grupper i ett program finns i [hantera användar konto etablering för företags program](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

De faktiska stegen som krävs för att aktivera och konfigurera automatisk etablering varierar beroende på programmet.

> [!NOTE]
> Du bör börja med att hitta installations självstudien som är specifik för att konfigurera etablering för programmet och följa dessa steg för att konfigurera både appen och Azure AD för att skapa etablerings anslutningen. 

Själv studie kurser för appar finns i [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Ett viktigt att tänka på när du konfigurerar etablering är att granska och konfigurera de mappningar och arbets flöden som definierar vilka användar egenskaper (eller grupp) som flödar från Azure AD till programmet. Detta innefattar att ange "matchande egenskap" som används för att unikt identifiera och matcha användare/grupper mellan de två systemen. Se länken i *Nästa steg* för mer information om mappning av attribut.

## <a name="next-steps"></a>Nästa steg
[Anpassa mappningar av användar Provisioning-attribut för SaaS-program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

