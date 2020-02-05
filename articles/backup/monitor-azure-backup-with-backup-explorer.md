---
title: Övervaka säkerhets kopieringar med Backup Explorer
description: En artikel som beskriver hur du använder backup Explorer för att utföra övervakning i real tid för säkerhets kopieringar i valv, prenumerationer, regioner och innehavare
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 331d8aeeb828dedb6700a94fafa074c179bef7ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992188"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Övervaka säkerhets kopieringar med Backup Explorer

När organisationer säkerhetskopierar fler och fler datorer till molnet blir det viktigt att ha en central plats för att Visa funktions information om säkerhets kopieringar på en stor egendom, för att effektivt övervaka säkerhets kopieringar.

Backup Explorer-arbetsboken är en inbyggd Azure Monitor arbets bok som gör det möjligt för säkerhets kopierings kunder att ha en enda fönster ruta för att utföra operativa övervaknings aktiviteter som relaterar till säkerhets kopiering över hela reserv utrymmet på Azure (som spänner över klienter, platser, prenumerationer, resurs grupper och valv), allt från en central plats. Det finns i stort sett följande funktioner:

* **I skalnings perspektiv** – en sammanställd vy av säkerhets kopierings objekt, jobb, aviseringar, principer och resurser som inte har kon figurer ATS för säkerhets kopiering över hela reserv fastigheten. 
* **Analys av detalj nivå** – du kan välja att få detaljerad information om varje entitet – jobb, aviseringar, principer och säkerhets kopierings objekt, allt från en enda plats.
* **Åtgärds bara gränssnitt** – när du har identifierat ett problem kan du välja att utföra åtgärder genom att navigera sömlöst till säkerhets kopierings objektet eller Azure-resursen.

Ovanstående funktioner tillhandahålls direkt av inbyggd integrering med Azure Resource Graph och Azure Monitor arbets böcker.

> [!NOTE]
> * Backup Explorer är för närvarande endast tillgängligt för Azure VM-data.
> * Backup Explorer är avsett att vara en drifts instrument panel för att visa information om dina säkerhets kopior under de senaste 7 dagarna (max).
> * För närvarande stöds inte anpassning av backup Explorer-mallen. Dessutom rekommenderar vi för närvarande inte att skriva anpassade automations i Azures resurs diagram data.

## <a name="getting-started"></a>Komma igång

Du kan komma åt backup Explorer genom att gå till något av dina Recovery Services-valv och klicka på länken **Backup Explorer** på sidan **Översikt** .

![Snabb länk för valvet](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

När du klickar på länken öppnas backup Explorer som innehåller en sammanställd vy över alla valv och prenumerationer som du har åtkomst till. Om du använder ett Azure Lighthouse-konto kan du visa data över alla klienter som du har åtkomst till (mer information finns i avsnittet nedan i vyer över flera innehavare).

![Utforskare, landnings sida](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Användnings fall för backup Explorer

Backup Explorer består av flera flikar, varje flik som tillhandahåller detaljerad information om en viss typ av säkerhets kopierings artefakt, till exempel säkerhets kopierings objekt, jobb, principer osv. Det här avsnittet innehåller en kort översikt över var och en av flikarna. Videor innehåller exempel på användnings fall för var och en av flikarna, tillsammans med en beskrivning av de olika kontrollerna som är tillgängliga för användaren.

### <a name="summary"></a>Sammanfattning

På fliken Sammanfattning får du en snabb överblick över det övergripande villkoret för din reserv egendom. Du kan visa information, till exempel antalet objekt som skyddas, antalet objekt för vilka skyddet inte har Aktiver ATS, hur många jobb som lyckades under de senaste 24 timmarna och så vidare. 

Var och en av de andra flikarna representerar en distinkt entitet – exempelvis säkerhets kopierings objekt, säkerhets kopierings jobb, säkerhets kopierings aviseringar och säkerhets kopierings principer Du kan också visa information om datorer där säkerhets kopiering inte har kon figurer ATS. Om du klickar på någon av dessa flikar visas information som är speciell för entiteten.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="backup-items"></a>Säkerhetskopiera objekt

Du kan visa var och en av dina säkerhets kopierings objekt som filtrerats efter prenumeration, valv och andra parametrar. Genom att klicka på namnet på ett säkerhets kopierings objekt kan du öppna Azure-bladet för det säkerhetskopierade objektet. Från tabellen kan du till exempel kontrol lera att den senaste säkerhets kopieringen misslyckades för objektet "X". Om du klickar på "X" öppnas säkerhets kopierings bladet för det här objektet, där du kan utlösa en säkerhets kopierings åtgärd på begäran.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="jobs"></a>Jobb

Du kan visa information om alla jobb som har utlösts under de senaste sju dagarna genom att gå till fliken jobb. Här kan du filtrera efter jobb åtgärd, jobb status och felkod (om det finns misslyckade jobb).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="alerts"></a>Aviseringar

Du kan visa information om alla aviseringar som har utlösts på dina valv under de senaste sju dagarna genom att klicka på fliken aviseringar. Här kan du filtrera poster efter typen av avisering (t. ex. säkerhets kopierings fel, återställnings fel), aviseringens aktuella status (till exempel aktiv eller löst) och allvarlighets graden för aviseringen (till exempel kritisk, varning, information). Du kan också navigera till den virtuella Azure-datorn genom att klicka på länken till den virtuella datorn och vidta nödvändiga åtgärder.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="policies"></a>Policy

Genom att klicka på fliken principer kan du visa viktig information om alla säkerhets kopierings principer som har skapats i reserv fastigheten. Du kan se hur många objekt som är associerade med varje princip, tillsammans med kvarhållningsintervallet och säkerhets kopierings frekvensen som anges av varje princip.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="backup-not-enabled"></a>Säkerhets kopiering är inte aktiverat

Det är viktigt för säkerhets kopierings administratören för en organisation att snabbt identifiera vilka datorer i organisationen som inte har säkerhets kopiering aktiverat än, så att säkerhets kopiering kan aktive ras för alla datorer som behöver skydd. Om du klickar på fliken **säkerhets kopiering inte aktiverat** får du hjälp i den här uppgiften.

På den här fliken visas en tabell som innehåller listan över objekt som inte är skyddade. Om din organisation följer metoden att tilldela olika taggar till produktions datorer och test datorer, eller datorer som betjänar olika funktioner, var och en av dem som kan behöva en separat säkerhets kopierings princip, kan filtrera efter Taggar hjälpa dig att visa information som är särskilt för en en viss klass av datorer. Om du klickar på ett objekt omdirigeras du till bladet **Konfigurera säkerhets kopia** för objektet, där du kan välja att säkerhetskopiera objektet med en lämplig säkerhets kopierings princip.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="exporting-to-excel"></a>Exportera till Excel

Om du klickar på nedåtpilen längst upp till höger i en widget (tabell/diagram) exporteras innehållet i widgeten som ett Excel-blad, precis som när befintliga filter tillämpas. Om du vill exportera fler rader i en tabell till Excel kan du öka antalet rader som visas på sidan med hjälp av list rutan **rader per sida** överst på varje flik.

## <a name="pinning-to-dashboard"></a>Fäst på instrument panelen

Du kan klicka på Fäst-ikonen längst upp i varje widget för att fästa widgeten på din Azure Portal-instrumentpanel. På så sätt kan du skapa anpassade instrument paneler som är skräddarsydda för att visa den viktigaste information som du behöver.

## <a name="cross-tenant-views"></a>Vyer mellan klienter

Om du är en Azure Lighthouse-användare med delegerad åtkomst till prenumerationer i flera klient miljöer kan du använda standard prenumerations filtret (genom att klicka på filter ikonen längst upp till höger i Azure Portal) för att välja alla prenumerationer du vill Se data för. Detta gör att backup Explorer kan samla in information om alla valv över dessa prenumerationer. Lär dig mer om Azure Lighthouse [här](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Efterföljande moment

[Lär dig hur du använder Azure Monitor för att få insikter om dina säkerhets kopierings data](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)