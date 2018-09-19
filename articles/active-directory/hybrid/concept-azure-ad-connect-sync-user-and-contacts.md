---
title: 'Azure AD Connect-synkronisering: Förstå användare, grupper och kontakter | Microsoft Docs'
description: Förklarar användare, grupper och kontakter i Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 46d0ce28d6381662847917ce83c77780f1bd9e4c
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46315240"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect-synkronisering: Förstå användare, grupper och kontakter
Det finns flera olika orsaker varför du skulle ha flera Active Directory-skogar och det finns flera olika distributionstopologier. Vanliga modeller är en distribution för resursen för lagringskonton och GAL sync'ed skogar efter en sammanslagning & förvärv. Men även om det finns endast modeller, hybridmodeller är gemensamma samt. Med standardkonfigurationen i Azure AD Connect-synkronisering förutsätter inte någon särskild modell men beroende på hur användaren matchar valdes i installationsguiden för olika beteenden kan observeras.

I det här avsnittet lär du dig hur standardkonfigurationen fungerar i vissa topologier. Vi går igenom konfigurationen och Synchronization Rules Editor som kan användas för att titta på konfigurationen.

Det finns några allmänna regler konfigurationen förutsätter:
* Oavsett vilken ordning som vi importerar från källa Active kataloger bör alltid slutresultatet vara samma.
* Ett aktivt konto kommer alltid att bidra inloggningsinformation, inklusive **userPrincipalName** och **sourceAnchor**.
* Ett inaktiverat konto kommer att bidra userPrincipalName och sourceAnchor, om det inte en länkad postlåda, om det finns inga aktiva konton som ska returneras.
* Ett konto med en länkad postlåda ska aldrig användas för userPrincipalName och sourceAnchor. Det förutsätts att finns ett aktivt konto senare.
* Ett kontaktobjekt kan etableras till Azure AD som en kontakt eller som en användare. Vet du inte verkligen förrän alla källa Active Directory-skogar har körts klart.

## <a name="groups"></a>Grupper
Viktiga saker att känna till när du synkroniserar grupper från Active Directory till Azure AD:

* Azure AD Connect utesluter inbyggda säkerhetsgrupper från katalogsynkronisering.

* Azure AD Connect har inte stöd för synkronisering av [primära gruppmedlemskap](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) till Azure AD.

* Azure AD Connect har inte stöd för synkronisering av [dynamisk distributionsgruppsmedlemskap](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) till Azure AD.

* Så här synkroniserar du en Active Directory-grupp till Azure AD som en e-postaktiverad grupp:

    * Om gruppens *proxyAddress* attribut är tom, dess *e* attributet måste ha ett värde

    * Om gruppens *proxyAddress* attributet är inte tom, måste den innehålla minst en SMTP-värde för proxyadress. Här följer några exempel:
    
      * En Active Directory-grupp vars proxyAddress-attributet har värdet *{”X500:/0=contoso.com/ou=users/cn=testgroup”}* kan inte e-postfunktioner i Azure AD. Den har inte en SMTP-adress.
      
      * En Active Directory-grupp vars proxyAddress-attribut har värden *{”X500:/0=contoso.com/ou=users/cn=testgroup” ”,SMTP:johndoe@contoso.com”}* blir e-postfunktioner i Azure AD.
      
      * En Active Directory-grupp vars proxyAddress-attribut har värden *{”X500:/0=contoso.com/ou=users/cn=testgroup” ”,smtp:johndoe@contoso.com”}* kommer också att e-postfunktioner i Azure AD.

## <a name="contacts"></a>Contacts
Med kontakter som representerar en användare i en annan skog är vanligt när du har en fusion & förvärv där en GALSync lösning bryggning två eller flera Exchange-skogar. Kontakta objektet alltid ansluter från anslutningsplatsen till metaversum med hjälp av e-postattributet. Om det finns redan ett kontaktobjekt eller användarobjekt med samma e-postadress, är objekt som sammankopplade. Detta är konfigurerat i regeln **i från AD – kontakta ansluta**. Det finns också en regel med namnet **i från AD – kontakta vanliga** med ett attributflöde till attribut för metaversum **sourceObjectType** med Ständiga **Kontakta**. Den här regeln har mycket låg prioritet så om alla användarobjekt som är ansluten till samma metaversumobjekt kommer regeln **i från AD – vanliga användare** bidrar värdet som användaren ska det här attributet. Med den här regeln har det här attributet det kontakt om ingen användare har anslutit och värdet användaren om minst en användare har hittats.

För att etablera ett objekt till Azure AD, utgående regel **ut till AAD – kontakta ansluta** skapar ett kontaktobjekt om attribut för metaversum **sourceObjectType** är inställd på **Kontakta**. Om det här attributet är inställt på **användaren**, sedan regeln **ut till AAD – användare ansluta** skapar ett användarobjekt i stället.
Det är möjligt att ett objekt höjs från att kontakta användaren när mer aktiva käll-kataloger som importerats och synkroniseras.

Till exempel i en topologi GALSync hittar vi kontaktobjekt för alla användare i den andra skogen när vi importerar den första skogen. Detta kommer mellanlagra nya kontaktobjekt i AAD-koppling. När vi senare importera och synkronisera den andra skogen kan vi hitta riktiga användare och koppla dem till befintliga metaversum-objekt. Vi kommer sedan att ta bort kontaktobjekt i AAD och skapa ett nytt användarobjekt i stället.

Om du har en topologi där dina användare representeras som kontakter, kontrollera att du väljer för att matcha användare på e-postattributet i installationsguiden. Om du väljer ett annat alternativ, kommer du ha en order-beroende-konfiguration. Kontaktobjekt ansluter alltid till på e-postattributet, men användarobjekt endast ansluter till på e-postattributet om det här alternativet har valts i installationsguiden. Du kan sedan få med två olika objekt i metaversumet med samma postattribut för e-om kontaktobjekt har hämtats innan användarobjektet. Ett fel utlöses under exporten till Azure AD. Det här beteendet är avsiktligt och visar felaktiga data eller att topologin inte har korrekt identifierats under installationen.

## <a name="disabled-accounts"></a>Inaktiverade konton
Inaktiverade konton synkroniseras samt till Azure AD. Inaktiverade konton är gemensamma för att representera resurser i Exchange, till exempel konferensrum. Undantaget är användare med en länkad postlåda; som tidigare nämnts, kommer dessa aldrig etablera ett konto med Azure AD.

Antagandet är att om ett inaktiverat konto hittas, och sedan vi kommer inte att hitta en annan aktivt konto senare och objektet har etablerats på Azure AD med userPrincipalName och sourceAnchor hittades. Om en annan aktivt konto ansluts till samma metaversum-objekt, används dess userPrincipalName och sourceAnchor.

## <a name="changing-sourceanchor"></a>Ändra sourceAnchor
När ett objekt som har exporterats till Azure AD och det inte går att ändra sourceAnchor längre. När objektet har exporterats attribut för metaversum **cloudSourceAnchor** anges med den **sourceAnchor** värdet som godkänns av Azure AD. Om **sourceAnchor** ändras och inte matchar **cloudSourceAnchor**, regeln **ut till AAD – användare ansluta** genereras felet **sourceAnchor-attribut har ändra**. I det här fallet måste konfiguration eller data åtgärdas så samma sourceAnchor finns i metaversum igen innan du kan synkronisera objektet igen.

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure AD Connect Sync: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

