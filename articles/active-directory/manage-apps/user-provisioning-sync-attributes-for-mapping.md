---
title: Synkronisera attribut till Azure AD för mappning av | Microsoft Docs
description: Lär dig hur du synkroniserar attribut från din lokala Active Directory till Azure AD. När du konfigurerar användaretablering för SaaS-appar, kan du använda katalogfunktionen-tillägget för att lägga till källattribut som inte ska synkroniseras som standard.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9460bc924ea662646360d1a3f5087949a39a03f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806123"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synkronisera ett attribut från din lokala Active Directory till Azure AD för etablering i ett program

När du anpassar attributmappningar för användaretablering, kanske du upptäcker att attribut som du vill mappa inte visas i den **källattribut** lista. Den här artikeln visar hur du lägger till det saknade attributet som synkroniserats från din lokala Active Directory (AD) till Azure Active Directory (AD Azure).

Azure AD måste innehålla alla data som krävs för att skapa en profil när du etablerar användarkonton från Azure AD till en SaaS-app. I vissa fall för att göra data tillgängliga du kanske behöver synkronisera attribut från din lokala AD till Azure AD. Azure AD Connect synkroniserar automatiskt vissa attribut till Azure AD, men inte alla attribut. Dessutom kan vissa attribut (till exempel SAMAccountName) som synkroniseras som standard inte exponeras via Azure AD Graph API. I dessa fall kan använda du Azure AD Connect-directory tilläggsfunktionen för att synkronisera attributet till Azure AD. På så sätt kan attributet ska vara synliga för Azure AD Graph API och den Azure AD-etableringstjänsten.

Om de data du behöver för att etablera finns i Active Directory men inte är tillgängligt för etablering på grund av de anledningar som beskrivs ovan, Följ dessa steg.
 
## <a name="sync-an-attribute"></a>Synkronisera ett attribut 

1. Öppna Azure AD Connect-guiden, Välj uppgifter och välj sedan **anpassa synkroniseringsalternativ**.

   ![Azure Active Directory Connect sida i guiden ytterligare uppgifter](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Logga in som en Global Azure AD-administratör. 

3. På den **valfria funktioner** väljer **katalogtilläggsattribut**.
 
   ![Azure Active Directory Connect sida i guiden valfria funktioner](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Välj det attribut som du vill utöka till Azure AD.
   > [!NOTE]
   > Sök under **tillgängliga attribut** är skiftlägeskänsligt.

   ![Azure Active Directory Connect Directory tillägg val av guidesidan](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Azure AD Connect-guiden har slutförts och att en fullständig synkroniseringscykel att köra. När rutinen är klar, schemat utökas och de nya värdena synkroniseras mellan din lokala AD och Azure AD.
 
6. I Azure-portalen när du använder [Redigera användare attributmappningar](customize-application-attributes.md), **källattribut** listan innehåller nu attributet har lagts till i formatet `<attributename> (extension_<appID>_<attributename>)`. Välj attributet och mappa den till målprogrammet för etablering.

   ![Azure Active Directory Connect Directory tillägg val av guidesidan](media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Möjlighet att etablera referensattribut från en lokal AD, till exempel **managedby** eller **DN/DistinguishedName**, stöds inte i dag. Du kan begära den här funktionen på [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Nästa steg

* [Ange vem som är inom omfånget för etablering](define-conditional-rules-for-provisioning-user-accounts.md)
