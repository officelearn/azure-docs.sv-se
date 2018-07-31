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
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2018
ms.author: rkarlin
ms.openlocfilehash: 27e013ad9e94bb025cfad87cc68b244882a207b3
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161940"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Anpassningsbara programkontroller i Azure Security Center
Lär dig att konfigurera programkontroll i Azure Security Center med den här genomgången.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Vad är anpassningsbara programkontroller i Security Center?
Med hjälp av anpassningsbara programkontroller kan du kontrollera vilka program som kan köras på din virtuella dator i Azure, vilket bland annat hjälper dig skydda dina virtuella datorer mot skadlig kod. Security Center använder maskininlärning för att analysera programmen som körs i den virtuella datorn och hjälper dig att tillämpa vitlisteregler med den här intelligensen. Den här funktionen förenklar processen avsevärt för att konfigurera och underhålla programs vitlistor, så att du kan:

- Blockera eller varna vid försök att köra skadliga program, inklusive de som annars kan missas av program mot skadlig kod
- Uppfylla organisationens säkerhetsprincip som anger att bara licensierade program får användas.
- Undvika oönskad programvara som ska användas i din miljö.
- Undvik att gamla program som inte stöds kan köras.
- Förhindra vissa verktyg som inte tillåts i din organisation.
- Aktivera IT för att kontrollera åtkomsten till känsliga data via appanvändning.

## <a name="how-to-enable-adaptive-application-controls"></a>Hur fungerar anpassningsbara programkontroller?
Anpassningsbara programkontroller hjälper dig att definiera en uppsättning program som ska tillåtas att köras på konfigurerade grupper. Den här funktionen är endast tillgänglig för Windows-datorer (alla versioner, klassisk eller Azure Resource Manager). Följ stegen nedan för att konfigurera listan över tillåtna program i Security Center:

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
  - **TILLSTÅND**: Rekommendationernas tillstånd, i de flesta fall öppet
  - **ALLVARLIGHETSGRAD**: Rekommendationernas allvarlighetsgrad

2. Välj en grupp för att öppna alternativet **Skapa regler för programkontroll**.

  ![Kontrollregler för program](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. I **Välj virtuella datorer** läser du listan med rekommenderade virtuella datorer och avmarkerar dem du inte vill använda programkontroll för. Nu visas två listor:

  - **Rekommenderade program**: en lista över program som är vanliga på virtuella datorer i den här gruppen och därför rekommenderas för kontrollregler för program av Security Center.
  - **Fler program**: en lista över program som är antingen är mindre vanligt förekommande på virtuella datorer i den här gruppen, eller program som kallas Exploitables (se mer nedan), och som du bör granska innan du tillämpar reglerna.

4. Granska programmen i listorna och avmarkera de du inte vill ska tillämpas. Varje lista innehåller:

  - **NAMN**: certifikatinformationen för program eller dess fullständiga programsökväg
  - **FILTYPER**: filtypen för programmet. Det kan vara EXE, Script eller MSI.
  - **EXPLOATERBAR**: En varningsikon anger om programmen skulle kunna användas av en angripare för att kringgå listan över tillåtna program. Vi rekommenderar att du granskar programmen innan du godkänner dem.
  - **ANVÄNDARE**: användare som rekommenderas få tillåtelse att köra program

5. När du är färdig med dina val klickar du på **Skapa**.

6. Security Center tillämpar lämpliga regler på var och en av de valda virtuella datorerna genom att använda inbyggda AppLocker-funktioner för Windows-servrar. Dessutom aktiverar Security Center alltid programkontroll i läget Granska som standard. När du har validerat att listan inte hade någon negativ inverkan på din arbetsbelastning kan du växla till läget **Framtvinga**. Mer information finns på sidan om [hur AppLocker fungerar](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-application-control/applocker/how-applocker-works-techref).
 
> [!NOTE]
> - Security Center förlitar sig på minst två veckors data för att skapa en baslinje och fylla i unika rekommendationer för varje grupp med virtuella datorer. Nya Security Center-kunder på standardnivån bör vara beredda på att deras grupp med virtuella datorer först visas på fliken *Ingen rekommendation*.
> - Anpassningsbara programkontroller från Security Center stöder inte virtuella datorer som en AppLocker-princip redan är aktiverad för genom antingen ett GPO eller en lokal säkerhetsprincip.
> -  Som ett säkerhetsregelverk försöker Security Center alltid att skapa en utgivarregel för programmen som ska vitlistas, och endast om ett program inte har någon utgivarinformation (alltså inte signerad) skapas en sökvägsregel för den specifika EXE-filens fullständiga sökväg.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Redigera och övervaka en grupp som har konfigurerats med programkontroll

1. Om du vill redigera och övervaka en grupp som har konfigurerats med programkontroll går du tillbaka till sidan **Anpassningsbar programkontroll** och väljer **KONFIGURERAD** under **Grupper av virtuella datorer**:

  ![Grupper](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  Listan innehåller:

  - **NAMN**: namnet på prenumerationen och gruppen
  - **Virtuella datorer**: antalet virtuella datorer i gruppen
  - **LÄGE**: I granskningsläget loggas försök att köra program som inte är vitlistade, och med Tvinga hindras program som inte är vitlistade från att köras
  - **PROBLEM**: aktuella överträdelser

2. Välj en grupp att göra ändringar i på sidan för att **redigera principer för programkontroll**.

  ![Skydd](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. Under **Skyddsläge** kan du välja mellan följande alternativ:

  - **Granska**: i det här läget tvingar inte programkontrollösningen reglerna, utan granskar endast aktiviteten för de skyddade virtuella datorerna. Det här rekommenderas för scenarier där du först vill observera det övergripande beteendet innan en app blockeras mot att köras i den virtuella måldatorn.
  - **Tvinga**: i det här läget tvingar programkontrollösningen reglerna, och ser till att program som inte är tillåtna blockeras.

  Som vi nämnde tidigare blir alltid en ny princip för programkontroll som standard konfigurerad i läget *Granska*. Under **Policy extension** (Principtillägg) kan du lägga till egna programsökvägar du vill lägga till på vitlistan. När du lägger till sökvägarna skapar Security Center lämpliga regler för programmen utöver de regler som redan finns.

  I avsnittet **Nya ärenden** visas eventuella aktuella överträdelser.

  ![Problem](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

  Listan innehåller:
  - **ÄRENDEN**: eventuella överträdelser som har loggats, vilket kan vara följande:

      - **ViolationsBlocked**: när lösningen är aktiverad i läget Tvinga och ett program som inte tillåts försöker köras.
      - **ViolationsAudited**: när lösningen är aktiverad i läget Granska och ett program som inte tillåts körs.

 - **ANTAL VIRTUELLA DATORER**: antalet virtuella datorer med den här ärendetypen.

  Om du klickar på någon av raderna omdirigeras du till sidan [Aktivitetslogg för Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) där du kan se information om alla virtuella datorer med den här typen av överträdelser. Om du klickar på de tre punkterna i slutet av en rad kan du ta bort den specifika posten. I avsnittet **Konfigurerade virtuella datorer** visas de virtuella datorer som reglerna gäller.

  ![Konfigurerade virtuella datorer](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

  Under **Utgivare av regler för lista över tillåtna** innehåller listan följande:

  - **REGEL**: program för vilka en utgivarregel skapats baserat på den certifikatinformation som hittades för respektive program
  - **FILTYP**: filtyper som omfattas av en specifik utgivarregel. Detta kan vara något av följande: EXE, Script eller MSI.
  - **ANVÄNDARE**: antalet användare som får köra respektive program

  Läs [Understanding Publisher Rules in Applocker](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker) (Förstå publiceringsregler i Applocker) för att få mer information.

  ![Regler för lista över tillåtna](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

  Om du klickar på de tre punkterna i slutet av en rad kan du ta bort den specifika regeln eller redigera tillåtna användare.

  I avsnittet **Sökväg för regler för lista över tillåtna** anges hela programsökvägen (inklusive den specifika filtypen) för de program som inte är signerade med ett digitalt certifikat, men som fortfarande finns med i listan över tillåtna program.

  > [!NOTE]
  > Som standard försöker Security Center som ett säkerhetsregelverk alltid att skapa en utgivarregel för EXE-filerna som ska vitlistas, och endast om en EXE-fil inte har någon utgivarinformation (alltså inte signerad) skapas en sökvägsregel för den specifika EXE-filens fullständiga sökväg.

  ![Sökväg för regler för lista över tillåtna](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

  Listan innehåller:
  - **NAMN**: fullständig korrigering för den körbara filen
  - **FILTYP**: filtyper som omfattas av en specifik sökväg. Detta kan vara något av följande: EXE, Script eller MSI.
  - **ANVÄNDARE**: antalet användare som får köra respektive program

  Om du klickar på de tre punkterna i slutet av en rad kan du ta bort den specifika regeln eller redigera tillåtna användare.

4. När du har gjort dina ändringar på sidan **Anpassningsbar programkontroll** klickar du på knappen **Spara**. Om du väljer att inte spara ändringarna klickar du på **Ta bort**.

### <a name="not-recommended-list"></a>Listan Rekommenderas inte

Security Center rekommenderar enbart att lägga till program i listan över tillåtna för virtuella datorer som kör en stabil uppsättning program. Inga rekommendationer skapas om programmen på den virtuella datorn ändras hela tiden.

![Rekommendation](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Listan innehåller:
- **NAMN**: namnet på prenumerationen och gruppen
- **Virtuella datorer**: antalet virtuella datorer i gruppen

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig att använda anpassningsbar programkontroll i Azure Security Center för att lista godkända program som körs i virtuella Azure-datorer. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
