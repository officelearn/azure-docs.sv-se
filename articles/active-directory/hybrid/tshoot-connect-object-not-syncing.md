---
title: Felsök ett objekt som inte synkroniseras med Azure AD | Microsoft Docs
description: Felsöka anledningen till ett objekt inte synkroniseras till Azure AD.
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
ms.openlocfilehash: 55668b8ef8019e1ee808bc0cba9d98c0db53c584
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198748"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Felsök ett objekt som inte synkroniseras med Azure AD

Om ett objekt inte synkroniseras som förväntat till Azure AD, kan det bero på flera orsaker. Om du har fått ett e-postmeddelande med fel från Azure AD eller om du ser felet i Azure AD Connect Health, läs sedan [felsöka export](tshoot-connect-sync-errors.md) i stället. Men om du felsöker ett problem där objektet inte är i Azure AD, sedan det här avsnittet är för dig. Den beskriver hur du hittar fel i den lokala komponenten Azure AD Connect-synkroniseringen.

>[!IMPORTANT]
>För Azure Active Directory (AAD) ansluta distribution med version 1.1.749.0 eller högre, använda den [felsökning uppgift](tshoot-connect-objectsync.md) i guiden för att felsöka problem med synkronisering av objektet. 

## <a name="synchronization-process"></a>Processen för synkronisering

Innan man har undersökt synkroniseringsproblem, låt oss se på **Azure AD Connect** synkroniseringsprocessen:

  ![Azure AD Connect-synkronisering](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologi**

* **CS:** En tabell i databasen i anslutningsplatsen.
* **MV:** Metaversum, en tabell i databasen.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Synkroniseringssteg**
Synkroniseringsprocessen omfattar följande steg:

1. **Importera från AD:** **Active Directory** objekt förs in **AD CS**.

2. **Importera från AAD:** **Azure Active Directory** objekt förs in **AAD CS**.

3. **Synkronisering:** **Inkommande Synkroniseringsregler** och **utgående Synkroniseringsregler** körs i den ordning som siffran från lägre till högre. Om du vill visa de Synkroniseringsregler som du kan gå till **Synchronization Rules Editor** från program. Den **inkommande Synkroniseringsregler** ger i data från CS MV. Den **utgående Synkroniseringsregler** flyttar data från MV till CS.

4. **Exportera till AD:** När synkronisering har körts kan exporteras objekt från AD CS att **Active Directory**.

5. **Exportera till AAD:** När du har kört synkronisering objekt exporteras från AAD CS till **Azure Active Directory**.

## <a name="troubleshooting"></a>Felsökning

Om du vill hitta fel som ska du titta på några olika platser i följande ordning:

1. Den [åtgärdsloggar](#operations) för att söka efter fel som identifierats av Synkroniseringsmotorn under import och synkronisering.
2. Den [anslutarplatsen](#connector-space-object-properties) för att söka efter objekt som saknas och synkroniseringsfel.
3. Den [metaversum](#metaverse-object-properties) för att söka efter data som är relaterade problem.

Starta [hanteraren för synkroniseringstjänsten](how-to-connect-sync-service-manager-ui.md) innan du påbörjar de här stegen.

## <a name="operations"></a>Åtgärder
Fliken åtgärder i hanteraren för synkroniseringstjänsten är var du ska börja felsökningen. Fliken åtgärder visas resultatet från de senaste åtgärderna.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/operations.png)  

Den övre delen visas alla körningar i kronologisk ordning. Som standard logga åtgärderna är fortfarande information om de senaste sju dagarna, men den här inställningen kan ändras med den [scheduler](how-to-connect-sync-feature-scheduler.md). Du vill söka efter alla körningar som inte visar statusen lyckades. Du kan ändra sorteringen genom att klicka på rubrikerna.

Den **Status** kolumnen är den viktigaste informationen och visar det mest allvarliga problemet för en körning. Här är en snabb sammanfattning av de vanligaste statusvärdena i prioritetsordning att undersöka (där * indikera flera möjliga felsträngar).

| Status | Kommentar |
| --- | --- |
| stopped-* |Det gick inte att slutföra körningen. Exempel: om fjärrdatorn är igång och kan inte kontaktas. |
| stopped-error-limit |Det finns fler än 5 000 fel. Körningen stoppades automatiskt på grund av det stora antalet fel. |
| slutförda -\*-fel |Körningen slutfördes, men det finns fel (färre än 5 000) som bör undersökas. |
| completed-\*-warnings |Körningen har slutförts, men vissa data är inte i förväntat tillstånd. Om du har fel sedan är det här meddelandet vanligtvis bara ett tecken. Du bör inte undersöka varningar förrän du har åtgärdat felen. |
| lyckades |Inga problem. |

När du har valt en rad uppdaterar längst ned för att visa information om som körs. Du kan ha en lista som säger att längst till vänster på nedersta, **steg #**. Den här listan visas bara om du har flera domäner i skogen där varje domän representeras av ett steg. Domännamnet finns under rubriken **Partition**. Under **Synkroniseringsstatistik**, du kan hitta mer information om antalet ändringar som har bearbetats. Du kan klicka på länkarna för att få en lista med ändrade objekt. Om du har objekt med fel felen som visas **synkroniseringsfel**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Felsöka fel i operations-fliken
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
När du har fel är båda objektet i fel och den själva länkar som innehåller mer information.

Starta genom att klicka på felsträngen (**sync-regel-fel-funktion-utlöst** i bilden). Först visas en översikt över objektet. Klicka på knappen om du vill se faktiska felet **stackspårning**. Den här spårningen innehåller felsökningsinformation för nivån för felet.

Du kan högerklicka på i den **anropa Stackinformation** väljer **Markera alla**, och **kopiera**. Du kan sedan kopiera stacken och titta på felet i din favoritredigerare, till exempel Anteckningar.

* Om felet är från **SyncRulesEngine**, och sedan informationen för anropsstacken först har en lista över alla attribut för objektet. Rulla nedåt tills du ser rubriken **InnerException = >**.  
  ![Sync Service Manager](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)  
  Rad efter visar felet. Felet är från en anpassad synkronisering regeln Fabrikam som skapats i bilden ovan.

Om felet själva inte ger tillräckligt med information, är det dags att titta på själva informationen. Du kan klicka på länken med objekt-ID och fortsätta felsökning i [connector utrymme importerade objektet](#cs-import).

## <a name="connector-space-object-properties"></a>Objektegenskaper för utrymme
Om du inte har några fel har påträffats i den [operations](#operations) fliken och sedan på nästa steg är att följa anslutarplatsen från Active Directory till metaversum och till Azure AD. Du bör hitta var problemet finns på den här sökvägen.

### <a name="search-for-an-object-in-the-cs"></a>Sök efter ett objekt i CS

I **hanteraren för synkroniseringstjänsten**, klickar du på **Anslutningsappar**, Välj den Active Directory-koppling och **Search Connector Space**.

I **omfång**väljer **RDN** (när du vill söka på attributet CN) eller **DN eller fästpunkt** (när du vill söka på attributet distinguishedName). Ange ett värde och klickar på **Search**.  
![Söka Anslutarplats](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Om du inte hittar objektet du letar efter så den kan filtreras med [domänbaserade filtreringen](how-to-connect-sync-configure-filtering.md#domain-based-filtering) eller [OU-baserad filtrering](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Läs den [filtreringen](how-to-connect-sync-configure-filtering.md) avsnittet om du vill kontrollera att filtrering är konfigurerad som förväntat.

En annan användbar sökning är att välja Azure AD-koppling i **omfång** Välj **väntande Import**, och välj den **Lägg till** kryssrutan. Den här sökningen ger dig alla synkroniserade objekt i Azure AD som inte kan associeras med ett lokalt-objekt.  
![Connector utrymme search rader](./media/tshoot-connect-object-not-syncing/cssearchorphan.png)  
Dessa objekt har skapats av en annan Synkroniseringsmotorn eller en Synkroniseringsmotorn med en annan konfiguration för filtrering. Den här vyn visas en lista över **rader** objekt som inte längre hanteras. Du bör granska den här listan och Överväg att ta bort dessa objekt med hjälp av den [Azure AD PowerShell](https://aka.ms/aadposh) cmdletar.

### <a name="cs-import"></a>CS-Import
När du öppnar ett cs-objekt, finns det flera flikar högst upp. Den **importera** fliken visar data som mellanlagras efter en import.  
![CS-objekt](./media/tshoot-connect-object-not-syncing/csobject.png)    
Den **Gammalt värde** visar vad för närvarande som lagras i Connect och **nytt värde** vad har tagits emot från källsystemet och har inte tillämpats. Om det finns ett fel på objektet, bearbetas inte ändringar.

**Fel**  
![CS-objekt](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  
Den **synkroniseringsfel** fliken visas endast om det finns ett problem med objektet. Mer information finns i [Felsök synkroniseringsfel](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>CS härkomst
Fliken härkomst visar hur anslutarplatsen som är kopplad till metaversumobjekt. Du kan se när anslutningen senast importerade en ändring från det anslutna systemet och vilka regler som används för att fylla i data i metaversum.  
![CS härkomst](./media/tshoot-connect-object-not-syncing/cslineage.png)  
I den **åtgärd** kolumn, du kan se det finns en sådan **inkommande** synkroniseringsregel med åtgärden **etablera**. Värde som anger att så länge som den här anslutarplatsen finns, förblir metaversumobjekt. Om listan över Synkroniseringsregler i stället visas en synkroniseringsregel riktning **utgående** och **etablera**, betyder det att det här objektet tas bort när metaversumobjekt tas bort.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/cslineageout.png)  
Du kan också se i den **PasswordSync** kolumn som kan bidra med inkommande anslutningsplatsen ändras till lösenordet eftersom en synkroniseringsregel har värdet **SANT**. Det här lösenordet skickas sedan till Azure AD via en utgående regel.

Från fliken härkomst du kommer till metaversum genom att klicka på [Metaversumobjektegenskaperna](#mv-attributes).

Finns två knappar längst ned på alla flikar: **Förhandsversion av** och **Log**.

### <a name="preview"></a>Förhandsversion
Förhandsgranskningssidan används för att synkronisera ett enda objekt. Det är användbart om du felsöker vissa anpassade Synkroniseringsregler och vill se effekten av en ändring på ett enda objekt. Du kan välja mellan **Full sync** och **Deltasynkronisering**. Du kan också välja mellan **Generera förhandsgranskning**, vilket upprätthåller bara ändringen i minnet, och **genomför förhandsversion**, som uppdateras metaversum och skapar etapper alla ändringar i mål-kopplingens utrymmen.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/preview.png)  
Du kan granska objektet och vilken regel tillämpas för en viss attributflöde.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Logga
Sidan Log används för att se status för synkronisering av lösenord och historik. Mer information finns i [Felsök synkronisering av lösenordshash](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Egenskaper för Metaversumobjekt
Det är vanligtvis bättre att börja söka från källan Active Directory-anslutarplatsen. Men du kan också starta söka från metaversum.

### <a name="search-for-an-object-in-the-mv"></a>Sök efter ett objekt i MV
I **hanteraren för synkroniseringstjänsten**, klickar du på **Metaversumsökning**. Skapa en fråga som du vet hittar användaren. Du kan söka efter vanliga attribut, till exempel accountName (SAM) eller userPrincipalName. Mer information finns i [metaversumsökning](how-to-connect-sync-service-manager-ui-mvsearch.md).
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

I den **sökresultat** fönstret klickar du på objektet.

Om du inte gick att hitta objektet, har sedan den ännu inte nått metaversum. Fortsätta att söka efter objektet i den **Active Directory** [anslutarplatsen](#connector-space-object-properties). Om du hittar objekt i den **Active Directory** anslutningsplatsen sedan det uppstå ett fel i synkronisering som blockerar objektet från kommer till metaversum eller så kanske en synkroniseringsregel Omfångsfilter tillämpas.

### <a name="object-not-found-in-the-mv"></a>Objektet hittades inte i MV
Om objektet är i den **Active Directory** CS, men finns inte i MV och sedan Omfångsfilter tillämpas. 

* För att titta på Omfångsfilter går du till menyn skrivbordsprogram och klicka på **Synchronization Rules Editor**. Filtrera reglerna som gäller objektet genom att justera i filtreringen nedan.

  ![Inkommande synkronisering regler sökning](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

* Visa varje regel i listan ovan och kontrollera den **Scoping filter**. I den nedan Omfångsfilter om den **isCriticalSystemObject** värdet är null eller FALSKT eller tomt så är det i omfånget.

  ![Inkommande synkronisering regler sökning](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

* Gå till den [CS Import](#cs-import) attributet lista och kontrollera vilka filter blockerar objektet för att flytta till MV. Det sig den **Anslutarplatsen** attributlistan visas bara attribut för icke-null och inte är tom. Till exempel om **isCriticalSystemObject** inte visas i listan och sedan det innebär att värdet för det här attributet är null eller tomt.

### <a name="object-not-found-in-the-aad-cs"></a>Objektet hittades inte i AAD-CS
Om objektet inte finns i den **Anslutarplatsen** av **Azure Active Directory**. Men den finns i MV, titta sedan på Scoping filtret för den **utgående** reglerna för motsvarande **Anslutarplatsen** och kontrollera om objektet är filtrerats bort eftersom den [MV attribut](#mv-attributes) uppfyller inte kriterierna.

* Om du vill titta på utgående Omfångsfilter, väljer du de tillämpliga reglerna för objektet genom att justera i filtreringen nedan. Visa varje regel och titta på motsvarande [MV-attributet](#mv-attributes) värde.

  ![Sök efter utgående Synchroniztion-regler](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV-attribut
Du kan se värdena och vilken anslutning som tillförts det på fliken attribut.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Om ett objekt inte synkroniseras, titta sedan på följande attribut i metaversum:
- Är attributet **cloudFiltered** presentera och inställd **SANT**? Om det är så den har filtrerats enligt anvisningarna i [filtrering baserad på attributet](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Är attributet **sourceAnchor** finns? Om inte, har du en konto-resurs skogstopologi? Om ett objekt identifieras som en länkad postlåda (attributet **msExchRecipientTypeDetails** har värdet 2), och sedan sourceAnchor är som tillförts av skogen med ett aktiverat Active Directory-konto. Kontrollera att huvudkontot har importerats och synkroniseras korrekt. Huvudkontot måste anges i den [anslutningsappar](#mv-connectors) för objektet.

### <a name="mv-connectors"></a>MV-kopplingar
Fliken för anslutningar visas alla kopplingens utrymmen som har en representation av-objektet.  
![Sync Service Manager](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  
Du bör ha en anslutning till:

- Varje Active Directory-skog användaren visas i. Detta kan inkludera foreignSecurityPrincipals och kontaktobjekt.
- En anslutning i Azure AD.

Om du saknar anslutning till Azure AD, läs sedan [MV attribut](#mv-attributes) att verifiera villkoren för håller på att etableras till Azure AD.

Den här fliken kan du gå till den [anslutarplatsen](#connector-space-object-properties). Välj en rad och klicka på **egenskaper**.

## <a name="next-steps"></a>Nästa steg
- [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md).
- [Vad är hybrididentitet? ](whatis-hybrid-identity.md).
