---
title: "Utvärdering av säkerhetsbaslinjen i säkerhets- och granskningslösningen i Operations Management | Microsoft Docs"
description: "Det här dokumentet beskriver hur du använder säkerhets- och granskningslösningen i OMS för att, i efterlevnads- och säkerhetssyfte, göra en utvärdering av säkerhetsbaslinjen för alla övervakade datorer."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: f0a512fb0684b1db25353bf4bbd35bdf2a07e1b2
ms.openlocfilehash: f29b20ba11b030d98a7a5574f816ea132edc3f6d
ms.lasthandoff: 12/06/2016


---
# <a name="baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Utvärdering av säkerhetsbaslinjen i säkerhets- och granskningslösningen i Operations Management Suite
Det här dokumentet beskriver hur du använder funktionerna för utvärdering av säkerhetsbaslinjen i [säkerhets- och granskningslösningen i Operations Management Suite (OMS)](operations-management-suite-overview.md) för att utvärdera de övervakade resursernas säkerhetsstatus.

## <a name="what-is-baseline-assessment"></a>Vad är en utvärdering av säkerhetsbaslinjen?
Microsoft definierar, i samarbete med branschspecifika och offentliga organisationer i hela världen, en Windows-konfiguration för mycket säkra serverinstallationer. Den här konfigurationen består av en uppsättning registernycklar, granskningsprincipinställningar och säkerhetsprincipinställningar tillsammans med Microsofts rekommenderade värden för dessa inställningar. Den här uppsättningen regler kallas för en säkerhetsbaslinje. Med funktionen Utvärdering av säkerhetsbaslinje i säkerhets- och granskningslösningen i OMS kan du smidigt söka igenom alla dina datorer och kontrollera att efterlevnaden upprätthålls. 

Det finns tre typer av regler:

* **Registerregler**: Kontrollerar att registernycklarna är korrekt angivna.
* **Granskningsprincipregler**: Regler relaterade till granskningsprinciper.
* **Säkerhetsprincipregler**: Regler relaterade till användarens behörigheter på datorn.

> [!NOTE]
> En kort översikt över den här funktionen finns i [Utvärdera säkerhetskonfigurationsbaslinjen med hjälp av säkerhetslösningen i OMS](https://blogs.technet.microsoft.com/msoms/2016/08/12/use-oms-security-to-assess-the-security-configuration-baseline/).
> 
> 

## <a name="security-baseline-assessment"></a>Utvärdering av säkerhetsbaslinje
Du kan granska din aktuella utvärdering av säkerhetsbaslinjen för alla datorer som övervakas av säkerhets- och granskningslösningen i OMS från instrumentpanelen.  Du kommer till instrumentpanelen för utvärdering av säkerhetsbaslinjen genom att följa dessa steg:

1. Klicka på panelen **Säkerhet och granskning** på huvudinstrumentpanelen för **Microsoft Operations Management Suite**.
2. Klicka på **Utvärdering av säkerhetsbaslinje** under **Säkerhetsdomäner** på instrumentpanelen för **Säkerhet och granskning**. Instrumentpanelen för **Utvärdering av säkerhetsbaslinje** visas, som du ser i följande bild:
   
    ![Utvärdering av säkerhetsbaslinjen i säkerhets- och granskningslösningen i OMS](./media/oms-security-baseline/oms-security-baseline-fig1.png)

Den här instrumentpanelen är uppdelat i tre huvudområden:

* **Datorer jämfört med baslinjen**: Det här avsnittet innehåller en sammanfattning av antalet datorer som kontrollerades och hur många datorer i procent som klarade utvärderingen. Översikten visar också de översta tio datorerna och utvärderingsresultatet i procent.
* **Status för obligatoriska regler**: Avsikten med det här avsnittet är att göra dig uppmärksam på regler som inte efterlevts baserat på allvarlighetsgrad och typ. Genom att titta på det första diagrammet kan du snabbt se om de flesta misslyckade regler är kritiska eller inte. Diagrammet visar också en lista över de översta tio misslyckade reglerna och deras allvarlighetsgrad. Det andra diagrammet visar vilken typ av regel som misslyckades under utvärderingen. 
* **Datorer som saknar basutvärdering**: Det här avsnittet innehåller en lista över de datorer som inte kontrollerades på grund av inkompatibilitet med eller fel relaterade till operativsystemet. 

### <a name="accessing-computers-compared-to-baseline"></a>Kontrollera datorer jämfört med baslinje
I bästa fall är alla dina datorer kompatibla vid utvärderingen av säkerhetsbaslinjen. I vissa fall kan det dock hända att så inte är fallet. Som en del av säkerhetshanteringsprocessen är det viktigt att granska datorer som inte klarade alla säkerhetsutvärderingstester. Ett snabbt sätt att se detta är genom att välja alternativet **Dator som öppnats** i avsnittet **Datorer jämfört med baslinje**. Du bör se listan med datorer i resultatet från loggsökningen, som du ser här:

![Resultat från Dator som öppnats](./media/oms-security-baseline/oms-security-baseline-fig2.png)

Sökresultatet visas i tabellformat, där den första kolumnen innehåller namnet på datorn och den andra kolumnen visar hur många regler som misslyckades. Du kan hämta information om vilken typ av regel som misslyckades genom att klicka på antalet misslyckade regler bredvid datornamnet. Du bör se ett resultat liknande det i följande bild:

![Resultatdetaljer från Dator som öppnats](./media/oms-security-baseline/oms-security-baseline-fig3.png)

I det här sökresultatet kan du se totalt antal regler som kontrollerades, antalet kritiska regler som misslyckades, samt varningsregler och informationsregler som misslyckades.

### <a name="accessing-required-rules-status"></a>Kontrollera statusen för obligatoriska regler
När du har fått information om antalet datorer (i procent) som klarade utvärderingen kanske du vill visa mer information om vilka regler som misslyckas utifrån deras allvarlighetsgrad. Den här visualiseringen hjälper dig att prioritera vilka datorer du bör fokusera på först så att de klarar nästa utvärdering. Hovra över Kritiskt i diagrammet på panelen **Regler som inte tillämpats per allvarlighetsgrad** under **Status för obligatoriska regler** och klicka. Du bör se ett resultat liknande följande skärm:

![Misslyckade regler efter allvarlighetsgrad](./media/oms-security-baseline/oms-security-baseline-fig4.png) 

I det här loggresultatet ser du vilken typ av baslinjeregel som misslyckades, beskrivningen av regeln och säkerhetsregelns CCE-ID (Common Configuration Enumeration). Dessa attribut bör räcka för att utföra en korrigerande åtgärd som åtgärdar problemet på måldatorn.

> [!NOTE]
> Mer information om CCE finns i [NVD-databasen (National Vulnerability Database)](https://nvd.nist.gov/cce/index.cfm).
> 
> 

### <a name="accessing-computers-missing-baseline-assessment"></a>Kontrollera datorer utan utvärdering av säkerhetsbaslinje
OMS stöder baslinjeprofilen för domänmedlemmar och domänkontrollanter i Windows Server 2008 R2 till och med Windows Server 2012 R2. Baslinjen för Windows Server 2016 är inte klar än och läggs till så fort den publiceras. Alla andra operativsystem som genomsöks med Utvärdering av säkerhetsbaslinje i säkerhets- och granskningslösningen i OMS visas i avsnittet **Datorer utan utvärdering av säkerhetsbaslinje**.

## <a name="see-also"></a>Se även
I det här dokumentet har du lärt dig om Utvärdering av säkerhetsbaslinje i säkerhets- och granskningslösningen i OMS. Mer information om säkerheten i OMS finns i följande artiklar:

* [Översikt över Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Övervaka och svara på säkerhetsaviseringar i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-responding-alerts.md)
* [Övervaka resurser i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-monitoring-resources.md)


