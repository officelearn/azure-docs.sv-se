---
title: Så här anpassar du en Synkroniseringsregel i Azure AD Connect | Microsoft Docs
description: Det här avsnittet innehåller anvisningar för hur du felsöker problem med att installera Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75300824d94ecbd77f426539ce99da6caaa2e27a
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85360001"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Så här skapar och anpassar du en synkroniseringsregel

## <a name="recommended-steps"></a>**Rekommenderade åtgärder**

Du kan redigera eller skapa en ny Synkroniseringsregel med hjälp av regel redigeraren för synkronisering. Du måste vara en avancerad användare för att kunna ändra regler för synkronisering. Eventuella fel ändringar kan leda till att objekt tas bort från mål katalogen. Läs [rekommenderade dokument](#recommended-documents) för att få expert kunskaper i formateringsregler. Om du vill ändra en Synkroniseringsregel går du igenom följande steg:

* Starta redigeraren för synkronisering från program-menyn i Skriv bordet enligt nedan:

    ![Redigerings meny för Synkroniseringsregel](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Om du vill anpassa en standard regel för synkronisering ska du klona den befintliga regeln genom att klicka på knappen Redigera i redigeraren för regler för synkronisering, som skapar en kopia av standard standard regeln och inaktiverar den. Spara den klonade regeln med prioriteten mindre än 100.  Prioritet avgör vilken regel WINS (lägre numeriska värden) en konflikt lösning om det finns en konflikt mellan olika flöden.

    ![Redigerare för Synkroniseringsregel](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* När du ändrar ett särskilt attribut bör du helst behålla attributet ändra i den klonade regeln.  Aktivera sedan standard regeln så att det ändrade attributet kommer från den klonade regeln och andra attribut hämtas från standard regeln. 

* Observera att om det beräknade värdet för det ändrade attributet är NULL i din klonade regel och inte är NULL i standard standard regeln, kommer värdet NOT NULL att vinnas och kommer att ersätta värdet NULL. Om du inte vill att ett NULL-värde ska ersättas med ett värde som inte är NULL tilldelar du AuthoritativeNull i din klonade regel.

* Om du vill ändra en **utgående** regel ändrar du filtret i redigeraren för synkroniseringsregeln.

## <a name="recommended-documents"></a>**Rekommenderade dokument**
* [Azure AD Connect synkronisering: tekniska begrepp](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Azure AD Connect synkronisering: förstå arkitekturen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect synkronisering: förstå deklarativ etablering](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect synkronisering: förstå uttryck för deklarativ etablering](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Azure AD Connect-synkronisering: Förstå standardkonfigurationen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure AD Connect synkronisering: förstå användare, grupper och kontakter](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD Connect synkronisering: skugg-attribut](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Efterföljande moment
- [Azure AD Connect synkronisering](how-to-connect-sync-whatis.md).
- [Vad är hybrid identitet?](whatis-hybrid-identity.md).
