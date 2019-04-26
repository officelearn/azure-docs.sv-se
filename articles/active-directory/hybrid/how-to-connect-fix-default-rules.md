---
title: Hur du åtgärdar ändrade standardregler – Azure AD Connect | Microsoft Docs
description: Lär dig hur du åtgärdar ändrade standardregler som medföljer Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 761f3e6e72319a2e63d6b66f2893130ec5a82ebf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60353100"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Åtgärda ändrade standardregler i Azure AD Connect

Azure AD Connect levereras med standardregler för synkronisering.  Tyvärr dessa regler gäller inte universellt för alla organisationer och det kan finnas tillfällen, baserat på dina krav när du behöver ändra dem.

 Om du har ändrat standardreglerna eller planerar att ändra dem kan sedan ta dig tid att läsa det här dokumentet.

Det här dokumentet presenteras 2 exempel vanligaste anpassningar som görs av användare och förklarar det korrekta sättet att uppnå dessa anpassningar.

>[!NOTE] 
> Ändra befintlig standard regler för att uppnå en anpassning som behövs finns inte stöd - vis slipper du uppdaterar de här reglerna till den senaste versionen i framtida versioner. Det innebär att du hämtar nödvändiga felkorrigeringar och nya funktioner.  Det här dokumentet förklarar hur du uppnå samma resultat utan att ändra de befintliga standardreglerna. 

## <a name="how-to-identify-modified-default-rules"></a>Så här identifierar du ändrade standardregler?
Från och med version 1.3.7.0 av **Azure AD Connect**, är det nu lätt att identifiera ändrade Standardregeln. Du kan gå till appar på skrivbordet och klicka på **Synchronization Rules Editor**.

![Redigeraren](media/how-to-connect-fix-default-rules/default1.png)

I redigeraren, ska alla ändrade standardreglerna visas med en ikon framför namnet som visas nedan:

![storleken på ikoner](media/how-to-connect-fix-default-rules/default2.png)

 Även visas en inaktiverad regel med samma namn som är standard Standardregeln:

![Standardregler](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Vanliga anpassningar
Följande är vanliga anpassningar till standardreglerna:

- [Ändra attributflöde](#changing-attribute-flow)
- [Ändra Omfångsfilter](#changing-scoping-filter)
- [Ändra kopplingsvillkoret](#changing-join-condition)

## <a name="before-changing-any-rules"></a>Innan du ändrar några regler
- Inaktivera sync scheduler.  Scheduler körs var 30: e minut som standard. Kontrollera att den inte startar när du gör ändringar och felsöka din nya regler. För att tillfälligt inaktivera scheduler, starta PowerShell och kör `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Standardregler](media/how-to-connect-fix-default-rules/default3.png)

- Ändringen av Omfångsfilter kan resultera i borttagning av objekt i målkatalogen. Var försiktig innan du gör några ändringar i omfånget för objekt. Vi rekommenderar att du gör ändringar i en mellanlagringsserver innan du gör ändringar på den aktiva servern.
- Kör en förhandsversion på ett enda objekt som vi nämnde i [Validera Synkroniseringsregel](#validate-sync-rule) avsnittet när du lägger till en ny regel.
- Kör en fullständig synkronisering när du lägger till en ny regel eller ändrat eventuella anpassade synkroniseringsregel. Den här synkroniseringen gäller nya regler för alla objekt.

## <a name="changing-attribute-flow"></a>Ändra attributflöde
Det finns 3 olika scenarier för attributflödet, ska vi titta på hur du uppnår den utan att ändra standard standardregler.
- Lägger till nytt attribut
- Åsidosätta värdet för befintligt attribut
- Synkronisera inte befintligt attribut

### <a name="adding-new-attribute"></a>Lägga till nya attribut:
Om du upptäcker att ett attribut inte flödar från källkatalogen till målkatalogen så kan du använda den [Azure AD Connect-synkronisering: Katalogtillägg](how-to-connect-sync-feature-directory-extensions.md) flöda nya attribut.

Observera att ditt förstahandsval bör vara att använda [Azure AD Connect-synkronisering: Katalogtillägg](how-to-connect-sync-feature-directory-extensions.md), en utanför rutan funktionen tillhandahålls av Azure AD Connect. Om det inte fungerar för dig sedan gå igenom följande steg för att flow ett attribut utan att ändra befintliga standard synkronisering standardregel, kan du dock göra detta genom att lägga till två nya Synkroniseringsregler för.


#### <a name="add-an-inbound-sync-rule"></a>Lägg till en regel för inkommande synkronisering:
Inkommande synkroniseringsregel innebär källan för attributet är en anslutarplats och målet är metaversum. Till exempel för att flöda ett nytt attribut från en lokal Active Directory till Azure Active Directory, skapa en ny regel för inkommande synkronisering genom att starta den **Synchronization Rule Editor**, välj sedan riktning som **inkommande** och klicka på **Lägg till ny regel**. 

 ![Standardregler](media/how-to-connect-fix-default-rules/default3a.png)

Följer din egen namngivningskonvention för att nämna regeln, här vi använde **anpassade i från AD - användare**, det innebär att regeln är en anpassad regel och en regel för inkommande från AD-anslutningsappens utrymme till metaversum.   

 ![Standardregler](media/how-to-connect-fix-default-rules/default3b.png)

Ge dina egna beskrivning av regeln så att framtida underhåll av regeln är enkelt, t.ex. Vad är syftet med den här regeln och varför det behövdes.
Välj ett anslutna System (skog) - attributet källan. Välj objekttyp för anslutna System och metaversum-objekttyp.

Ange prioritetsvärde mellan 0 – 99 (lägre nummer, högre prioritet). Håll andra fält som ”tagg”, ”aktivera Lösenordssynkronisering' och inaktiverad som standard.

Håll ”Scoping Filtrera” tom, det innebär att regeln gäller för alla objekt som är anslutna mellan AD-anslutna System och metaversum.

Behåll tom ”ansluta till regler, vilket innebär att den här regeln kommer andra datorn på det kopplingsvillkoret som definierats i standard Standardregeln. Det här är en annan anledning inte att inaktivera/ta bort Standardregeln för standard eftersom om det finns inga kopplingsvillkoret andra datorn sedan attributet inte flödar. 

Lägg till lämpliga omvandling för dina attribut, du kan tilldela konstant flöda ett konstant värde till din målattribut eller dirigera mappningen mellan käll- eller attributet eller ett uttryck för attributet. Här följer olika [uttryck](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) du kan använda.

#### <a name="add-an-outbound-sync-rule"></a>Lägg till en utgående synkroniseringsregel:
Så här långt genom att lägga till bara en regel för inkommande synkronisering har vi gjort halva arbetet eftersom attributet inte är ännu har länkats till målkatalogen. För att länka attributet till mål-director måste du skapa en utgående regel, vilket innebär att källan är metaversum och målet är anslutna system. Om du vill skapa en utgående regel starta **Synchronization Rule Editor**, ändra den **riktning** till **utgående** och klicka på **Lägg till ny regel**. 

![Standardregler](media/how-to-connect-fix-default-rules/default3c.png)

Som i den inkommande regeln, du kan använda din egen namnkonvention som **namn** regeln. Välj den **anslutna System** väljer anslutna system-objektet som du vill att ange ett attributvärde Azure AD-klient. Ange prioritet mellan 0 - 99. 

![Standardregler](media/how-to-connect-fix-default-rules/default3d.png)

Behåll **Scoping filter** tomt, håll **ansluta regler** tomma, Fyll i transformeringen som konstant, direkt eller uttryck. 

I det här exemplet har vi lärt dig att flöda nytt attribut för användarobjekt från Active Directory till Azure Active Directory. Du kan använda de här stegen för att mappa attribut från objekt källa och mål.  Mer information finns i [skapar anpassade Synkroniseringsregler](how-to-connect-create-custom-sync-rule.md) och [förberedelser för att etablera användare](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="overriding-value-of-existing-attribute"></a>Åsidosätta värdet för befintligt attribut
Det är möjligt att du vill åsidosätta värdet för redan mappat attribut, till exempel du alltid vill du ange Null-värde till ett attribut i Azure AD, du kan göra detta genom att skapa bara en inkommande regel som anges i föregående steg och flow  **AuthoritativeNull** konstant värde för målattributet. Observera att vi har använt AuthoritativeNulll i stället för Null i det här fallet. Det beror på att icke-nullvärde ersätter Null-värde, även om den har lägre prioritet (högre numeriskt värde i regeln). Men AuthoritativeNull behandlas som Null och inte kommer att ersättas med icke-nullvärde av andra regler. 

### <a name="dont-sync-existing-attribute"></a>Synkronisera inte befintligt attribut
Om du vill exkludera ett attribut från att synkronisera kan du använda attributfiltrering-funktionen i Azure AD Connect. Starta **Azure AD Connect** skrivbordsikonen och välj sedan **anpassa synkroniseringsalternativ**.

![Standardregler](media/how-to-connect-fix-default-rules/default4.png)

 Se till att **Azure AD-app och attributfiltrering** är markerad och klicka på **nästa**.

![Standardregler](media/how-to-connect-fix-default-rules/default5.png)

Avmarkera de attribut som du vill undanta från att synkronisera.

![Standardregler](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>Ändra Omfångsfilter
Azure AD-synkronisering tar hand om de flesta av objekt, kan du minska omfånget för objekt och minska det färre objekt som ska exporteras på ett sätt som stöds utan att ändra standard standardregler för synkronisering. Om du vill öka omfånget för objekt och sedan kan du **redigera** den befintliga regeln klona den och inaktivera den ursprungliga regeln. Microsoft rekommenderar att du inte öka området som konfigurerats av Azure AD Connect. Ökning i omfattningen av objekt gör det svårt för supportteamet att förstå anpassningarna och stöd för produkten.

Här är hur du kan minska omfånget för objekt som synkroniseras till Azure AD. Obs! Om du minska omfånget för den **användare** synkroniseras sedan lösenord hash-synkronisering även att stoppa för filtrerat ut användare. Om objekten sedan redan synkroniserar minska antalet omfattning, filtrerat ut objekt tas bort från målkatalogen, kontakta på omfång mycket noggrant.
Här följer de stödda sätten att minska omfånget för de objekt som du synkroniserar.

- [cloudFiltered attribute](#cloudfiltered-attribute)
- [OU-filtrering](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>cloudFiltered attribut
Observera att detta inte är ett attribut som kan ställas in i Active Directory. Du måste ange värdet för det här attributet genom att lägga till en ny inkommande regel som vi nämnde i **åsidosätta värdet för befintliga attributet** avsnittet. Du kan sedan använda den **omvandling** och använda **uttryck** du ställer in det här attributet i metaversum. Här är ett exempel som du inte vill synkronisera alla användarens vars avdelningsnamn börjar med skiftlägesokänslig **HRD**:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Vi har först konverterat avdelningen från källa (Active Directory) till gemener. Använda funktionen Left, vi tog bara först 3 tecken och jämförs med identifieringen av startsfären. Om den matchas sedan anger du värdet till True annars NULL. Observera att vi anger värdet NULL, så att någon annan regel med lägre prioritet (högre tal värde) kan skriva till den med olika villkor. Kör förhandsversion på ett objekt för att verifiera synkroniseringsregel som vi nämnde i [Validera Synkroniseringsregel](#validate-sync-rule) avsnittet.

![Standardregler](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>OU-filtrering
Du kan skapa en eller flera organisationsenheter och flytta objekt som du inte vill synkronisera till de här organisationsenheterna. Konfigurera den Organisationsenhet som filtrering i Azure AD Connect genom att starta **Azure AD Connect** från skrivbordsikonen och väljer alternativ som visas nedan. Du kan också konfigurera OU-filtrering vid tidpunkten för installationen av Azure AD Connect. 

![Standardregler](media/how-to-connect-fix-default-rules/default8.png)

Följ anvisningarna i guiden och sedan avmarkera de organisationsenheter som du inte vill synkronisera.

![Standardregler](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>Ändra kopplingsvillkoret
Microsoft rekommenderar att du att du använder standard ansluta villkor som konfigurerats av Azure AD Connect. Ändra standard kopplingsvillkor gör det svårt för supportteamet att förstå anpassningarna och stöd för produkten.

## <a name="validate-sync-rule"></a>Verifiera synkroniseringsregel
Du kan verifiera den nyligen tillagda synkroniseringsregel med hjälp av funktionen preview utan att köra fullständig synkroniseringscykel. Starta **synkroniseringstjänsten** Användargränssnittet.

![Standardregler](media/how-to-connect-fix-default-rules/default10.png)

Klicka på den **Metaversumsökning**väljer omfångsobjektet som **person**, **Lägg till sats** och nämner sökvillkoren. Klicka på **Search** knappen och dubbelklicka på objektet i den **sökresultat** Tänk på att du kör import och synkronisering på skogen innan du kör det här steget kan det här är att säkerställa att data i Azure AD Connect är uppdaterad för objektet.

![Standardregler](media/how-to-connect-fix-default-rules/default11.png)



Välj **Anslutningsappar**, markerar du objektet i motsvarande connector(forest), klicka på **egenskaper...** .

![Standardregler](media/how-to-connect-fix-default-rules/default12.png)

Klicka på den **förhandsversion...**

![Standardregler](media/how-to-connect-fix-default-rules/default13a.png)

Klicka på **Generera förhandsgranskning** och **Import av attributflöde** i den vänstra rutan.

![Standardregler](media/how-to-connect-fix-default-rules/default14.png)
 
Här ser du att den nya regeln körs för objektet och har attributet cloudFiltered till True.

![Standardregler](media/how-to-connect-fix-default-rules/default15a.png)
 
Hur du kan jämföra ändrade regel med Standardregeln?
Du kan exportera båda reglerna separat som textfiler. De här reglerna kommer att exporteras som powershell-skriptfil. Du kan jämföra dem med hjälp av ett verktyg för jämförelse av filen för att se vilken typ av ändringar är klar. Här i det här exemplet använde jag windiff för att jämföra två filer.
 
Du kan märka att användaren ändrade regeln, msExchMailboxGuid attributet ändras till **uttryck** Skriv i stället för **direkt** med värdet som **NULL** och  **ExecuteOnce** alternativet. Du kan ignorera urskiljas och prioritet skillnader. 

![Standardregler](media/how-to-connect-fix-default-rules/default17.png)
 
Hur du löser en ändrad standardregel?
För att åtgärda dina regler till standardinställningar du ta bort den ändrade regeln och aktiverar Standardregeln enligt nedan och kör sedan en **fullständig synkronisering**. Du försöker uppnå innan detta som du kan vidta åtgärder som nämns ovan så att du inte förlorar anpassningen ## nästa steg

## <a name="next-steps"></a>Nästa steg
- [Maskinvara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Anpassade inställningar](how-to-connect-install-custom.md)

