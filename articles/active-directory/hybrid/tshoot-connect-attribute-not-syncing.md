---
title: Felsöka ett attribut som inte synkroniseras i Azure AD Connect | Microsoft Dokument"
description: Det här avsnittet innehåller steg för felsÃ¶kning av problem med attributsynkronisering med hjälp av felsÃ¶kningsuppgiften.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60456000"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Felsöka ett attribut som inte synkroniseras i Azure AD Connect

## <a name="recommended-steps"></a>**Rekommenderade åtgärder**

Innan du undersöker problem med attributsynkronisering ska vi förstå synkroniseringsprocessen för **Azure AD Connect:**

  ![Synkroniseringsprocess för Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologi**

* **CS:** Kopplingsutrymme, en tabell i databasen.
* **MV:** Metaverse, en tabell i databasen.
* **AD:** Aktiv katalog
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Synkroniseringssteg**

* Importera från AD: Active Directory-objekt förs in i AD CS.

* Importera från AAD: Azure Active Directory-objekt förs in i AAD CS.

* Synkronisering: **Inkommande synkroniseringsregler** och regler för **utgående synkronisering** körs i prioritetsordning från lägre till högre. Om du vill visa synkroniseringsreglerna kan du gå till Redigeraren för **synkroniseringsregler** från skrivbordsprogrammen. Reglerna **för inkommande synkronisering** innehåller data från CS till MV. **Reglerna för utgående synkronisering** flyttar data från MV till CS.

* Exportera till AD: När synkroniseringen har körts exporteras objekt från AD CS till **Active Directory**.

* Exportera till AAD: När synkroniseringen har körts exporteras objekt från AAD CS till **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Steg för steg-undersökning**

* Vi kommer att starta vår sökning från **Metaverse** och titta på attributet kartläggning från källa till mål.

* Starta **Synkroniseringstjänsthanteraren** från skrivbordsprogrammen, som visas nedan:

  ![Starta tjänsthanteraren för synkronisering](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* På **Synkroniseringstjänsthanteraren**markerar du **Metaverse Search**, väljer **Omfattning efter objekttyp,** markerar objektet med ett attribut och klickar på **Sök-knappen.**

  ![Sök efter metaversum](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Dubbelklicka på objektet som hittades i **metaversen** för att visa alla dess attribut. Du kan klicka på fliken Kopplingar för att titta på motsvarande objekt i alla **kopplingsutrymmen**. **Connectors**

  ![Metaversumobjektkopplingar](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Dubbelklicka på **Active Directory Connector** för att visa **kopplingarsutrymmesattributen.** Klicka på **knappen Förhandsgranska,** i följande dialogruta, klicka på knappen **Generera förhandsgranskning.**

  ![Kopplingars utrymmesattribut](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Klicka nu på **importattributflödet**, visar detta flödet av attribut från **Active Directory Connector Space** till **Metaverse**. **Kolumnen Synkroniseringsregel** visar vilken **synkroniseringsregel** som bidragit till attributet. **Kolumnen Datakälla** visar attributen från **anslutningsutrymmet**. **Kolumnen Metaversumattribut** visar attributen i **metaversumet**. Du kan leta efter attributet som inte synkroniseras här. Om du inte hittar attributet här mappas inte detta och du måste skapa en ny anpassad **synkroniseringsregel** för att mappa attributet.

  ![Kopplingars utrymmesattribut](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Klicka på **exportattributflödet** i den vänstra rutan om du vill visa attributflödet från **Metaversit** tillbaka till **Active Directory Connector Space** med hjälp av **utgående synkroniseringsregler**.

  ![Kopplingars utrymmesattribut](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* På samma sätt kan du visa **Azure Active Directory Connector Space-objektet** och kan generera **förhandsgranskningen** för att visa attributflödet från **Metaverse** till **Anslutningsutrymmet** och vice versa, på så sätt kan du undersöka varför ett attribut inte synkroniseras.

## <a name="recommended-documents"></a>**Rekommenderade dokument**
* [Synkronisering av Azure AD Connect: Tekniska koncept](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Synkronisering av Azure AD Connect: Förstå arkitekturen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Synkronisering av Azure AD Connect: Förstå deklarativ etablering](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Synkronisering av Azure AD Connect: Förstå deklarativa etableringsuttryck](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Azure AD Connect-synkronisering: Förstå standardkonfigurationen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Synkronisering av Azure AD Connect: Förstå användare, grupper och kontakter](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Synkronisering av Azure AD Connect: Skuggattribut](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Efterföljande moment

- [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md).
- [Vad är hybrididentitet?](whatis-hybrid-identity.md).
