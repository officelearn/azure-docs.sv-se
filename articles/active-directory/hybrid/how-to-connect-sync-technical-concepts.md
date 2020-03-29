---
title: 'Synkronisering av Azure AD Connect: Tekniska koncept | Microsoft-dokument'
description: Förklarar de tekniska koncepten för Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ec4a6100cfbb4419d7e30f4b97589113b88939
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347588"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure AD Connect-synkronisering: Tekniska begrepp
Den här artikeln är en sammanfattning av ämnet [Förstå arkitektur](how-to-connect-sync-technical-concepts.md).

Azure AD Connect-synkronisering bygger på en solid metakatalogsynkroniseringsplattform.
I följande avsnitt beskrivs begreppen för synkronisering av metakataloger.
Azure Active Directory Sync Services bygger på MIIS, ILM och FIM och tillhandahåller nästa plattform för anslutning till datakällor, synkronisering av data mellan datakällor samt etablering och avetablering av identiteter.

![Tekniska begrepp](./media/how-to-connect-sync-technical-concepts/scenario.png)

I följande avsnitt finns mer information om följande aspekter av FIM-synkroniseringstjänsten:

* Anslutningsprogram
* Attributflöde
* Anslutningsutrymme
* Metaverse
* Etablering

## <a name="connector"></a>Anslutningsprogram
De kodmoduler som används för att kommunicera med en ansluten katalog kallas kopplingar (tidigare kallade hanteringsagenter (MAs)).

Dessa är installerade på datorn som kör Azure AD Connect-synkronisering. Kopplingarna ger agentlös möjlighet att samtala med hjälp av fjärrsystemprotokoll i stället för att förlita sig på distribution av specialiserade agenter. Detta innebär minskade risk- och driftsättningstider, särskilt när det gäller kritiska program och system.

I bilden ovan är kontakten synonym med anslutningsutrymmet men omfattar all kommunikation med det externa systemet.

Anslutningsappen ansvarar för alla import- och exportfunktioner till systemet och befriar utvecklare från att behöva förstå hur du ansluter till varje system internt när du använder deklarativ etablering för att anpassa dataomvandlingar.

Import och export sker endast när den är schemalagd, vilket möjliggör ytterligare isolering från ändringar som sker i systemet, eftersom ändringar inte automatiskt sprids till den anslutna datakällan. Dessutom kan utvecklare också skapa egna kopplingar för anslutning till praktiskt taget alla datakällor.

## <a name="attribute-flow"></a>Attributflöde
Metaversumet är den konsoliderade vyn för alla kopplade identiteter från angränsande kopplingsutrymmen. I figuren ovan visas attributflödet av linjer med pilspetsar för både inkommande och utgående flöde. Attributflödet är processen att kopiera eller omvandla data från ett system till ett annat och alla attributflöden (inkommande eller utgående).

Attributflödet sker mellan kopplingsutrymmet och metaversumet dubbelriktat när synkroniseringsåtgärder (fullständig eller delta) schemaläggs för att köras.

Attributflödet inträffar bara när dessa synkroniseringar körs. Attributflöden definieras i synkroniseringsregler. Dessa kan vara inkommande (ISR på bilden ovan) eller utgående (OSR på bilden ovan).

## <a name="connected-system"></a>Anslutet system
Anslutet system (aka ansluten katalog) refererar till fjärrsystemet Azure AD Connect sync har anslutit till och läsa och skriva identitetsdata till och från.

## <a name="connector-space"></a>Anslutningsutrymme
Varje ansluten datakälla representeras som en filtrerad delmängd av objekten och attributen i anslutningsutrymmet.
Detta gör att synkroniseringstjänsten kan fungera lokalt utan att behöva kontakta fjärrsystemet när objekten synkroniseras och endast begränsar interaktionen till import och export.

När datakällan och kopplingen har möjlighet att tillhandahålla en lista över ändringar (en deltaimport) ökar driftseffektiviteten dramatiskt eftersom endast ändringar sedan den senaste avsökningscykeln utbyts. Kopplingsutrymmet isolerar den anslutna datakällan från ändringar som sprids automatiskt genom att kräva att kopplingsschemat importeras och exporteras. Den här tillagda försäkringen ger dig sinnesro när du testar, förhandsgranskar eller bekräftar nästa uppdatering.

## <a name="metaverse"></a>Metaverse
Metaversumet är den konsoliderade vyn för alla kopplade identiteter från angränsande kopplingsutrymmen.

När identiteter länkas samman och auktoritet tilldelas för olika attribut genom mappningar av importflöde börjar det centrala metaversumobjektet att sammanställa information från flera system. Från det här objektattributflödet utför mappningar information till utgående system.

Objekt skapas när ett auktoritativt system projicerar dem i metaversumet. Så snart alla anslutningar har tagits bort tas metaversumobjektet bort.

Det går inte att redigera objekt i metaversumet direkt. Alla data i objektet måste bidra via attributflödet. Metaversumet upprätthåller beständiga kopplingar med varje kopplingsutrymme. Dessa kopplingar kräver inte omvärdering för varje synkroniseringskörning. Det innebär att Azure AD Connect-synkronisering inte behöver hitta det matchande fjärrobjektet varje gång. På så sätt undviker du behovet av kostsamma agenter för att förhindra ändringar av attribut som normalt skulle vara ansvariga för att korrelera objekten.

När du upptäcker nya datakällor som kan ha befintliga objekt som måste hanteras, använder Azure AD Connect-synkronisering en process som kallas en kopplingsregel för att utvärdera potentiella kandidater som du kan upprätta en länk med.
När länken har upprättats upprepas inte utvärderingen och normalt attributflöde kan uppstå mellan fjärranslutna datakällan och metaversumet.

## <a name="provisioning"></a>Etablering
När en auktoritär källa projicerar ett nytt objekt i metaversumet kan ett nytt kopplingsutrymmesobjekt skapas i en annan koppling som representerar en nedströmsansluten datakälla.

Detta upprättar i sig en länk och attributflödet kan fortsätta varannan riktning.

När en regel bestämmer att ett nytt anslutningsutrymmesobjekt behöver skapas kallas det etablering. Men eftersom den här åtgärden endast sker inom anslutningsutrymmet överförs den inte till den anslutna datakällan förrän en export utförs.

## <a name="additional-resources"></a>Ytterligare resurser
* [Synkronisering av Azure AD Connect: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
