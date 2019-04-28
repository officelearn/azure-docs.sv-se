---
title: 'Azure AD Connect: Felsöka källa ankare problem under installationen | Microsoft Docs'
description: Det här avsnittet innehåller anvisningar att felsöka problem med källfästpunkten under installationen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114164"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Felsökning av källa ankare problem under installationen
Den här artikeln beskriver de olika källfästpunkt-relaterade problem som kan uppstå under installation och erbjudanden sätt att lösa dessa problem.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Ogiltig Källfästpunkt i Azure Active Directory

### <a name="custom-installation"></a>Anpassad installation

Under installationen av anpassad läser Azure AD Connect ankare källprincip från Azure Active Directory. Om principen finns i Azure Active Directory, tillämpar Azure AD Connect samma princip såvida den inte åsidosätts av kunden. Guiden informerar dig vilket attribut har lästs. Guiden varnar också om du försöker åsidosätta ankare källprincip.

Under den här läsning är det möjligt att förtroendeankare källprincip i Azure Active Directory är oväntad. I det här fallet vet inte vilka källfästpunkten att använda Azure AD Connect och måste manuell åsidosättning.</br>
![oväntat](media/tshoot-connect-source-anchor/source1.png)

Du kan manuellt åsidosätta källfästpunkten genom att välja ett specifikt attribut för att lösa problemet. Gå vidare med det här alternativet om du är säker på vilka attribut för att välja. Om du inte är säker, kontaktar du [Microsoft-supporten](https://support.microsoft.com/contactus/) anvisningar. Om du ändrar ankare källprincip kan skadas appen associationen mellan dina lokala användare och deras associerade Azure-resurser.</br>
![oväntat](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Snabbinstallation
Under expressinstallationsfiler läser Azure AD Connect ankare källprincip från Azure Active Directory. Om principen finns i Azure Active Directory, tillämpar Azure AD Connect samma princip. Det går inte att utföra manuell åsidosättning.

Under den här läsning är det möjligt att förtroendeankare källprincip i Azure Active Directory är oväntad. I det här fallet vet Azure AD Connect inte vad källfästpunkten bör vara.</br>
![oväntat](media/tshoot-connect-source-anchor/source3.png)

För att lösa problemet måste du installera med hjälp av anpassade läget och manuellt åsidosätta källfästpunkten genom att välja ett specifikt attribut. Gå vidare med det här alternativet om du är säker på vilka attribut för att välja. Om du inte är säker, kontaktar du [Microsoft-supporten](https://support.microsoft.com/contactus/) anvisningar. Om du ändrar ankare källprincip kan skadas appen associationen mellan dina lokala användare och deras associerade Azure-resurser.

### <a name="invalid-source-anchor-in-sync-engine"></a>Ogiltig Källfästpunkt i Synkroniseringsmotorn
Under installationen, det är möjligt Azure AD anslutningsförsöken konfigurera Synkroniseringsmotorn med hjälp av en ogiltig källfästpunkt. Den här åtgärden är troligen ett produktproblem och misslyckas installationen av Azure AD Connect. Kontakta [Microsoft-supporten](https://support.microsoft.com/contactus/) om du får det här problemet.</br>
![oväntat](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).