---
title: 'Azure AD Connect: Designkoncept | Microsoft-dokument'
description: I det här avsnittet beskrivs vissa implementeringsområden
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb41e14a7ecf41a2698a063c3067a98d8acf8f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253889"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Designkoncept
Syftet med det här dokumentet är att beskriva områden som måste tänkas igenom under implementeringsdesignen av Azure AD Connect. Detta dokument är en djupdykning på vissa områden och dessa begrepp beskrivs kortfattat i andra dokument också.

## <a name="sourceanchor"></a>källaAnchor
Attributet sourceAnchor definieras som *ett attribut som inte kan ändras under ett objekts livstid*. Det identifierar unikt ett objekt som samma objekt lokalt och i Azure AD. Attributet kallas också **oföränderligtId** och de två namnen används utbytbara.

Ordet oföränderlig, det vill säga "kan inte ändras", är viktigt för detta dokument. Eftersom attributets värde inte kan ändras när det har angetts, är det viktigt att välja en design som stöder ditt scenario.

Attributet används för följande scenarier:

* När en ny synkroniseringsmotorserver byggs eller återskapas efter ett katastrofåterställningsscenario länkar det här attributet befintliga objekt i Azure AD med lokala objekt.
* Om du flyttar från en molnidentitet till en synkroniserad identitetsmodell, tillåter det här attributet objekt att "hårdmatcha" befintliga objekt i Azure AD med lokala objekt.
* Om du använder federation används det här attributet tillsammans med **användarenPrincipalName** i anspråket för att unikt identifiera en användare.

Det här avsnittet talar bara om sourceAnchor när det gäller användare. Samma regler gäller för alla objekttyper, men det är bara för användare detta problem är oftast ett problem.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Välja ett bra sourceAnchor-attribut
Attributvärdet måste följa följande regler:

* Färre än 60 tecken långa
  * Tecken som inte är a-z, A-Ö eller 0-9 kodas och räknas som 3 tecken
* Innehåller inte ett specialtecken: &#92; ! # $ % & * + / = ? ^ &#96; { } | - < > . " : , [ ] " \@ _
* Måste vara globalt unik
* Måste vara antingen en sträng, heltal eller binär
* Bör inte baseras på användarens namn eftersom dessa kan ändra
* Bör inte vara skiftlägeskänsliga och undvika värden som kan variera mellan
* Ska tilldelas när objektet skapas

Om den valda sourceAnchor inte är av typen sträng, sedan Azure AD Connect Base64Encode attributvärdet för att säkerställa att inga specialtecken visas. Om du använder en annan federationsserver än ADFS kontrollerar du att servern även kan base64Encode attributet.

Attributet sourceAnchor är skiftlägeskänsligt. Värdet "JohnDoe" är inte samma som "johndoe". Men du bör inte ha två olika objekt med bara en skillnad i fall.

Om du har en enda skog lokalt, då attributet du ska använda är **objectGUID**. Detta är också det attribut som används när du använder expressinställningar i Azure AD Connect och även det attribut som används av DirSync.

Om du har flera skogar och inte flyttar användare mellan skogar och domäner är **objectGUID** ett bra attribut att använda även i det här fallet.

Om du flyttar användare mellan skogar och domäner måste du hitta ett attribut som inte ändras eller kan flyttas med användarna under flytten. En rekommenderad metod är att införa ett syntetiskt attribut. Ett attribut som kan innehålla något som ser ut som en GUID skulle vara lämpligt. När objektet skapas skapas och stämplas ett nytt GUID på användaren. En anpassad synkroniseringsregel kan skapas i synkroniseringsmotorservern för att skapa det här värdet baserat på **objectGUID** och uppdatera det valda attributet i ADD. När du flyttar objektet måste du också kopiera innehållet i det här värdet.

En annan lösning är att välja ett befintligt attribut som du vet inte ändras. Vanliga attribut är **employeeID**. Om du tänker på ett attribut som innehåller bokstäver kontrollerar du att det inte finns någon chans att ärendet (versaler kontra gemener) kan ändras för attributets värde. Felaktiga attribut som inte ska användas inkluderar dessa attribut med användarens namn. I ett äktenskap eller skilsmässa, är namnet förväntas förändras, vilket inte är tillåtet för detta attribut. Det är också en anledning till att attribut som **userPrincipalName**, **e-post**och **targetAddress** inte ens går att välja i installationsguiden för Azure AD Connect. Dessa attribut innehåller också\@" " tecken, vilket inte är tillåtet i sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Ändra attributet sourceAnchor
Attributet sourceAnchor-attributvärde kan inte ändras när objektet har skapats i Azure AD och identiteten synkroniseras.

Därför gäller följande begränsningar för Azure AD Connect:

* Attributet sourceAnchor kan bara ställas in under den första installationen. Om du kör installationsguiden igen är det här alternativet skrivskyddat. Om du behöver ändra den här inställningen måste du avinstallera och installera om.
* Om du installerar en annan Azure AD Connect-server måste du välja samma sourceAnchor-attribut som tidigare använts. Om du tidigare har använt DirSync och flyttat till Azure AD Connect måste du använda **objectGUID** eftersom det är attributet som används av DirSync.
* Om värdet för sourceAnchor ändras efter att objektet har exporterats till Azure AD, genererar Azure AD Connect-synkroniseringen ett fel och tillåter inte några fler ändringar på objektet innan problemet har åtgärdats och sourceAnchor ändras tillbaka i källan Katalog.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Använda ms-DS-ConsistencyGuid som sourceAnchor
Som standard använder Azure AD Connect (version 1.1.486.0 och äldre) objectGUID som sourceAnchor-attribut. ObjectGUID är systemgenererat. Du kan inte ange dess värde när du skapar lokala AD-objekt. Som förklaras i avsnittet [sourceAnchor](#sourceanchor)finns det scenarier där du måste ange sourceAnchor-värdet. Om scenarierna är tillämpliga på dig måste du använda ett konfigurerbart AD-attribut (till exempel ms-DS-ConsistencyGuid) som sourceAnchor-attribut.

Azure AD Connect (version 1.1.524.0 och efter) underlättar nu användningen av attributet ms-DS-ConsistencyGuid som sourceAnchor. När du använder den här funktionen konfigurerar Azure AD Connect automatiskt synkroniseringsreglerna till:

1. Använd ms-DS-ConsistencyGuid som sourceAnchor-attribut för användarobjekt. ObjectGUID används för andra objekttyper.

2. För ett givet lokalt AD-användarobjekt vars ms-DS-ConsistencyGuid-attribut inte är ifyllt skriver Azure AD Connect tillbaka sitt objectGUID-värde till attributet ms-DS-ConsistencyGuid i lokal Active Directory. När attributet ms-DS-ConsistencyGuid har fyllts i exporterar Azure AD Connect objektet till Azure AD.

>[!NOTE]
> När ett lokalt AD-objekt har importerats till Azure AD Connect (det vill säga importeras till AD-anslutningsutrymmet och projiceras till Metaverse) kan du inte längre ändra dess sourceAnchor-värde. Om du vill ange sourceAnchor-värdet för ett givet lokalt AD-objekt konfigurerar du attributet ms-DS-ConsistencyGuid innan det importeras till Azure AD Connect.

### <a name="permission-required"></a>Behörighet krävs
För att den här funktionen ska fungera måste AD DS-kontot som används för att synkronisera med lokal Active Directory beviljas skrivbehörighet till attributet ms-DS-ConsistencyGuid i lokal Active Directory.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Aktivera funktionen ConsistencyGuid - Ny installation
Du kan aktivera användningen av ConsistencyGuid som sourceAnchor under nyinstallation. Det här avsnittet beskriver både Express och Anpassad installation i detaljer.

  >[!NOTE]
  > Endast nyare versioner av Azure AD Connect (1.1.524.0 och efter) stöder användningen av ConsistencyGuid som sourceAnchor under ny installation.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Aktivera funktionen ConsistencyGuid

#### <a name="express-installation"></a>Snabb installation
När Azure AD Connect installeras med expressläge avgör Azure AD Connect-guiden automatiskt det lämpligaste AD-attributet att använda som sourceAnchor-attribut med hjälp av följande logik:

* Först frågar Azure AD Connect-guiden din Azure AD-klient för att hämta AD-attributet som används som sourceAnchor-attribut i den tidigare Azure AD Connect-installationen (om sådan finns). Om den här informationen är tillgänglig använder Azure AD Connect samma AD-attribut.

  >[!NOTE]
  > Endast nyare versioner av Azure AD Connect (1.1.524.0 och efter) lagrar information i din Azure AD-klient om sourceAnchor-attributet som används under installationen. Äldre versioner av Azure AD Connect gör det inte.

* Om information om attributet sourceAnchor som används inte är tillgänglig kontrollerar guiden tillståndet för attributet ms-DS-ConsistencyGuid i din lokala Active Directory. Om attributet inte är konfigurerat för något objekt i katalogen används ms-DS-ConsistencyGuid som sourceAnchor-attribut. Om attributet är konfigurerat på ett eller flera objekt i katalogen, drar guiden slutsatsen att attributet används av andra program och är inte lämpligt som sourceAnchor-attribut...

* I så fall återgår guiden till att använda objectGUID som sourceAnchor-attribut.

* När attributet sourceAnchor har bestämts lagras informationen i din Azure AD-klientorganisation. Informationen kommer att användas av framtida installation av Azure AD Connect.

När Express-installationen är klar informerar guiden dig om vilket attribut som har valts som attributet Source Anchor.

![Guiden informerar AD-attribut som valts för sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Anpassad installation
När du installerar Azure AD Connect med anpassat läge innehåller Azure AD Connect-guiden två alternativ när du konfigurerar sourceAnchor-attribut:

![Anpassad installation - sourceAnchor konfiguration](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Inställning | Beskrivning |
| --- | --- |
| Låt Azure hantera källfästpunkten | Välj det här alternativet om du vill att Azure AD ska hämta attributet. Om du väljer det här alternativet använder Azure AD Connect-guiden samma [källAnchor-attributvalslogik som används under Express-installationen](#express-installation). I likhet med Express-installationen informeras du om vilket attribut som har valts som attributet Source Anchor när anpassad installation har slutförts. |
| Ett specifikt attribut | Välj det här alternativet om du vill ange ett befintligt AD-attribut som sourceAnchor-attribut. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Aktivera funktionen ConsistencyGuid – befintlig distribution
Om du har en befintlig Azure AD Connect-distribution som använder objectGUID som source anchor-attribut kan du växla den till att använda ConsistencyGuid i stället.

>[!NOTE]
> Endast nyare versioner av Azure AD Connect (1.1.552.0 och efter) har stöd för att växla från ObjectGuid till ConsistencyGuid som source anchor-attribut.

Så här växlar du från objectGUID till ConsistencyGuid som attributet Source Anchor:

1. Starta Azure AD Connect-guiden och klicka på **Konfigurera** för att gå till uppgiftsskärmen.

2. Markera alternativet **Konfigurera källankare** och klicka på **Nästa**.

   ![Aktivera ConsistencyGuid för befintlig distribution – steg 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Ange dina Azure AD-administratörsuppgifter och klicka på **Nästa**.

4. Azure AD Connect-guiden analyserar tillståndet för attributet ms-DS-ConsistencyGuid i din lokala Active Directory. Om attributet inte är konfigurerat för något objekt i katalogen drar Azure AD Connect slutsatsen att inget annat program för närvarande använder attributet och är säkert att använda det som source anchor-attribut. Klicka på **Nästa** för att fortsätta.

   ![Aktivera ConsistencyGuid för befintlig distribution – steg 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. Klicka på **Konfigurera** på skärmen **Klar att konfigurera** för att göra konfigurationen ändrad.

   ![Aktivera ConsistencyGuid för befintlig distribution – steg 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. När konfigurationen är klar anger guiden att ms-DS-ConsistencyGuid nu används som source anchor-attribut.

   ![Aktivera ConsistencyGuid för befintlig distribution – steg 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Under analysen (steg 4), om attributet är konfigurerat på ett eller flera objekt i katalogen, drar guiden slutsatsen att attributet används av ett annat program och returnerar ett fel som visas i diagrammet nedan. Det här felet kan också uppstå om du tidigare har aktiverat ConsistencyGuid-funktionen på din primära Azure AD Connect-server och du försöker göra samma sak på mellanlagringsservern.

![Aktivera ConsistencyGuid för befintlig distribution - fel](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Om du är säker på att attributet inte används av andra befintliga program kan du undertrycka felet genom att starta om Azure AD Connect-guiden med växeln **/SkipLdapSearch** angiven. Om du vill göra det kör du följande kommando i kommandotolken:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Inverkan på AD FS- eller federationskonfiguration från tredje part
Om du använder Azure AD Connect för att hantera lokal AD FS-distribution uppdaterar Azure AD Connect automatiskt anspråksreglerna för att använda samma AD-attribut som sourceAnchor. Detta säkerställer att ImmutableID-anspråket som genereras av ADFS överensstämmer med sourceAnchor-värden som exporteras till Azure AD.

Om du hanterar AD FS utanför Azure AD Connect eller använder federationsservrar från tredje part för autentisering, måste du manuellt uppdatera anspråksreglerna för OföränderligaID-anspråk för att vara förenligt med de sourceAnchor-värden som exporteras till Azure AD enligt beskrivningen i artikelavsnittet [Ändra AD FS-anspråksregler](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). Guiden returnerar följande varning när installationen är klar:

![Konfiguration av federation från tredje part](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Lägga till nya kataloger i befintlig distribution
Anta att du har distribuerat Azure AD Connect med funktionen ConsistencyGuid aktiverad, och nu vill du lägga till en annan katalog i distributionen. När du försöker lägga till katalogen kontrollerar Azure AD Connect-guiden tillståndet för attributet ms-DS-ConsistencyGuid i katalogen. Om attributet är konfigurerat på ett eller flera objekt i katalogen, drar guiden slutsatsen att attributet används av andra program och returnerar ett fel som visas i diagrammet nedan. Om du är säker på att attributet inte används av befintliga program kan du undertrycka felet genom att starta om Azure AD Connect-guiden med växeln **/SkipLdapSearch** som anges ovan eller kontakta supporten för mer information.

![Lägga till nya kataloger i befintlig distribution](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD-inloggning
När du integrerar din lokala katalog med Azure AD är det viktigt att förstå hur synkroniseringsinställningarna kan påverka hur användaren autentiserar. Azure AD använder userPrincipalName (UPN) för att autentisera användaren. Men när du synkroniserar användarna måste du välja det attribut som ska användas för värdet för userPrincipalName noggrant.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Välja attribut för userPrincipalName
När du väljer attributet för att ange värdet av UPN som ska användas i Azure bör man se till att

* Attributvärdena överensstämmer med UPN-syntaxen (RFC 822), det\@vill säga det ska vara av formatet användarnamn domän
* Suffixet i värdena matchar till en av de verifierade anpassade domänerna i Azure AD

I snabbinställningar är det förmodade valet för attributet userPrincipalName. Om attributet userPrincipalName inte innehåller det värde som du vill att användarna ska logga in på Azure måste du välja **Anpassad installation**.

### <a name="custom-domain-state-and-upn"></a>Anpassat domäntillstånd och UPN
Det är viktigt att se till att det finns en verifierad domän för UPN-suffixet.

John är en användare i contoso.com. Du vill att John ska använda\@den lokala UPN-contoso.com för att logga in på Azure när du har synkroniserat användare med din Azure AD-katalog contoso.onmicrosoft.com. För att göra det måste du lägga till och verifiera contoso.com som en anpassad domän i Azure AD innan du kan börja synkronisera användarna. Om UPN-suffixet för John, till exempel contoso.com, inte matchar en verifierad domän i Azure AD, ersätter Azure AD UPN-suffixet med contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Icke-dirigerbara lokala domäner och UPN för Azure AD
Vissa organisationer har domäner som inte kan dirigeras, till exempel contoso.local eller enkla domäner med en etikett, till exempel contoso. Du kan inte verifiera en icke-dirigerbar domän i Azure AD. Azure AD Connect kan synkroniseras med endast en verifierad domän i Azure AD. När du skapar en Azure AD-katalog skapas en dirigerbar domän som blir standarddomän för din Azure AD, till exempel contoso.onmicrosoft.com. Därför blir det nödvändigt att verifiera någon annan dirigerbar domän i ett sådant scenario om du inte vill synkronisera med standarddomänen onmicrosoft.com.

Läs [Lägg till ditt eget domännamn i Azure Active Directory](../active-directory-domains-add-azure-portal.md) för mer information om hur du lägger till och verifierar domäner.

Azure AD Connect identifierar om du kör i en icke-dirigerbar domänmiljö och på lämpligt sätt skulle varna dig från att gå vidare med expressinställningar. Om du arbetar i en icke-dirigerbar domän är det troligt att UPN, för användarna, också har icke-dirigerbara suffix. Om du till exempel kör under contoso.local föreslår Azure AD Connect att du använder anpassade inställningar i stället för att använda expressinställningar. Med hjälp av anpassade inställningar kan du ange det attribut som ska användas som UPN för att logga in på Azure när användarna har synkroniserats till Azure AD.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
