---
title: Felsöka ett objekt som inte synkroniseras med Azure Active Directory | Microsoft Dokument"
description: Felsöka ett objekt som inte synkroniseras med Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 931865803328189d89c0fbae15caa801c3f7f7c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253538"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Felsöka ett objekt som inte synkroniseras med Azure Active Directory

Om ett objekt inte synkroniseras som förväntat med Microsoft Azure Active Directory (Azure AD) kan det bero på flera orsaker. Om du har fått ett felmeddelande e-postmeddelande från Azure AD eller om du ser felet i Azure AD Connect Health läser [du Felsökningsfel under synkroniseringen](tshoot-connect-sync-errors.md) i stället. Men om du felsöker ett problem där objektet inte finns i Azure AD, är den här artikeln för dig. Den beskriver hur du hittar fel i den lokala komponenten Azure AD Connect-synkronisering.

>[!IMPORTANT]
>För Azure AD Connect-distribution med version 1.1.749.0 eller senare använder du [felsökningsuppgiften](tshoot-connect-objectsync.md) i guiden för att felsöka problem med objektsynkronisering. 

## <a name="synchronization-process"></a>Synkroniseringsprocess

Innan vi undersöker synkroniseringsproblem ska vi förstå synkroniseringsprocessen för Azure AD Connect:

  ![Diagram över synkroniseringsprocessen för Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologi**

* **CS:** Kopplingsutrymme, en tabell i en databas
* **MV:** Metaverse, en tabell i en databas

### <a name="synchronization-steps"></a>**Synkroniseringssteg**
Synkroniseringsprocessen omfattar följande steg:

1. **Importera från AD:** Active Directory-objekt förs in i Active Directory CS.

2. **Importera från Azure AD:** Azure AD-objekt förs in i Azure AD CS.

3. **Synkronisering:** Inkommande synkroniseringsregler och utgående synkroniseringsregler körs i prioritetsordning, från lägre till högre. Om du vill visa synkroniseringsreglerna går du till Redigeraren för synkroniseringsregler från skrivbordsprogrammen. Reglerna för inkommande synkronisering tar in data från CS till MV. De utgående synkroniseringsreglerna flyttar data från MV till CS.

4. **Exportera till AD:** När objekten har synkroniserats exporteras de från Active Directory CS till Active Directory.

5. **Exportera till Azure AD:** Efter synkronisering exporteras objekt från Azure AD CS till Azure AD.

## <a name="troubleshooting"></a>Felsökning

Om du vill hitta felen tittar du på några olika platser i följande ordning:

1. [Åtgärden loggar](#operations) för att hitta fel som identifierats av synkroniseringsmotorn under import och synkronisering.
2. [Kopplingsutrymmet](#connector-space-object-properties) för att hitta saknade objekt och synkroniseringsfel.
3. [Metaversumet](#metaverse-object-properties) för att hitta datarelaterade problem.

Starta [Synkroniseringstjänsthanteraren](how-to-connect-sync-service-manager-ui.md) innan du påbörjar de här stegen.

## <a name="operations"></a>Åtgärder
Fliken **Åtgärder** i Synkroniseringstjänsthanteraren är där du ska starta felsökningen. På den här fliken visas resultaten från de senaste åtgärderna. 

![Skärmbild av Synkroniseringstjänsthanteraren, som visar fliken Operationer markerat](./media/tshoot-connect-object-not-syncing/operations.png)  

Den övre halvan av fliken **Operationer** visar alla körningar i kronologisk ordning. Som standard behåller verksamhetsloggen information om de senaste sju dagarna, men den här inställningen kan ändras med [schemaläggaren](how-to-connect-sync-feature-scheduler.md). Leta efter en körning som inte visar en **framgångsstatus.** Du kan ändra sorteringen genom att klicka på rubrikerna.

Kolumnen **Status** innehåller den viktigaste informationen och visar det allvarligaste problemet för en körning. Här är en snabb sammanfattning av de vanligaste statusarna i ordning efter undersökningsprioritet (där * anger flera möjliga felsträngar).

| Status | Kommentar |
| --- | --- |
| stoppad-* |Körningen kunde inte avslutas. Detta kan till exempel inträffa om fjärrsystemet är nere och inte kan kontaktas. |
| stoppad-fel-gräns |Det finns mer än 5000 fel. Körningen stoppades automatiskt på grund av det stora antalet fel. |
| slutfört-\*-fel |Körningen avslutades, men det finns fel (färre än 5 000) som ska undersökas. |
| -varningar\* |Körningen avslutades, men vissa data är inte i förväntat tillstånd. Om du har fel är det här meddelandet vanligtvis bara ett symptom. Undersök inte varningar förrän du har åtgärdat fel. |
| lyckades |Inga problem. |

När du väljer en rad uppdateras längst ned på fliken **Operationer** för att visa information om körningen. Längst till vänster i det här området kan du ha en lista med namnet **Steg #**. Den här listan visas bara om du har flera domäner i skogen och varje domän representeras av ett steg. Domännamnet finns under rubriken **Partition**. Under rubriken **Synkroniseringsstatistik** hittar du mer information om antalet ändringar som har bearbetats. Markera länkarna för att få en lista över de ändrade objekten. Om du har objekt med fel visas dessa fel under rubriken **Synkroniseringsfel.**

### <a name="errors-on-the-operations-tab"></a>Fel på fliken Operationer
När du har fel visar Synkroniseringstjänsthanteraren både objektet av misstag och själva felet som länkar som ger mer information.

![Skärmbild av fel i Synkroniseringstjänsthanteraren](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Börja med att välja felsträngen. (I föregående bild är felsträngen **sync-rule-error-function-triggered**.) Du får först en översikt över objektet. Om du vill se det faktiska felet väljer du **Stackspårning**. Den här spårningen innehåller felsökningsnivåinformation för felet.

Högerklicka på rutan **Ring stackinformation,** klicka på **Markera alla**och välj sedan **Kopiera**. Kopiera sedan stacken och titta på felet i din favoritredigerare, till exempel Anteckningar.

Om felet är från **SyncRulesEngine**visas först alla attribut på objektet i anropsstaplsinformationen. Bläddra nedåt tills rubriken **InnerException =>**.  

  ![Skärmbild av Synkroniseringstjänsthanteraren som visar felinformation under rubriken InnerException =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
Raden efter rubriken visar felet. I föregående bild kommer felet från en anpassad synkroniseringsregel som Fabrikam skapade.

Om felet inte ger tillräckligt med information är det dags att titta på själva data. Markera länken med objektidentifieraren och fortsätt felsöka det importerade objektet för [anslutningsutrymme](#cs-import).

## <a name="connector-space-object-properties"></a>Objektegenskaper för anslutarplats
Om fliken [**Åtgärder**](#operations) inte visar några fel följer du anslutningsutrymmesobjektet från Active Directory till metaversum till Azure AD. I den här sökvägen bör du hitta var problemet finns.

### <a name="searching-for-an-object-in-the-cs"></a>Söka efter ett objekt i CS

I Synkroniseringstjänsthanteraren väljer du **Kopplingar,** markerar Active Directory Connector och väljer **Sök kopplingsutrymme**.

I rutan **Omfattning** väljer du **RDN** när du vill söka på CN-attributet eller väljer **DN eller ankare** när du vill söka på **attributet distinguishedName.** Ange ett värde och välj **Sök**. 
 
![Skärmbild av en anslutningsutrymmessökning](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Om du inte hittar det objekt du letar efter kan det ha filtrerats med [domänbaserad filtrering](how-to-connect-sync-configure-filtering.md#domain-based-filtering) eller [OU-baserad filtrering](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Om du vill kontrollera att filtreringen är konfigurerad som förväntat läser du [Azure AD Connect-synkronisering: Konfigurera filtrering](how-to-connect-sync-configure-filtering.md).

Du kan utföra en annan användbar sökning genom att välja Azure AD Connector. Markera **Väntande import**i rutan **Omfattning** och markera sedan kryssrutan **Lägg till.** Den här sökningen ger dig alla synkroniserade objekt i Azure AD som inte kan associeras med ett lokalt objekt.  

![Skärmbild av överblivna i en anslutningsutrymmessökning](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Dessa objekt skapades av en annan synkroniseringsmotor eller en synkroniseringsmotor med en annan filtreringskonfiguration. Dessa överblivna objekt hanteras inte längre. Granska den här listan och överväg att ta bort dessa objekt med hjälp av Azure AD PowerShell-cmdletar. [Azure AD PowerShell](https://aka.ms/aadposh)

### <a name="cs-import"></a>CS-import
När du öppnar ett CS-objekt finns det flera flikar högst upp. På fliken **Importera** visas de data som mellanlagras efter en import.  

![Skärmbild av fönstret Egenskaper för kopplingsutrymme objekt, med fliken Importera markerad](./media/tshoot-connect-object-not-syncing/csobject.png)    

Kolumnen **Gammalt värde** visar vad som för närvarande lagras i Anslut och kolumnen **Nytt värde** visar vad som har tagits emot från källsystemet och har inte tillämpats ännu. Om det finns ett fel på objektet bearbetas inte ändringarna.

Fliken **Synkroniseringsfel** visas bara i fönstret Egenskaper för **anslutningsutrymmeobjekt** om det är problem med objektet. Mer information finns i [felsÃ¶kning av synkroniseringsfel på fliken **Åtgärder** ](#errors-on-the-operations-tab).

![Skärmbild av fliken Synkroniseringsfel i fönstret Egenskaper för anslutningsutrymmeobjekt](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS-härstamning
Fliken **Härstamning** i fönstret Egenskaper för **kopplingsutrymmeobjekt** visar hur kopplingsutrymmets objekt är relaterat till metaversumobjektet. Du kan se när kopplingen senast importerade en ändring från det anslutna systemet och vilka regler som tillämpas för att fylla i data i metaversumet.  

![Skärmbild som visar fliken Härstamning i fönstret Egenskaper för objekt i kopplingsutrymme](./media/tshoot-connect-object-not-syncing/cslineage.png)  

I föregående bild visar kolumnen **Åtgärd** en inkommande synkroniseringsregel med åtgärden **Etablera**. Det indikerar att så länge det här anslutningsutrymmet finns kvarstår metaversumobjektet. Om listan över synkroniseringsregler i stället visar en utgående synkroniseringsregel med en **etableringsåtgärd** tas det här objektet bort när metaversumobjektet tas bort.  

![Skärmbild av ett härstamningsfönster på fliken Härstamning i fönstret Egenskaper för kopplingsutrymmeobjekt](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

I föregående bild kan du också se i kolumnen **PasswordSync** att det inkommande anslutningsutrymmet kan bidra med ändringar i lösenordet eftersom en synkroniseringsregel har värdet **True**. Det här lösenordet skickas till Azure AD via regeln för utgående.

På fliken **Härstamning** kan du komma åt metaversumet genom att välja [**Egenskaper för Metaversumobjekt**](#mv-attributes).

### <a name="preview"></a>Förhandsversion
I det nedre vänstra hörnet i fönstret Egenskaper för **anslutningsutrymmeobjekt** finns **förhandsgranskningsknappen.** Välj den här knappen om du vill öppna **sidan Förhandsgranska,** där du kan synkronisera ett enda objekt. Den här sidan är användbar om du felsöker vissa anpassade synkroniseringsregler och vill se effekten av en ändring på ett enda objekt. Du kan välja en **fullständig synkronisering** eller en **Delta-synkronisering**. Du kan också välja **Generera förhandsgranskning**, som bara behåller ändringen i minnet. Eller välj **Commit Preview**, som uppdaterar metaversum och faser alla ändringar i målkopplingsutrymmen.  

![Skärmbild av sidan Förhandsgranska, med Startförhandsgranskning markerad](./media/tshoot-connect-object-not-syncing/preview.png)  

I förhandsgranskningen kan du inspektera objektet och se vilken regel som tillämpas för ett visst attributflöde.  

![Skärmbild av sidan Förhandsgranska med importattributflöde](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Logga
Bredvid knappen **Förhandsgranska** väljer du **knappen Logga** för att öppna **loggsidan.** Här kan du se status och historik för lösenordssynkronisering. Mer information finns i [Felsöka synkronisering av lösenordshÃ¶kning med Azure AD Connect-synkronisering](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Egenskaper för metaversumobjekt
Det är oftast bättre att börja söka från källan Active Directory-anslutningsutrymme. Men du kan också börja söka från metaversumet.

### <a name="searching-for-an-object-in-the-mv"></a>Söka efter ett objekt i MV
Välj **Metaverse Search**i Synkroniseringstjänsthanteraren , som i följande bild. Skapa en fråga som du vet hittar användaren. Sök efter vanliga attribut, till exempel **accountName** (**sAMAccountName**) och **userPrincipalName**. Mer information finns i [Metaverse-sökning i Synkroniseringshanteraren.](how-to-connect-sync-service-manager-ui-mvsearch.md)

![Skärmbild av Synkroniseringstjänsthanteraren, med fliken Sök i metaversum markerat](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Klicka på objektet i fönstret **Sökresultat.**

Om du inte hittade objektet har det ännu inte nått metaversumet. Fortsätt att söka efter objektet i Active [Directory-anslutningsutrymmet](#connector-space-object-properties). Om du hittar objektet i Active Directory-anslutningsutrymmet kan det finnas ett synkroniseringsfel som blockerar objektet från att komma till metaversumet, eller så kan ett filtreringsfilter för synkroniseringsregeln tillämpas.

### <a name="object-not-found-in-the-mv"></a>Objektet hittades inte i MV
Om objektet finns i Active Directory CS men inte finns i MV används ett omfångsfilter. Om du vill titta på omfångsfiltret går du till programmenyn för skrivbordet och väljer **Redigerare för synkroniseringsregler**. Filtrera de regler som gäller för objektet genom att justera filtret nedan.

  ![Skärmbild av Redigeraren för synkroniseringsregler, som visar en inkommande synkroniseringsreglerssökning](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Visa varje regel i listan ovanifrån och kontrollera **filtret Omfång .** Om värdet **isCriticalSystemObject** är null eller FALSKT eller tomt i följande omfångsfilter, är det i omfånget.

  ![Skärmbild av ett omfångsfilter i en inkommande synkroniseringsregelsökning](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Gå till attributlistan [cs import](#cs-import) och kontrollera vilket filter som blockerar objektet från att flytta till MV. Attributlistan **för Anslutningsutrymme** visar endast attribut som inte är null och icke-tomma. Om **isCriticalSystemObject** till exempel inte visas i listan är värdet för det här attributet null eller tomt.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Objektet hittades inte i Azure AD CS
Om objektet inte finns i anslutningsutrymmet i Azure AD men finns i MV, titta på omfångsfiltret för de utgående reglerna för motsvarande anslutningsutrymme och ta reda på om objektet filtreras bort eftersom [MV-attributen](#mv-attributes) inte uppfyller villkoren.

Om du vill titta på det utgående omfångsfiltret markerar du tillämpliga regler för objektet genom att justera filtret nedan. Visa varje regel och titta på motsvarande [MV-attributvärde.](#mv-attributes)

  ![Skärmbild av en utgående synkroniseringsregler söker i Redigeraren för synkroniseringsregler](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV-attribut
På fliken **Attribut** kan du se värdena och vilka kopplingar som bidrog med dem.  

![Skärmbild av fönstret Egenskaper för metaversumobjekt, med fliken Attribut markerat](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Om ett objekt inte synkroniseras ställer du följande frågor om attributtillstånd i metaversumet:
- Är **attributet cloudFiltered** närvarande och inställt på **Sant?** Om så är fallet har den filtrerats enligt stegen i [attributbaserad filtrering](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Finns **attributet sourceAnchor?** Om inte, har du en kontoresursskogstopologi? Om ett objekt identifieras som en länkad postlåda (attributet **msExchRecipientTypeDetails** har värdet **2)** bidrar **källanAnchor** av skogen med ett aktiverat Active Directory-konto. Kontrollera att huvudkontot har importerats och synkroniserats korrekt. Huvudkontot måste anges bland [kopplingarna](#mv-connectors) för objektet.

### <a name="mv-connectors"></a>MV-kontakter
På fliken Kopplingar visas alla **kopplingsutrymmen** som har en representation av objektet. 
 
![Skärmbild av fönstret Egenskaper för metaversumobjekt, med fliken Kopplingar markerat](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Du bör ha en koppling till:

- Varje Active Directory-skog som användaren finns representerad i. Den här representationen kan omfatta **foreignSecurityPrincipals** och **Contact-objekt.**
- En anslutningsapp i Azure AD.

Om du saknar kopplingen till Azure AD läser du avsnittet om [MV-attribut](#mv-attributes) för att verifiera villkoren för etablering till Azure AD.

På fliken Kontakter kan du också gå till [anslutningsutrymmet](#connector-space-object-properties). **Connectors** Markera en rad och klicka på **Egenskaper**.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md).
- Läs mer om [hybrididentitet](whatis-hybrid-identity.md).
