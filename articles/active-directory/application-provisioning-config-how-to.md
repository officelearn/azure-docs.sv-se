---
title: Hur du konfigurerar användaretablering för ett Azure AD-galleriprogram | Microsoft Docs
description: Hur du snabbt kan konfigurera omfattande användarkonto etablering och avetablering för program som redan visas i Azure AD-Programgalleriet
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ef9bb5540691f322620e2c510df52f62899048ff
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366838"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Hur du konfigurerar användaretablering för ett Azure AD-galleriprogram

*Etablering av användarkonto* syftar till att skapa, uppdatera och/eller inaktivera användarkonton i ett programs lokala användarprofilarkiv. De flesta moln- och SaaS-program som lagrar användarrollen och behörigheterna i sina egna lokala användarprofilarkiv och närvaro på en sådan användarpost i sin lokala arkivet är *krävs* för enkel inloggning och åtkomst till arbete.

I Azure-portalen i **etablering** fliken i det vänstra navigeringsfönstret för en Företagsapp visar vilka etablering lägen som stöds för appen. Detta kan vara ett av två värden:

## <a name="configuring-an-application-for-manual-provisioning"></a>Konfigurera ett program för manuell etablering

*Manuell* etablering innebär att användarkonton måste skapas manuellt med hjälp av de metoder som tillhandahålls av appen. Detta kan innebära att logga in på någon administrativ portal för appen och lägga till användare med hjälp av ett webbaserat användargränssnitt. Eller överför ett kalkylblad med användarens kontoinformation, med hjälp av en mekanism som tillhandahålls av programmet. I dokumentationen som tillhandahålls av appen eller kontakta apputvecklaren för att fastställa wat metoder är tillgängliga.

Om manuell är det enda läge som visas för ett visst program, innebär det att inga automatisk Azure AD etablering koppling har skapats för appen ännu. Eller det innebär att appen inte har stöd för nödvändiga användaren management-API som du vill skapa en automatisk etablering anslutning.

Om du vill begära stöd för automatisk etablering för en viss app, så kan du fylla en begäran med hjälp av den [Azure Active Directory-programförfrågningar](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Konfigurera ett program för automatisk etablering

*Automatisk* innebär att en Azure AD etablering connector har utvecklats för det här programmet. Mer information om Azure AD etableringstjänsten och hur det fungerar finns i [automatisera Användaretablering och avetablering för SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Mer information om hur du etablerar specifika användare och grupper till ett program finns i [hantering av användarkontoetablering för företagsappar](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

De faktiska steg som krävs för att aktivera och konfigurera automatisk etablering varierar beroende på program.

>[!NOTE]
>Du bör börja genom att söka efter installationen självstudien specifikt för att konfigurera etablering för ditt program och följande som de som steg för att konfigurera både appen och Azure AD för att skapa etablering anslutningen. 
>
>

App-självstudier finns på [lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

En viktig sak att tänka på när du konfigurerar etablering är att granska och konfigurera attributmappningar och arbetsflöden som definierar vilka användare (eller grupp) egenskaper flow från Azure AD till programmet. Detta inkluderar egenskapen ”matchande” som används för att identifiera och matcha användare/grupper mellan de två systemen. Mer information om den här viktiga processen.

## <a name="next-steps"></a>Nästa steg
[Anpassa attributmappningar för Användaretablering för SaaS-program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

