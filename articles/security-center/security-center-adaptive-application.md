---
title: Anpassningsbara programkontroller i Azure Security Center | Microsoft Docs
description: Det här dokumentet hjälper dig att använda anpassningsbar programkontroll i Azure Security Center för att vitlista godkända program som körs i virtuella Azure-datorer.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: cd5530cf27b88f0f1ccba392c0ea2d714fedcd66
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129785"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Anpassningsbara programkontroller i Azure Security Center
Lär dig att konfigurera programkontroll i Azure Security Center med den här genomgången.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Vad är anpassningsbara programkontroller i Security Center?
Anpassningsbara programkontroller är en intelligent, automatiserad slutpunkt till slutpunkt vitlistning lösning från Azure Security Center. Det hjälper dig att styra vilka program kan köras på din virtuella dator i Azure, vilket bland annat hjälper dig skydda dina virtuella datorer mot skadlig kod. Security Center använder maskininlärning att analysera programmen som körs på dina virtuella datorer och hjälper dig att tillämpa de specifika vitlisteregler med den här intelligensen. Den här funktionen förenklar processen med att konfigurera och underhålla användningsprinciper för listan över tillåtna program, så att du kan:

- Blockera eller Varna vid försök att köra skadliga program, inklusive de som annars kan missas av program mot skadlig kod.
- Uppfylla organisationens säkerhetsprincip som anger att bara licensierade program får användas.
- Undvika oönskad programvara som ska användas i din miljö.
- Undvik att gamla program som inte stöds kan köras.
- Förhindra vissa verktyg som inte tillåts i din organisation.
- Aktivera IT för att kontrollera åtkomsten till känsliga data via appanvändning.

## <a name="how-to-enable-adaptive-application-controls"></a>Hur fungerar anpassningsbara programkontroller?
Anpassningsbara programkontroller hjälper dig att definiera en uppsättning program som ska tillåtas att köras på konfigurerade grupper med virtuella datorer. Den här funktionen är endast tillgänglig för Windows-datorer (alla versioner, klassisk eller Azure Resource Manager). Följ stegen nedan för att konfigurera listan över tillåtna program i Security Center:

1. Öppna instrumentpanelen för **Security Center**.
2. Välj **Anpassningsbara programkontroller** i den vänstra rutan under **Avancerat molnskydd**.

    ![Skydd](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

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
1. Klicka på fliken **Rekommenderas** för att visa en lista över grupper med rekommendationer för programkontroll:

  ![Rekommenderas](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  Listan innehåller:

  - **NAMN**: namnet på prenumerationen och gruppen
  - **Virtuella datorer**: antalet virtuella datorer i gruppen
  - **TILLSTÅND**: tillståndet för rekommendationer
  - **ALLVARLIGHETSGRAD**: Rekommendationernas allvarlighetsgrad

2. Klicka på en grupp för att öppna den **skapa regler för programkontroll** alternativet.

  ![Kontrollregler för program](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. I den **Välj virtuella datorer**, granska listan med rekommenderade virtuella datorer och avmarkerar dem du inte vill tillämpa en princip för whitelising till. Nu visas två listor:

  - **Rekommenderade program**: en lista över program som är vanliga på virtuella datorer i den här gruppen och rekommenderas för att kunna köras.
  - **Fler program**: en lista över program som antingen är mindre vanligt förekommande på virtuella datorer i den här gruppen eller som kallas Exploitables (se mer nedan), och rekommenderas för granskning.

4. Granska programmen i listorna och avmarkera de du inte vill ska tillämpas. Varje lista innehåller:

  - **NAMN på**: certifikatinformationen eller den fullständiga sökvägen till ett program
  - **FILTYPER**: filtypen för programmet. Detta kan vara EXE, Script, MSI eller alla permutation av de här typerna.
  - **EXPLOATERBAR**: en varningsikon anger om ett visst program skulle kunna användas av en angripare för att kringgå en lösning för listan över tillåtna program. Vi rekommenderar att du granskar programmen innan du godkänner dem.
  - **ANVÄNDARE**: användare som rekommenderas få tillåtelse att köra program

5. När du är färdig med dina val klickar du på **Skapa**. <br>
När du har valt skapa skapar Azure Security Center automatiskt lämpliga regler ovanpå den inbyggda listan över tillåtna Programlösningen tillgängliga på Windows-servrar (AppLocker).


> [!NOTE]
> - Security Center förlitar sig på minst två veckors data för att skapa en baslinje och fylla i unika rekommendationer för varje grupp med virtuella datorer. Nya Security Center-kunder på standardnivån bör vara beredda på att deras grupp med virtuella datorer först visas på fliken *Ingen rekommendation*.
> - Anpassningsbara programkontroller från Security Center stöder inte virtuella datorer som en AppLocker-princip redan är aktiverad för genom antingen ett GPO eller en lokal säkerhetsprincip.
> -  Som vi rekommenderar Security Center försöker alltid att skapa en utgivarregel för program som är markerad som ska tillåtas och endast om ett program inte har någon utgivarinformation (alltså inte signerad), skapas en sökvägsregel för den fullständiga sökvägen till den specifika program.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Redigera och övervaka en grupp som har konfigurerats med programkontroll

1. Om du vill redigera och övervaka en grupp som har konfigurerats med en princip för vitlistning av program, gå tillbaka till den **anpassningsbara programkontroller** och välj **KONFIGURERAD** under **grupper av virtuella datorer**:

  ![Grupper](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  Listan innehåller:

  - **Namn på**: namnet på prenumerationen och gruppen
  - **Virtuella datorer**: antalet virtuella datorer i gruppen
  - **Läget**: granskningsläget loggas försök att köra program; Framtvinga kommer inte att program ska köras
  - **Aviseringar**: aktuella överträdelser

2. Klicka på en grupp att göra ändringar i den **redigera principer för programkontroll** sidan.

  ![Skydd](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. Under **Skyddsläge** kan du välja mellan följande alternativ:

  - **Granska**: i det här läget tvingar inte programkontrollösningen reglerna, utan granskar endast aktiviteten för de skyddade virtuella datorerna. Det här rekommenderas för scenarier där du först vill observera det övergripande beteendet innan en app blockeras mot att köras i den virtuella måldatorn.
  - **Tvinga**: i det här läget tvingar programkontrollösningen reglerna, och ser till att program som inte är tillåtna blockeras.

   > [!NOTE]
   > Som vi nämnde tidigare blir alltid en ny princip för programkontroll som standard konfigurerad i läget *Granska*. 
   >

4. Under **principtillägg**, du kan lägga till valfri programsökväg som du vill tillåta. När du lägger till sökvägarna skapar Security Center uppdaterar whielisting-princip för program på de virtuella datorerna i den markerade gruppen av virtuella datorer och skapar lämpliga regler för programmen utöver de regler som redan finns på plats.

5. Granska de aktuella överträdelser som anges i den **de senaste aviseringarna** avsnittet. Klicka på varje rad omdirigeras till den **aviseringar** sidan i Azure Security Center och visa alla aviseringar som har identifierats av Azure Security Center på virtuella.
  - **Aviseringar**: eventuella överträdelser som har loggats.
  - **Nej. virtuella datorer**: antalet virtuella datorer med den här aviseringstypen.

6. Under **utgivare av regler för**, **sökväg för regler för lista över tillåtna**, och **Hash-vitlisteregler** ser du vilka listan över tillåtna program regler är för närvarande konfigurerats på virtuella datorer i en grupp, enligt Samlingstypen av regler. För varje regel kan du se:

  - **Regeln**: de specifika parametrarna som ett program granskas av AppLocker att avgöra om ett program kan köras.
  - **Filtypen**: de filtyper som omfattas av en specifik regel. Detta kan vara något av följande: EXE, Script, MSI eller alla permutation av dessa filtyper.
  - **Användare**: namn eller antal användare som har tillåtelse att köra ett program som omfattas av en regel för lista över tillåtna program.

   ![Regler för lista över tillåtna](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Klicka på de tre punkterna i slutet av varje rad om du vill ta bort den specifika regeln eller redigera tillåtna användare.

8. När du ändrar en **anpassningsbara programkontroller** principen, klickar du på **spara**.

### <a name="not-recommended-list"></a>Listan Rekommenderas inte

Security Center rekommenderar enbart att användningsprinciper för lista över tillåtna för virtuella datorer som kör en stabil uppsättning program. Inga rekommendationer skapas om programmen på den virtuella datorn ändras hela tiden.

![Rekommendation](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Listan innehåller:
- **NAMN**: namnet på prenumerationen och gruppen
- **Virtuella datorer**: antalet virtuella datorer i gruppen

Azure Security Center kan du definiera en princip för lista över tillåtna på icke-rekommenderas grupper med virtuella datorer samt. Följ samma principer som beskrevs, om du vill konfigurera en princip för listan över tillåtna program på dessa grupper samt.


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig att använda anpassningsbar programkontroll i Azure Security Center för att lista godkända program som körs i virtuella Azure-datorer. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
