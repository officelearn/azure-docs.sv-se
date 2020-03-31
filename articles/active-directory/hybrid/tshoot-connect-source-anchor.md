---
title: 'Azure AD Connect: Felsöka problem med källankare under installationen | Microsoft-dokument'
description: Det här avsnittet innehåller steg för felsÃ¶kning av problem med källankaret under installationen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "62114164"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Felsöka problem med källankare under installationen
I den här artikeln beskrivs de olika källankarerelaterade problem som kan uppstå under installationen och erbjuder sätt att lösa dessa problem.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Ogiltigt källankare i Azure Active Directory

### <a name="custom-installation"></a>Anpassad installation

Under anpassad installation läser Azure AD Connect källankarprincipen från Azure Active Directory. Om principen finns i Azure Active Directory tillämpar Azure AD Connect samma princip om den inte åsidosätts av kunden. Guiden informerar dig om vilket attribut som har lästs. Dessutom varnar guiden om du försöker åsidosätta källankareprincipen.

Under den här läsåtgärden är det möjligt att källankareprincipen i Azure Active Directory är oväntad. I det här fallet vet Inte Azure AD Connect vad källankaret ska använda och behöver manuell åsidosättning.</br>
![Oväntade](media/tshoot-connect-source-anchor/source1.png)

Lös problemet genom att manuellt åsidosätta källankaret genom att välja ett specifikt attribut. Fortsätt med det här alternativet om och endast om du är säker på vilket attribut du ska välja. Om du inte är säker kontaktar du [Microsofts support](https://support.microsoft.com/contactus/) för vägledning. Om du ändrar principen för källankare kan den bryta kopplingen mellan lokala användare och deras associerade Azure-resurser.</br>
![Oväntade](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Snabb installation
Under expressinstallation läser Azure AD Connect källankareprincipen från Azure Active Directory. Om principen finns i Azure Active Directory tillämpar Azure AD Connect samma princip. Det finns inget alternativ att göra manuell åsidosättning.

Under den här läsåtgärden är det möjligt att källankareprincipen i Azure Active Directory är oväntad. I det här fallet vet Inte Azure AD Connect vad källankaret ska vara.</br>
![Oväntade](media/tshoot-connect-source-anchor/source3.png)

För att lösa problemet måste du installera om med det anpassade läget och manuellt åsidosätta källankaret genom att välja ett visst attribut. Fortsätt med det här alternativet om och endast om du är säker på vilket attribut du ska välja. Om du inte är säker kontaktar du [Microsofts support](https://support.microsoft.com/contactus/) för vägledning. Om du ändrar principen för källankare kan den bryta kopplingen mellan lokala användare och deras associerade Azure-resurser.

### <a name="invalid-source-anchor-in-sync-engine"></a>Ogiltigt källankare i synkroniseringsmotorn
Under installationen är det möjligt azure AD Connect försök att konfigurera synkroniseringsmotorn med hjälp av en ogiltig källa ankare. Den här åtgärden är troligen ett produktproblem och installationen av Azure AD Connect misslyckas. Kontakta [Microsoft-supporten](https://support.microsoft.com/contactus/) om du stöter på det här problemet.</br>
![Oväntade](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).