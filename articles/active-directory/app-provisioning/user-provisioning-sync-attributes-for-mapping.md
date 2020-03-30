---
title: Synkronisera attribut till Azure AD för mappning | Microsoft-dokument
description: Lär dig hur du synkroniserar attribut från din lokala Active Directory till Azure AD. När du konfigurerar användaretablering till SaaS-appar använder du funktionen för katalogtillägg för att lägga till källattribut som inte synkroniseras som standard.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d1efaf54bee65bd3274987e68e643f887baade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522279"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synkronisera ett attribut från din lokala Active Directory till Azure AD för etablering till ett program

När du anpassar attributmappningar för användaretablering kan det hända att attributet du vill mappa inte visas i **attributlistan Källa.** I den här artikeln visas hur du lägger till attributet som saknas genom att synkronisera det från din lokala Active Directory (AD) till Azure Active Directory (Azure AD).

Azure AD måste innehålla alla data som krävs för att skapa en användarprofil när användarkonton etableras från Azure AD till en SaaS-app. I vissa fall kan du behöva synkronisera attribut från lokala AD till Azure AD för att göra data tillgängliga. Azure AD Connect synkroniserar automatiskt vissa attribut till Azure AD, men inte alla attribut. Dessutom kanske vissa attribut (till exempel SAMAccountName) som synkroniseras som standard inte visas med Hjälp av Microsoft Graph API. I dessa fall kan du använda katalogtilläggsfunktionen Azure AD Connect för att synkronisera attributet till Azure AD. På så sätt visas attributet för Microsoft Graph API och Azure AD-etableringstjänsten.

Om de data du behöver för etablering finns i Active Directory men inte är tillgängliga för etablering på grund av de orsaker som beskrivs ovan följer du dessa steg.
 
## <a name="sync-an-attribute"></a>Synkronisera ett attribut 

1. Öppna Azure AD Connect-guiden, välj Uppgifter och välj sedan **Anpassa synkroniseringsalternativ**.

   ![Sidan Ytterligare uppgifter i Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Logga in som global Azure AD-administratör. 

3. På sidan **Valfria funktioner** väljer du **Synkronisering av attribut för katalogtillägg**.
 
   ![Sidan Valfria funktioner i Azure Active Directory Connect-guiden](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Välj de attribut som du vill utöka till Azure AD.
   > [!NOTE]
   > Sökningen under Tillgängliga attribut är **skiftlägeskänslig.**

   ![Urvalssida för Azure Active Directory Connect-guiden Katalogtillägg](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Slutför Azure AD Connect-guiden och tillåt en fullständig synkroniseringscykel att köras. När cykeln är klar utökas schemat och de nya värdena synkroniseras mellan din lokala AD och Azure AD.
 
6. När du [redigerar mappningar av användarattribut](customize-application-attributes.md)i Azure-portalen innehåller **attributlistan** `<attributename> (extension_<appID>_<attributename>)`Källa nu det tillagda attributet i formatet . Markera attributet och mappa det till målprogrammet för etablering.

   ![Urvalssida för Azure Active Directory Connect-guiden Katalogtillägg](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Möjligheten att etablera referensattribut från lokala AD, till exempel **managedby** eller **DN/DistinguishedName,** stöds inte idag. Du kan begära den här funktionen på [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Nästa steg

* [Definiera vem som är i omfång för etablering](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
