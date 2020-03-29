---
title: 'Synkronisering av Azure AD Connect: Ändra standardkonfigurationen | Microsoft-dokument'
description: Innehåller metodtips för att ändra standardkonfigurationen för Azure AD Connect-synkronisering.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 940a35d89996b1eb9600fe4214863d2b5304750e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60242128"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Synkronisering av Azure AD Connect: Metodtips för att ändra standardkonfigurationen
Syftet med det här avsnittet är att beskriva ändringar som stöds och inte stöds i Azure AD Connect-synkronisering.

Konfigurationen som skapas av Azure AD Connect fungerar "i det andra fallet" för de flesta miljöer som synkroniserar lokal Active Directory med Azure AD. I vissa fall är det dock nödvändigt att tillämpa vissa ändringar i en konfiguration för att uppfylla ett visst behov eller krav.

## <a name="changes-to-the-service-account"></a>Ändringar i tjänstkontot
Azure AD Connect-synkronisering körs under ett tjänstkonto som skapats av installationsguiden. Det här tjänstkontot innehåller krypteringsnycklarna till databasen som används av synkroniseringen. Det skapas med ett 127 tecken långt lösenord och lösenordet är inställt på att inte löpa ut.

* Det stöds inte att ändra eller återställa lösenordet för **tjänstkontot.** Om du gör det förstörs krypteringsnycklarna och tjänsten kan inte komma åt databasen och kan inte starta.

## <a name="changes-to-the-scheduler"></a>Ändringar i schemaläggaren
Från och med versionerna från version 1.1 (februari 2016) kan du konfigurera [schemaläggaren](how-to-connect-sync-feature-scheduler.md) så att den har en annan synkroniseringscykel än standard30 minuter.

## <a name="changes-to-synchronization-rules"></a>Ändringar i synkroniseringsregler
Installationsguiden innehåller en konfiguration som ska fungera för de vanligaste scenarierna. Om du behöver göra ändringar i konfigurationen måste du följa dessa regler för att fortfarande ha en konfiguration som stöds.

> [!WARNING]
> Om du gör ändringar i standardsynkroniseringsreglerna skrivs dessa ändringar över nästa gång Azure AD Connect uppdateras, vilket resulterar i oväntade och sannolika oönskade synkroniseringsresultat.

* Du kan [ändra attributflöden](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) om standardflödena för direkt attribut inte är lämpliga för din organisation.
* Om du [inte](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) vill flöda ett attribut och ta bort befintliga attributvärden i Azure AD måste du skapa en regel för det här scenariot.
* [Inaktivera en oönskad synkroniseringsregel](#disable-an-unwanted-sync-rule) i stället för att ta bort den. En borttagen regel återskapas under en uppgradering.
* Om du vill [ändra en out-of-box-regel](#change-an-out-of-box-rule)bör du göra en kopia av den ursprungliga regeln och inaktivera regeln om direkt. Redigeraren för synkroniseringsregeln frågar och hjälper dig.
* Exportera dina anpassade synkroniseringsregler med hjälp av Redigeraren för synkroniseringsregler. Redigeraren ger dig ett PowerShell-skript som du kan använda för att enkelt återskapa dem i ett katastrofåterställningsscenario.

> [!WARNING]
> De färdiga synkroniseringsreglerna har ett tumavtryck. Om du gör en ändring av dessa regler matchar tumavtrycket inte längre. Du kan ha problem i framtiden när du försöker tillämpa en ny version av Azure AD Connect. Gör bara ändringar på det sätt som beskrivs i den här artikeln.

### <a name="disable-an-unwanted-sync-rule"></a>Inaktivera en oönskad synkroniseringsregel
Ta inte bort en out-of-box synkroniseringsregel. Det återskapas under nästa uppgradering.

I vissa fall har installationsguiden skapat en konfiguration som inte fungerar för din topologi. Om du till exempel har en kontoresursskogstopologi men har utökat schemat i kontoskogen med Exchange-schemat skapas regler för Exchange för kontoskogen och resursskogen. I det här fallet måste du inaktivera synkroniseringsregeln för Exchange.

![Inaktiverad synkroniseringsregel](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

I bilden ovan har installationsguiden hittat ett gammalt Exchange 2003-schema i kontoskogen. Det här schematillägget lades till innan resursskogen introducerades i Fabrikams miljö. Om du vill vara säkra på att inga attribut från den gamla Exchange-implementeringen synkroniseras bör synkroniseringsregeln inaktiveras enligt bilden.

### <a name="change-an-out-of-box-rule"></a>Ändra en out-of-box-regel
Den enda gången du bör ändra en out-of-box-regel är när du behöver ändra kopplingsregeln. Om du behöver ändra ett attributflöde bör du skapa en synkroniseringsregel med högre prioritet än de färdiga reglerna. Den enda regel du praktiskt taget behöver klona är regeln **i från AD - Användarkoppling**. Du kan åsidosätta alla andra regler med en regel med högre prioritet.

Om du behöver göra ändringar i en out-of-box-regel bör du göra en kopia av regeln om out-of-box och inaktivera den ursprungliga regeln. Gör sedan ändringarna i den klonade regeln. Redigeraren för synkroniseringsregel hjälper dig med dessa steg. När du öppnar en out-of-box-regel visas den här dialogrutan:  
![Varning utanför rut-regeln](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Välj **Ja** om du vill skapa en kopia av regeln. Den klonade regeln öppnas sedan.  
![Klonad regel](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

I den här klonade regeln gör du nödvändiga ändringar i omfattning, koppling och omvandlingar.

## <a name="next-steps"></a>Nästa steg
**Avsnitt om översikt**

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
