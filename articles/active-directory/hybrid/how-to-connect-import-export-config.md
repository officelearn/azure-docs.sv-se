---
title: Så här importerar och exporterar du Azure AD Connect konfigurations inställningar
description: Det här dokumentet beskriver vanliga frågor om moln etablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94d356cb3c0345f575b4b5a44aa7f228535e66d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019887"
---
# <a name="importing-and-exporting-azure-ad-connect-configuration-settings-public-preview"></a>Importera och exportera Azure AD Connect konfigurations inställningar (offentlig för hands version) 

Azure AD Connect distributioner skiljer sig från en enda skog Express-läge installation, till komplexa distributioner som synkroniseras över flera skogar med hjälp av anpassade regler för synkronisering. På grund av det stora antalet konfigurations alternativ och-mekanismer är det viktigt att förstå vilka inställningar som gäller och snabbt kunna distribuera en server med en identisk konfiguration. Med den här funktionen kan du katalogisera konfigurationen av en specifik synkroniseringstjänst och importera inställningarna till en ny distribution. Olika ögonblicks bilder av synkroniseringsinställningar kan jämföras för att enkelt visualisera skillnaderna mellan två servrar, eller samma server över tid. 

Varje gången som konfigurationen ändras från Azure AD Connects guiden exporteras en ny tids stämplings fil med JSON-inställningar automatiskt till **%programdata%\AADConnect**. Inställnings fil namnet har formatet **tillämpad-SynchronizationPolicy-*. JSON** där den sista delen av fil namnet är en tidsstämpel. 

> [!IMPORTANT]
> Endast ändringar som görs av Azure AD Connect exporteras automatiskt. Alla ändringar som görs med PowerShell, Synchronization Service Manager eller redigeraren för formateringsregler måste exporteras vid behov för att underhålla en uppdaterad kopia. Export på begäran kan också användas för att placera en kopia av inställningarna på en säker plats för haveri beredskap. 

## <a name="exporting-azure-ad-connect-settings"></a>Exportera Azure AD Connect inställningar 

Om du vill visa en sammanfattning av konfigurations inställningarna öppnar du Azure AD Connect-verktyget och väljer den ytterligare uppgiften med namnet: Visa eller exportera aktuell konfiguration. En snabb sammanfattning av dina inställningar visas tillsammans med möjligheten att exportera den fullständiga konfigurationen av servern. 

Som standard exporteras inställningarna till **%programdata%\AADConnect**, men du kan välja att spara inställningarna på en skyddad plats för att säkerställa tillgängligheten i händelse av en katastrof. Inställningarna exporteras med JSON-filformat och ska inte skapas eller redige ras för att säkerställa logisk konsekvens. Import av handskapade eller redigerade filer stöds inte och kan leda till oväntade resultat. 

## <a name="importing-azure-ad-connect-settings"></a>Importerar Azure AD Connect inställningar 

Om du vill importera tidigare exporterade inställningar gör du följande:
 
1. Installera **Azure AD Connect** på en ny server. 
2. Välj **Anpassa** alternativ efter **välkomst** sidan. 
3. Klicka på **Importera synkroniseringsinställningar**. Bläddra till filen med tidigare exporterade JSON-inställningar.  
4. Klicka på **Installera**.

![Installera komponenter](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Åsidosätt inställningarna på den här sidan, t. ex. användning av SQL Server i stället för LocalDB eller användning av befintligt tjänst konto i stället för standard-VSA osv. De här inställningarna importeras inte från konfigurations inställnings filen, men det finns information och jämförelse.

### <a name="import-installation-experience"></a>Importera installations miljö 

Att importera installations upplevelsen är avsiktligt förenklat med minimala indata från användaren för att enkelt tillhandahålla reproducerbarhet för en befintlig server.  

Här är de enda ändringar som kan göras under installationen. Alla andra ändringar kan göras efter installationen från guiden Azure AD Connect.: 
- **Azure Active Directory autentiseringsuppgifter**   – konto namnet för den globala Azure-administratören som används för att konfigurera den ursprungliga servern föreslås som standard och **måste**   ändras om du vill synkronisera information till en ny katalog.
- **Användar inloggning**   – de inloggnings alternativ som kon figurer ATS för den ursprungliga servern väljs som standard och kommer automatiskt att fråga efter autentiseringsuppgifter eller annan information som behövs under konfigurationen. I sällsynta fall kan det finnas behov av att konfigurera en server med olika alternativ för att undvika att ändra beteendet för den aktiva servern. Annars trycker du på Nästa för att använda samma inställningar. 
- Autentiseringsuppgifter för lokal **katalog**   – för varje lokal katalog som ingår i dina synkroniseringsinställningar måste du ange autentiseringsuppgifter för att skapa ett konto för synkronisering eller ange ett förskapat anpassat synkroniseringsschema. Den här proceduren är identisk med den rena installations upplevelsen med undantaget du kan inte lägga till eller ta bort kataloger. 
- **Konfigurations alternativ**   – precis som med en ren installation kan du välja att konfigurera de ursprungliga inställningarna för om du vill starta automatisk synkronisering eller aktivera mellanlagrings läge. Den största skillnaden är att mellanlagrings läget är avsiktligt aktiverat som standard för att tillåta jämförelse av konfigurations-och synkroniseringsresultat innan du aktivt exporterar resultaten till Azure. 

![Anslut kataloger](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> Endast en synkroniseringstjänst kan vara i den primära rollen och aktivt exportera konfigurations ändringar till Azure. Alla andra servrar måste placeras i mellanlagrings läge. 

## <a name="migrating-settings-from-an-existing-server"></a>Migrera inställningar från en befintlig server 

Om en befintlig server inte stöder hantering av inställningar kan du antingen välja att uppgradera servern på plats eller migrera inställningarna för användning på en ny mellanlagrings Server.  

Vid migreringen krävs ett PowerShell-skript som extraherar de befintliga inställningarna för användning i en ny installation.Den här metoden rekommenderas för att katalogisera inställningarna för den befintliga servern och tillämpa dem på en nyinstallerad mellanlagrings Server.Om du jämför inställningarna för den ursprungliga servern med den nyligen skapade servern kan du snabbt visualisera ändringarna mellan servrarna.Som alltid följer du organisationens certifierings process för att se till att ingen ytterligare konfiguration krävs.  

### <a name="migration-process"></a>Migreringsprocessen 
Om du vill migrera inställningarna gör du följande:

1. Starta **AzureADConnect.msi** på den nya Staging-servern och stoppa på Välkomst sidan för Azure AD Connect.

2. Kopiera **MigrateSettings.ps1** från katalogen Microsoft Azure AD Connect\Tools till en plats på den befintliga servern.  Till exempel C:\setup.  Där installations programmet är en katalog som skapades på den befintliga servern.

   ![Anslut kataloger](media/how-to-connect-import-export-config/migrate1.png)

3. Kör skriptet som det visas nedan och spara hela Server konfigurations katalogen för äldre versioner. Kopiera den här katalogen till den nya Staging-servern. Observera att du måste kopiera hela den **exporterade-ServerConfiguration-*-** mappen till den nya servern.

   ![Anslut kataloger ](media/how-to-connect-import-export-config/migrate2.png)
    ![ Anslut kataloger](media/how-to-connect-import-export-config/migrate3.png)

5. Starta **Azure AD Connect** genom att dubbelklicka på ikonen på Skriv bordet. Godkänn EULA, på nästa sida klickar du på knappen **Anpassa** . 
6. Markera kryss rutan **Importera synkroniseringsinställningar** och klicka på knappen **Bläddra** för att bläddra i mappen kopierad över exporterad-ServerConfiguration-* och välj MigratedPolicy.jspå för att importera de migrerade inställningarna.

   ![Anslut kataloger](media/how-to-connect-import-export-config/migrate4.png)

7. Om du vill jämföra de migrerade inställningarna med den tillämpade inställningen, letar du efter den senaste **migrerade-SynchronizationPolicy-*. JSON** och **applicerad-SynchronizationPolicy-*. JSON** (* är tidstämpeln) under **%programdata%\AADConnect**. Använd ditt favorit verktyg för fil jämförelse för att jämföra pariteten. 

## <a name="post-installation-verification"></a>Efter verifiering av installationen 

Att jämföra den ursprungliga importerade inställnings filen med den exporterade inställnings filen, är av den nyligen distribuerade servern ett viktigt steg i att förstå eventuella skillnader mellan den avsedda, jämfört med den resulterande distributionen. Genom att använda ditt favorit program sida-vid-sida-jämförelse ger du en direkt visualisering som snabbt visar alla önskade ändringar eller oavsiktliga ändringar. Även om många tidigare manuella konfigurations steg nu har eliminerats bör du fortfarande följa organisationens certifierings process för att se till att ingen ytterligare konfiguration krävs. Den här konfigurationen kan inträffa om du använder avancerade inställningar, som för närvarande inte samlas in i den offentliga för hands versionen av inställnings hantering. 

Kända begränsningar omfattar följande: 
- **Regler**   för synkronisering – prioriteten för en anpassad regel måste vara inom det reserverade intervallet 0-99 för att undvika konflikter med Microsofts standard regler. Att placera en anpassad regel utanför det reserverade intervallet kan leda till att din anpassade regel flyttas runt när standard reglerna läggs till i konfigurationen. Ett liknande problem kan uppstå om konfigurationen innehåller ändrade standard regler. Att ändra en standard regel rekommenderas inte och regel placeringen är förmodligen felaktig. 
- **Tillbakaskrivning**   av enhet – de här inställningarna är katalogiserade men de används inte för närvarande under konfigurationen. Om tillbakaskrivning av enheten har Aktiver ATS för den ursprungliga servern måste du konfigurera funktionen manuellt på den nya distribuerade servern. 
- **Synkroniserade objekt typer**   – även om det är möjligt att begränsa listan över synkroniserade objekt typer (användare, kontakter, grupper osv.) med hjälp av Synchronization Service Manager, stöds den här funktionen för närvarande inte via synkroniseringsinställningar. När installationen är klar måste du manuellt tillämpa den avancerade konfigurationen. 
- **Anpassade körnings profiler**   – även om det är möjligt att ändra standard uppsättningen körnings profiler med hjälp av Synchronization Service Manager, stöds den här funktionen för närvarande inte via synkroniseringsinställningar. När installationen är klar måste du manuellt tillämpa den avancerade konfigurationen. 
- **Konfigurera etablerings hierarkin**   – den här avancerade funktionen i Synchronization Service Manager stöds inte via synkroniseringsinställningar och måste konfigureras manuellt när du har slutfört den första distributionen. 
- **AD FS-och PingFederate-autentisering**   – de inloggnings metoder som är kopplade till dessa autentiseringsmetoder kommer att väljas automatiskt, men du måste ange alla andra nödvändiga konfigurations parametrar interaktivt. 
- **En inaktive rad anpassad Synkroniseringsregel importeras som aktive rad** En inaktive rad anpassad Synkroniseringsregel importeras som aktive rad. Se till att inaktivera det även på den nya servern.

 ## <a name="next-steps"></a>Nästa steg

- [Maskin vara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Anpassade inställningar](how-to-connect-install-custom.md)
- [Installera Azure AD Connect Health-agenter](how-to-connect-health-agent-install.md) 
