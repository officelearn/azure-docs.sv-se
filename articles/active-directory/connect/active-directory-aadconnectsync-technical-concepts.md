---
title: 'Azure AD Connect-synkronisering: tekniska begrepp | Microsoft Docs'
description: Beskriver de tekniska begrepp för Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4c7f1a0f1199daf9409f9ef3b4230e774321fe59
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918721"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure AD Connect-synkronisering: Tekniska begrepp
Den här artikeln är en sammanfattning av avsnittet [förstå arkitekturen](active-directory-aadconnectsync-technical-concepts.md).

Azure AD Connect-synkronisering bygger på en plattform för solid metadirectory synkronisering.
I följande avsnitt beskrivs begrepp för metadirectory synkronisering.
Azure Active Directory Sync Services innehåller bygger på MIIS, ILM och FIM nästa plattformen för att ansluta till datakällor, synkronisera data mellan datakällor, samt etablering och avetablering identiteter.

![Tekniska begrepp](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

Följande avsnitt innehåller mer information om följande aspekter av FIM-synkroniseringstjänsten:

* Koppling
* Attributflöde
* Anslutarplats
* Metaversum
* Etablering

## <a name="connector"></a>Koppling
Kodmoduler som används för att kommunicera med en ansluten katalog kallas kopplingar (kallades tidigare hanteringsagenter (MAs)).

Dessa är installerade på den dator som kör Azure AD Connect-synkronisering. De ger utan Agent kan kommunicera med hjälp av fjärrsystemet protokoll i stället för en distribution av specialiserade agenter. Det innebär att minskar risken och distributionstid, särskilt när du hanterar kritiska program och system.

I bilden ovan kopplingen är synonyma med anslutningsplatsen men omfattar all kommunikation med det externa systemet.

Anslutningen är ansvarig för alla importera och exportera funktioner i systemet och frigör utvecklare inte behöver förstå hur du ansluter till varje system internt när du använder deklarativ etablering för att anpassa Datatransformationer.

Import och export bara inträffa när schemat, vilket ger ytterligare isolering från ändringar som inträffar i systemet, eftersom ändringar inte automatiskt att spridas till den anslutna datakällan. Utvecklare kan dessutom också skapa sina egna anslutningsprogram för att ansluta till valfri datakälla.

## <a name="attribute-flow"></a>Attributflöde
Metaversum är samlad vy över alla kopplade identiteter från intilliggande kopplingens utrymmen. I bilden ovan visas attributflöde med linjer med pilar för både inkommande och utgående flow. Attributflöde är processen att kopiera eller Transformera data från ett system till ett annat och alla attribut flöden (inkommande eller utgående).

Attributflöde uppstår mellan anslutningsplatsen och metaversum riktningarna när synkroniseringsåtgärder (full eller deltadata) är schemalagda att köras.

Attributflöde inträffar bara när dessa synkroniseringar körs. Attributflöden definieras i Synkroniseringsregler. Dessa kan vara inkommande (ISR i bilden ovan) eller utgående (OSR i bilden ovan).

## <a name="connected-system"></a>Anslutna system
Anslutna system (även kallat anslutna katalogen) refererar till fjärrsystemet Azure AD Connect sync har anslutit till och läser och skriver identitetsdata till och från.

## <a name="connector-space"></a>Anslutarplats
Varje ansluten datakälla representeras som en filtrerad delmängd av de objekt och attribut i anslutningsplatsen.
Detta tillåter synkroniseringstjänsten fungera lokalt utan att behöva kontakta fjärrsystemet vid synkronisering av objekten och begränsar interaktion kan importerar och exporterar endast.

När datakällan och connector har du möjlighet att tillhandahålla en lista över ändringar (en Deltaimport), sedan ökar effektiviteten dramatiskt som endast ändringar sedan den senaste Avsökningscykel har angetts. Anslutningsplatsen gör den anslutna datakällan från ändringar som sprider automatiskt genom att kräva att connector-schema import och export. Den här extra insurance beviljar dig lugn och ro under testning, förhandsgranska eller bekräftar nästa uppdatering.

## <a name="metaverse"></a>Metaversum
Metaversum är samlad vy över alla kopplade identiteter från intilliggande kopplingens utrymmen.

Eftersom identiteter länkas samman och tilldelas behörighet för olika attribut via import mappningar av attributflöde, börjar centrala metaversumobjekt samlar in information från flera system. Från det här objektet i attributflöde har mappningar information till utgående system.

Objekt som skapas när ett system som auktoritativ projekt dem till metaversum. När alla anslutningar tas bort raderas metaversumobjekt.

Objekt i metaversum kan inte redigeras direkt. Alla data i objektet måste bidras via attributflöde. Metaversum underhåller beständiga kopplingar med varje anslutningsplatsen. Dessa anslutningar kräver inte utvärdering för varje synkronisering körs. Det innebär att Azure AD Connect-synkronisering inte har att hitta matchande fjärrobjektet varje gång. På så sätt undviker behovet av dyra agenter att attribut som vanligtvis är ansvarig för att korrelera objekten ska kunna ändras.

När upptäcka nya datakällor som kan ha redan befintliga objekt som måste hanteras, använder Azure AD Connect-synkronisering en process som kallas en join-regel för att utvärdera möjliga kandidater som kan upprätta en länk.
När länken är etablerad kan den här utvärderingsversionen försvinner och normala attributflöde ska kunna ske mellan fjärranslutna ansluten datakälla och metaversum.

## <a name="provisioning"></a>Etablering
När en auktoritativ källprojekt kan ett nytt objekt till en ny anslutarplatsen metaversum skapas i en annan koppling som representerar en underordnad ansluten datakälla.

Detta skapar en länk för sin natur och attributflöde kan fortsätta riktningarna.

När en regel anger att en ny anslutarplatsen måste skapas, kallas etablering. Men eftersom den här åtgärden endast sker i anslutarplatsen, innehåller den inte till den anslutna datakällan förrän en export utförs.

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure AD Connect Sync: Anpassa synkroniseringsalternativ](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
