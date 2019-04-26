---
title: Felsök ett objekt som inte synkroniseras med Azure Active Directory | Microsoft Docs
description: Felsök ett objekt som inte synkroniseras med Azure Active Directory.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455238"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Felsök ett objekt som inte synkroniseras med Azure Active Directory

Om ett objekt inte synkroniserar som förväntat med Microsoft Azure Active Directory (AD Azure), kan det vara på grund av flera skäl. Om du har fått ett e-postmeddelande med fel från Azure AD eller om du ser felet i Azure AD Connect Health, läsa [Felsök fel under synkronisering](tshoot-connect-sync-errors.md) i stället. Men om du felsöker ett problem där objektet inte är i Azure AD, den här artikeln rätt för dig. Den beskriver hur du hittar fel i lokalt komponenten Azure AD Connect-synkronisering.

>[!IMPORTANT]
>För Azure AD ansluta distribution med version 1.1.749.0 eller högre, använda den [felsökning uppgift](tshoot-connect-objectsync.md) i guiden för att felsöka objekt synkroniserar problem. 

## <a name="synchronization-process"></a>Processen för synkronisering

Innan vi undersöka synkroniserar problem, låt oss se Azure AD Connect synkroniserar processen:

  ![Diagram över Azure AD Connect-synkroniseringen](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologi**

* **CS:** Anslutarplats, en tabell i en databas
* **MV:** Metaversum, en tabell i en databas

### <a name="synchronization-steps"></a>**Synkroniseringssteg**
Synkroniserar processen omfattar följande steg:

1. **Importera från AD:** Active Directory-objekt förs in Active Directory-CS.

2. **Import från Azure AD:** Azure AD-objekt överförs till Azure AD CS.

3. **Synkronisering:** Regler för inkommande synkronisering och regler för utgående synkronisering körs i den ordning som siffran från lägre till högre. Om du vill visa synkroniseringsreglerna som går du till Synchronization Rules Editor från program. Regler för inkommande synkronisering importera data från CS till MV. Regler för utgående synkronisering flytta data från MV till CS.

4. **Exportera till AD:** När synkroniseringen, exporteras objekt från Active Directory-CS till Active Directory.

5. **Exportera till Azure AD:** När synkroniseringen, exporteras objekt från Azure AD CS till Azure AD.

## <a name="troubleshooting"></a>Felsökning

För att hitta felen, titta på några olika platser i följande ordning:

1. Den [åtgärdsloggar](#operations) att hitta fel som identifierats av Synkroniseringsmotorn under import och synkronisering.
2. Den [anslutarplatsen](#connector-space-object-properties) att hitta objekt som saknas och synkroniseringsfel.
3. Den [metaversum](#metaverse-object-properties) att hitta data som är relaterade problem.

Starta [hanteraren för synkroniseringstjänsten](how-to-connect-sync-service-manager-ui.md) innan du påbörjar de här stegen.

## <a name="operations"></a>Åtgärder
Den **Operations** fliken i hanteraren för synkroniseringstjänsten är var du ska börja felsökningen. Den här fliken visas resultaten från de senaste åtgärderna. 

![Skärmbild av Synchronization Service Manager, som visar fliken för åtgärder som valts](./media/tshoot-connect-object-not-syncing/operations.png)  

Den övre delen av den **Operations** fliken visas alla körningar i kronologisk ordning. Som standard logga åtgärderna är fortfarande information om de senaste sju dagarna, men den här inställningen kan ändras med den [scheduler](how-to-connect-sync-feature-scheduler.md). Utseende för något kör som visar inte en **lyckades** status. Du kan ändra sorteringen genom att klicka på rubrikerna.

Den **Status** kolumn som innehåller den viktigaste informationen och visar det mest allvarliga problemet för en körning. Här är en snabb sammanfattning av de vanligaste statusvärdena i undersökningen prioritetsordning (där * anger flera möjliga felsträngar).

| Status | Kommentar |
| --- | --- |
| stopped-* |Kör kunde inte slutföras. Detta kan inträffa, till exempel om fjärrsystemet är igång och kan inte kontaktas. |
| stopped-error-limit |Det finns fler än 5 000 fel. Körningen stoppades automatiskt på grund av det stora antalet fel. |
| slutförda -\*-fel |Körningen är klar, men det finns fel (färre än 5 000) som bör undersökas. |
| completed-\*-warnings |Körningen är klar, men vissa data är inte i det förväntade tillståndet. Om du har fel kan det här meddelandet är vanligtvis bara ett tecken. Inte Undersök varningar förrän du har åtgärdat felen. |
| lyckades |Inga problem. |

När du väljer en rad, längst ned på den **Operations** fliken uppdateras för att visa information om den körningen. Du kan ha en lista med titeln på längst till vänster sida av det här området, **steg #**. Den här listan visas bara om du har flera domäner i skogen och domänerna representeras av ett steg. Domännamnet finns under rubriken **Partition**. Under den **Synkroniseringsstatistik** rubrik, kan du hitta mer information om antalet ändringar som har bearbetats. Välj länken för att hämta en lista med ändrade objekt. Om du har objekt med fel felen som visas den **synkroniseringsfel** rubrik.

### <a name="errors-on-the-operations-tab"></a>Fel på fliken åtgärder
När du har fel visas hanteraren för synkroniseringstjänsten både objektet i fel och själva felet som länkar som innehåller mer information.

![Skärmbild av fel i hanteraren för synkroniseringstjänsten](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Starta genom att välja felsträngen. (I bilden ovan är felsträngen **sync-regel-fel-funktion-utlöst**.) Först visas en översikt över objektet. Om du vill se faktiska felet **stackspårning**. Den här spårningen innehåller felsökningsnivå information om felet.

Högerklicka på den **anropa Stackinformation** klickar du på **Markera alla**, och välj sedan **kopiera**. Kopiera stacken och titta på fel i din favoritredigerare, till exempel Anteckningar.

Om felet är från **SyncRulesEngine**, stack samtalsinformation först visar en lista över alla attribut för objektet. Rulla nedåt tills du ser rubriken **InnerException = >**.  

  ![Skärmbild av hanteraren för synkroniseringstjänsten, som visar information om fel under rubriken InnerException = >](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
Rad efter rubriken visas felet. Felet är från en anpassad synkroniseringsregel som Fabrikam skapade i föregående bild.

Om felet inte ger tillräckligt med information, är det dags att titta på själva informationen. Klicka på länken med objekt-ID och fortsätta felsökningen i [connector utrymme importerade objektet](#cs-import).

## <a name="connector-space-object-properties"></a>Objektegenskaper för utrymme
Om den [ **Operations** ](#operations) fliken visar några fel, följ anslutarplatsen från Active Directory till metaversum till Azure AD. Du bör hitta var problemet finns på den här sökvägen.

### <a name="searching-for-an-object-in-the-cs"></a>Söker efter ett objekt i CS

I hanteraren för synkroniseringstjänsten, Välj **Anslutningsappar**, Välj den Active Directory-koppling och välj **Search Connector Space**.

I den **omfång** väljer **RDN** när du vill söka på CN-attributet eller välj **DN eller fästpunkt** när du vill söka på den **distinguishedName**  attribut. Ange ett värde och välj **Search**. 
 
![Skärmbild av en söka anslutarplats](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Om du inte hittar objektet du söker efter den kan filtreras med [domänbaserade filtreringen](how-to-connect-sync-configure-filtering.md#domain-based-filtering) eller [OU-baserad filtrering](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Kontrollera att filtrering är konfigurerad som förväntat genom att läsa [Azure AD Connect-synkronisering: Konfigurera filtrering](how-to-connect-sync-configure-filtering.md).

Du kan utföra en annan användbar sökning genom att välja Azure AD Connector. I den **omfång** väljer **väntande Import**, och välj sedan den **Lägg till** markerar du kryssrutan. Den här sökningen ger dig alla synkroniserade objekt i Azure AD som inte kan associeras med ett lokalt-objekt.  

![Skärmbild av rader i en söka anslutarplats](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Dessa objekt har skapats av en annan Synkroniseringsmotorn eller en Synkroniseringsmotorn med en annan konfiguration för filtrering. Dessa överblivna objekt hanteras inte längre. Granska listan och Överväg att ta bort dessa objekt med hjälp av den [Azure AD PowerShell](https://aka.ms/aadposh) cmdletar.

### <a name="cs-import"></a>CS-import
När du öppnar ett CS-objekt, finns det flera flikar högst upp. Den **importera** fliken visar data som mellanlagras efter en import.  

![Skärmbild av fönstret objektegenskaper för utrymmet med fliken importera valt](./media/tshoot-connect-object-not-syncing/csobject.png)    

Den **Gammalt värde** kolumnen visar det för närvarande lagras i Connect, och **nytt värde** kolumnen visar vad som har tagits emot från källsystemet och har inte tillämpats. Om det finns ett fel på objektet, bearbetas inte ändringar.

Den **synkroniseringsfel** mallfliken visas i den **objektegenskaper för utrymme** fönstret endast om det finns ett problem med objektet. Mer information hur du [Felsök synkroniseringsfel på den **Operations** fliken](#errors-on-the-operations-tab).

![Skärmbild av fliken synkroniseringsfel i fönstret objektegenskaper för utrymme](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS härkomst
Den **härkomst** fliken i den **objektegenskaper för utrymme** visar hur anslutarplatsen som är kopplad till metaversumobjekt. Du kan se när anslutningen senast importeras en ändring från det anslutna systemet och vilka regler som används för att fylla i data i metaversum.  

![Skärmbild som visar fliken härkomst i fönstret objektegenskaper för utrymme](./media/tshoot-connect-object-not-syncing/cslineage.png)  

På bilden ovan den **åtgärd** kolumnen visar en regel för inkommande synkronisering med åtgärden **etablera**. Värde som anger att så länge som den här anslutarplatsen finns, förblir metaversumobjekt. Om listan över Synkroniseringsregler i stället visas en utgående synkroniseringsregel med en **etablera** åtgärd, det här objektet tas bort när metaversumobjekt tas bort.  

![Skärmbild av ett härkomst fönster på fliken härkomst i fönstret objektegenskaper för utrymme](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

I föregående bild kan du också se i den **PasswordSync** kolumn som kan bidra med inkommande anslutningsplatsen ändras till lösenordet eftersom en regel för synkronisering har värdet **SANT**. Det här lösenordet skickas till Azure AD via en utgående regel.

Från den **härkomst** fliken du kommer till metaversum genom att välja [ **Metaversumobjektegenskaperna**](#mv-attributes).

### <a name="preview"></a>Förhandsversion
I det nedre vänstra hörnet av den **objektegenskaper för utrymme** fönstret är den **förhandsversion** knappen. Välj den här knappen för att öppna den **förhandsversion** sidan där du kan synkronisera ett enda objekt. Den här sidan är användbart om du felsöker vissa anpassade Synkroniseringsregler och vill se effekten av en ändring på ett enda objekt. Du kan välja en **Full sync** eller en **Deltasynkronisering**. Du kan också välja **Generera förhandsgranskning**, vilket upprätthåller bara ändringen i minnet. Eller välj **genomför förhandsversion**, vilket uppdaterar metaversum och skapar etapper alla ändringar i mål-kopplingens utrymmen.  

![Skärmbild av sidan för förhandsgranskning med starta förhandsgranskning som valts](./media/tshoot-connect-object-not-syncing/preview.png)  

I förhandsversionen kan du granska objektet och se vilken regel som tillämpas för en viss attributflöde.  

![Skärmbild av sidan för förhandsversionen som visar Import av attributflöde](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Logga
Bredvid den **förhandsversion** knapp, väljer den **Log** knappen för att öppna den **Log** sidan. Här kan du se status för synkronisering av lösenord och historik. Mer information finns i [felsöka lösenordshashsynkronisering med Azure AD Connect-synkronisering](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Egenskaper för Metaversumobjekt
Det är vanligtvis bättre att börja söka från källan Active Directory-anslutarplatsen. Men du kan också starta söka från metaversum.

### <a name="searching-for-an-object-in-the-mv"></a>Söker efter ett objekt i MV
I hanteraren för synkroniseringstjänsten, Välj **Metaversumsökning**, som i följande bild. Skapa en fråga som du vet hittar användaren. Sök efter vanliga attribut, till exempel **accountName** (**sAMAccountName**) och **userPrincipalName**. Mer information finns i [Sync Service Manager Metaverse search](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Skärmbild av Synchronization Service Manager, med fliken Metaversumsökning markerad](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

I den **sökresultat** fönstret klickar du på objektet.

Om du inte gick att hitta objektet, har det ännu inte nått metaversum. Fortsätta att söka efter objekt i Active Directory [anslutarplatsen](#connector-space-object-properties). Om du hittar objektet i Active Directory-anslutarplatsen det kan vara ett synkroniseringsfel som blockerar objektet från kommer till metaversum eller ett Omfångsfilter för synkronisering-regel kan användas.

### <a name="object-not-found-in-the-mv"></a>Objektet hittades inte i MV
Om objektet är i Active Directory-CS men finns inte i MV, ett Omfångsfilter tillämpas. Om du vill titta på Omfångsfilter, gå till menyn skrivbordsprogram och välj **Synchronization Rules Editor**. Filtrera regler för objektet genom att justera i filtreringen nedan.

  ![Skärmbild av Synchronization Rules Editor, som visar en sökning för regler för inkommande synkronisering](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Visa varje regel i listan ovan och kontrollera den **Scoping filter**. I följande Omfångsfilter, om den **isCriticalSystemObject** värdet är null eller FALSKT eller tomt, är det i omfånget.

  ![Skärmbild av ett Omfångsfilter i en regel för inkommande synkronisering-sökning](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Gå till den [CS Import](#cs-import) attributet lista och kontrollera vilka filter blockerar objekt från att flyttas till MV. Den **Anslutarplatsen** attributlistan visas bara attribut för icke-null och inte är tom. Till exempel om **isCriticalSystemObject** visas inte i listan med värdet för det här attributet är null eller tomt.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Objektet hittades inte i Azure AD CS
Om objektet finns inte i anslutarplatsen för Azure AD men finns i MV, titta på Omfångsfilter om utgående regler för motsvarande anslutningsplatsen och ta reda på om objektet är filtrerats bort eftersom den [MV attribut](#mv-attributes)inte uppfyller villkoren.

Om du vill titta på utgående Omfångsfilter, väljer du de tillämpliga reglerna för objektet genom att justera i filtreringen nedan. Visa varje regel och titta på motsvarande [MV-attributet](#mv-attributes) värde.

  ![Skärmbild av en utgående synkronisering regler sökning i Synchronization Rules Editor](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV-attribut
På den **attribut** fliken kan du se värdena och vilka kopplingar som tillförts dem.  

![Skärmbild av fliken attribut som valts i fönstret Egenskaper för Metaversumobjekt](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Om ett objekt inte synkroniserar du ställa följande frågor om attributet tillstånd i metaversum:
- Är attributet **cloudFiltered** presentera och inställd **SANT**? Om det är den har filtrerats enligt anvisningarna i [attributet-baserad filtrering](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Är attributet **sourceAnchor** finns? Om inte, har du en konto-resurs skogstopologi? Om ett objekt identifieras som en länkad postlåda (attributet **msExchRecipientTypeDetails** har värdet **2**), **sourceAnchor** datavetenskapscommunityn skog med en Aktivera Active Directory-konto. Kontrollera att huvudkontot har importerats och synkroniserats korrekt. Huvudkontot måste anges mellan den [anslutningsappar](#mv-connectors) för objektet.

### <a name="mv-connectors"></a>MV-kopplingar
Den **kopplingar** fliken visar alla kopplingens utrymmen som har en representation av-objektet. 
 
![Skärmbild av fönstret Egenskaper för Metaversumobjekt med fliken för anslutningar valt](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Du bör ha en anslutning till:

- Varje Active Directory-skog användaren visas i. Detta kan inkludera **foreignSecurityPrincipals** och **Kontakta** objekt.
- En anslutning i Azure AD.

Om du saknar anslutning till Azure AD, Läs avsnittet på [MV attribut](#mv-attributes) att verifiera villkoren för etablering i Azure AD.

Från den **Anslutningsappar** fliken som du kan även gå till den [anslutarplatsen](#connector-space-object-properties). Välj en rad och klicka på **egenskaper**.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md).
- Läs mer om [hybrididentitet](whatis-hybrid-identity.md).
