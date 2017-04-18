---
title: "Granska aktivitetsrapporter i Azure Active Directory-portalen – förhandsversion | Microsoft Docs"
description: "Introduktion till granskning av aktivitetsrapporter i Azure Active Directory-portalen – förhandsversion"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: aef5bce6f440f4a0a57763f915d307297f50281b
ms.lasthandoff: 04/10/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal---preview"></a>Granska aktivitetsrapporter i Azure Active Directory-portalen – förhandsversion

Med rapportering i Azure Active Directory-[förhandsversionen](active-directory-preview-explainer.md) får du all information du behöver för att ta reda på hur din miljö klarar sig.

Rapporteringsarkitekturen i Azure Active Directory består av följande komponenter:

- **Aktivitet** 
    - **Inloggningsaktiviteter** – Information om användningen av hanterade program och användares inloggningsaktiviteter
    - **Granskningsloggar** – Granska information om systemaktivitet för användare och grupphantering, dina hanterade program och katalogaktiviteter.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. Mer information finns i avsnittet om riskfyllda inloggningar.
    - **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. Mer information finns i avsnittet om användare som har flaggats för risk.

I det här ämnet får du en översikt över granskningsaktiviteterna.
 


## <a name="audit-logs"></a>Granskningsloggar

Granskningsloggarna i Azure Active Directory ger dokumentation över systemaktiviteter för kontroll av överensstämmelse.  
Din startpunkt för alla granskningsdata är **Granskningsloggar** i avsnittet **Aktivitet** i **Azure Active Directory**.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/61.png "Granskningsloggar")

En granskningslogg har en standardlistvy som visar:

- datum och tid för förekomsten
- initieraren/aktören (*vem*) för en aktivitet 
- aktiviteten (*vad*) 
- målet

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/18.png "Granskningsloggar")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/19.png "Granskningsloggar")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/21.png "Granskningsloggar")


När du klickar på ett objekt i listvyn visas all tillgänglig information om det.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/22.png "Granskningsloggar")


## <a name="filtering-audit-logs"></a>Filtrera granskningsloggar

Om du vill begränsa de data som rapporteras till en nivå som passar dig kan du filtrera granskningsdata med hjälp av följande fält:

- Datumintervall
- Initierad av
- Kategori
- Kategori
- Resurstyp för aktivitet
- Aktivitet

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/23.png "Granskningsloggar")


Med filtret för **datumintervall** kan du definiera en tidsram för de data som returneras.  
Möjliga värden:

- 1 månad
- 7 dagar
- 24 timmar
- Anpassat

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Med filtret **initierad av** kan du definiera en aktörs namn eller dess UPN (Universal Principal Name).

Med filtret **kategori** kan du välja något av följande filter:

- Alla
- Grundläggande kategori
- Grundläggande katalog
- Lösenordshantering via självbetjäning
- Självbetjäning, grupphantering
- Kontoetablering
- Automatiserad lösenordsförnyelse
- inbjudna användare
- MIM-tjänst

Med filtret för **aktivitsresurstyp** kan du välja något av följande filter:

- Alla 
- Grupp
- Katalog
- Användare
- Program
- Princip
- Enhet
- Annat

När du väljer **Grupp** som **aktivitetsresurstyp** får du tillgång till ytterligare en filterkategori som du kan använda för att även ange en **källa**:

- Azure AD
- O365




Filtret **aktivitet** baseras på kategorin och den aktivitetsresurstyp som du väljer. Du kan välja en specifik aktivitet som du vill visa eller välja alla. 

| Aktivitetskategori| Aktivitetsresurstyp| Aktivitet |
| :-- | :-: | :-- |
| Kärnkatalog| Grupp| Ta bort gruppinställningar|
| Kärnkatalog| Katalog| Uppdatera domän|
| Kärnkatalog| Katalog| Ta bort partner från företag|
| Kärnkatalog| Användare| Uppdatera roll|
| Kärnkatalog| Användare| Lägg till roll från mall|
| Kärnkatalog| Grupp| Lägg till approlltilldelning till grupp|
| Kärnkatalog| Grupp| Börja tillämpa gruppbaserad licens för användare|
| Kärnkatalog| Program| Lägg till tjänstens huvudnamn|
| Kärnkatalog| Princip| Uppdatera princip|
| Kärnkatalog| Princip| Lägg till princip till tjänstens huvudnamn|
| Kärnkatalog| Enhet| Lägg till registrerad ägare till enhet|
| Kärnkatalog| Enhet| Lägg till registrerade användare till enhet|
| Kärnkatalog| Enhet| Uppdatera enhetskonfiguration|
| Hantering av lösenord för självbetjäning| Användare| Återställ lösenord (självbetjäning)|
| Hantering av lösenord för självbetjäning| Användare| Lås upp användarkonto (självbetjäning)|
| Hantering av lösenord för självbetjäning| Användare| Återställ lösenord (av administratör)|
| Självbetjäning, grupphantering| Grupp| Ta bort en väntande begäran om att gå med i en grupp|
| Kontoetablering| Program| Processdeponering|
| Automatiserad förnyelse av lösenord| Program| Automatiserad förnyelse av lösenord|
| Inbjudna användare| Annat| Bearbetade batchinbjudningar|
| Kärnkatalog| Katalog| Ta bort verifierad domän|
| Kärnkatalog| Katalog| Lägg till overifierad domän|
| Kärnkatalog| Katalog| Lägg till verifierad domän|
| Kärnkatalog| Katalog| Ange katalogfunktion för klientorganisation|
| Kärnkatalog| Katalog| Ange flaggan Dirsyncenabled|
| Kärnkatalog| Katalog| Skapa företagsinställningar|
| Kärnkatalog| Katalog| Uppdatera företagsinställningar|
| Kärnkatalog| Katalog| Ta bort företagsinställningar|
| Kärnkatalog| Katalog| Ange företagstillåten dataplats|
| Kärnkatalog| Katalog| Ange företagsfunktionen Multinationell som aktiverad|
| Kärnkatalog| Användare| Uppdatera användare|
| Kärnkatalog| Användare| Ta bort användare|
| Kärnkatalog| Grupp| Ta bort medlem från grupp|
| Kärnkatalog| Grupp| Ange grupplicens|
| Kärnkatalog| Grupp| Skapa gruppinställningar|
| Kärnkatalog| Program| Uppdatera tjänstens huvudnamn|
| Kärnkatalog| Program| Ta bort program|
| Kärnkatalog| Program| Uppdatera program|
| Kärnkatalog| Program| Ta bort tjänstens huvudnamn|
| Kärnkatalog| Program| Lägg till autentiseringsuppgifter för tjänstens huvudnamn|
| Kärnkatalog| Program| Ta bort approlltilldelning från tjänstens huvudnamn|
| Kärnkatalog| Program| Ta bort ägare från program|
| Kärnkatalog| Enhet| Ta bort registrerad ägare från enhet|
| Hantering av lösenord för självbetjäning| Användare| Aktivitetsförlopp för lösenordsåterställningsflöde via självbetjäning|
| Kontoetablering| Program| Administration|
| Kontoetablering| Program| Katalogåtgärd|
| MIM-tjänst| Grupp| Ta bort medlem|
| Kärnkatalog| Princip| Ta bort princip|
| Inbjudna användare| Användare| Generering av viral klientorganisation|
| Kärnkatalog| Katalog| Uppdatera externa hemligheter|
| Kärnkatalog| Katalog| Ange egenskaper för Rights Management|
| Kärnkatalog| Katalog| Uppdatera företag|
| Kärnkatalog| Användare| Lägg till användare|
| Kärnkatalog| Användare| Konvertera federerad användare till hanterad|
| Kärnkatalog| Användare| Skapa programlösenord för användare|
| Kärnkatalog| Grupp| Lägg till medlem i grupp|
| Kärnkatalog| Grupp| Lägg till grupp|
| Kärnkatalog| Program| Samtyck till program|
| Kärnkatalog| Program| Lägg till program|
| Kärnkatalog| Program| Lägg till ägare till tjänstens huvudnamn|
| Kärnkatalog| Program| Ta bort Oauth2Permissiongrant|
| Kärnkatalog| Princip| Ta bort autentiseringsuppgifter för princip|
| Kärnkatalog| Enhet| Ta bort enhetskonfiguration|
| Självbetjäning, grupphantering| Grupp| Ange dynamiska gruppegenskaper|
| Självbetjäning, grupphantering| Grupp| Uppdatera princip för livscykelhantering|
| Kontoetablering| Program| Synkroniseringsregelåtgärd|
| Inbjudna användare| Annat| Överförda batchinbjudningar|
| MIM-tjänst| Grupp| Lägg till medlem|
| Kärnkatalog| Användare| Ange licensegenskaper|
| Kärnkatalog| Användare| Återställ användare|
| Kärnkatalog| Användare| Ta bort medlem från roll|
| Kärnkatalog| Användare| Ta bort approlltilldelning från användare|
| Kärnkatalog| Användare| Ta bort begränsad medlem från roll|
| Kärnkatalog| Grupp| Uppdatera grupp|
| Kärnkatalog| Grupp| Lägg till ägare till grupp|
| Kärnkatalog| Grupp| Slutför tillämpning av gruppbaserad licens för användare|
| Kärnkatalog| Grupp| Ta bort approlltilldelning från grupp|
| Kärnkatalog| Grupp| Ange grupp som ska hanteras av användare|
| Kärnkatalog| Program| Lägg till Oauth2Permissiongrant|
| Kärnkatalog| Program| Lägg till approlltilldelning till tjänstens huvudnamn|
| Kärnkatalog| Program| Ta bort autentiseringsuppgifter för tjänstens huvudnamn|
| Kärnkatalog| Princip| Ta bort princip från tjänstens huvudnamn|
| Kärnkatalog| Enhet| Uppdatera enhet|
| Kärnkatalog| Enhet| Lägg till enhet|
| Kärnkatalog| Enhet| Lägg till enhetskonfiguration|
| Hantering av lösenord för självbetjäning| Användare| Ändra lösenord (självbetjäning)|
| Hantering av lösenord för självbetjäning| Användare| Användare registrerad för lösenordsåterställning via självbetjäning|
| Självbetjäning, grupphantering| Grupp| Godkänn en väntande begäran om att gå med i en grupp|
| Kärnkatalog| Katalog| Ta bort overifierad domän|
| Kärnkatalog| Katalog| Verifiera domän|
| Kärnkatalog| Katalog| Ange domänautentisering|
| Kärnkatalog| Katalog| Ange lösenordsprincip|
| Kärnkatalog| Katalog| Lägg till partner till företag|
| Kärnkatalog| Katalog| Flytta upp företag till partner|
| Kärnkatalog| Katalog| Ange partnerskap|
| Kärnkatalog| Katalog| Ange tröskelvärde för oavsiktlig borttagning|
| Kärnkatalog| Katalog| Flytta ned partner|
| Inbjudna användare| Användare| Bjud in extern användare|
| Kontoetablering| Program| Importera|
| Kärnkatalog| Program| Ta bort ägare från tjänstens huvudnamn|
| Kärnkatalog| Enhet| Ta bort registrerade användare från enhet|
| Kärnkatalog| Katalog| Ange företagsinformation|
| Kärnkatalog| Katalog| Ange federationsinställningar för domän|
| Kärnkatalog| Katalog| Skapa företag|
| Kärnkatalog| Katalog| Rensa egenskaper för Rights Management|
| Kärnkatalog| Katalog| Ange Dirsync-funktion|
| Kärnkatalog| Katalog| Verifiera e-postverifierad domän|
| Kärnkatalog| Användare| Ändra användarlicens|
| Kärnkatalog| Användare| Ändra användarlösenord|
| Kärnkatalog| Användare| Återställ användarlösenord|
| Kärnkatalog| Användare| Lägg till approlltilldelning som användare beviljats|
| Kärnkatalog| Användare| Lägg till medlem till roll|
| Kärnkatalog| Användare| Ta bort programlösenord för användare|
| Kärnkatalog| Användare| Uppdatera autentiseringsuppgifter för användare|
| Kärnkatalog| Användare| Ange användarhanterare|
| Kärnkatalog| Användare| Lägg till begränsad medlem till roll|
| Kärnkatalog| Grupp| Ta bort grupp|
| Kärnkatalog| Grupp| Ta bort ägare från grupp|
| Kärnkatalog| Grupp| Uppdatera gruppinställningar|
| Kärnkatalog| Program| Lägg till ägare till program|
| Kärnkatalog| Program| Återkalla medgivande|
| Kärnkatalog| Princip| Lägg till princip|
| Kärnkatalog| Enhet| Ta bort enhet|
| Hantering av lösenord för självbetjäning| Användare| Blockerad från lösenordsåterställning via självbetjäning|
| Självbetjäning, grupphantering| Grupp| Begäran om att gå med i en grupp|
| Självbetjäning, grupphantering| Grupp| Skapa princip för livscykelhantering|
| Självbetjäning, grupphantering| Grupp| Avvisa en väntande begäran om att gå med i en grupp|
| Självbetjäning, grupphantering| Grupp| Avbryt en väntande begäran om att gå med i en grupp|
| Självbetjäning, grupphantering| Grupp| Förnya grupp|
| Kontoetablering| Program| Exportera|
| Kontoetablering| Program| Annat|
| Inbjudna användare| Användare| Lös in inbjudan för extern användare|
| Inbjudna användare| Användare| Generering av viral användare|
| Inbjudna användare| Användare| Tilldela extern användare till program|




## <a name="audit-logs-shortcuts"></a>Genvägar till granskningsloggar

Förutom **Azure Active Directory** finns det ytterligare två ställen på Azure Portal där du kan granska data:

- Användare och grupper
- Företagsprogram

En fullständig lista över granskningsrapporteringsaktiviteter finns i [lista över granskningsrapporthändelser](active-directory-reporting-audit-events.md#list-of-audit-report-events).


### <a name="users-and-groups-audit-logs"></a>Granskningsloggar för användare och grupper

Med användar- och gruppbaserade granskningsrapporter kan du få svar på frågor som:

- Vilka typer av uppdateringar har getts användare?

- Hur många användare ändrades?

- Hur många lösenord ändrades?

- Vad har en administratör gjort i en katalog?

- Vilka grupper har lagts till?

- Finns det grupper med ändringar i medlemskap?

- Har ägare av en grupp ändrats?

- Vilka licenser har tilldelats en grupp eller en användare?

Om du bara vill kontrollera granskningsdata relaterade till användare och grupper finns det en filtrerad vy under **Granskningsloggar** i avsnittet **Aktivitet** i **Användare och grupper**. I det här området är **Användare och grupper** den förvalda **aktivitetsresurstypen**.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/93.png "Granskningsloggar")

### <a name="enterprise-applications-audit-logs"></a>Granskningsloggar för företagsprogram

Med programbaserade granskningsrapporter kan du få svar på frågor som:

* Vilka program har lagts till eller uppdaterats?
* Vilka program har tagits bort?
* Har tjänstprincipen för ett program ändrats?
* Har namnen på program ändrats?
* Vem gav tillstånd till ett program?

Om du bara vill kontrollera granskningsdata relaterade till dina program finns det en filtrerad vy under **Granskningsloggar** i avsnittet **Aktivitet** på bladet **Företagsprogram**. I det här området är **Program** den förvalda **aktivitetsresurstypen**.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/134.png "Granskningsloggar")

Du kan filtrera den här vyn ytterligare till bara **grupper** eller bara **användare**.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/25.png "Granskningsloggar")


## <a name="next-steps"></a>Nästa steg
Se guiden [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).


