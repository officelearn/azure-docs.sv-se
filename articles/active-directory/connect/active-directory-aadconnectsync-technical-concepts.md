---
title: 'Azure AD Connect-synkronisering: tekniska begrepp | Microsoft Docs'
description: Förklarar tekniska begrepp i Azure AD Connect-synkronisering.
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
ms.author: markvi;andkjell
ms.openlocfilehash: 591f67747316b950f32c5a113edda1080ed814a0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592848"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure AD Connect-synkronisering: Tekniska begrepp
Den här artikeln är en sammanfattning av avsnittet [förstå arkitektur](active-directory-aadconnectsync-technical-concepts.md).

Azure AD Connect-synkronisering bygger på en fast metadirectory synkronisering plattform.
I följande avsnitt beskrivs begrepp för metadirectory synkronisering.
Azure Active Directory Sync Services tillhandahåller bygger på MIIS, ILM och FIM, den nästa plattformen för att ansluta till datakällor, synkronisera data mellan datakällor, samt etablering och borttagning av identiteter.

![Tekniska begrepp](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

Följande avsnitt innehåller mer information om följande aspekter av FIM-synkroniseringstjänsten:

* Koppling
* Attributflöde
* Anslutarplats
* Metaversum
* Etablering

## <a name="connector"></a>Koppling
Kodmoduler som används för att kommunicera med en ansluten katalog kallas kopplingar (kallades tidigare hanteringsagenter (MAs)).

Dessa är installerade på den dator som kör Azure AD Connect-synkronisering. Kopplingarna ger utan Agent möjlighet att kommunicera med hjälp av fjärrsystemet protokoll i stället för en distribution av särskilda agenter. Detta innebär att minskar risken och distributionstiden, särskilt när du hanterar viktiga program och system.

I bilden ovan kopplingen är synonym med anslutningsplatsen men omfattar all kommunikation med det externa systemet.

Anslutningen är ansvarig för alla importera och exportera funktioner i systemet och frigör utvecklare från behöver förstå hur du ansluter till varje system internt när du använder deklarativ etablering för att anpassa Datatransformationer.

Import och export inträffar bara när schemat, vilket ger ytterligare isolering från ändringar som sker i systemet, eftersom ändringar automatiskt inte replikerats till den anslutna datakällan. Dessutom kan kan utvecklare också skapa egna kopplingar för att ansluta till valfri datakälla.

## <a name="attribute-flow"></a>Attributflöde
Metaversum är samlad vy över alla kopplade identiteter från intilliggande kopplingens utrymmen. I bilden ovan visas attributflöde med linjer med pilar för både inkommande och utgående flöde. Attributflöde är processen att kopiera eller överföra data från ett system till en annan och alla attribut flöden (inkommande eller utgående).

Attributflöde sker mellan anslutningsplatsen och metaversum båda riktningarna när synkroniseringsåtgärder (fullständig eller delta) är schemalagda att köras.

Attributflöde inträffar bara när dessa synkroniseringar körs. Attributflöden definieras i Synkroniseringsregler. Dessa kan vara inkommande (ISR i bilden ovan) eller utgående (OSR i bilden ovan).

## <a name="connected-system"></a>Det anslutna systemet
Anslutna system (aka ansluten katalog) refererar till Azure AD Connect sync har anslutit till fjärrsystemet och läser och skriver identitetsdata till och från.

## <a name="connector-space"></a>Anslutarplats
Varje anslutna datakällan representeras som en filtrerad delmängd av de objekt och attribut i anslutningsplatsen.
Detta kan sync-tjänsten fungerar lokalt utan att behöva kontakta fjärrsystemet när du synkroniserar objekten och begränsar interaktion för import och exporterar endast.

När datakällan och anslutningen har du möjlighet att ange en lista över ändringar (Deltaimport), sedan ökar effektiviteten kraftigt eftersom endast ändringar sedan den senaste avsökningscykeln utbyts. Anslutningsplatsen gör den anslutna datakällan från ändringar som sprider automatiskt genom att kräva att kopplingen schemat import och export. Den här tillagda insurance ger lugn vid testning, förhandsgranska eller bekräfta nästa uppdatering.

## <a name="metaverse"></a>Metaversum
Metaversum är samlad vy över alla kopplade identiteter från intilliggande kopplingens utrymmen.

Identiteter är länkade till varandra, och tilldelas behörighet för olika attribut via importera mappningar av attributflöde börjar centrala metaversumobjekt samlar in information från flera system. Från det här objektet attributflöde utföra mappningar information för utgående system.

Objekt som skapas när en auktoritativ system projekt dem i metaversum. När alla anslutningar tas bort raderas metaversum-objekt.

Objekt i metaversumet kan inte ändras direkt. Alla data i objektet måste vara bidragit via attributflöde. Metaversum underhåller beständiga kopplingar med varje anslutningsplatsen. Dessa anslutningar kräver inte utvärdering för varje synkronisering körs. Det innebär att Azure AD Connect-synkronisering inte har att hitta matchande fjärrobjektet varje gång. Detta förhindrar behovet av kostsamma agenter att attribut som vanligtvis är ansvarig för korrelation mellan objekten ska kunna ändras.

När upptäcka nya datakällor som kan ha redan befintliga objekt som måste hanteras, använder Azure AD Connect-synkronisering en process som kallas en join-regel för att utvärdera potentiella kandidater som du vill skapa en länk.
När länken är etablerad utvärderingen försvinner och normal attributflöde kan ske mellan fjärranslutna ansluten datakälla och metaversum.

## <a name="provisioning"></a>Etablering
När ett projekt som auktoritativ datakälla skapas ett nytt objekt i metaversum ett nytt objekt i kopplingen kan i en annan koppling som representerar en underordnad ansluten datakälla.

Detta upprättar en länk natur och attributflöde kan fortsätta båda riktningarna.

När en regel anger att ett nytt connector utrymme objekt måste skapas, kallas etablering. Men eftersom åtgärden endast sker inom anslutningsplatsen, innehåller det inte till den anslutna datakällan förrän en export utförs.

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure AD Connect Sync: Anpassa synkroniseringsalternativ](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
