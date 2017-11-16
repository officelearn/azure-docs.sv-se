---
title: "Azure AD Connect-synkronisering: Förstå användare, grupper och kontakter | Microsoft Docs"
description: "Beskriver användare, grupper och kontakter i Azure AD Connect-synkronisering."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi;andkjell
ms.openlocfilehash: c298a2f99750ead099b8761699c914a3a6e41ce1
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect-synkronisering: Förstå användare, grupper och kontakter
Det finns flera olika skäl varför har du flera Active Directory-skogar och det finns flera olika distributionstopologier. Vanliga modeller innehåller en konto-resurs-distribution och GAL sync'ed skogar efter en fusion & förvärv. Men även om det finns endast modeller, hybridmodeller är vanliga samt. Standardkonfigurationen i Azure AD Connect-synkronisering förutsätter inte någon särskild modell men beroende på hur användaren matchar valdes i installationsguiden för olika beteenden kan observeras.

I det här avsnittet går vi igenom hur standardkonfigurationen ska fungera i vissa topologier. Vi går igenom konfigurationen och redigeraren för regler för synkronisering kan användas för att titta på konfigurationen.

Det finns några allmänna regler förutsätter att konfigurationen:
* Oavsett vilken ordning som vi importera från källan Active kataloger ska alltid slutresultatet vara samma.
* Ett aktivt konto kommer alltid att bidra inloggningsinformation, inklusive **userPrincipalName** och **sourceAnchor**.
* Ett inaktiverat konto bidrar userPrincipalName och sourceAnchor, såvida det inte är en länkad postlåda, om det finns inga aktiva konton som ska returneras.
* Ett konto med en länkad postlåda ska aldrig användas för userPrincipalName och sourceAnchor. Det förutsätts att ett aktivt konto kommer att hittas senare.
* En kontaktobjekt kan etableras till Azure AD som en kontakt eller som en användare. Du verkligen vet inte förrän alla källa Active Directory-skogar har bearbetats.

## <a name="groups"></a>Grupper
Viktiga saker att vara medveten om när du synkroniserar grupper från Active Directory till Azure AD:

* Azure AD Connect utesluter inbyggda säkerhetsgrupper från directory-synkronisering.

* Azure AD Connect har inte stöd för [primära gruppmedlemskap](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) till Azure AD.

* Azure AD Connect har inte stöd för [dynamisk Distribution gruppmedlemskap](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) till Azure AD.

* Så här synkroniserar du en Active Directory-grupp med Azure AD som en e-postaktiverad grupp:

    * Om gruppens *proxyAddress* attribut är tom, dess *e* attributet måste ha ett värde, eller 

    * Om gruppens *proxyAddress* attributet är icke-tom, måste den också innehålla ett primära SMTP-proxy adressvärde (med versaler **SMTP** prefix). Här följer några exempel:
    
      * En Active Directory-grupp vars proxyAddress-attributet har värdet *{”X500:/0=contoso.com/ou=users/cn=testgroup”}* inte e-postaktiverade i Azure AD. Det har inte en primär SMTP-adress.
      
      * En Active Directory-grupp vars proxyAddress-attributet har värden *{”X500:/0=contoso.com/ou=users/cn=testgroup” ”,smtp:johndoe@contoso.com”}* inte e-postaktiverade i Azure AD. Den har en SMTP-adress, men det är inte primär.
      
      * En Active Directory-grupp vars proxyAddress-attributet har värden *{”X500:/0=contoso.com/ou=users/cn=testgroup” ”,SMTP:johndoe@contoso.com”}* blir e-postaktiverade i Azure AD.

## <a name="contacts"></a>Contacts
Med kontakter som representerar en användare i en annan skog är vanliga när en fusion & förvärv där en GALSync lösning bryggning två eller flera Exchange-skogar. Kontakta objektet alltid koppla från anslutningsplatsen metaversum med postattributet e. Om det finns redan ett kontakta eller användarobjekt med samma e-postadress, är objekten sammankopplade. Detta är konfigurerat i regeln **i från AD-ansluta Kontakta**. Det finns också en regel med namnet **i från AD – kontakta vanliga** med ett attributflöde till attributet metaversum **sourceObjectType** med konstanten **Kontakta**. Den här regeln har mycket låg prioritet så om alla användarobjekt som är ansluten till samma metaversumobjekt kommer regeln **i från AD-användaren vanliga** bidrar värdet användaren till det här attributet. Med den här regeln har det här attributet det kontakt om ingen användare har anslutit och värdet användaren om minst en användare har hittats.

För att etablera ett objekt till Azure AD och den utgående regeln **på AAD – kontakta ansluta** skapar en kontaktobjekt om attribut för metaversum **sourceObjectType** är inställd på **Kontakta**. Om det här attributet har angetts till **användare**, sedan regeln **på AAD-användare ansluta** skapar ett användarobjekt i stället.
Det är möjligt att ett objekt höjs från att kontakta användaren när flera källa Active kataloger importeras och synkroniseras.

Till exempel i en topologi med GALSync hittar vi kontaktobjekt för alla i den andra skogen när vi importerar den första skogen. Detta kommer mellanlagra nya kontaktobjekt i AAD-koppling. När vi senare importerar och synkroniserar den andra skogen kommer att hitta verkliga användare och koppla dem till befintliga metaversum-objekt. Vi tar sedan bort kontaktobjekt i AAD och skapa ett nytt användarobjekt i stället.

Om du har en topologi där användare representeras som kontakter, kontrollera att du väljer för att matcha användare på e-postattribut i installationsguiden. Om du väljer ett annat alternativ kommer du ha en order-beroende konfiguration. Kontakta objekt ansluta till alltid på e-postattributet, men användarobjekt kommer bara att ansluta till på e-postattributet om det här alternativet har valts i installationsguiden. Du kan sedan hamna med två olika objekt i metaversumet med samma postattribut för e-om objektet kontakta importerades innan användarobjektet. Ett fel genereras under exporten till Azure AD. Det här beteendet är inbyggt och visar att det finns skadade data eller att topologin inte korrekt identifierades under installationen.

## <a name="disabled-accounts"></a>Inaktiva konton
Inaktiverade konton synkroniseras samt till Azure AD. Inaktiva konton är gemensamma för att representera resurser i Exchange, till exempel konferensrum. Undantaget är användare med en länkad postlåda; som tidigare nämnts etablerar dessa aldrig ett konto till Azure AD.

Antagandet är att om ett inaktiverat användarkonto finns det kommer inte att hitta active ett annat konto senare och sedan objektet har etablerats till Azure AD med userPrincipalName och sourceAnchor hittades. Om ett annat konto i active ska ansluta till samma metaversumobjekt, används dess userPrincipalName och sourceAnchor.

## <a name="changing-sourceanchor"></a>Ändra sourceAnchor
När ett objekt som har exporterats till Azure AD och det inte är tillåtet att ändra sourceAnchor längre. När objektet har exporterats attribut för metaversum **cloudSourceAnchor** anges med den **sourceAnchor** värdet som godkänns av Azure AD. Om **sourceAnchor** ändras och inte matchar **cloudSourceAnchor**, regeln **på AAD-användare ansluta** genereras felet **attributet sourceAnchor har ändra**. I det här fallet måste konfiguration eller data åtgärdas så samma sourceAnchor finns i metaversum igen innan du kan synkronisera objektet igen.

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure AD Connect Sync: Anpassa synkroniseringsalternativ](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)

