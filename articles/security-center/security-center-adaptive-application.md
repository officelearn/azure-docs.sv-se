---
title: Anpassningsbara programkontroller i Azure Security Center
description: Det här dokumentet hjälper dig att använda anpassad programkontroll i Azure Security Center för att vitlista program som körs i Azure-datorer.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/23/2019
ms.author: memildin
ms.openlocfilehash: 1dc94c5ec08cc27fb1819ccc16fd766c62aad796
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604670"
---
# <a name="adaptive-application-controls"></a>Anpassningsbara programkontroller
Lär dig att konfigurera programkontroll i Azure Security Center med den här genomgången.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Vad är anpassningsbara programkontroller i Security Center?
Adaptiv programkontroll är en intelligent, automatiserad, heltäckande lösning från Azure Security Center som hjälper dig att styra vilka program som kan köras på dina Azure- och icke-Azure-datorer (Windows och Linux). Bland andra fördelar, detta hjälper till att härda dina maskiner mot skadlig kod. Security Center använder maskininlärning för att analysera de program som körs på dina datorer och skapar en tillåt-lista från den här informationen. Den här funktionen förenklar avsevärt processen för att konfigurera och underhålla principer för tillåt programlista, så att du kan:

- Blockera eller avisera vid försök att köra skadliga program, inklusive de som annars kan missas av lösningar mot skadlig kod.
- Uppfylla organisationens säkerhetsprincip som anger att bara licensierade program får användas.
- Undvika oönskad programvara som ska användas i din miljö.
- Undvik att gamla program som inte stöds kan köras.
- Förhindra vissa verktyg som inte tillåts i din organisation.
- Aktivera IT för att kontrollera åtkomsten till känsliga data via appanvändning.

> [!NOTE]
> För icke-Azure- och Linux-datorer stöds anpassade programkontroller endast i granskningsläge.

## <a name="how-to-enable-adaptive-application-controls"></a>Hur fungerar anpassningsbara programkontroller?

Anpassningsbara programkontroller hjälper dig att definiera en uppsättning program som tillåts köras på konfigurerade grupper av datorer. Den här funktionen är tillgänglig för både Azure- och non-Azure Windows (alla versioner, klassiska eller Azure Resource Manager) och Linux-datorer. Så här konfigurerar du listor för att tillåta program:

1. Öppna instrumentpanelen för **Security Center**.

1. Välj **Anpassningsbara programkontroller** i den vänstra rutan under **Avancerat molnskydd**.

    [![Skydd](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

Sidan **Anpassningsbara programkontroller** visas.

![kontroller](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

Avsnittet **Grupper av virtuella datorer** innehåller tre flikar:

* **Konfigurerad**: lista över grupper som innehåller de virtuella datorer som konfigurerades med programkontroll.
* **Rekommenderas**: lista över grupper som programkontroll rekommenderas för. Security Center använder Machine Learning för att identifiera virtuella datorer som är bra kandidater för programkontroll baserat på om de virtuella datorerna konsekvent kör samma program.
* **Ingen rekommendation**: lista över grupper som innehåller virtuella datorer utan rekommendationer för programkontroll. Till exempel virtuella datorer där program alltid byts ut och inte har uppnått ett stabilt tillstånd.

> [!NOTE]
> Security Center använder egen klusteralgoritm för att skapa grupper med virtuella datorer och säkerställa att liknande virtuella datorer får den programkontrollsprincip som rekommenderas.
>
>

### <a name="configure-a-new-application-control-policy"></a>Konfigurera en ny princip för programkontroll

1. Välj fliken **Rekommenderad** för en lista med grupper med rekommendationer för programkontroll:

   ![Rekommenderas](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   Listan innehåller:

   - **Gruppnamn**: Namnet på prenumerationen och gruppen
   - **Virtuella datorer och datorer**: Antalet virtuella datorer i gruppen
   - **Staten**: rekommendationernas tillstånd
   - **Allvarlighetsgrad**: rekommendationernas allvarlighetsgrad

2. Klicka på en grupp för att öppna alternativet **Skapa programkontrollregler.**

   [![Kontrollregler för program](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. I select **vms**granskar du listan över rekommenderade virtuella datorer och avmarkerar alla som du inte vill använda en princip för att vitlista för program på. Nu visas två listor:

   - **Rekommenderade program:** en lista över program som är vanliga på de virtuella datorerna i den här gruppen och rekommenderas att tillåtas att köras.
   - **Fler program**: en lista över program som antingen är mindre frekventa på de virtuella datorerna i den här gruppen eller som kallas Exploitables (se mer nedan) och som rekommenderas för granskning.

4. Granska programmen i listorna och avmarkera de du inte vill ska tillämpas. Varje lista innehåller:

   - **NAMN:** certifikatinformationen eller den fullständiga sökvägen till ett program
   - **FILTYPER**: filtypen för programmet. Detta kan vara EXE, Script, MSI eller någon permutation av dessa typer.
   - **EXPLOITABLE**: en varningsikon anger om ett visst program kan användas av en angripare för att kringgå en lista över programtillåtna. Vi rekommenderar att du granskar programmen innan du godkänner dem.
   - **ANVÄNDARE**: användare som rekommenderas få tillåtelse att köra program

5. När du är färdig med dina val klickar du på **Skapa**. <br>
   När du har valt Skapa skapar Azure Security Center automatiskt lämpliga regler ovanpå den inbyggda programtillåtna listlösningen som är tillgänglig på Windows-servrar (AppLocker).

> [!NOTE]
> - Security Center förlitar sig på minst två veckors data för att skapa en baslinje och fylla i unika rekommendationer för varje grupp med virtuella datorer. Nya Security Center-kunder på standardnivån bör vara beredda på att deras grupp med virtuella datorer först visas på fliken *Ingen rekommendation*.
> - Anpassningsbara programkontroller från Security Center stöder inte virtuella datorer som en AppLocker-princip redan är aktiverad för genom antingen ett GPO eller en lokal säkerhetsprincip.
> -  Som säkerhetspraxis försöker Security Center alltid skapa en utgivarregel för program som har valts för att tillåtas, och endast om ett program inte har en utgivarinformation (aka som inte är signerad) skapas en sökvägsregel för den fullständiga sökvägen till specifik tillämpning.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Redigera och övervaka en grupp som har konfigurerats med programkontroll

1. Om du vill redigera och övervaka en grupp som konfigurerats med en lista för program tillåt kan du gå tillbaka till sidan **Adaptiva programkontroller** och välja **KONFIGURERAD** under **Grupper av virtuella datorer:**

   ![Grupper](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   Listan innehåller:

   - **Gruppnamn**: namnet på prenumerationen och gruppen
   - **Virtuella datorer och datorer:** antalet virtuella datorer i gruppen
   - **Läge**: Granskningsläge loggar försök att köra program som inte finns med i listan över tillåtna. Framtvinga tillåter inte att program körs om de inte finns med i listan över tillåtna
   - **Varningar:** eventuella aktuella överträdelser

2. Klicka på en grupp om du vill göra ändringar på sidan **Redigera programkontrollprincip.**

   ![Skydd](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. Under **Skyddsläge** kan du välja mellan följande alternativ:

   - **Granska**: i det här läget tvingar inte programkontrollösningen reglerna, utan granskar endast aktiviteten för de skyddade virtuella datorerna. Det här rekommenderas för scenarier där du först vill observera det övergripande beteendet innan en app blockeras mot att köras i den virtuella måldatorn.
   - **Tvinga**: i det här läget tvingar programkontrollösningen reglerna, och ser till att program som inte är tillåtna blockeras.

   > [!NOTE]
   > -  **Framtvinga** skyddsläget är inaktiverat tills vidare.
   > - Som vi nämnde tidigare blir alltid en ny princip för programkontroll som standard konfigurerad i läget *Granska*. 
   >

4. Lägg till alla programsökvägar som du vill tillåta under **Principtillägg.** När du har lagt till dessa sökvägar uppdaterar Security Center principen för programmet tillåt list på de virtuella datorerna inom den valda gruppen VMS och skapar lämpliga regler för dessa program, utöver de regler som redan finns.

5. Granska de aktuella överträdelserna i avsnittet **Senaste aviseringar.** Klicka på varje rad som ska **omdirigeras** till sidan Aviseringar i Azure Security Center och visa alla aviseringar som har identifierats av Azure Security Center på de associerade virtuella datorerna.
   - **Aviseringar**: eventuella överträdelser som loggats.
   - **- Nej, det är jag inte. antal virtuella datorer:** antalet virtuella datorer med den här aviseringstypen.

6. Under **regler för vitlistning**av Publisher, **Regler för blankning**av sökvägar och **Hash** kan du se vilka regler för vitlistning av program som för närvarande är konfigurerade på de virtuella datorerna inom en grupp, enligt regelsamlingstypen. För varje regel kan du se:

   - **Regel**: De specifika parametrar enligt vilka ett program granskas av AppLocker för att avgöra om ett program tillåts köras.
   - **Filtyp**: De filtyper som omfattas av en viss regel. Detta kan vara något av följande: EXE, Script, MSI eller någon permutation av dessa filtyper.
   - **Användare**: Namn eller antal användare som får köra ett program som omfattas av en regel för vitlistning av program.

   ![Regler för lista över tillåtna](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Klicka på de tre punkterna i slutet av varje rad om du vill ta bort den specifika regeln eller redigera de tillåtna användarna.

8. När du har gjort ändringar i principen **om adaptiva programkontroller** klickar du på **Spara**.

### <a name="not-recommended-list"></a>Listan Rekommenderas inte

Security Center rekommenderar endast principer för vitlistning av program för virtuella datorer som kör en stabil uppsättning program. Inga rekommendationer skapas om programmen på den virtuella datorn ändras hela tiden.

![Rekommendation](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Listan innehåller:
- **Gruppnamn**: namnet på prenumerationen och gruppen
- **Virtuella datorer och datorer:** antalet virtuella datorer i gruppen

Med Azure Security Center kan du definiera en princip för vitlistning av program även för icke-rekommenderade grupper av virtuella datorer. Följ samma principer som tidigare beskrivits, för att konfigurera en princip för vitlistning av program även för dessa grupper.

## <a name="move-a-vm-from-one-group-to-another"></a>Flytta en virtuell dator från en grupp till en annan

 När du flyttar en virtuell dator från en grupp till en annan ändras programkontrollprincipen för den i inställningarna för den grupp som du flyttade den till. Du kan också flytta en virtuell dator från en konfigurerad grupp till en icke-konfigurerad grupp, vilket resulterar i att ta bort alla programkontrollprinciper som tidigare tillämpats på den virtuella datorn.

 1. På sidan **Adaptiva programkontroller** klickar du på den grupp som den virtuella datorn som ska flyttas för närvarande tillhör på fliken **KONFIGURERAD.**
1. Klicka på **Konfigurerade virtuella datorer och datorer**.
1. Klicka på de tre punkterna på raden på den virtuella datorn för att flytta och klicka på **Flytta**. Fönstret **Flytta datorn till ett annat gruppfönster** öppnas.

    ![Skydd](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Markera den grupp som den virtuella datorn ska flyttas till och klicka på **Flytta dator**och klicka på **Spara**.

    ![Skydd](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Var noga med att klicka på **Spara** när du har klickat på **Flytta dator**. Om du inte klickar på **Spara**flyttas inte datorn.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet lärde du dig hur du använder adaptiv programkontroll i Azure Security Center för att vitlista program som körs i Azure och andra virtuella datorer utanför Azure. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
