---
title: Felsöka ett attribut som inte synkroniseras i Azure AD Connect | Microsoft Docs
description: Det här avsnittet innehåller anvisningar att felsöka problem med attributet synkronisering med hjälp av felsökningsaktiviteten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456000"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Felsöka ett attribut som inte synkroniseras i Azure AD Connect

## <a name="recommended-steps"></a>**Rekommenderade åtgärder**

Innan man har undersökt attribut som synkroniseras problem, låt oss se på **Azure AD Connect** synkroniserar processen:

  ![Azure AD Connect-synkronisering](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologi**

* **CS:** En tabell i databasen i anslutningsplatsen.
* **MV:** Metaversum, en tabell i databasen.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Synkroniseringssteg**

* Importera från AD: Active Directory-objekt förs in AD CS.

* Importera från AAD: Azure Active Directory-objekt överförs till AAD CS.

* Synkronisering: **Inkommande Synkroniseringsregler** och **utgående Synkroniseringsregler** körs i den ordning som siffran från lägre till högre. Om du vill visa de Synkroniseringsregler som du kan gå till **Synchronization Rules Editor** från program. Den **inkommande Synkroniseringsregler** ger i data från CS MV. Den **utgående Synkroniseringsregler** flyttar data från MV till CS.

* Exportera till AD: När synkronisering har körts kan exporteras objekt från AD CS att **Active Directory**.

* Exportera till AAD: När du har kört synkronisering objekt exporteras från AAD CS till **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Steg för steg undersökning**

* Vi ska starta våra sökning från den **metaversum** och titta på attributmappning från källan till målet.

* Starta **hanteraren för synkroniseringstjänsten** från skrivbordsprogram, som visas nedan:

  ![Starta hanteraren för synkroniseringstjänsten](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* På den **hanteraren för synkroniseringstjänsten**väljer den **Metaversumsökning**väljer **omfång efter objekttyp**, markerar du objektet med hjälp av ett attribut och klicka på **Search** knappen.

  ![Metaversumsökning](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Dubbelklicka på det objekt som hittats i den **metaversum** -sökning för att visa dess attribut. Du kan klicka på den **kopplingar** flik för att titta på motsvarande objekt i alla de **-kopplingens utrymmen**.

  ![Metaversumobjekt kopplingar](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Dubbelklicka på den **Active Directory-koppling** att visa den **Anslutarplatsen** attribut. Klicka på den **förhandsversion** knappen i den följande dialogrutan klickar du på den **Generera förhandsgranskning** knappen.

  ![Connector utrymme attribut](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Nu klickar du på den **Import attributflöde**, detta visar flödet av attribut från **Active Directory-Anslutarplatsen** till den **metaversum**. **Synkronisera regeln** kolumnen visar vilka **Synkroniseringsregeln** bidragit till attributet. **Datakällan** kolumnen visar attribut från den **Anslutarplatsen**. **Attribut för metaversum** kolumnen visar attributen i den **metaversum**. Du kan söka efter attribut som synkroniseras inte här. Om du inte hittar attributet här, detta är inte mappad och du behöver skapa nya anpassade **Synkroniseringsregeln** att mappa attributet.

  ![Connector utrymme attribut](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Klicka på den **exportera attributflöde** i den vänstra rutan att visa attributflöde från **metaversum** tillbaka till **Active Directory-Anslutarplatsen** med  **Utgående Synkroniseringsregler**.

  ![Connector utrymme attribut](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* På samma sätt kan du visa den **Azure Active Directory-Anslutarplatsen** objekt och kan generera den **förhandsversion** att visa attributflöde från **metaversum** till **Anslutningsplatsen** och vice versa sätt du kan undersöka varför ett attribut synkroniseras inte.

## <a name="recommended-documents"></a>**Rekommenderade dokument**
* [Azure AD Connect-synkronisering: Tekniska begrepp](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Azure AD Connect-synkronisering: Förstå arkitekturen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect-synkronisering: Förstå deklarativ etablering](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect-synkronisering: Förstå uttryck för deklarativ etablering](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Azure AD Connect-synkronisering: Förstå standardkonfigurationen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure AD Connect-synkronisering: Förstå användare, grupper och kontakter](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD Connect-synkronisering: Skuggattribut](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Nästa steg

- [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md).
- [Vad är hybrididentitet? ](whatis-hybrid-identity.md).
