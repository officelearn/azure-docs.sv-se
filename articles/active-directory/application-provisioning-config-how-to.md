---
title: Konfigurera användaretablering till ett program för Azure AD-galleriet | Microsoft Docs
description: Hur du snabbt kan konfigurera omfattande användarkonto etablering och borttagning till program som redan finns i Azure AD Application Gallery
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: da7050edae282c90c4f92c588ce8fe501ccba50d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
ms.locfileid: "29464433"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Konfigurera användaretablering till ett program för Azure AD-galleriet

*Etablering av konto* innebär att skapa, uppdatera och/eller inaktivera konto användarposter i ett program lokal profil användararkivet. De flesta moln och SaaS-program lagrar användarrollen och behörigheterna i sina egna lokala användararkivet profil och förekomsten av sådana en användarpost i sina lokala arkivet är *krävs* för enkel inloggning och åtkomst till arbetsobjekt.

I Azure-portalen på **etablering** fliken i det vänstra navigeringsfönstret för en Enterprise-App visar vilka etablering lägen stöds för appen. Detta kan vara ett av två värden:

## <a name="configuring-an-application-for-manual-provisioning"></a>Konfigurera ett program för manuell etablering

*Manuell* etablering innebär att användarkonton måste skapas manuellt med hjälp av de metoder som tillhandahålls av appen. Detta kan betyda att logga in på en administrativ portal för appen och lägga till användare som använder ett webbaserat användargränssnitt. Eller överför ett kalkylblad med kontoinformation för användare med hjälp av en mekanism som tillhandahålls av programmet. I dokumentationen som tillhandahålls av appen eller kontakta apputvecklaren för att fastställa wat metoder är tillgängliga.

Om manuell är det enda läge som visas för ett visst program, innebär det att ingen automatisk Azure AD connector-etablering har skapats för appen ännu. Eller det innebär att appen inte har stöd för förutvärdering användaren management API som du vill skapa en automatisk etablering koppling.

Om du vill begära stöd för automatisk etablering för en viss app kan du fylla i en begäran med hjälp av den [Azure Active Directory-programförfrågningar](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Konfigurera ett program för automatisk etablering

*Automatisk* innebär att en Azure AD connector-etablering har utvecklats för det här programmet. Läs mer om Azure AD etablering tjänsten och hur det fungerar [automatisera Användaretablering och avetablering för SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Mer information om hur du etablerar specifika användare och grupper till ett program finns i [hantera användaretablering konto för företagsappar](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

De faktiska steg som krävs för att aktivera och konfigurera automatisk etablering varierar beroende på programmet.

>[!NOTE]
>Du bör börja med att söka efter installationen kursen specifika för att skapa etablering för ditt program och följa dem om du vill konfigurera både appen och Azure AD för att skapa etablering anslutningen. 
>
>

Appen självstudier finns på [lista över självstudier om hur man integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

En viktig sak att tänka på när du ställer in etablering att granska och konfigurera attributmappning och arbetsflöden som definierar vilka användare (eller grupp) egenskaper flödar från Azure AD till programmet. Detta inkluderar egenskapen ”matchande” som används för att identifiera och matchar användare eller grupper mellan de två systemen. Mer information om viktiga processen.

## <a name="next-steps"></a>Nästa steg
[Anpassa attributmappning för Användaretablering för SaaS-program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

