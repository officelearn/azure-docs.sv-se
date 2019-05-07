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
ms.openlocfilehash: d2f0956b44d6df64fb73e5eee7844574237d8755
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067638"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Åtgärda ändrade standardregler i Azure AD Connect

Azure Active Directory (Azure AD) Connect använder standardregler för synkronisering.  Tyvärr gäller dessa regler inte universellt för alla organisationer. Baserat på dina krav kan behöva du ändra dem. Den här artikeln beskrivs två exempel på de vanligaste anpassningarna och förklarar det korrekta sättet att uppnå dessa anpassningar.

>[!NOTE] 
> Ändra befintlig standardregler för att uppnå en nödvändig anpassning stöds inte. Om du gör detta blir den förhindrar att uppdatera de här reglerna till den senaste versionen i framtida versioner. Du kommer inte felkorrigeringar som du behöver, eller nya funktioner. Det här dokumentet beskriver hur du uppnå samma resultat utan att ändra de befintliga standardreglerna. 

## <a name="how-to-identify-modified-default-rules"></a>Så här identifierar du ändrade standardregler
Från och med version 1.3.7.0 av Azure AD Connect, är det enkelt att identifiera ändrade Standardregeln. Gå till **appar på skrivbordet**, och välj **Synchronization Rules Editor**.

![Azure AD Connect med Synchronization Rules Editor markerat](media/how-to-connect-fix-default-rules/default1.png)

I redigeraren visas alla ändrade standardregler med en varningsikon framför namnet.

![Varningsikon](media/how-to-connect-fix-default-rules/default2.png)

 En inaktiverad regel med samma namn bredvid den visas också (detta är standard Standardregeln).

![Synchronization Rules Editor som visar standard Standardregeln och ändrade standardregel](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Vanliga anpassningar
Följande är vanliga anpassningar till standardreglerna:

- Ändra attributflöde
- Ändra Omfångsfilter
- Ändra kopplingsvillkoret

Innan du ändrar några regler:

- Inaktivera sync scheduler. Scheduler körs var 30: e minut som standard. Kontrollera att den inte startar när du gör ändringar och felsöka din nya regler. För att tillfälligt inaktivera scheduler, starta PowerShell och kör `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![PowerShell-kommandon för att inaktivera sync scheduler](media/how-to-connect-fix-default-rules/default3.png)

- Ändringen av Omfångsfilter kan leda till borttagning av objekt i målkatalogen. Var försiktig innan du gör några ändringar i omfånget för objekt. Vi rekommenderar att du gör ändringar i en mellanlagringsserver innan du gör ändringar på den aktiva servern.
- Kör en förhandsversion på ett enda objekt, som vi nämnde i den [Validera Synkroniseringsregel](#validate-sync-rule) avsnittet när du lägger till en ny regel.
- Kör en fullständig synkronisering när du lägger till en ny regel eller ändra eventuella anpassade synkroniseringsregel. Den här synkroniseringen gäller nya regler för alla objekt.

## <a name="change-attribute-flow"></a>Ändra attributflöde
Det finns tre olika scenarier för att ändra attributflödet:
- Lägger till ett nytt attribut.
- Åsidosätta värdet för ett befintligt attribut.
- Välja att inte synkronisera ett befintligt attribut.

Du kan göra detta utan att ändra standard standardregler.

### <a name="add-a-new-attribute"></a>Lägg till ett nytt attribut
Om du upptäcker att ett attribut inte flödar från källkatalogen till målkatalogen, använda den [Azure AD Connect-synkronisering: Katalogtillägg](how-to-connect-sync-feature-directory-extensions.md) att åtgärda detta.

Om tilläggen inte fungerar kan du försöka lägga till två nya Synkroniseringsregler som beskrivs i följande avsnitt.


#### <a name="add-an-inbound-sync-rule"></a>Lägg till en regel för inkommande synkronisering
En regel för inkommande synkronisering innebär källan för attributet är en anslutarplats och målet är metaversum. Till exempel om du vill att ett nytt attribut som flödar från den lokala Active Directory till Azure Active Directory, skapa en ny regel för inkommande synkronisering. Starta den **Synchronization Rules Editor**väljer **inkommande** som riktning och välj **Lägg till ny regel**. 

 ! Synkronisering regler Editor](media/how-to-connect-fix-default-rules/default3a.png)

Följ dina egna namngivningskonvention för att nämna regeln. Här kan vi använda **anpassad i från AD - användare**. Det innebär att regeln är en anpassad regel och är en regel för inkommande från Active Directory-anslutarplatsen till metaversum.   

 ![Skapa regel för inkommande synkronisering](media/how-to-connect-fix-default-rules/default3b.png)

Ange en egen beskrivning av regeln, så att det är enkelt att framtida underhåll av regeln. Beskrivningen kan till exempel baseras på målet med regeln är och varför det behövs.

Gör dina val den **anslutna System**, **anslutna System objekttyp**, och **typ av Metaversumobjekt** fält.

Ange prioritetsvärde mellan 0 och 99 (Ju lägre nummer, desto högre prioritet). För den **taggen**, **aktivera Lösenordssynkronisering**, och **inaktiverad** fält, använder du standardinställningarna.

Behåll **Scoping filter** tom. Det innebär att regeln gäller för alla objekt som är anslutna mellan de anslutna System i Active Directory och metaversum.

Behåll **ansluta regler** tom. Det innebär att den här regeln använder kopplingsvillkor som definierats i standard Standardregeln. Det här är en annan anledning inte att inaktivera eller ta bort Standardregeln för standard. Om det finns inga kopplingsvillkoret, attributflöde inte 

Lägg till lämpliga transformationer för dina attribut. Du kan tilldela en konstant, göra en konstant värde flöde till Målattributet. Du kan använda direkt mappning mellan käll- eller attribut. Eller så kan du använda ett uttryck för attributet. Här följer olika [uttryck](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) du kan använda.

#### <a name="add-an-outbound-sync-rule"></a>Lägg till en utgående synkroniseringsregel
Om du vill länka attributet till målkatalogen, måste du skapa en utgående regel. Det innebär att källan är metaversum och målet är det anslutna systemet. Om du vill skapa en utgående regel, starta den **Synchronization Rules Editor**, ändra den **riktning** till **utgående**, och välj **Lägg till ny regel**. 

![Synchronization Rules Editor](media/how-to-connect-fix-default-rules/default3c.png)

Som du kan använda din egen namngivningskonvention för att namnge regeln med den inkommande regeln. Välj den **anslutna System** som Azure AD-klient och välj det anslutna systemet objekt som du vill ange attribut-värde. Ange prioritet mellan 0 och 99. 

![Skapa regel för utgående synkronisering](media/how-to-connect-fix-default-rules/default3d.png)

Behåll **Scoping filter** och **ansluta regler** tom. Fyll i transformeringen som konstant, direkt eller uttryck. 

Nu vet du hur du gör ett nytt attribut för ett användarflöde objekt från Active Directory i Azure Active Directory. Du kan använda de här stegen för att mappa attribut från objekt källa och mål. Mer information finns i [skapar anpassade Synkroniseringsregler](how-to-connect-create-custom-sync-rule.md) och [förberedelser för att etablera användare](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Tillgång till ett befintligt attribut
Du kanske vill åsidosätta värdet för ett attribut som har redan mappats. Till exempel om du alltid vill använda ett null-värde till ett attribut i Azure AD skapa bara en inkommande regel. Gör det konstanta värdet `AuthoritativeNull`, flöde till Målattributet. 

>[!NOTE] 
> Använd `AuthoritativeNull` i stället för `Null` i det här fallet. Detta är eftersom den icke-nullvärde ersätter null-värde, även om den har lägre prioritet (ett högre antal värde i regeln). `AuthoritativeNull`, å andra sidan inte ersättas med ett icke-null-värde med andra regler. 

### <a name="dont-sync-existing-attribute"></a>Synkronisera inte befintligt attribut
Om du vill undanta ett attribut från att synkronisera använda attributfiltrering-funktionen i Azure AD Connect. Starta **Azure AD Connect** skrivbordsikonen och välj sedan **anpassa synkroniseringsalternativ**.

![Alternativ för ytterligare uppgifter för Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Se till att **Azure AD-app och attributfiltrering** är markerat och välj **nästa**.

![Valfria funktioner i Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Avmarkera de attribut som du vill undanta från att synkronisera.

![Azure AD Connect-attribut](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Ändra Omfångsfilter
Azure AD-synkronisering hand tar om de flesta av objekten. Du kan minska omfånget för objekt och minska antalet objekt som ska exporteras, utan att ändra standard standardregler för synkronisering. 

Använd någon av följande metoder för att minska omfånget för de objekt som du synkroniserar:

- cloudFiltered attribut
- Organisationsenhet filtrering

Om du minska omfånget för de användare som synkroniseras stoppas lösenord hash-synkronisering även för filtrerat ut användare. Om objekten redan synkroniserar när du minska omfånget, tas filtrerat ut objekt bort från målkatalogen. Se till att du mycket noggrant begränsa därför.

>[!IMPORTANT] 
> Öka omfånget för objekt som konfigurerats av Azure AD Connect rekommenderas inte. Det gör det svårt för Microsoft-supporten att förstå anpassningarna. Om du måste öka omfånget för objekt, redigera den befintliga regeln, klona den och inaktivera den ursprungliga regeln. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered attribut
Du kan inte ange det här attributet i Active Directory. Ange värdet för det här attributet genom att lägga till en ny inkommande regel. Du kan sedan använda **omvandling** och **uttryck** du ställer in det här attributet i metaversum. I följande exempel visar att du inte vill synkronisera alla användare vars avdelningsnamn börjar med **HRD** (skiftlägesokänsligt):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Vi först konverteras avdelningen från källa (Active Directory) till gemener. Sedan använder den `Left` funktion, som vi tog bara de första tre tecknen och jämfört med den med `hrd`. Om den matchade värdet `True`, annars `NULL`. I Ange värdet till null, skriva någon annan regel med lägre prioritet (ett högre antal värde) till den med ett annat villkor. Kör förhandsversionen av ett objekt för att verifiera synkroniseringsregel, enligt den [verifiera synkroniseringsregel](#validate-sync-rule) avsnittet.

![Skapa inkommande synkronisering regelalternativ](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Organisationsenhet filtrering
Du kan skapa en eller flera organisationsenheter (OU) och flytta objekt som du inte vill synkronisera till de här organisationsenheterna. Konfigurera sedan den Organisationsenhet som filtrering i Azure AD Connect. Starta **Azure AD Connect** skrivbordsikonen och välj följande alternativ. Du kan också konfigurera OU-filtrering vid tidpunkten för installationen av Azure AD Connect. 

![Azure AD Connect ytterligare uppgifter](media/how-to-connect-fix-default-rules/default8.png)

Följ anvisningarna i guiden och avmarkera de organisationsenheter som du inte vill synkronisera.

![Azure AD Connect domän och Organisationsenhet filtreringsalternativ](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Ändra kopplingsvillkoret
Använd standard kopplingsvillkor som konfigurerats av Azure AD Connect. Ändra standard kopplingsvillkor gör det svårt för Microsoft-supporten att förstå anpassningarna och stöd för produkten.

## <a name="validate-sync-rule"></a>Verifiera synkroniseringsregel
Du kan verifiera den nyligen tillagda synkroniseringsregel med hjälp av funktionen preview utan att köra fullständig synkroniseringscykel. I Azure AD Connect, väljer **synkroniseringstjänsten**.

![Azure AD Connect med synkroniseringstjänsten markerat](media/how-to-connect-fix-default-rules/default10.png)

Välj **Metaversumsökning**. Välj omfångsobjektet som **person**väljer **Lägg till sats**, och nämner sökvillkoren. Välj sedan **Search**, och dubbelklicka på objektet i sökresultaten. Se till att dina data i Azure AD Connect är uppdaterad för objektet, genom att köra import och synkronisering på skogen innan du kör det här steget.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

På **Metaversumobjektegenskaperna**väljer **Anslutningsappar**, markerar du objektet i motsvarande connector (skog) och välj **egenskaper...** .

![Egenskaper för Metaversumobjekt](media/how-to-connect-fix-default-rules/default12.png)

Välj **förhandsversion...**

![Objektegenskaper för utrymme](media/how-to-connect-fix-default-rules/default13a.png)

Välj i förhandsgranskningsfönstret **Generera förhandsgranskning** och **Import av attributflöde** i den vänstra rutan.

![Förhandsversion](media/how-to-connect-fix-default-rules/default14.png)
 
Här, Lägg märke till att nyligen tillagda regeln körs för objektet, och har ställt in den `cloudFiltered` attributet till true.

![Förhandsversion](media/how-to-connect-fix-default-rules/default15a.png)
 
Om du vill jämföra ändrade regeln med Standardregeln exportera båda reglerna separat, som textfiler. De här reglerna exporteras som en PowerShell-skriptfil. Du kan jämföra dem med ett fil på en jämförelse-verktyg (till exempel windiff) kan se ändringarna. 
 
Observera att i regeln ändrade den `msExchMailboxGuid` attributet ändras till den **uttryck** typ, i stället för **direkt**. Dessutom ändra värdet till **NULL** och **ExecuteOnce** alternativet. Du kan ignorera urskiljas och prioritet skillnader. 

![Windiff verktyget utdata](media/how-to-connect-fix-default-rules/default17.png)
 
Ta bort den ändrade regeln och aktivera Standardregeln för att åtgärda dina regler för att ändra dem till standardinställningar. Se till att du inte förlorar anpassning som du försöker uppnå. När du är klar, köra **fullständig synkronisering**.

## <a name="next-steps"></a>Nästa steg
- [Maskinvara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Anpassade inställningar](how-to-connect-install-custom.md)



