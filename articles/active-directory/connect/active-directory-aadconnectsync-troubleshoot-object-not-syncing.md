---
title: "Felsökning av ett objekt som inte synkroniseras med Azure AD | Microsoft Docs"
description: "Felsöka anledningen till ett objekt inte synkroniseras med Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: e68b70ce87a6fedab1b85bf2800a50e512910dea
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Felsökning av ett objekt som inte synkroniseras med Azure AD

Om ett objekt inte synkroniseras som förväntat till Azure AD, kan det vara på grund av flera skäl. Om du har fått ett e-postmeddelande med fel från Azure AD eller om du ser felet i Azure AD Connect Health, Läs [felsöka export](active-directory-aadconnect-troubleshoot-sync-errors.md) i stället. Men om du felsöker ett problem där objektet inte är i Azure AD, sedan det här avsnittet för dig. Det beskriver hur du söka efter fel i Azure AD Connect-synkronisering lokalt komponent.

>[!IMPORTANT]
>För Azure Active Directory (AAD) Anslut distribution med version <verison> eller högre, Använd den [felsökning uppgiften](active-directory-aadconnect-troubleshoot-objectsync.md) i guiden för att felsöka objekt synkroniseringsproblem. 

Om du vill hitta felen ska du titta på några olika platser i följande ordning:

1. Den [åtgärdsloggar](#operations) för att söka efter fel som identifierats av Synkroniseringsmotorn under import och synkronisering.
2. Den [anslutarplats](#connector-space-object-properties) för att söka efter objekt som saknas och synkroniseringsfel.
3. Den [metaversum](#metaverse-object-properties) för att söka efter data som är relaterade problem.

Starta [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) innan du påbörjar de här stegen.

## <a name="operations"></a>Åtgärder
Fliken åtgärder i hanteraren för synkroniseringstjänsten är där du ska starta felsökningen. Fliken åtgärder visar resultaten från de senaste åtgärderna.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/operations.png)  

Den övre delen visas alla körs i kronologisk ordning. Som standard åtgärderna logga behåller information om de senaste sju dagarna, men den här inställningen kan ändras med den [scheduler](active-directory-aadconnectsync-feature-scheduler.md). Du vill söka efter alla kör som inte visar statusen lyckades. Du kan ändra sortering genom att klicka på rubrikerna.

Den **Status** kolumnen är den viktigaste informationen och visar de svåraste problemet för en körning. Här är en kort sammanfattning av de vanligaste status i prioritetsordning att undersöka (där * ange flera möjliga felsträngar).

| Status | Kommentar |
| --- | --- |
| stopped-* |Kör kunde inte slutföras. Till exempel om fjärrdatorn är igång och kan inte kontaktas. |
| stopped-error-limit |Det finns fler än 5 000 fel. Kör har automatiskt stoppats på grund av det stora antalet fel. |
| slutförda -\*-fel |Kör slutfördes, men det finns fel (färre än 5 000) som bör undersökas. |
| slutförda -\*-varningar |Kör slutfördes, men vissa data är inte i det förväntade tillståndet. Om du har fel sedan är det här meddelandet vanligtvis bara ett symtom. Du bör inte undersöka varningar förrän du har åtgärdat felen. |
| lyckades |Inga problem. |

När du har valt en rad uppdateras längst ned om du vill visa detaljer för som kör. Du kanske har en lista som säger att längst till vänster i längst ned, **steg #**. Den här listan visas bara om du har flera domäner i skogen där varje domän som representeras av ett steg. Domännamnet finns under rubriken **Partition**. Under **Synkroniseringsstatistik**, du kan hitta mer information om antalet ändringar som har bearbetats. Du kan klicka på länkarna för att hämta en lista över de ändrade objekt. Om du har objekt med fel felen visas **synkroniseringsfel**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Felsöka i fliken åtgärder
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorsync.png)  
När du har fel är båda objektet i fel och den själva länkar som ger mer information.

Starta genom att klicka på felsträngen (**sync regeln-fel-funktionen-utlöst** i bilden). Först visas en översikt över objektet. Klicka på knappen för att se de faktiska fel **stackspårning**. Den här spårningen ger debug nivån information om felet.

Du kan högerklicka på den **information för anropsstacken** väljer **Markera alla**, och **kopiera**. Du kan kopiera stacken och titta på fel i din favorit redigerare, t.ex Anteckningar.

* Om felet är från **SyncRulesEngine**, och sedan Stackinformation anropet har först en lista över alla attribut för objektet. Bläddra nedåt tills du ser rubriken **InnerException = >**.  
  ![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorinnerexception.png)  
  Rad efter visar felet. Felet är från en anpassad Sync regeln Fabrikam som skapats i bilden ovan.

Om felet själva inte ger tillräckligt med information, är det dags att titta på själva informationen. Du kan klicka på länken med objekt-ID och fortsätta felsökningen av [connector utrymme importerade objektet](#cs-import).

## <a name="connector-space-object-properties"></a>Objektegenskaper för anslutarplats
Om du inte har några fel har påträffats i den [operations](#operations) fliken, då är nästa steg att följa utrymme kopplingsobjektet från Active Directory metaversum och Azure AD. I den här sökvägen bör du hitta var problemet finns.

### <a name="search-for-an-object-in-the-cs"></a>Söka efter ett objekt i CS

I **Synchronization Service Manager**, klickar du på **kopplingar**, Välj den Active Directory-koppling och **söka Anslutarplats**.

I **omfång**väljer **RDN** (när du vill söka på attributet CN) eller **DN eller fästpunkt** (när du vill söka i distinguishedName attribut). Ange ett värde och klicka på **Sök**.  
![Söka Anslutarplats](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearch.png)  

Om inte att hitta objektet du letar efter, så den kan ha filtrerats med [domänbaserade filtreringen](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) eller [OU-baserade filtrering](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering). Läs den [filtreringen](active-directory-aadconnectsync-configure-filtering.md) avsnittet om du vill verifiera att filtrering är konfigurerad som förväntat.

En annan användbar sökning är att välja Azure AD-koppling i **omfång** Välj **väntande Import**, och välj den **Lägg till** kryssrutan. Den här sökningen ger alla synkroniserade objekt i Azure AD kan inte kopplas till ett lokalt objekt.  
![Kopplingen utrymme Sök rader](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearchorphan.png)  
Dessa objekt har skapats av en annan Synkroniseringsmotorn eller en Synkroniseringsmotorn med en annan filtrering konfiguration. Den här vyn visas en lista över **rader** objekt som inte längre hanteras. Du bör granska listan och ta bort dessa objekt med hjälp av den [Azure AD PowerShell](https://aka.ms/aadposh) cmdlets.

### <a name="cs-import"></a>CS-Import
När du öppnar ett cs-objekt, finns det flera flikar högst upp. Den **importera** visar de data som mellanlagras efter en import.  
![CS-objekt](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/csobject.png)    
Den **Gammalt värde** visar vad för närvarande lagras i Anslut och **nytt värde** vad har tagits emot från källsystemet och har inte tillämpats ännu. Om det finns ett fel i objektet, bearbetas inte ändringar.

**Fel**  
![CS-objekt](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssyncerror.png)  
Den **synkroniseringsfel** visas endast om det är problem med objektet. Mer information finns i [felsöka synkroniseringsfel](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>CS härkomst
Fliken härkomst visar hur utrymme kopplingsobjektet är relaterad till metaversum-objekt. Du kan se när anslutningen importeras senast en ändring från det anslutna systemet och vilka regler som används för att fylla i data i metaversum.  
![CS härkomst](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineage.png)  
I den **åtgärd** kolumn och, du kan se en **inkommande** synkroniseringsregel med åtgärden **etablera**. Värde som anger så länge objektet connector utrymme finns kvar metaversum-objekt. Om listan över regler för synkronisering i stället visas en synkroniseringsregel med riktning **utgående** och **etablera**, indikerar det att objektet tas bort när metaversumobjekt tas bort.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineageout.png)  
Du kan också se i den **PasswordSync** som inkommande anslutningsplatsen kan bidra ändras till lösenordet eftersom en synkroniseringsregel har värdet **SANT**. Det här lösenordet skickas sedan till Azure AD via den utgående regeln.

På fliken härkomst kan du få till metaversum genom att klicka på [metaversum objektegenskaper](#mv-attributes).

Längst ned i alla flikar finns två knappar: **Preview** och **loggen**.

### <a name="preview"></a>Förhandsversion
Sidan för förhandsgranskning används för att synkronisera ett enda objekt. Det är användbart om du felsöker vissa regler för synkronisering av anpassade och vill se effekten av en ändring i ett enda objekt. Du kan välja mellan **fullständig synkronisering** och **Deltasynkronisering**. Du kan också välja mellan **Generera förhandsgranskning**, som endast behålls ändringen i minnet, och **genomför Preview**, som uppdateras metaversum och skapar etapper alla ändringar till mål-kopplingens utrymmen.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/preview.png)  
Du kan inspektera objektet och vilken regel tillämpas för en viss attributflöde.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/previewresult.png)

### <a name="log"></a>Logga
Sidan logg används för att se status för synkronisering av lösenord och historik. Mer information finns i [Felsöka Lösenordssynkronisering](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## <a name="metaverse-object-properties"></a>Objektet metaversumegenskaper
Det är oftast bättre att börja sökningen från Active Directory-källan [anslutarplats](#connector-space). Men du kan också starta sökningen från metaversum.

### <a name="search-for-an-object-in-the-mv"></a>Sök efter ett objekt i MV
I **Synchronization Service Manager**, klickar du på **metaversumsökningen**. Skapa en fråga som du vet hittar användaren. Du kan söka efter vanliga attribut, till exempel accountName (SAM) och userPrincipalName. Mer information finns i [metaversumsökningen](active-directory-aadconnectsync-service-manager-ui-mvsearch.md).
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvsearch.png)  

I den **sökresultat** fönstret klickar du på objektet.

Om du inte kan hitta objektet, har sedan den ännu inte nått metaversum. Fortsätter att söka efter objekt i Active Directory [anslutarplats](#connector-space-object-properties). Det kan finnas ett fel i synkronisering som blockerar objektet från kommer för metaversum eller det kan finnas ett filter.

### <a name="mv-attributes"></a>MV-attribut
Du kan se värdena och vilka Connector bidragit den på fliken attribut.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvobject.png)  

Om ett objekt inte synkroniserar titta på följande attribut i metaversum:
- Är attributet **cloudFiltered** finns och ange **SANT**? Om det är sedan det har filtrerats enligt stegen i [attributet baserat filtrering](active-directory-aadconnectsync-configure-filtering.md#attribute-based-filtering).
- Är attributet **sourceAnchor** finns? Om inte, har du ett konto-resurs skogstopologi? Om ett objekt som identifieras som en länkad postlåda (attributet **msExchRecipientTypeDetails** har värdet 2), och sedan sourceAnchor bidragit med skogen med ett aktiverat Active Directory-konto. Kontrollera att kontot master har importerats och synkroniseras korrekt. Huvudkontot måste anges i den [kopplingar](#mv-connectors) för objektet.

### <a name="mv-connectors"></a>MV-kopplingar
Fliken kopplingar visar alla kopplingens utrymmen som har en representation av objektet.  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvconnectors.png)  
Du bör ha en koppling till:

- Varje Active Directory-skog användaren representeras i. Detta kan inkludera foreignSecurityPrincipals och kontaktobjekt.
- En koppling i Azure AD.

Om du saknar anslutningen till Azure AD läser [MV attribut](#MV-attributes) Kontrollera villkoren för tillhandahålls till Azure AD.

Den här fliken kan du gå till den [utrymme kopplingsobjektet](#connector-space-object-properties). Välj en rad och klicka på **egenskaper**.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
