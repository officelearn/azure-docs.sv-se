---
title: Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till rapporter om inloggningsaktiviteter i Azure Active Directory-portalen
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0d651f9d4fa48cec3a61f1f307f4447fe2cba63b
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248959"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen

Med rapportering i Azure Active Directory (Azure AD) i [Azure Portal](https://portal.azure.com) får du all information du behöver för att ta reda på hur din miljö klarar sig.

Rapporteringsarkitekturen i Azure Active Directory består av följande komponenter:

- **Aktivitet** 
    - **Inloggningsaktiviteter** – Information om användningen av hanterade program och användares inloggningsaktiviteter
    - **Granskningsloggar** – Granska information om systemaktivitet för användare och grupphantering, dina hanterade program och katalogaktiviteter.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. Mer information finns i avsnittet om riskfyllda inloggningar.
    - **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. Mer information finns i avsnittet om användare som har flaggats för risk.

I det här ämnet får du en översikt över inloggningsaktiviteterna.

## <a name="prerequisites"></a>Förutsättningar

### <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?
* Användare i säkerhetsadministratören, Security-läsare rollen rapportläsare
* Globala administratörer
* Alla användare (icke-administratörer) kan komma åt sina egna inloggningar 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?
* Din klient måste ha en associerad Azure AD Premium-licens för att det ska gå att se alla rapporter om inloggningsaktiviteter


## <a name="sign-in-activities"></a>Inloggningsaktiviteter

Med den information som tillhandahålls av rapporten över användarinloggningsaktiviteter får du svar på frågor som:

* Vilket inloggningsmönster har en användare?
* Hur många användare har loggat in under en vecka?
* Vad är status för dessa inloggningar?

Din startpunkt för alla inloggning granskningsdata är **inloggningar** i avsnittet aktivitet i **Azure Active Directory**.


![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/61.png "inloggningsaktivitet")


En inloggningslogg har en standardlistvy som visar:

- inloggningsdatum
- den aktuella användaren
- programmet som användaren har loggat in i
- inloggningsstatus
- status för riskidentifieringen
- status för MFA-kravet (multifaktorautentisering).

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/01.png "inloggningsaktivitet")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/19.png "inloggningsaktivitet")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/02.png "inloggningsaktivitet")

När du klickar på ett objekt i listvyn visas all tillgänglig information om det i en horisontell vy.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/03.png "inloggningsaktivitet")

> [!NOTE]
> Kunder kan nu felsöka principer för villkorlig åtkomst via alla inloggning rapporter. Genom att klicka på den **villkorlig åtkomst** fliken för en inloggning post kan kunder läsa statusen för villkorlig åtkomst och genomgång av information om de principer som tillämpas på inloggningen och resultatet för varje princip.
> Mer information finns i den [vanliga frågor och svar om CA-information i alla inloggningar](active-directory-reporting-faq.md#conditional-access).

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/ConditionalAccess.png "inloggningsaktivitet")


## <a name="filter-sign-in-activities"></a>Filtrera inloggningsaktivitet

Om du vill begränsa vilka data som rapporteras till en nivå som passar dig kan du filtrera inloggningsdata med hjälp av följande standardfält:

- Användare
- Program
- Inloggningsstatus
- Status för riskidentifieringen
- Date

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/04.png "inloggningsaktivitet")

Med filtret **Användare** kan du ange namn eller användarhuvudnamn (UPN) för den användare som intresserar dig.

Med filtret **Program** kan du ange namnet på programmet som intresserar dig.

Med filtret **Inloggningsstatus** kan du välja något av följande filter:

- Alla
- Lyckades
- Fel

Med filtret **Risk identifierades** kan du välja något av följande filter:

- Alla
- Ja
- Nej

Med filtret **Datum** kan du definiera en tidsram för de data som returneras.  
Möjliga värden:

- 1 månad
- 7 dagar
- 24 timmar
- Anpassat tidsintervall

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Om du lägger till ytterligare fält i inloggningsvyn läggs de automatiskt till i listan med filter. Om du till exempel lägger till fältet **Klientapp** i listan får du även ett annat filteralternativ som gör att du kan ange följande filter:

- Webbläsare      
- Exchange ActiveSync (stöds)               
- Exchange ActiveSync (stöds inte)
- Övriga klienter               
    - IMAP
    - MAPI
    - Äldre Office-klienter
    - POP
    - SMTP


![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/12.png "inloggningsaktivitet")


## <a name="download-sign-in-activities"></a>Ladda ned inloggningsaktivitet

Du kan ladda ned informationen om inloggningsaktivitet om du vill arbete med den utanför Azure Portal. När du klickar på **Ladda ned** skapas en CSV-fil med de senaste 5 000 posterna.  Förutom en nedladdningsknapp finns också ett alternativ på Azure Portal där du kan generera ett skript för att ladda ned data.  

![Ladda ned](./media/active-directory-reporting-activity-sign-ins/71.png "Ladda ned")

Om du behöver mer flexibilitet kan du använda skriptlösningen. Klicka på **skriptet** skapar ett PowerShell.skript som innehåller alla filter som du har angett. Ladda ned och köra skriptet i **administratörsläge** att skapa CSV-filen. 

### <a name="running-the-script-on-a-windows-10-machine"></a>Kör skriptet på en Windows 10-dator

Om du vill köra skriptet på en **Windows 10** dator, måste du utföra några ytterligare steg först. 

1. Installera den [AzureRM-modulen](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0l).
2. Importera modulen genom att öppna en PowerShell-kommandotolk och kör kommandot **Import-Module AzureRM**.
3. Kör **Set-ExecutionPolicy obegränsad** och välj **Ja till alla**. 
4. Du kan nu köra det hämta PowerShell-skriptet i administratörsläge för att skapa CSV-filen.

Förutom den tekniska implementeringen begränsas antalet poster som du kan ladda ned även av [bevarandereglerna för Azure Active-rapporter](active-directory-reporting-retention.md).  


## <a name="sign-in-activities-shortcuts"></a>Genvägar till inloggningsaktiviteter

Förutom Azure Active Directory, Azure-portalen ger dig ytterligare startpunkter att logga in aktiviteter data:

- Översikt över identitetssäkerhet och skydd
- Användare
- Grupper
- Företagsprogram


### <a name="users-sign-ins-activities"></a>Användares inloggningsaktivitet

Med den information som tillhandahålls av rapporten över användarinloggningsaktiviteter får du svar på frågor som:

- Vilket inloggningsmönster har en användare?
- Hur många användare har en användare loggat in under en vecka?
- Vad är status för dessa inloggningar?

Din startpunkt för dessa data är diagrammet över användarinloggning på översiktssidan **Identitetssäkerhet och skydd**. Diagrammet med användarinloggningar visar veckovisa sammanställningar av inloggningar för alla användare under en viss tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/06.png "inloggningsaktivitet")

När du klickar på en dag i inloggningsdiagrammet visas en översikt över inloggningsaktiviteterna för den dagen.

Det här visas på varje rad i listan med inloggningsaktiviteter:

* Vem har loggat in?
* Vilket program var målet för inloggningen?
* Vilken status har inloggningen?
* Vilken MFA-status har inloggningen?

När du klickar på ett objekt visas mer information om inloggningen:

- Användar-ID
- Användare
- Användarnamn
- Program-ID:t
- Program
- Client
- Plats
- IP-adress
- Date
- MFA krävs
- Inloggningsstatus

 
På sidan **Användare** visas en fullständig översikt över alla användarinloggningar om du klickar på **Inloggningar** i avsnittet **Aktivitet**.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/08.png "inloggningsaktivitet")

## <a name="usage-of-managed-applications"></a>Användning av hanterade program

Med en programcentrerad vy över dina inloggningsuppgifter kan du få svar på frågor som:

* Vem använder mina program?
* Vilka är de tre främsta programmen i organisationen?
* Jag har nyligen distribuerat ett program. Hur går det för det?

Din startpunkt för denna data är rapporten över de tre främsta programmen i organisationen under de senaste 30 dagarna avsnittet **Översikt**, under **Företagsprogram**.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/10.png "inloggningsaktivitet")

Diagram över programanvändning visar veckovisa sammanställning av inloggningar för dina tre främsta program under en given tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/47.png "inloggningsaktivitet")

Om du vill kan du ange att fokusera på ett visst program.

![Rapportering](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Rapportering")

När du klickar på en dag i programanvändningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

Alternativet **Inloggningar** ger dig en fullständig översikt över alla inloggningshändelser för dina program.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/11.png "inloggningsaktivitet")



## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om inloggningsaktivitetsfelkoder finns på [Felkoder inloggningsaktivitetsrapporten i Azure Active Directory-portalen](active-directory-reporting-activity-sign-ins-errors.md).

