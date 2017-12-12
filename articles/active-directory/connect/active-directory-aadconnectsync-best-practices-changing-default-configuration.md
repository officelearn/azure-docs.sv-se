---
title: "Azure AD Connect-synkronisering: ändra standardkonfigurationen | Microsoft Docs"
description: "Innehåller metodtips för att ändra standardkonfigurationen av Azure AD Connect-synkronisering."
services: active-directory
documentationcenter: 
author: andkjell
manager: mtillman
editor: 
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 86b9af69063ded2740ac353b863442725104c071
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect-synkronisering: bästa praxis för att ändra standardkonfigurationen
Syftet med det här avsnittet är att beskriva som stöds respektive ändringar av Azure AD Connect-synkronisering.

Den konfiguration som skapats av Azure AD Connect fungerar ”i befintligt skick” för de flesta miljöer används för att synkroniserar lokala Active Directory med Azure AD. I vissa fall kan är det dock nödvändigt att tillämpa ändringar på en konfiguration som ska uppfylla ett speciellt behov eller krav.

## <a name="changes-to-the-service-account"></a>Ändringar av kontot
Azure AD Connect-synkronisering körs under ett tjänstkonto som skapats av installationsguiden. Tjänstkontot innehåller krypteringsnycklarna på den databas som används av synkronisering. Den har skapats med ett långt lösenord 127 tecken och lösenordet upphör att gälla inte.

* Det är **stöds inte** att ändra eller återställa lösenordet för tjänstkontot. Gör det förstör krypteringsnycklarna och tjänsten kan inte få åtkomst till databasen och går inte att starta.

## <a name="changes-to-the-scheduler"></a>Ändringar i Schemaläggaren
Från och med versioner från version 1.1 (februari 2016) som du kan konfigurera den [scheduler](active-directory-aadconnectsync-feature-scheduler.md) har en annan synkronisering cykel än standardvärdet 30 minuter.

## <a name="changes-to-synchronization-rules"></a>Ändringar av Synkroniseringsregler
Guiden tillhandahåller en konfiguration som ska fungera för de vanligaste scenarierna. Om du behöver göra ändringar i konfigurationen, måste du följa dessa regler så att den fortfarande har en konfiguration som stöds.

* Du kan [ändra attributflöden](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) om de direkta attributflöden standard inte är lämpliga för din organisation.
* Om du vill [inte flöda attributet](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) och ta bort alla befintliga attribut värden i Azure AD, måste du skapa en regel för det här scenariot.
* [Inaktivera en oönskade Synkroniseringsregel](#disable-an-unwanted-sync-rule) i stället för att ta bort den. Borttagna regeln återskapas under en uppgradering.
* Att [ändra en regel för out-of-box](#change-an-out-of-box-rule), bör du göra en kopia av den ursprungliga regeln och inaktiverar regeln för out-of-box. Synkronisera Rule Editor uppmanar och hjälper dig.
* Exportera anpassade Synkroniseringsregler med hjälp av Redigeraren för regler för synkronisering. Redigeraren för ger dig ett PowerShell-skript som du kan använda för att enkelt återskapa dem i en katastrofåterställning.

> [!WARNING]
> Out-of-box sync-regler har ett tumavtryck. Om du ändrar de här reglerna matchar inte längre tumavtrycket. Du kan få problem i framtiden när du försöker installera en ny version av Azure AD Connect. Endast ändra det sätt som det beskrivs i den här artikeln.

### <a name="disable-an-unwanted-sync-rule"></a>Inaktivera en oönskade Synkroniseringsregel
Ta inte bort en synkroniseringsregel för av out-of-box. Den återskapas under nästa uppgraderingen.

I vissa fall har installationsguiden resulterade i en konfiguration som inte fungerar för din topologi. Till exempel om du har ett konto-resurs skogstopologi men du har utökat schemat i skogen med Exchange-schemat, skapas regler för Exchange för kontoskogen och resursskogen. I det här fallet måste du inaktivera synkronisering regeln för Exchange.

![Inaktiverade synkroniseringsregel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Installationsguiden har hittat ett gamla Exchange 2003-schema i skogen i bilden ovan. Det här schemat har lagts till innan resursskogen infördes i Fabrikams miljö. Om du vill se till att inga attribut från den gamla Exchange-implementeringen är synkroniserade bör sync regeln inaktiveras enligt.

### <a name="change-an-out-of-box-rule"></a>Ändra en out-of-box-regel
Den enda gången som du bör ändra en out-of-box-regel är när du behöver ändra join-regel. Om du behöver ändra ett attributflöde bör du skapa en synkroniseringsregel med högre prioritet än reglerna out-of-box. Den enda regeln praktiskt taget måste du klona är regeln **i från AD - användare ansluta**. Du kan åsidosätta alla regler med en regel på högre prioritet.

Om du behöver göra ändringar i en out-of-box-regel bör du göra en kopia av out-of-box-regeln och inaktiverar den ursprungliga regeln. Sedan gör ändringar i den klonade regeln. Synkronisera Rule Editor hjälper dig med de här stegen. När du öppnar en out-of-box-regel kan visas med den här dialogrutan:  
![Varning out-of-box-regel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Välj **Ja** att skapa en kopia av regeln. Klonade regeln sedan öppnas.  
![Klonade regel](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Genomför nödvändiga ändringar på regeln klonade omfång, koppling och transformationer.

## <a name="next-steps"></a>Nästa steg
**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
