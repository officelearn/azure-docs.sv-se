<properties
   pageTitle="Azure Active Directory Reporting - förhandsgranskning | Microsoft Azure"
   description="Visar de olika tillgängliga rapporterna i Azure Active Directory-förhandsgranskning"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/12/2016"
   ms.author="markvi"/>


# Azure Active Directory Reporting - förhandsgranskning

*Den här dokumentationen är en del av [Azure Active Directory Reporting-guiden](active-directory-reporting-guide.md).*

Med rapportering i Azure Active Directory-förhandsgranskning får du all information du behöver för att ta reda på hur din miljö klarar sig. [Vad finns i förhandsgranskningen?](active-directory-preview-explainer.md)

Det finns två huvudområden för rapportering:

- **Inloggningsaktiviteter** – Information om användningen av hanterade program och användares inloggningsaktiviteter

- **Systemaktiviteter** – Granska information om användare och grupphantering, dina hanterade program och katalogaktiviteter

Beroende på omfånget för de data som du söker har du åtkomst till dessa rapporter antingen genom att klicka på **Användare och grupper** eller **Företagsprogram** i listan över tjänster i [Azure-portalen](https://portal.azure.com).

## Inloggningsaktiviteter

### Användarinloggningsaktiviteter

Med den information som tillhandahålls av rapporten över användarinloggningsaktiviteter får du svar på frågor som:

- Vilket inloggningsmönster har en användare?
- Hur många användare har en användare loggat in under en vecka?
- Vad är status för dessa inloggningar?

Din startpunkt för denna data är användarinloggnings-diagrammet i avsnittet **Översikt** under **Användare och grupper**.

 ![Rapportering](./media/active-directory-reporting-azure-portal/05.png "Reporting")

Diagrammet med användarinloggningar visar veckovisa sammanställningar av inloggningar för alla användare under en viss tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Rapportering](./media/active-directory-reporting-azure-portal/02.png "Reporting")

När du klickar på en dag i inloggningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

![Rapportering](./media/active-directory-reporting-azure-portal/03.png "Reporting")

Varje rad i aktivitetslistan över inloggningar ger dig detaljerad information om den valda inloggningen som:

- Vem har loggat in?

- Vad var relaterad UPN?

- Vilket program var målet för inloggningen?

- Vilken IP-adress hade inloggningen?

- Vad var status för inloggningen?

### Användning av hanterade program

Med en programcentrerad vy över dina inloggningsuppgifter kan du få svar på frågor som:

- Vem använder mina program?

- Vilka är de tre främsta programmen i organisationen?

- Jag har nyligen distribuerat ett program. Hur går det för det?


Din startpunkt för denna data är rapporten över de tre främsta programmen i organisationen under de senaste 30 dagarna avsnittet **Översikt**, under **Företagsprogram**.

 ![Rapportering](./media/active-directory-reporting-azure-portal/06.png "Reporting")


Diagram över programanvändning visar veckovisa sammanställning av inloggningar för dina tre främsta program under en given tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Rapportering](./media/active-directory-reporting-azure-portal/78.png "Reporting")

Om du vill kan du ange att fokusera på ett visst program.

![Rapportering](./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Reporting")


När du klickar på en dag i programanvändningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.


![Rapportering](./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Reporting")



Alternativet **Inloggningar** ger dig en fullständig översikt över alla inloggningshändelser för dina program.

![Rapportering](./media/active-directory-reporting-azure-portal/85.png "Reporting")

Med hjälp av kolumnväljaren kan du välja datafält som du vill visa.

![Rapportering](./media/active-directory-reporting-azure-portal/column_chooser.png "Reporting")



### Filtrera inloggningar

Du kan filtrera inloggningar efter ett tidsintervall som begränsar mängden visad data.

![Rapportering](./media/active-directory-reporting-azure-portal/927.png "Reporting")


En annan metod för att filtrera inloggningsaktivitetsposterna är att söka efter specifika poster.
Sökmetoden gör att du kan definiera omfattningen av din inloggningar runt specifika **Användare**, **Grupper** eller **Program**.


![Rapportering](./media/active-directory-reporting-azure-portal/84.png "Reporting")

## Systemaktiviteter

Granskningsloggarna i Azure Active Directory ger dokumentation över systemaktiviteter för kontroll av överensstämmelse.

Det finns tre huvudkategorier för granskningsrelaterade aktiviteter i Azure-portalen:

- Användare och grupper   

- Program

- Katalog   


En fullständig lista över granskningsrapporteringsaktiviteter finns i [lista över granskningsrapporthändelser](active-directory-reporting-audit-events.md#list-of-audit-report-events).


Din startpunkt för all granskningsdata är **Granskningsloggar** i avsnittet **Aktivitet** i **Azure Active Directory**.


![Granskning](./media/active-directory-reporting-azure-portal/61.png "Auditing")


En granskningslogg har en listvy som visar aktörer (vem) aktiviteter (vad) och målen.


![Granskning](./media/active-directory-reporting-azure-portal/345.png "Auditing")


Genom att klicka på ett objekt i listvyn kan du få mer information.

![Granskning](./media/active-directory-reporting-azure-portal/873.png "Auditing")




### Granskningsloggar för användare och grupper


Med användar- och gruppbaserade granskningsrapporter kan du få svar på frågor som:

- Vilka typer av uppdateringar har getts användare?

- Hur många användare ändrades?

- Hur många lösenord ändrades?

- Vad har en administratör gjort i en katalog?

- Vilka grupper har lagts till?

- Finns det grupper med ändringar i medlemskap?

- Har ägare av en grupp ändrats?

- Vilka licenser har tilldelats en grupp eller en användare?


Om du bara vill kontrollera granskningsdata som relateras till användare och grupper kan du hitta en filtrerad vy under **Granskningsloggar** i avsnittet **Aktivitet** i **Användare och grupper**.


![Granskning](./media/active-directory-reporting-azure-portal/93.png "Auditing")


### Granskningsloggar för program

Med programbaserade granskningsrapporter kan du få svar på frågor som:

- Vilka program har lagts till eller uppdaterats?

- Vilka program har tagits bort?

- Har tjänstprincipen för ett program ändrats?

- Har namnen på program ändrats?

- Vem gav tillstånd till ett program?


Om du bara vill kontrollera granskningsdata som relateras till program kan du hitta en filtrerad vy under **Granskningsloggar** i avsnittet **Aktivitet** i **Företagsprogram**.


![Granskning](./media/active-directory-reporting-azure-portal/134.png "Auditing")


### Filtrera granskningsloggar

Du kan filtrera en granskningsrapport efter ett tidsintervall som begränsar mängden visad data.

![Granskning](./media/active-directory-reporting-azure-portal/324.png "Auditing")

En annan metod för att filtrera posterna i en granskningslogg är att söka efter specifika poster.

![Granskning](./media/active-directory-reporting-azure-portal/237.png "Auditing")

## Nästa steg

Se guiden [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).



<!--HONumber=Sep16_HO3-->


