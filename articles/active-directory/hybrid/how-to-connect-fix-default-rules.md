---
title: Åtgärdar ändrade standardregler – Azure AD Connect | Microsoft-dokument
description: Lär dig hur du åtgärdar ändrade standardregler som medar Azure AD Connect.
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
ms.openlocfilehash: 4626e0149028a140d143fb8d0969a03b732201fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036983"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Åtgärda ändrade standardregler i Azure AD Connect

Azure Active Directory (Azure AD) Connect använder standardregler för synkronisering.  Tyvärr gäller inte dessa regler universellt för alla organisationer. Baserat på dina krav kan du behöva ändra dem. I den här artikeln beskrivs två exempel på de vanligaste anpassningarna och det finns ett korrekt sätt att uppnå dessa anpassningar.

>[!NOTE] 
> Det går inte att ändra befintliga standardregler för att uppnå en nödvändig anpassning. Om du gör det förhindrar det att dessa regler uppdateras till den senaste versionen i framtida versioner. Du får inte de buggfixar du behöver eller nya funktioner. I det här dokumentet beskrivs hur du uppnår samma resultat utan att ändra de befintliga standardreglerna. 

## <a name="how-to-identify-modified-default-rules"></a>Identifiera ändrade standardregler
Från och med version 1.3.7.0 av Azure AD Connect är det enkelt att identifiera den ändrade standardregeln. Gå till **Appar på skrivbordet**och välj **Redigerare för synkroniseringsregler**.

![Azure AD Connect, med Redigeraren för synkroniseringsregler markerad](media/how-to-connect-fix-default-rules/default1.png)

I Redigeraren visas alla ändrade standardregler med en varningsikon framför namnet.

![Varningsikon](media/how-to-connect-fix-default-rules/default2.png)

 En inaktiverad regel med samma namn bredvid visas också (detta är standardstandardregeln).

![Redigerare för synkroniseringsregler, som visar standardstandardregel och ändrad standardregel](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Vanliga anpassningar
Följande är vanliga anpassningar av standardreglerna:

- Ändra attributflöde
- Ändra omfångsfilter
- Ändra kopplingsvillkor

Innan du ändrar några regler:

- Inaktivera synkroniseringsschemat. Schemaläggaren körs som standard var 30:e minut. Se till att den inte startar när du gör ändringar och felsöker dina nya regler. Om du tillfälligt vill inaktivera schemaläggaren `Set-ADSyncScheduler -SyncCycleEnabled $false`startar du PowerShell och kör .
 ![PowerShell-kommandon för att inaktivera synkroniseringsschemat](media/how-to-connect-fix-default-rules/default3.png)

- Ändringen i omfångsfiltret kan leda till att objekt tas bort i målkatalogen. Var försiktig innan du gör några ändringar i omfånget av objekt. Vi rekommenderar att du gör ändringar i en mellanlagringsserver innan du gör ändringar på den aktiva servern.
- Kör en förhandsgranskning på ett enskilt objekt, som nämns i avsnittet [Verifiera synkroniseringsregel,](#validate-sync-rule) när du har lagt till en ny regel.
- Kör en fullständig synkronisering när du har lagt till en ny regel eller ändrat en anpassad synkroniseringsregel. Den här synkroniseringen tillämpar nya regler för alla objekt.

## <a name="change-attribute-flow"></a>Ändra attributflöde
Det finns tre olika scenarier för att ändra attributflödet:
- Lägga till ett nytt attribut.
- Åsidosätta värdet för ett befintligt attribut.
- Välja att inte synkronisera ett befintligt attribut.

Du kan göra dessa utan att ändra standardstandardregler.

### <a name="add-a-new-attribute"></a>Lägga till ett nytt attribut
Om du upptäcker att ett attribut inte flödar från källkatalogen till målkatalogen använder du [Synkroniseringen Azure AD Connect: Directory-tillägg](how-to-connect-sync-feature-directory-extensions.md) för att åtgärda detta.

Om tilläggen inte fungerar för dig kan du prova att lägga till två nya synkroniseringsregler som beskrivs i följande avsnitt.


#### <a name="add-an-inbound-sync-rule"></a>Lägga till en inkommande synkroniseringsregel
En inkommande synkroniseringsregel innebär att källan för attributet är ett anslutningsutrymme och målet är metaversumet. Om du till exempel vill att ett nytt attributflöde ska finnas från lokal Active Directory till Azure Active Directory skapar du en ny inkommande synkroniseringsregel. Starta **Redigeraren för synkroniseringsregler**, välj **Inkommande** som riktning och välj **Lägg till ny regel**. 

 ![Redigeraren för synkroniseringsregler](media/how-to-connect-fix-default-rules/default3a.png)

Följ din egen namngivningskonvention för att namnge regeln. Här använder vi **Custom In från AD - Användare**. Det innebär att regeln är en anpassad regel och är en inkommande regel från Active Directory-anslutningsutrymmet till metaversumet.   

 ![Skapa regel för inkommande synkronisering](media/how-to-connect-fix-default-rules/default3b.png)

Ge din egen beskrivning av regeln, så att framtida underhåll av regeln är lätt. Beskrivningen kan till exempel baseras på vad syftet med regeln är och varför den behövs.

Gör dina val för fälten **Ansluten system,** **ansluten systemobjekttyp**och **metaversumobjekttyp.**

Ange prioritetsvärdet från 0 till 99 (ju lägre tal är, desto högre prioritet). För fälten **Tagga,** **Aktivera lösenordssynkronisering**och **Inaktiverat** använder du standardvalen.

Håll **Omfångsfiltret** tomt. Det innebär att regeln gäller för alla objekt som är kopplade mellan Active Directory Connected System och metaversumet.

Håll **join-reglerna** tomma. Det innebär att den här regeln använder kopplingsvillkoret som definierats i standardstandardregeln. Detta är ytterligare en anledning att inte inaktivera eller ta bort standardstandardregeln. Om det inte finns något kopplingsvillkor flödar inte attributet. 

Lägg till lämpliga omvandlingar för attributet. Du kan tilldela en konstant för att göra ett konstant värdeflöde till ditt målattribut. Du kan använda direkt mappning mellan käll- eller målattributet. Du kan också använda ett uttryck för attributet. Här är olika [uttrycksfunktioner](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) som du kan använda.

#### <a name="add-an-outbound-sync-rule"></a>Lägga till en utgående synkroniseringsregel
Om du vill länka attributet till målkatalogen måste du skapa en utgående regel. Detta innebär att källan är metaversum, och målet är det anslutna systemet. Om du vill skapa en utgående regel startar du **Redigeraren för synkroniseringsregler,** ändrar **riktning** till **utgående**och väljer **Lägg till ny regel**. 

![Redigeraren för synkroniseringsregler](media/how-to-connect-fix-default-rules/default3c.png)

Precis som med den inkommande regeln kan du använda din egen namngivningskonvention för att namnge regeln. Välj **det anslutna systemet** som Azure AD-klient och välj det anslutna systemobjekt som du vill ange attributvärdet till. Ange prioritet från 0 till 99. 

![Skapa utgående synkroniseringsregel](media/how-to-connect-fix-default-rules/default3d.png)

Håll **omfångsfiltret** och **anslut regler** tomma. Fyll i omformningen som konstant, direkt eller uttryck. 

Nu vet du hur du gör ett nytt attribut för ett användarobjekt flöde från Active Directory till Azure Active Directory. Du kan använda dessa steg för att mappa alla attribut från alla objekt till källa och mål. Mer information finns i [Skapa anpassade synkroniseringsregler](how-to-connect-create-custom-sync-rule.md) och [Förbered för att etablera användare](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Åsidosätta värdet för ett befintligt attribut
Du kanske vill åsidosätta värdet för ett attribut som redan har mappats. Om du till exempel alltid vill ange ett null-värde till ett attribut i Azure AD skapar du bara en inkommande regel. Gör konstantvärdet, `AuthoritativeNull`flöde till målattributet. 

>[!NOTE] 
> Använd `AuthoritativeNull` i `Null` stället för i det här fallet. Detta beror på att icke-null-värdet ersätter null-värdet, även om det har lägre prioritet (ett högre talvärde i regeln). `AuthoritativeNull`, å andra sidan, ersätts inte med ett icke-null-värde av andra regler. 

### <a name="dont-sync-existing-attribute"></a>Synkronisera inte befintliga attribut
Om du vill utesluta ett attribut från synkronisering använder du attributfiltreringsfunktionen som finns i Azure AD Connect. Starta **Azure AD Connect** från skrivbordsikonen och välj sedan Anpassa **synkroniseringsalternativ**.

![Azure AD Connect ytterligare uppgifter alternativ](media/how-to-connect-fix-default-rules/default4.png)

 Kontrollera att **Azure AD-appen och attributfiltrering** är markerat och välj **Nästa**.

![Valfria Azure AD Connect-funktioner](media/how-to-connect-fix-default-rules/default5.png)

Ta bort de attribut som du vill utesluta från synkronisering.

![Azure AD Connect-attribut](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Ändra omfångsfilter
Azure AD Sync tar hand om de flesta objekt. Du kan minska objektens omfattning och minska antalet objekt som ska exporteras, utan att ändra standardstandardsynkroniseringsreglerna. 

Använd någon av följande metoder för att minska omfattningen av de objekt som du synkroniserar:

- molnFilterattribut
- Filtrering av organisationsenheter

Om du minskar omfattningen för de användare som synkroniseras stoppas även synkroniseringen av lösenordsh hash för de filtrerade användarna. Om objekten redan synkroniseras tas de filtrerade objekten bort från målkatalogen när du har minskat omfattningen. Av denna anledning, se till att du räckvidd mycket noggrant.

>[!IMPORTANT] 
> Det rekommenderas inte att öka omfattningen av objekt som konfigurerats av Azure AD Connect. Om du gör det svårt för Microsofts supportteam att förstå anpassningarna. Om du måste öka objektens omfattning redigerar du den befintliga regeln, klonar den och inaktiverar den ursprungliga regeln. 

### <a name="cloudfiltered-attribute"></a>molnFilterattribut
Du kan inte ange det här attributet i Active Directory. Ange värdet för det här attributet genom att lägga till en ny inkommande regel. Du kan sedan använda **Omformning** och **uttryck** för att ange det här attributet i metaversumet. I följande exempel visas att du inte vill synkronisera alla användare vars avdelningsnamn börjar med **HRD** (skiftlägesokänsligt):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Vi konverterade först avdelningen från källa (Active Directory) till gemener. Sedan, med `Left` hjälp av funktionen, tog vi bara `hrd`de tre första tecknen och jämförde det med . Om det matchade, värdet `True`är `NULL`inställt på , annars . Vid inställningen av värdet till null kan någon annan regel med lägre prioritet (ett högre talvärde) skriva till det med ett annat villkor. Kör förhandsgranskning på ett objekt för att validera synkroniseringsregeln, som nämns i avsnittet [Validera synkroniseringsregel.](#validate-sync-rule)

![Skapa alternativ för inkommande synkroniseringsregel](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtrering av organisationsenheter
Du kan skapa en eller flera organisationsenheter och flytta de objekt som du inte vill synkronisera med dessa organisationsenheter. Konfigurera sedan organisationsenhetsfiltrering i Azure AD Connect. Starta **Azure AD Connect** från skrivbordsikonen och välj följande alternativ. Du kan också konfigurera organisationsenhetsfiltrering vid tidpunkten för installationen av Azure AD Connect. 

![Azure AD Connect ytterligare uppgifter](media/how-to-connect-fix-default-rules/default8.png)

Följ guiden och avmarkera de ru: er som du inte vill synkronisera.

![Azure AD Connect-domän- och ou-filtreringsalternativ](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Ändra kopplingsvillkor
Använd standardanslutningsvillkoren som konfigurerats av Azure AD Connect. Om du ändrar standardinställningarna för kopplingsförbehÃrar du att Microsofts support förstår anpassningarna och stöder produkten.

## <a name="validate-sync-rule"></a>Validera synkroniseringsregel
Du kan validera den nyligen tillagda synkroniseringsregeln med hjälp av förhandsgranskningsfunktionen, utan att köra hela synkroniseringscykeln. Välj **Synkroniseringstjänst**i Azure AD Connect .

![Azure AD Connect, med synkroniseringstjänsten markerad](media/how-to-connect-fix-default-rules/default10.png)

Välj **Metaverse Search**. Markera scopeobjektet som **person,** välj **Lägg till sats**och ange dina sökvillkor. Välj sedan **Sök**och dubbelklicka på objektet i sökresultaten. Kontrollera att dina data i Azure AD Connect är uppdaterade för det objektet genom att köra import och synkronisering i skogen innan du kör det här steget.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Välj **Kopplingar**i objektet **Metaversumegenskaper**, markera objektet i motsvarande koppling (skog) och välj **Egenskaper...**.

![Egenskaper för metaversumobjekt](media/how-to-connect-fix-default-rules/default12.png)

Välj **Förhandsgranska...**

![Egenskaper för kopplingsutrymmesobjekt](media/how-to-connect-fix-default-rules/default13a.png)

I förhandsgranskningsfönstret väljer du **Generera förhandsgransknings-** och **importattributflöde** i den vänstra rutan.

![Förhandsversion](media/how-to-connect-fix-default-rules/default14.png)
 
Här märker du att den nyligen tillagda regeln körs `cloudFiltered` på objektet och har angett att attributet är true.

![Förhandsversion](media/how-to-connect-fix-default-rules/default15a.png)
 
Om du vill jämföra den ändrade regeln med standardregeln exporterar du båda reglerna separat, som textfiler. Dessa regler exporteras som en PowerShell-skriptfil. Du kan jämföra dem med hjälp av valfritt filjämförelseverktyg (till exempel windiff) för att se ändringarna. 
 
Observera att i den `msExchMailboxGuid` ändrade regeln ändras attributet till **typen Uttryck** i stället för **Direct**. Dessutom ändras värdet till **null** och **executeonce-alternativet.** Du kan ignorera skillnader i identifierad och prioritet. 

![windiff verktyg utgång](media/how-to-connect-fix-default-rules/default17.png)
 
Om du vill åtgärda reglerna för att ändra tillbaka dem till standardinställningarna tar du bort den ändrade regeln och aktiverar standardregeln. Se till att du inte förlorar den anpassning du försöker uppnå. När du är klar kör du **Fullständig synkronisering**.

## <a name="next-steps"></a>Nästa steg
- [Hårdvara och förutsättningar](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Anpassade inställningar](how-to-connect-install-custom.md)



