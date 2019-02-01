---
title: 'Azure AD Connect-synkronisering: Ändra standardkonfigurationen | Microsoft Docs'
description: Beskriver Metodtips för att ändra standardkonfigurationen för Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 1cebd5d049475a5962a87abede449bd6d53f3ee2
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492461"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect-synkronisering: Metodtips för att ändra standardkonfigurationen
Syftet med det här avsnittet är att beskriva som stöds respektive ändringar i Azure AD Connect-synkronisering.

Den konfiguration som skapats av Azure AD Connect fungerar ”i befintligt skick” för de flesta miljöer används för att synkroniserar lokala Active Directory med Azure AD. I vissa fall kan är det dock nödvändigt att tillämpa vissa ändringar till en konfiguration för att uppfylla ett särskilt behov eller krav.

## <a name="changes-to-the-service-account"></a>Ändringar till kontot
Azure AD Connect-synkronisering körs under ett tjänstkonto som skapats av installationsguiden. Det här tjänstkontot innehåller krypteringsnycklarna i databasen som används av synkronisering. Den har skapats med högst 127 tecken långa och lösenordet upphör att gälla inte.

* Det är **stöds inte** ändra eller återställa lösenordet för tjänstkontot. Då förstör krypteringsnycklarna och tjänsten kan inte få åtkomst till databasen och går inte att starta.

## <a name="changes-to-the-scheduler"></a>Ändringar i scheduler
Från och med versioner från version 1.1 (februari 2016) som du kan konfigurera den [scheduler](how-to-connect-sync-feature-scheduler.md) och ha en annan synkroniseringscykel än standardvärdet 30 minuter.

## <a name="changes-to-synchronization-rules"></a>Ändringar av Synkroniseringsregler
Installationsguiden innehåller en konfiguration som ska fungera för de vanligaste scenarierna. Om du behöver göra ändringar i konfigurationen måste du följa de här reglerna så att den fortfarande har en konfiguration som stöds.

> [!WARNING]
> Om du gör ändringar i standardregler för synkronisering sedan dessa ändringar kommer att skrivas över vid nästa Azure AD Connect har uppdaterats, vilket resulterar i oväntade och sannolikt oönskad Synkroniseringsresultat.

* Du kan [ändra attributflöden](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) om flödena som standard direkt attributet inte är lämplig för din organisation.
* Om du vill [inte flödar attributet](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) och ta bort alla befintliga attributvärden i Azure AD måste du skapa en regel för det här scenariot.
* [Inaktivera en oönskad Synkroniseringsregel](#disable-an-unwanted-sync-rule) i stället för att ta bort den. En borttagen regel återskapas under en uppgradering.
* Att [ändra en regel för out-of-box](#change-an-out-of-box-rule), bör du göra en kopia av den ursprungliga regeln och inaktivera out-of-box-regel. Synkronisera Rule Editor frågar och hjälper dig att.
* Exportera dina anpassade Synkroniseringsregler som använder Synchronization Rules Editor. Redigeraren får du ett PowerShell-skript som du kan använda för att enkelt återskapa dem i ett katastrofåterställningsscenario.

> [!WARNING]
> Out-of-box Synkroniseringsregler har ett tumavtryck. Om du gör en ändring i de här reglerna matchar tumavtrycket inte längre. Du kan få problem i framtiden när du försöker installera en ny version av Azure AD Connect. Endast göra ändringar på sätt som det beskrivs i den här artikeln.

### <a name="disable-an-unwanted-sync-rule"></a>Inaktivera en oönskad Synkroniseringsregel
Ta inte bort en synkroniseringsregel för av out-of-box. Den återskapas under nästa uppgraderingen.

I vissa fall kan har guiden Installera producerat en konfiguration som inte fungerar för topologin. Till exempel om du har en topologi för resursen för lagringskonton skog, men du har utökat schemat i skogen med Exchange-schemat, har sedan regler för Exchange skapats för kontoskogen och resursskogen. I så fall måste du inaktivera den Synkroniseringsregel för Exchange.

![Inaktiverad synkroniseringsregel](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Installationsguiden har hittat ett schema för gamla Exchange 2003 i skogen i bilden ovan. Det här schematillägget har lagts till innan resursskogen introducerades i Fabrikams miljö. För att säkerställa att inga attribut från den gamla Exchange-implementeringen synkroniseras, bör synkroniseringsregel inaktiveras enligt.

### <a name="change-an-out-of-box-rule"></a>Ändra en out-of-box-regel
Den enda gången som du bör ändra en out-of-box-regel är när du behöver ändra join-regeln. Om du vill ändra ett attributflöde bör du skapa en synkroniseringsregel för med högre prioritet än out-of-box-regler. Den enda regeln praktiskt taget måste du klona är regeln **i från AD - användare ansluta**. Du kan åsidosätta alla andra regler med en regel på högre prioritet.

Om du behöver göra ändringar i en regel för out-of-box, bör du göra en kopia av out-of-box-regeln och inaktiverar den ursprungliga regeln. Göra ändringarna för den klonade regeln. Synkronisera Rule Editor hjälper dig med de här stegen. När du öppnar en regel för out-of-box, visas den här dialogrutan:  
![Varning från box-regel](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Välj **Ja** att skapa en kopia av regeln. Den klonade regeln sedan öppnas.  
![Klonade regel](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

Gör nödvändiga ändringar på regeln klonade omfång, koppling och transformationer.

## <a name="next-steps"></a>Nästa steg
**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
