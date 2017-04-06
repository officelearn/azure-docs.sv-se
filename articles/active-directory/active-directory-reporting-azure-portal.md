---
title: "Azure Active Directory Reporting - förhandsgranskning | Microsoft Docs"
description: "Visar de olika tillgängliga rapporterna i Azure Active Directory-förhandsgranskning"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: be986fd7bb1745dcf43a1066dfabc1e1c699ab4c
ms.openlocfilehash: b9cd11954a52600c1cd50155cb7ce9b7d2355cd3
ms.lasthandoff: 01/20/2017


---
# <a name="azure-active-directory-reporting---preview"></a>Azure Active Directory Reporting - förhandsgranskning


*Den här dokumentationen är en del av [Azure Active Directory Reporting-guiden](active-directory-reporting-guide.md).*

Med rapportering i Azure Active Directory-förhandsgranskning får du all information du behöver för att ta reda på hur din miljö klarar sig. [Vad finns i förhandsgranskningen?](active-directory-preview-explainer.md)

Det finns två huvudområden för rapportering:

* **Inloggningsaktiviteter** – Information om användningen av hanterade program och användares inloggningsaktiviteter
* **Granskningsloggar** – Granska information om systemaktivitet för användare och grupphantering, dina hanterade program och katalogaktiviteter

Beroende på omfånget för de data som du söker har du åtkomst till dessa rapporter antingen genom att klicka på **Användare och grupper** eller **Företagsprogram** i listan över tjänster i [Azure-portalen](https://portal.azure.com).

## <a name="sign-in-activities"></a>Inloggningsaktiviteter
### <a name="user-sign-in-activities"></a>Användarinloggningsaktiviteter
Med den information som tillhandahålls av rapporten över användarinloggningsaktiviteter får du svar på frågor som:

* Vilket inloggningsmönster har en användare?
* Hur många användare har en användare loggat in under en vecka?
* Vad är status för dessa inloggningar?

Din startpunkt för denna data är användarinloggnings-diagrammet i avsnittet **Översikt** under **Användare och grupper**.

 ![Rapportering](./media/active-directory-reporting-azure-portal/05.png "Rapportering")

Diagrammet med användarinloggningar visar veckovisa sammanställningar av inloggningar för alla användare under en viss tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Rapportering](./media/active-directory-reporting-azure-portal/02.png "Rapportering")

När du klickar på en dag i inloggningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

![Rapportering](./media/active-directory-reporting-azure-portal/03.png "Rapportering")

Varje rad i aktivitetslistan över inloggningar ger dig detaljerad information om den valda inloggningen som:

* Vem har loggat in?
* Vad var relaterad UPN?
* Vilket program var målet för inloggningen?
* Vilken IP-adress hade inloggningen?
* Vad var status för inloggningen?

### <a name="usage-of-managed-applications"></a>Användning av hanterade program
Med en programcentrerad vy över dina inloggningsuppgifter kan du få svar på frågor som:

* Vem använder mina program?
* Vilka är de tre främsta programmen i organisationen?
* Jag har nyligen distribuerat ett program. Hur går det för det?

Din startpunkt för denna data är rapporten över de tre främsta programmen i organisationen under de senaste 30 dagarna avsnittet **Översikt**, under **Företagsprogram**.

 ![Rapportering](./media/active-directory-reporting-azure-portal/06.png "Rapportering")

Diagram över programanvändning visar veckovisa sammanställning av inloggningar för dina tre främsta program under en given tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Rapportering](./media/active-directory-reporting-azure-portal/78.png "Rapportering")

Om du vill kan du ange att fokusera på ett visst program.

![Rapportering](./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Rapportering")

När du klickar på en dag i programanvändningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

![Rapportering](./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Rapportering")

Alternativet **Inloggningar** ger dig en fullständig översikt över alla inloggningshändelser för dina program.

![Rapportering](./media/active-directory-reporting-azure-portal/85.png "Rapportering")

Med hjälp av kolumnväljaren kan du välja datafält som du vill visa.

![Rapportering](./media/active-directory-reporting-azure-portal/column_chooser.png "Rapportering")

### <a name="filtering-sign-ins"></a>Filtrera inloggningar
Du kan filtrera inloggningar efter ett tidsintervall som begränsar mängden visad data:

* Datum och tid 
* Användarens huvudnamn
* Programnamn
* Klientnamn
* Inloggningsstatus

![Rapportering](./media/active-directory-reporting-azure-portal/293.png "Rapportering")

En annan metod för att filtrera inloggningsaktivitetsposterna är att söka efter specifika poster.
Sökmetoden gör att du kan definiera omfattningen av din inloggningar runt specifika **Användare**, **Grupper** eller **Program**.

![Rapportering](./media/active-directory-reporting-azure-portal/84.png "Rapportering")

## <a name="audit-logs"></a>Granskningsloggar
Granskningsloggarna i Azure Active Directory ger dokumentation över systemaktiviteter för kontroll av överensstämmelse.

Det finns tre huvudkategorier för granskningsrelaterade aktiviteter i Azure-portalen:

* Användare och grupper   
* Program
* Katalog   

En fullständig lista över granskningsrapporteringsaktiviteter finns i [lista över granskningsrapporthändelser](active-directory-reporting-audit-events.md#list-of-audit-report-events).

Din startpunkt för all granskningsdata är **Granskningsloggar** i avsnittet **Aktivitet** i **Azure Active Directory**.

![Granskning](./media/active-directory-reporting-azure-portal/61.png "Granskning")

En granskningslogg har en listvy som visar aktörer (vem) aktiviteter (vad) och målen.

![Granskning](./media/active-directory-reporting-azure-portal/345.png "Granskning")

Genom att klicka på ett objekt i listvyn kan du få mer information.

![Granskning](./media/active-directory-reporting-azure-portal/873.png "Granskning")

### <a name="users-and-groups-audit-logs"></a>Granskningsloggar för användare och grupper
Med användar- och gruppbaserade granskningsrapporter kan du få svar på frågor som:

* Vilka typer av uppdateringar har getts användare?
* Hur många användare ändrades?
* Hur många lösenord ändrades?
* Vad har en administratör gjort i en katalog?
* Vilka grupper har lagts till?
* Finns det grupper med ändringar i medlemskap?
* Har ägare av en grupp ändrats?
* Vilka licenser har tilldelats en grupp eller en användare?

Om du bara vill kontrollera granskningsdata som relateras till användare och grupper kan du hitta en filtrerad vy under **Granskningsloggar** i avsnittet **Aktivitet** i **Användare och grupper**.

![Granskning](./media/active-directory-reporting-azure-portal/93.png "Granskning")

### <a name="application-audit-logs"></a>Granskningsloggar för program
Med programbaserade granskningsrapporter kan du få svar på frågor som:

* Vilka program har lagts till eller uppdaterats?
* Vilka program har tagits bort?
* Har tjänstprincipen för ett program ändrats?
* Har namnen på program ändrats?
* Vem gav tillstånd till ett program?

Om du bara vill kontrollera granskningsdata som relateras till program kan du hitta en filtrerad vy under **Granskningsloggar** i avsnittet **Aktivitet** i **Företagsprogram**.

![Granskning](./media/active-directory-reporting-azure-portal/134.png "Granskning")

### <a name="filtering-audit-logs"></a>Filtrera granskningsloggar
Du kan filtrera inloggningar efter ett tidsintervall som begränsar mängden visad data:

* Datum och tid
* Användarens huvudnamn
* Aktivitetstyp
* Aktivitet

![Granskning](./media/active-directory-reporting-azure-portal/356.png "Granskning")

Innehållet i **aktivitetstypslistan** är knutet till din åtkomstpunkt för det här bladet.  
Om din startpunkt är Azure Active Directory innehåller den här listan alla möjliga aktivitetstyper:

* Program 
* Grupp 
* Användare
* Enhet
* Katalog
* Princip
* Annat

![Granskning](./media/active-directory-reporting-azure-portal/825.png "Granskning")

De angivna aktiviteterna omfattas av aktivitetstypen.
Om du har till exempel valt en **grupp** som **aktivitetstyp**, innehåller listan **aktivitet** endast gruppaktiviteter.   

![Granskning](./media/active-directory-reporting-azure-portal/654.png "Granskning")

En annan metod för att filtrera posterna i en granskningslogg är att söka efter specifika poster.

![Granskning](./media/active-directory-reporting-azure-portal/237.png "Granskning")

## <a name="next-steps"></a>Nästa steg
Se guiden [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).


