---
title: Skapa en lösning för dataanalys av mellanlagrade data med Azure och Azure Stack | Microsoft Docs
description: Lär dig hur du skapar en lösning för dataanalys av mellanlagrade data med Azure och Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 1115e11d6bf830afad3746eb41d6368cb89bdbf3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534386"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>Självstudier: Skapa en lösning för dataanalys av mellanlagrade data med Azure och Azure Stack 

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Lär dig hur du använder både lokala och offentliga molnmiljöer för att uppfylla kraven från flera anläggning företag. Azure Stack är en snabb, säker och flexibel lösning för att samla in, bearbeta, lagra och distribuera lokal och fjärransluten data, särskilt om säkerhet, sekretess, företagsprinciper och regelkrav kan variera mellan platser och användare.

I det här mönstret kunderna samlar in data som måste du analysera vid samling så att snabba beslut kan göras. Insamling av det här inträffar ofta utan någon Internet-åtkomst. När anslutningen har upprättats kan behöva du göra en resurskrävande analys av data för att få mer information. Du kan fortfarande analysera data när ett offentligt moln är för sent eller saknas.

I den här självstudien skapar du en exempel-miljö för att:

> [!div class="checklist"]
> - Skapa lagringsblob rådata.
> - Skapa en ny Azure Stack-funktion för att flytta rensa data från Azure Stack till Azure.
> - Skapa en funktion för lagring som utlöses av Blob.
> - Skapa ett Azure Stack-lagringskonto som innehåller en blob och en kö.
> - Skapa en funktion som utlöses av lagringskön.
> - Funktion som utlöses av test kön.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack är en utökning av Azure. Azure Stack får du flexibilitet och utvecklingsmöjligheterna med molnberäkning i din lokala miljö och aktivera det enda hybridmolnet som hjälper dig att skapa och distribuera hybridappar var som helst.  
> 
> Faktabladet [designöverväganden för Hybridtillämpningar](https://aka.ms/hybrid-cloud-applications-pillars) granskar grundpelare för programkvalitet (placering, skalbarhet, tillgänglighet, återhämtning, hantering och säkerhet) för att utforma, distribuera och driva hybridprogram. Designöverväganden kan optimera hybrid programdesign, minimerar utmaningar i produktionsmiljöer.

## <a name="prerequisites"></a>Förutsättningar

Vissa förberedelser krävs för att skapa den här lösningen:

-   Ett installerat och fungerar Azure Stack (Mer information finns här: [Översikt över Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-storage-overview))

-   En Azure-prenumeration. (Skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   Hämta och installera [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

-   Du måste ange dina egna data som ska bearbetas av funktioner. Data måste vara skapas och vara tillgänglig att överföra till Azure Stack storage blob-behållare.

## <a name="issues-and-considerations"></a>Problem och överväganden

### <a name="scalability-considerations"></a>Skalbarhetsöverväganden

Azure functions och lagringslösningar skalas för att uppfylla datavolym och bearbetning av krav. Azures skalbarhetsinformation och mål kan du se [Azures skalbarhet dokumentation](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

### <a name="availability-considerations"></a>Överväganden för tillgänglighet

Lagring är den primära tillgänglighetsrepliken bör tänka på vid det här mönstret. Anslutning via snabba länkar krävs för bearbetning av stora mängder data och distribution.

### <a name="manageability-considerations"></a>Överväganden för hantering

Överväg hur din utvecklingsverktyg och källkontroll gör att du kan hantera din lösning.

## <a name="create-the-raw-data-storage-blob"></a>Skapa rådata storage blob

Storage-konto och blob behållare ska innehålla alla ursprungliga data som genereras av den lokala aktiviteter, inklusive dator-och medarbetare, anläggning data, produktion mått och andra reporting.

1.  Logga in på den [ *Azure Stack-portalen*](https://portal.local.azurestack.external/).

2.  I Azure Stack-portalen expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **alla tjänster**. Rulla ned till **Storage** och välj **lagringskonton**. I fönstret Storage-konton väljer **Lägg till**.

3.  Använd följande information för kontot:

    a.  Namn: **Ditt val**

    b.  Distributionsmodell: **Resource Manager**

    c.  Typ av konto: **Lagring (general-purpose V1)**

    d.  Plats: **USA, västra**

    e.  Replikering: **Lokalt redundant lagring (LRS)**

    f.  Prestanda: **Standard**

    g.  Säker överföring krävs: **Inaktiverad**

    h.  Prenumeration: Välj ett

    i.  Resursgrupp: Ange en ny resursgrupp eller välja en befintlig resursgrupp

    j.  Konfigurera virtuella nätverk: **Inaktiverad**

4.  Välj **skapa för att skapa lagringskontot**.

    ![Alternativ text](media/azure-stack-solution-staged-data-analytics/image1.png)

5.  Välj namnet på lagringskontot när du skapat.

6.  I bladet för kontot under den **BLOBTJÄNSTEN** väljer **behållare**.

7.  Längst ned på bladet välj **+ behållare.** Välj **behållare**.

    ![Alternativ text](media/azure-stack-solution-staged-data-analytics/image2.png)

8.  Namn: **Ditt val**

9.  Offentlig åtkomstnivå: **Behållaren** (anonym läsåtkomst för behållare och blobbar)

10.  Välj **OK**.

## <a name="create-an-azure-stack-function"></a>Skapa en Azure Stack-funktion

Skapa en ny Azure Stack-funktion för att flytta rensa data från Azure Stack till Azure.

### <a name="create-the-azure-stack-function-app"></a>Skapa Azure Stack-funktionsapp

1. Logga in på den [Azure Stack-portalen](https://portal.local.azurestack.external).
2. Välj **Alla tjänster**.
3. Välj **Funktionsappar** i den **webb + mobilt** grupp.

4.  Skapa funktionsappen med hjälp av inställningarna i tabellen nedanför bilden.

    | Inställning | Föreslaget värde | Beskrivning |
    | ---- | ---- | ---- |
    | Appnamn | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a` - `z`, `0``-9`, och `-`. |
    | Prenumeration | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **Resursgrupp** |  |  |
    | myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |  |
    | Operativsystem | Windows | Serverlösa värdtjänster är för närvarande endast tillgängliga om du kör Windows. |
    | **Värdplan** |  |  |
    | Förbrukningsplan | Värdplan som definierar hur resurser allokeras till din funktionsapp. I standard Standardförbrukningsplanen läggs resurser dynamiskt när de behövs i funktionerna. I den här serverlösa värdtjänster betalar du bara för den tid som dina funktioner körs. |  |
    | Plats | Region nära dig | Välj en region nära dig eller nära andra tjänster för din functions-åtkomst. |
    | **Lagringskonto** |  |  |
    | \<Storage-konto som skapades ovan > | Namnet på det nya lagringskonto som ska användas av funktionsappen. Lagringskontonamn måste vara mellan 3 och 24 tecken långt. Namnet kan bara använda siffror och gemener. Du kan också använda ett befintligt konto. |  |

    **Exempel:**

    ![Definiera nya funktionsappinställningar](media/azure-stack-solution-staged-data-analytics/image6.png)

5.  Välj **Skapa** för att etablera och distribuera funktionsappen.

6.  Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

    ![Definiera nya funktionsappinställningar](media/azure-stack-solution-staged-data-analytics/image7.png)

7.  Välj **gå till resurs** att visa nya funktionsappen.

![Funktionsappen skapades.](media/azure-stack-solution-staged-data-analytics/image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Lägga till en funktion i Azure Stack-funktionsappen

1.  Skapa en ny funktion genom att klicka på **Functions**, kommer **+ ny funktion** knappen.

    ![Alternativ text](media/azure-stack-solution-staged-data-analytics/image3.png)

2.  Välj **Timerutlösare**.

    ![Alternativ text](media/azure-stack-solution-staged-data-analytics/image4.png)

3.  Välj **C\#**  som språk och namnet funktionen: `upload-to-azure`  Ställ in schemat `0 0 * * * *`, som i CRON-notation är en gång i timmen.

    ![Alternativ text](media/azure-stack-solution-staged-data-analytics/image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>Skapa en funktion som utlöses av Blob Storage

1.  Expandera funktionsappen och välj den **+** bredvid knappen **Functions**.

2.  I sökfältet skriver `blob` och välj sedan önskat språk för den **Blob-utlösare** mall.

  ![Välj utlösarmallen för Blob Storage.](media/azure-stack-solution-staged-data-analytics/image10.png)

3.  Använd inställningarna som anges i tabellen nedan:

    | Inställning | Föreslaget värde | Beskrivning |
    | ------- | ------- | ------- |
    | Namn | Ett unikt namn i funktionsappen | Namnge funktionen som utlöses av blobben. |
    | Sökväg | \<sökväg från lagringsplatsen ovan > | Platsen i Blob Storage som övervakas. Filnamnet för bloben skickas i bindningen som Namnparametern. |
    | Anslutning till lagringskonto | Funktionen App-anslutning | Du kan använda den lagringskontoanslutning som redan används av funktionsappen eller skapa en ny. |

    **Exempel:**

    ![Skapa funktionen som utlöses av Blob Storage.](media/azure-stack-solution-staged-data-analytics/image11.png)

4.  Välj **skapa** skapa funktionen.

### <a name="test-the-function"></a>Testa funktionen

1.  I Azure-portalen bläddrar du till funktionen. Expandera den **loggar** längst ned på sidan och se till att loggströmningen inte är pausad.

2.  Öppna Storage Explorer och Anslut till det lagringskonto som skapats i början av det här avsnittet.

3.  Expandera lagringskontot **Blobbehållare**, och blobben som du skapade tidigare. Välj **överför** och sedan **ladda upp filer**.

    ![Ladda upp en fil till blobcontainern.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  I dialogrutan ladda upp filer väljer du fältet filer. Bläddra till en fil på en lokal dator, till exempel en bildfil, markera den och välj **öppna** och sedan **överför**.

5.  Gå tillbaka till funktionsloggarna och kontrollera bloben har lästs.

    **Exempel:**

    ![Visa meddelande i loggarna.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Skapa ett Azure Stack-lagringskonto

Skapa ett Azure Stack-lagringskonto som innehåller en blob och en kö.

### <a name="storage-blob--data-archiving"></a>Storage Blob dataarkivering

Det här lagringskontot kommer att innehålla två behållare. De här behållarna är en blob som används för att lagra Arkivera data och en kö som används för bearbetning av data som distribueras på huvudkontoret.

Använd stegen och inställningar som beskrivs ovan för att skapa ett annat konto- och blob lagringsbehållare som våra arkivlagring.

### <a name="storage-queue--filtered-data-holding"></a>Storage-kö filtrerade Data på vägen

1.  Använd stegen och inställningar som beskrivs ovan för att få åtkomst till det nya lagringskontot.

2.  Markera under Lagringskontoöversikten **kö.**

3.  Välj den **+ kö** och fyll **namn** fältet och fyll ett namn för den nya kön.

4.  Välj **OK.**

    ![Alternativ text](media/azure-stack-solution-staged-data-analytics/image14.png)

    ![Alternativ text](media/azure-stack-solution-staged-data-analytics/image15.png)

## <a name="create-a-queue-triggered-function"></a>Skapa en funktion som utlöses av lagringskön

1.  Använd stegen i avsnittet Skapa ovan funktion för att skapa en ytterligare Azure Stack-funktion med en kö-utlösare i stället för en blob-utlösare.

2.  Använd inställningarna som anges i tabellen nedan:

    | Inställning | Föreslaget värde | Beskrivning |
    | ------- | ------- | ------- |
    | Namn | Ett unikt namn i funktionsappen | Namnge funktionen som utlöses av kön. |
    | Sökväg | \<sökväg från lagringsplatsen ovan > | Plats i storage som övervakas. Namnet på kön skickas i bindningen som Namnparametern. |
    | Anslutning till lagringskonto | Funktionen App-anslutning | Du kan använda den lagringskontoanslutning som redan används av funktionsappen eller skapa en ny. |

3.  Välj **skapa** skapa funktionen.

## <a name="test-the-queue-triggered-function"></a>Funktion som utlöses av test kön

1.  I Azure Stack-portalen bläddrar du till funktionen. Expandera den **loggar** längst ned på sidan och se till att loggströmningen inte är pausad.

2.  Öppna Storage Explorer och Anslut till det lagringskonto som skapats i början av det här avsnittet.

3.  Expandera lagringskontot **Blobbehållare**, och blobben som du skapade tidigare. Välj **överför** och sedan **ladda upp filer.**

    ![Ladda upp en fil till blobcontainern.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  I dialogrutan ladda upp filer väljer du fältet filer. Bläddra till en fil på en lokal dator, till exempel en bildfil, markera den och välj **öppna** och sedan **överför**.

5.  Gå tillbaka till funktionsloggarna och kontrollera bloben har lästs.

  **Exempel:**

    ![Visa meddelande i loggarna.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>Säkert lagrade och används kompatibla data

Global företagsdata är på ett säkert sätt lagras, bearbetas, distribuerade och kan nås via Azure och Azure Stack mellanlagrade Data Analytics och anpassad slutpunkt direktiv. Fjärrkontor anställda och maskiner aktiviteter, anläggning-data och affärsmått kontinuerligt kompileras, lagras, testas för kompatibilitet, och distribuerad enligt företagets policy och regionala förordning.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure molnmönster i [Molndesignmönster](https://docs.microsoft.com/azure/architecture/patterns).