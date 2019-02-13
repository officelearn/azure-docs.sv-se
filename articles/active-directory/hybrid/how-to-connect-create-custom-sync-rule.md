---
title: Så här anpassar du en regel för synkronisering i Azure AD Connect | Microsoft Docs
description: Det här avsnittet innehåller anvisningar att felsöka problem med att installera Azure AD Connect.
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
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172441"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Hur du anpassar en synkroniseringsregel

## <a name="recommended-steps"></a>**Rekommenderade åtgärder**

Du kan använda regelredigeraren synkronisering för att redigera eller skapa en ny synkroniseringsregel. Du måste vara en avancerad användare att göra ändringar i Synkroniseringsregler. Eventuella fel ändringar kan resultera i ta bort objekt från din målkatalogen. Läs [rekommenderade dokument](#recommended-documents) att få kunskap om Synkroniseringsregler. Ändra en synkroniseringsregel gå igenom följande steg:

* Starta Redigeraren synkronisering från program-menyn i desktop enligt nedan:

    ![Synchronization Rule Editor menyn](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Klona den befintliga regeln genom att klicka på knappen ”Redigera” i Synchronization Rules Editor som skapar en kopia av standard Standardregeln och inaktivera det för att anpassa en standardregel för synkronisering. Spara den klonade regeln med prioritet mindre än 100.  Prioritet avgör vilken regel wins (lägre numeriska värde) en lösning om det finns en konflikt för attributet flöde.

    ![Synchronization Rule Editor](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* När du ändrar ett specifikt attribut, bör helst du bara behålla ändra attributet i klonade regeln.  Aktivera Standardregeln så att ändrade attribut kommer från klonade regel och andra attribut hämtas från standard Standardregeln. 

* Observera att i de fall där det beräknade värdet för attributet ändrade är NULL på klonade regeln och inte är NULL i kodstandarden inte regel sedan NULL-värde som vinner och ersätter NULL-värde. Om du inte vill ersätta ett NULL-värde ska vara med ett annat värde än NULL tilldela AuthoritativeNull i din klonade regel.

* Ändra en **utgående** regel, ändra filter från Regelredigeraren synkronisering.

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
