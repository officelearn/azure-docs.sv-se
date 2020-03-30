---
title: Fliken Testenhet för virtuella datorer i Cloud Partner Portal för Azure Marketplace
description: Beskriver fliken Test Drive som används för att skapa ett Azure Marketplace VM-erbjudande.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: d4fc8762e25825b21637b16b751d57a0dcbf369e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288809"
---
# <a name="virtual-machine-test-drive-tab"></a>Fliken Testkörning för virtuella datorer

På fliken **Provkörning** på sidan **Nytt erbjudande** kan du förse dina potentiella kunder med en praktisk, självstyrd demonstration av produktens viktigaste funktioner och fördelar, vilket visas i ett standardiserat scenario.  Test drive är en valfri funktion för de erbjudandetyper som stöder Test Drive.  Provkörning kräver att stödtillgångar implementeras korrekt.  Mer information finns i artikeln [Azure Marketplace Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Om du vill aktivera den här funktionen klickar du på alternativet **Ja** på **Aktivera en provkörning**på fliken **Provkörning** .  På fliken **Provkörning** visas de fält som är tillgängliga för redigering.  En bifogad asterisk (*) i fältnamnet anger att det krävs.

![Fliken Provkörning i formuläret Nytt erbjudande för virtuella datorer](./media/publishvm_007.png)


## <a name="field-values"></a>Fältvärden

I följande tabell beskrivs syftet med och innehållet i dessa fält.  Obligatoriska fält åtalas av en asterisk (*).


|    Field                  |       Beskrivning                                                            |
|  ---------                |     ---------------                                                          |
|  *Detaljer*   |  |
| **Beskrivning\***           | Ge en översikt över testkörningsscenariot. Den här texten visas för användaren medan provkörningen etableras. Det här fältet stöder grundläggande HTML om du vill tillhandahålla formaterat innehåll.  |
| **Användarhandbok\***           | Ladda upp en detaljerad bruksanvisning (.pdf) som hjälper Test Drive-användare att förstå hur du använder din lösning.  |
| **Demovideo för testkörning** | Ladda upp en video som visar din lösning.  Om du väljer det här alternativet måste du ange ett namn, en webbadress till videon (som finns på YouTube eller Vimeo) och en (533x324 pixel) miniatyrbild för videon. |
| *Teknisk konfiguration* |  |
| **Instanser\***             | Ange regiontillgänglighet och relativt tillgänglighet för vm-instansen (klicka på informationsikonen för mer information).  <br/>Potentiella samtidiga Test Drive-sessioner bör inte överskrida kvotgränsen för din prenumeration.  Den förstnämnda beräknas som: [Antal markerade regioner] x [Heta instanser] + [Antal markerade regioner] x [Varma förekomster] + [Antal markerade regioner] x [Kalla förekomster] |
| **Varaktighet för provkörning\***   | Maximal sessionslängd i timmar. Testkörningssessionen avslutas automatiskt efter att den här tidsperioden har överskridits.  |
|**ARM-mall för provkörning\***| Ladda upp Azure Resource Manager-mallen som är associerad med den här testenheten. Mer information finns i [Omvandla distributionsmall för virtuell dator för testenhet](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Få tillgång till information\***    | Azure Resource Manager åtkomst och utvärdering inloggningsinformation, skriven som vanlig text eller enkel HTML. |
| *Prenumerationsinformation för distribution av testkörning* |  |
| **Azure-prenumerations-ID\*** | Kan erhållas genom att logga in på [Microsoft Azure-portalen](https://ms.portal.azure.com) och klicka på **Prenumerationer** i det vänstra menyfältet. (Exempel: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Den här identifieraren bör vara `a83645ac-1234-5ab6-6789-1h234g764ghty`ett GUID för formuläret .|
| **Azure AD-klient-ID\***    | Azure Active Directory-klient-ID.  Kan erhållas genom att logga in på [Microsoft Azure-portalen](https://ms.portal.azure.com) och klicka på **Azure Active Directory** på det vänstra menyfältet och sedan klicka på **Egenskaper** i mittenmenyraden och sedan kopiera **katalog-ID:t** från formuläret .  Den här identifieraren bör också vara en GUID.  Om det är tomt måste du skapa ett klient-ID för din organisation. |
| **Azure AD-app-ID\***       | Identifierare för din registrerade Azure VM-lösning  |
| **Azure AD-appnyckel\***      | Autentiseringsnyckel för din registrerade lösning |
|   |   |


## <a name="next-steps"></a>Nästa steg

På nästa [marketplace-flik](./cpp-marketplace-tab.md) tillhandahåller du marknadsföring och juridisk information om din lösning.
