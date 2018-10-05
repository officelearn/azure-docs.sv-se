---
title: Uppdatera ett befintligt erbjudande för Azure Marketplace | Microsoft Docs
description: Beskriver hur du uppdaterar ett befintligt Azure Marketplace-erbjudande med partnerportalen i molnet.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: bfe2b0c70c8b912f6489ed461f5bcf6f233ed60d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811389"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Uppdatera ett befintligt erbjudande för Azure Marketplace
==============================================

Det finns olika typer av uppdateringar som du kan använda för ditt erbjudande när det har gått live.   Den [Cloud Partner Portal](https://cloudpartner.azure.com/) har flera funktioner som hjälper dig att redigera ett erbjudande för att säkerställa att ändringarna är korrekta, inklusive:

-  Lägger till ny version för avbildning av virtuell dator (VM) till en befintlig SKU
-  Ändra de regioner som en SKU finns i
-  Att lägga till nya SKU: er
-  Uppdatera metadata för marketplace för erbjudanden och SKU: er 
-  Uppdatera priser på användningsbaserad betalning ger
-  Jämför funktioner
-  Historik över funktioner

När du har ändrat ett erbjudande eller SKU måste du publicera den innan ändringarna Driftsätt.
Den här artikeln vägleder dig genom de olika delarna av uppdaterar VM-erbjudande.


<a name="unpermitted-changes-to-vm-offersku"></a>Unpermitted ändringar av erbjudande/SKU för virtuell dator
-----------------------------------

Det finns vissa attribut för ett erbjudande för virtuell dator eller en SKU som inte kan ändras när erbjudandet är live i Azure Marketplace.

-  Erbjudande-ID och Publicerings-ID för erbjudandet.
-  SKU-ID för befintliga SKU: er.
-  Antal befintliga SKU: er som datadisk.
-  Fakturerings-/ licensmodell ändras till befintliga SKU: er.


<a name="updating-the-vm-image-version-for-a-sku"></a>Uppdaterar versionsnumret för VM-avbildningen för en SKU
---------------------------------------

VM-avbildning för en SKU i ditt erbjudande kan ha uppdaterats med ytterligare funktioner, säkerhetsuppdateringar, osv. I sådana fall kanske du vill uppdatera VM-avbildning som refererar till din SKU. Använd följande steg för att uppdatera en VM-avbildning. 

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  Under **alla erbjudanden**, erbjudandet som du vill uppdatera.
3.  Under den **SKU: er** formuläret klickar du på den SKU som vars VM avbildning som du vill uppdatera.
4.  Under **Disk version**, klicka på **+ ny Disk Version** att lägga till en ny VM-avbildning.
5.  Ange de nya VM-avbildningarna **Disk version**. Disk-version måste följa den [sémantickou verzi](http://semver.org/) format. Versionerna bör vara i formatet X.Y.Z, där X, Y och Z är heltal. Kontrollera att den nya versionen som du anger är större än i tidigare versioner, annars det nya versionsnumret visas inte i Azure-portalen eller Azure marketplace på Publicera.
6.  För **OS VHD URL**, ange signatur för delad åtkomst (SAS) URI: N som skapats för det virtuella Hårddisken för operativsystemet. Observera att datadiskar inte kan ändra mellan olika versioner av SKU: N. Om tidigare versioner hade datadiskar som är konfigurerad, måste den nya versionen också ha de datadiskar som har konfigurerats.
7.  Klicka på **publicera** inledningsanförande publicera arbetsflödet att hämta din nya VM-versionen publiceras på Azure marketplace och Azure-portalen.


<a name="changing-regions-a-sku-is-available-in"></a>Ändra regioner finns en SKU finns i
--------------------------------------

Med tiden kan du göra ditt erbjudande/SKU tillgängligt i fler regioner.
Alternativt kan du stoppa stöder erbjudande/SKU i en viss region.
Följ stegen nedan för att implementera dessa ändringar.

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  Under **alla erbjudanden** erbjudandet som du vill uppdatera.
3.  Under den **SKU: er** klickar du på på SKU vars regiontillgänglighet som du vill uppdatera.
4.  Klicka på den **Välj länder** knappen den **Land/Region tillgänglighet** fält.
5.  I popup-regiontillgänglighet, Lägg till/ta bort de regioner som du vill använda för denna SKU.
6.  Klicka på **publicera** inledningsanförande publicera arbetsflödet att uppdatera din regiontillgänglighet för SKU: er.

Om en SKU som görs tillgänglig i en ny region, får du möjligheten att ange priser för en viss regionen via den **exportera priser Data** funktioner. Om du lägger till en region tillbaka som tidigare när tillgänglig innan kan du inte uppdatera dess priser eftersom ändringar i prissättning inte är tillåtna.


<a name="adding-a-new-sku"></a>Att lägga till en ny SKU
----------------

Så gör en ny SKU tillgängligt för ditt befintliga erbjudande i stegen nedan.

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  Under **alla erbjudanden** erbjudandet som du vill uppdatera.
3.  Under den **SKU: er** formuläret, clik på **lägga till nya SKU: N** och ger en **SKU ID** i popup-fönstret.
4.  Följ resten av stegen som beskrivs i [publicera en virtuell dator på Azure Marketplace](./cloud-partner-portal-publish-virtual-machine.md).
5.  Klicka på **publicera** inledningsanförande publicera arbetsflödet har ditt nya SKU: N som publiceras.


<a name="updating-offer-marketplace-metadata"></a>Uppdaterar erbjudande marketplace metadata.
------------------------------------

Du kan ha scenarier där du behöver uppdatera marketplace metadata som är associerade med ditt erbjudande som uppdatering av din logotyp, osv. Använd följande steg för att uppdatera metadata för ditt erbjudande.

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  Under **alla erbjudanden** erbjudandet som du vill uppdatera.
3.  Gå till den **Marketplace** formuläret och följ instruktionerna här för att göra några ändringar.
4.  Klicka på **publicera** inledningsanförande publicera arbetsflödet till att dina ändringar publiceras.


<a name="updating-pricing-on-published-offers"></a>Uppdaterar priser på publicerade erbjudanden
------------------------------------

När din betalning har publicerats, du kan inte öka priset för en befintlig SKU, men du kan skapa en ny SKU under samma erbjudandet, ta bort den gamla SKU: N och sedan publicera ditt erbjudande. Vi också kan du minska priset på redan publicerat erbjudanden. Att minska ditt erbjudande pris:

1.  Klicka på den SKU som du vill minska priser.
2.  Om du har angett baseras på 1 x 1 grafiska Användargränssnittet, kan du ändra priset direkt i Användargränssnittet. Om du ställer in priser via import/export-kalkylblad, kan du endast minska priserna via import/export-funktionen.
3.  Klicka på **Spara**.
4.  Publicera ditt erbjudande och Driftsätt.

Priserna blir synliga för nya kunder när den är helt aktiv på webbplatsen och alla nya kunder betalar det nya sämre priset.

För befintliga kunder återspeglas pris minskningen retroaktivt i början av den faktureringsperiod då priset minskningen trädde i kraft.
Om de redan har faktureras för cykeln då en minskning av priset inträffat, får de en återbetalning under deras nästa faktureringsperiod att täcka sämre priset.


<a name="simplified-currency-pricing"></a>Förenklad valuta priser
---------------------------

Från och med September 2018, ett nytt avsnitt kallas **förenklad valuta priser** skulle vara synliga för dig. Microsoft effektivisera verksamheten Azure Marketplace genom att aktivera mer förutsägbara priser och samlingar från dina kunder över hela världen. Den här effektivisera kommer att uppnås genom att minska antalet valutor där vi fakturerar dina kunder.

Nytt avsnitt tar priser i dessa nya valutor. När alla kunder har migrerats till dessa nya avräkning valutor, ursprungliga prissättningsavsnittet kommer att dras tillbaka och bara avsnittet ”förenklad priser för valuta” kommer att finnas kvar.

Du får förrän den 1 November 2018 att ange ett nytt pris för regionerna där avräkning valuta ändras. Du kommer inte att kunna öka priset för regioner där avräkning valuta inte ändras. Följande är de regioner där valutan ändras:

| Land/region                  | ISO2 kod | Förenklad faktureringsvalutan |
|--------------------------|-----------|-----------------------------|
| **Algeriet**              | DZ        | USD                         |
| **Argentina**            | KUNDRESKONTRA        | USD                         |
| **Bahrain**              | BH        | USD                         |
| **Vitryssland**              | GENOM ATT        | USD                         |
| **Brasilien**               | BR        | BRL                         |
| **Bulgarien**             | BG        | EUR                         |
| **Chile**                | CL        | USD                         |
| **Colombia**             | CO        | USD                         |
| **Costa Rica**           | CR        | USD                         |
| **Kroatien**              | Personal        | EUR                         |
| **Tjeckien**       | CZ        | EUR                         |
| **Egypten**                | T.EX.        | USD                         |
| **Guatemala**            | GT        | USD                         |
| **Hong Kong**            | HK        | USD                         |
| **Ungern**              | HU        | EUR                         |
| **Island**              | ÄR        | EUR                         |
| **Indonesien**            | ID        | USD                         |
| **Israel**               | IL        | USD                         |
| **Jordanien**               | JO        | USD                         |
| **Kazakstan**           | KZ        | USD                         |
| **Kenya**                | KE        | USD                         |
| **Kuwait**               | KW        | USD                         |
| **Liechtenstein**        | LI        | EUR                         |
| **Makedonien (FYRO)**     | MK        | USD                         |
| **Malaysia**             | MIN        | USD                         |
| **Mexico**               | MX        | USD                         |
| **Montenegro**           | MIG        | USD                         |
| **Marocko**              | MA        | USD                         |
| **Nigeria**              | NG        | USD                         |
| **Oman**                 | OM        | USD                         |
| **Pakistan**             | PK        | USD                         |
| **Paraguay**             | PY        | USD                         |
| **Peru**                 | PE        | USD                         |
| **Filippinerna**          | PH        | USD                         |
| **Polen\***             | PL        | EUR                         |
| **Qatar**                | KVALITETSKONTROLL        | USD                         |
| **Rumänien**              | RO        | EUR                         |
| **Saudiarabien**         | SA        | USD                         |
| **Serbien**               | RS        | USD                         |
| **Singapore**            | SG        | USD                         |
| **Sydafrika**         | ZA        | USD                         |
| **Thailand**             | TH        | USD                         |
| **Trinidad och Tobago**  | TT        | USD                         |
| **Tunisien**              | TN        | USD                         |
| **Turkiet**               | TR        | USD                         |
| **Ukraina**              | UA        | USD                         |
| **Förenade Arabemiraten** | AE        | USD                         |
| **Uruguay**              | UY        | USD                         |
|  |  |  |

> [!NOTE]
> Om du använder API: er för att publicera ditt erbjudande, kan du få se ett nytt avsnitt i erbjudandet med namnet **JSON**. Detta skulle kommenteras som `virtualMachinePricingV2` eller `monthlyPricingV2`, beroende på vilken typ av erbjudande.

Om du har några frågor om den här ändringen kan du kontakta supporten för Azure Marketplace.


<a name="compare-feature"></a>Jämför funktioner
---------------

När du gör ändringar på ett redan publicerat erbjudande, kan du använda den *jämför* funktionen för att granska de ändringar som har gjorts. Använda jämför:

1.  När som helst redigeringsprocessen, kan du klicka på den **jämför** knappen för ditt erbjudande.

2.  Visa sida-vid-sida-versioner av marknadsföring tillgångar och metadata.


<a name="history-of-publishing-actions"></a>Historik för publiceringen av åtgärder
-----------------------------

Om du vill visa alla historiska publiceringsaktivitet, klickar du på den **historik** fliken i det vänstra navigeringsfältet på partnerportalen i molnet. Här kommer du att kunna visa tidsstämplad åtgärder som har vidtagits under livslängden för dina Azure Marketplace-erbjudanden.
