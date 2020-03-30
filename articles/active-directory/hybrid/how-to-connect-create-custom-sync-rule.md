---
title: Anpassa en synkroniseringsregel i Azure AD Connect | Microsoft Dokument"
description: Det här avsnittet innehåller steg för felsÃ¶kning av problem med att installera Azure AD Connect.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60351076"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Så här skapar och anpassar du en synkroniseringsregel

## <a name="recommended-steps"></a>**Rekommenderade åtgärder**

Du kan använda redigeraren för synkroniseringsregel för att redigera eller skapa en ny synkroniseringsregel. Du måste vara en avancerad användare för att kunna ändra synkroniseringsreglerna. Felaktiga ändringar kan leda till att objekt tas bort från målkatalogen. Läs [rekommenderade dokument](#recommended-documents) för att få expertis inom synkroniseringsregler. Så här ändrar du en synkroniseringsregel:

* Starta synkroniseringsredigeraren från programmenyn på skrivbordet enligt nedan:

    ![Redigerare för synkroniseringsregel](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* För att anpassa en standardsynkroniseringsregel klonar du den befintliga regeln genom att klicka på knappen "Redigera" på Redigeraren för synkroniseringsregler, som skapar en kopia av standardstandardregeln och inaktiverar den. Spara den klonade regeln med en prioritet som är mindre än 100.  Prioritet avgör vilken regel som vinner (lägre numeriskt värde) en konfliktlösning om det finns en attributflödeskonflikt.

    ![Redigerare för synkroniseringsregel](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* När du ändrar ett visst attribut bör du helst bara behålla attributet ändra i den klonade regeln.  Aktivera sedan standardregeln så att det ändrade attributet kommer från klonade regel och andra attribut plockas från standardstandardregeln. 

* Observera att om det beräknade värdet för det ändrade attributet är NULL i den klonade regeln och inte är NULL i standardstandardregeln kommer värdet inte NULL att vinna och ersätter NULL-värdet. Om du inte vill att ett NULL-värde ska ersättas med ett null-värde som inte är null tilldelar du AuthoritativeNull i den klonade regeln.

* Om du vill ändra en **utgående** regel ändrar du filtret från redigeraren för synkroniseringsregeln.

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
