---
title: 'Synkronisering av Azure AD Connect: Förstå användare, grupper och kontakter | Microsoft-dokument'
description: Förklarar användare, grupper och kontakter i Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661747754369c17ca98ae69d477e04124b6a2942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245480"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Synkronisering av Azure AD Connect: Förstå användare, grupper och kontakter
Det finns flera olika orsaker till varför du skulle ha flera Active Directory-skogar och det finns flera olika distributionstopologier. Vanliga modeller är en account-resource-distribution och GAL sync'ed skogar efter en sammanslagning & förvärv. Men även om det finns rena modeller, hybridmodeller är vanliga också. Standardkonfigurationen i Azure AD Connect-synkroniseringen förutsätter inte någon särskild modell, men beroende på hur användarmatchning valdes i installationsguiden kan olika beteenden observeras.

I det här avsnittet kommer vi att gå igenom hur standardkonfigurationen fungerar i vissa topologier. Vi kommer att gå igenom konfigurationen och Redigeraren för synkroniseringsregler kan användas för att titta på konfigurationen.

Det finns några allmänna regler som konfigurationen förutsätter:
* Oavsett vilken ordning vi importerar från källan Active Directories, bör slutresultatet alltid vara detsamma.
* Ett aktivt konto bidrar alltid med inloggningsinformation, inklusive **userPrincipalName** och **sourceAnchor**.
* Ett inaktiverat konto bidrar med userPrincipalName och sourceAnchor, såvida det inte är en länkad postlåda, om det inte finns något aktivt konto att hitta.
* Ett konto med en länkad postlåda kommer aldrig att användas för userPrincipalName och sourceAnchor. Det antas att ett aktivt konto kommer att hittas senare.
* Ett kontaktobjekt kan etableras till Azure AD som en kontakt eller som användare. Du vet inte riktigt förrän alla källa Active Directory skogar har bearbetats.

## <a name="groups"></a>Grupper
Viktiga punkter att vara medveten om när du synkroniserar grupper från Active Directory till Azure AD:

* Azure AD Connect utesluter inbyggda säkerhetsgrupper från katalogsynkronisering.

* Azure AD Connect stöder inte synkronisering [av primära gruppmedlemskap](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) till Azure AD.

* Azure AD Connect stöder inte synkronisering av [medlemskap i dynamiska distributionsgrupper](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) till Azure AD.

* Så här synkroniserar du en Active Directory-grupp till Azure AD som en e-postaktiverad grupp:

    * Om gruppens *proxyAddress-attribut* är tomt måste dess *e-postattribut* ha ett värde

    * Om gruppens *proxyAddress-attribut* inte är tomt måste det innehålla minst ett SMTP-proxyadressvärde. Här följer några exempel:
    
      * En Active Directory-grupp vars proxyAddress-attribut har värdet *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* kommer inte att vara e-postaktiverat i Azure AD. Den har ingen SMTP-adress.
      
      * En Active Directory-grupp vars proxyAddress-attribut har värden *\@{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe contoso.com"}* kommer att e-postaktiveras i Azure AD.
      
      * En Active Directory-grupp vars proxyAddress-attribut har värden *{"X500:/0=contoso.com/ou=users/cn=testgroup",\@"smtp:johndoe contoso.com"}* kommer också att vara e-postaktiverat i Azure AD.

## <a name="contacts"></a>Contacts
Att ha kontakter som representerar en användare i en annan skog är vanligt efter en sammanslagning & förvärv där en GALSync-lösning överbryggar två eller flera Exchange-skogar. Kontaktobjektet kopplas alltid från kopplingsutrymmet till metaversumet med e-postattributet. Om det redan finns ett kontaktobjekt eller ett användarobjekt med samma e-postadress sammanfogas objekten. Detta är konfigurerat i regeln **In från AD – Kontaktkoppling**. Det finns också en regel med namnet **In från AD – Kontakt vanligt** med ett attributflöde till metaverstributet **sourceObjectType** med **konstanten Kontakt**. Den här regeln har mycket låg prioritet så om ett användarobjekt är anslutet till samma metaversumobjekt, kommer regeln **In från AD – User Common** att bidra med värdet Användaren till det här attributet. Med den här regeln har det här attributet värdet Kontakt om ingen användare har anslutits och värdet Användaren om minst en användare har hittats.

För att etablera ett objekt till Azure AD skapas ett kontaktobjekt för utgående regel **Utgående till AAD – Kontaktkoppling** om metaversumattributet **sourceObjectType** är inställt på **Kontakt**. Om det här attributet är inställt på **Användare**skapas ett användarobjekt i stället av regeln **Ut till AAD– Användarkoppling.**
Det är möjligt att ett objekt befordras från kontakt till användare när fler aktiva källkataloger importeras och synkroniseras.

I en GALSync-topologi hittar vi till exempel kontaktobjekt för alla i den andra skogen när vi importerar den första skogen. Då arrangeras nya kontaktobjekt i AAD-kopplingen. När vi senare importerar och synkroniserar den andra skogen hittar vi de verkliga användarna och ansluter dem till de befintliga metaversumobjekten. Vi tar sedan bort kontaktobjektet i AAD och skapar ett nytt användarobjekt istället.

Om du har en topologi där användare representeras som kontakter kontrollerar du att du väljer att matcha användare på e-postattributet i installationsguiden. Om du väljer ett annat alternativ har du en orderberoende konfiguration. Kontaktobjekt kopplas alltid till i e-postattributet, men användarobjekt ansluter bara till e-postattributet om det här alternativet har valts i installationsguiden. Du kan sedan sluta med två olika objekt i metaversumet med samma e-postattribut om kontaktobjektet importerades före användarobjektet. Under export till Azure AD genereras ett fel. Detta är avsiktligt och indikerar felaktiga data eller att topologin inte identifierades korrekt under installationen.

## <a name="disabled-accounts"></a>Inaktiverade konton
Inaktiverade konton synkroniseras också till Azure AD. Inaktiverade konton är vanliga för att representera resurser i Exchange, till exempel konferensrum. Undantaget är användare med en länkad postlåda. Som tidigare nämnts kommer dessa aldrig att etablera ett konto till Azure AD.

Antagandet är att om ett inaktiverat användarkonto hittas, kommer vi inte att hitta ett annat aktivt konto senare och objektet etableras till Azure AD med userPrincipalName och sourceAnchor hittades. Om ett annat aktivt konto kommer att ansluta till samma metaversumobjekt, kommer dess userPrincipalName och sourceAnchor att användas.

## <a name="changing-sourceanchor"></a>Ändra sourceAnchor
När ett objekt har exporterats till Azure AD är det inte längre tillåtet att ändra sourceAnchor. När objektet har exporterats är metaversumattributet **cloudSourceAnchor** inställt med **sourceAnchor-värdet** som accepteras av Azure AD. Om **sourceAnchor** ändras och inte matchar **cloudSourceAnchor**har regeln **Ut till AAD – User Join** kasta felet **sourceAnchor-attributet ändrats**. I det här fallet måste konfigurationen eller data korrigeras så att samma sourceAnchor finns i metaversum igen innan objektet kan synkroniseras igen.

## <a name="additional-resources"></a>Ytterligare resurser
* [Synkronisering av Azure AD Connect: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

