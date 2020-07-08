---
title: Anpassningsbara programkontroller i Azure Security Center
description: Det här dokumentet hjälper dig att använda anpassningsbar program kontroll i Azure Security Center för att vitlista program som körs på Azure-datorer.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77604670"
---
# <a name="adaptive-application-controls"></a>Anpassningsbara programkontroller
Lär dig att konfigurera programkontroll i Azure Security Center med den här genomgången.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Vad är anpassningsbara programkontroller i Security Center?
Anpassningsbar program kontroll är en intelligent, automatiserad lösning från slut punkt till slut punkt från Azure Security Center som hjälper dig att styra vilka program som kan köras på dina Azure-och icke-Azure-datorer (Windows och Linux). Bland andra förmåner hjälper detta till att förstärka dina datorer mot skadlig kod. Security Center använder Machine Learning för att analysera programmen som körs på dina datorer och skapar en lista över tillåtna från denna intelligens. Den här funktionen fören klar processen att konfigurera och underhålla listan över tillåtna List principer för program, så att du kan:

- Blockera eller Varna vid försök att köra skadliga program, inklusive de som annars kan missas av lösningar mot skadlig kod.
- Uppfylla organisationens säkerhetsprincip som anger att bara licensierade program får användas.
- Undvika oönskad programvara som ska användas i din miljö.
- Undvik att gamla program som inte stöds kan köras.
- Förhindra vissa verktyg som inte tillåts i din organisation.
- Aktivera IT för att kontrollera åtkomsten till känsliga data via appanvändning.

> [!NOTE]
> För icke-Azure-och Linux-datorer stöds anpassningsbara program kontroller endast i gransknings läge.

## <a name="how-to-enable-adaptive-application-controls"></a>Hur fungerar anpassningsbara programkontroller?

Anpassningsbara program kontroller hjälper dig att definiera en uppsättning program som tillåts köras på konfigurerade grupper av datorer. Den här funktionen är tillgänglig för både Azure-och icke-Azure-fönster (alla versioner, klassiska eller Azure Resource Manager) och Linux-datorer. Använd följande steg för att konfigurera listan över tillåtna program:

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

1. Välj fliken **rekommenderas** för en lista med grupper med rekommendationer för program kontroll:

   ![Rekommenderas](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   Listan innehåller:

   - **Grupp namn**: namnet på prenumerationen och gruppen
   - **Virtuella datorer och datorer**: antalet virtuella datorer i gruppen
   - **Tillstånd**: rekommendationernas tillstånd
   - **Allvarlighets**grad: rekommendationernas allvarlighets grad

2. Klicka på en grupp för att öppna alternativet **skapa regler för program kontroll** .

   [![Kontrollregler för program](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. I **Välj virtuella datorer**granskar du listan med rekommenderade virtuella datorer och avmarkerar alla du inte vill använda en vit listning-princip för program. Nu visas två listor:

   - **Rekommenderade program**: en lista över program som används ofta på de virtuella datorerna i den här gruppen och rekommenderas att köras.
   - **Fler program**: en lista över program som antingen är mindre frekventa på de virtuella datorerna i den här gruppen eller som kallas Exploitables (se nedan) och rekommenderas för granskning.

4. Granska programmen i listorna och avmarkera de du inte vill ska tillämpas. Varje lista innehåller:

   - **Namn**: certifikat informationen eller den fullständiga sökvägen till ett program
   - **FILTYPER**: filtypen för programmet. Detta kan vara EXE, script, MSI eller någon permutation av de här typerna.
   - Kan **utnyttjas**: en varnings ikon anger om ett enskilt program kan användas av en angripare för att kringgå en lista över tillåtna program. Vi rekommenderar att du granskar programmen innan du godkänner dem.
   - **ANVÄNDARE**: användare som rekommenderas få tillåtelse att köra program

5. När du är färdig med dina val klickar du på **Skapa**. <br>
   När du har valt skapa skapar Azure Security Center automatiskt lämpliga regler ovanpå den inbyggda listan över tillåtna program för att tillåta listor på Windows-servrar (AppLocker).

> [!NOTE]
> - Security Center förlitar sig på minst två veckors data för att skapa en baslinje och fylla i unika rekommendationer för varje grupp med virtuella datorer. Nya Security Center-kunder på standardnivån bör vara beredda på att deras grupp med virtuella datorer först visas på fliken *Ingen rekommendation*.
> - Anpassningsbara programkontroller från Security Center stöder inte virtuella datorer som en AppLocker-princip redan är aktiverad för genom antingen ett GPO eller en lokal säkerhetsprincip.
> -  Som en säkerhets åtgärd försöker Security Center alltid att skapa en utgivar regel för program som har marker ATS för att tillåtas, och endast om ett program inte har någon utgivar information (aka inte signerad) skapas en Sök vägs regel för den fullständiga sökvägen till det specifika programmet.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Redigera och övervaka en grupp som har konfigurerats med programkontroll

1. Om du vill redigera och övervaka en grupp som har kon figurer ATS med en lista över tillåtna program för program, återgår du till sidan **anpassningsbar program kontroll** och väljer **konfigurerad** under **grupper av virtuella datorer**:

   ![Grupper](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   Listan innehåller:

   - **Grupp namn**: namnet på prenumerationen och gruppen
   - **Virtuella datorer och datorer**: antalet virtuella datorer i gruppen
   - **Läge**: gransknings läget loggar försök att köra program som inte finns på listan över tillåtna. Använd tillåter inte att program körs, om de inte finns med i listan över tillåtna
   - **Aviseringar**: eventuella aktuella överträdelser

2. Klicka på en grupp för att göra ändringar på sidan **Redigera princip för program kontroll** .

   ![Skydd](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. Under **Skyddsläge** kan du välja mellan följande alternativ:

   - **Granska**: i det här läget tvingar inte programkontrollösningen reglerna, utan granskar endast aktiviteten för de skyddade virtuella datorerna. Det här rekommenderas för scenarier där du först vill observera det övergripande beteendet innan en app blockeras mot att köras i den virtuella måldatorn.
   - **Tvinga**: i det här läget tvingar programkontrollösningen reglerna, och ser till att program som inte är tillåtna blockeras.

   > [!NOTE]
   > -  **Tvinga** skydds läge inaktive ras tills vidare meddelande.
   > - Som vi nämnde tidigare blir alltid en ny princip för programkontroll som standard konfigurerad i läget *Granska*. 
   >

4. Under **princip tillägg**lägger du till alla program Sök vägar som du vill tillåta. När du har lagt till dessa sökvägar uppdaterar Security Center listan över tillåtna program för program på de virtuella datorerna i den valda gruppen med virtuella datorer och skapar lämpliga regler för dessa program, utöver de regler som redan finns på plats.

5. Granska aktuella överträdelser som anges i avsnittet **senaste aviseringar** . Klicka på varje rad som ska omdirigeras till sidan **aviseringar** i Azure Security Center och Visa alla aviseringar som identifierades av Azure Security Center på de associerade virtuella datorerna.
   - **Aviseringar**: eventuella överträdelser som loggats.
   - **Nej. virtuella datorer: antalet**virtuella datorer med den här aviserings typen.

6. Under **utgivar vit listning-regler**, **vit listning-regler**och **hash-vit listning** -regler kan du se vilka program vit listning-regler som för närvarande är konfigurerade på de virtuella datorerna i en grupp, enligt regel samlings typen. För varje regel kan du se:

   - **Regel**: de specifika parametrar enligt vilka ett program granskas av AppLocker för att avgöra om ett program får köras.
   - **Filtyp**: de filtyper som omfattas av en speciell regel. Detta kan vara något av följande: EXE, skript, MSI eller en permutation av dessa filtyper.
   - **Användare**: namn eller antal användare som har tillåtelse att köra ett program som omfattas av en vit listning-regel för program.

   ![Regler för lista över tillåtna](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Klicka på de tre punkterna i slutet av varje rad om du vill ta bort den specifika regeln eller redigera tillåtna användare.

8. När du har gjort ändringar i en princip för **anpassningsbara program kontroller** klickar du på **Spara**.

### <a name="not-recommended-list"></a>Listan Rekommenderas inte

Security Center rekommenderar endast program vit listning-principer för virtuella datorer som kör en stabil uppsättning program. Inga rekommendationer skapas om programmen på den virtuella datorn ändras hela tiden.

![Rekommendation](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Listan innehåller:
- **Grupp namn**: namnet på prenumerationen och gruppen
- **Virtuella datorer och datorer**: antalet virtuella datorer i gruppen

Med Azure Security Center kan du definiera en program vit listning-princip på icke-rekommenderade grupper av virtuella datorer. Följ samma principer som tidigare beskrevs för att konfigurera en program vit listning-princip i dessa grupper också.

## <a name="move-a-vm-from-one-group-to-another"></a>Flytta en virtuell dator från en grupp till en annan

 När du flyttar en virtuell dator från en grupp till en annan så ändras den program kontroll princip som tillämpas på den till inställningarna för den grupp som du flyttade den till. Du kan också flytta en virtuell dator från en konfigurerad grupp till en icke-konfigurerad grupp, vilket innebär att du tar bort alla program kontroll principer som tidigare tillämpades på den virtuella datorn.

 1. På sidan **adaptiva program kontroller** från fliken **konfigurerad** klickar du på den grupp som den virtuella dator som ska flyttas för närvarande tillhör.
1. Klicka på **konfigurerade virtuella datorer och datorer**.
1. Flytta genom att klicka på de tre punkterna i den virtuella datorns rad och klicka på **Flytta**. Fönstret **flytta datorn till en annan grupp** öppnas.

    ![Skydd](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Välj den grupp som du vill flytta den virtuella datorn till och klicka på **Flytta dator**, och klicka på **Spara**.

    ![Skydd](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Se till att klicka på **Spara** när du har klickat på **Flytta dator**. Om du inte klickar på **Spara**kommer datorn inte att flyttas.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du använder anpassningsbar program kontroll i Azure Security Center för att vitlista program som körs i Azure och virtuella datorer som inte är Azure-datorer. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhets aviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Azure Security Center fel söknings guide](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
