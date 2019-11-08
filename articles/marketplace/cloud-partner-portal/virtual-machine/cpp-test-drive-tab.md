---
title: Fliken testenhet för virtuell dator i Cloud Partner Portal för Azure Marketplace
description: Beskriver fliken testenhet som används för att skapa ett erbjudande för virtuella Azure Marketplace-datorer.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 94ebbcca50916f1675ab990a4b45f3b90e069104
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808862"
---
# <a name="virtual-machine-test-drive-tab"></a>Fliken test enhet för virtuell dator

På fliken **testenhet** på sidan **ny erbjudande** kan du ge dina potentiella kunder en praktisk och guidad demonstration av produktens viktiga funktioner och fördelar, som demonstreras i ett standardiserat scenario.  Test Drive är en valfri funktion för de erbjudande typer som stöder Test Drive.  Test enheten kräver att stöd till gångar implementeras korrekt.  Mer information finns i artikeln [Azure Marketplace Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Om du vill aktivera den här funktionen klickar du på alternativet **Ja** på fliken **test enhet** och **aktiverar en testen het**.  Fliken **test enhet** visar de fält som är tillgängliga för redigering.  En asterisk (*) som lagts till i fält namnet anger att det är obligatoriskt.

![Fliken testenhet i det nya erbjudande formuläret för virtuella datorer](./media/publishvm_007.png)


## <a name="field-values"></a>Fält värden

I följande tabell beskrivs syftet med och innehållet i dessa fält.  Obligatoriska fält förutsägs av en asterisk (*).


|    Fält                  |       Beskrivning                                                            |
|  ---------                |     ---------------                                                          |
|  *Detaljer*   |  |
| **Beskrivning\***           | Ge en översikt över ditt test enhets scenario. Den här texten visas för användaren medan test enheten håller på att tillhandahållas. Det här fältet stöder grundläggande HTML om du vill tillhandahålla formaterat innehåll.  |
| **Användar handbok\***           | Ladda upp en detaljerad Användar handbok (. pdf) som hjälper dig att testa användarna förstå hur du använder din lösning.  |
| **Video om prov Drive demo** | Ladda upp en video som demonstrerar din lösning.  Om du väljer det här alternativet måste du ange ett namn, en URL för videon (som finns på YouTube eller Vimeo) och en (533x324 pixel) miniatyr av videon. |
| *Teknisk konfiguration* |  |
| **Instanser\***             | Ange regions tillgänglighet och relativt tillgänglighet för den virtuella dator instansen (klicka på informations ikonen för mer information).  <br/>Potentiella samtidiga test enhets sessioner bör inte överskrida kvot gränsen för din prenumeration.  Den tidigare beräknas som: [antal valda regioner] x [aktiva instanser] + [antal regioner valda] x [varma instanser] + [antal regioner som valts] x [kall instanser] |
| **Test enhetens varaktighet\***   | Längsta sessions-varaktighet i timmar. Test Drive-sessionen avslutas automatiskt när den här tids perioden har överskridits.  |
|**Testa ARM-mall\***| Ladda upp Azure Resource Manager-mallen som är associerad med den här test enheten. Mer information finns i [transformera distribution av mall för virtuella datorer för test enheten](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Åtkomst information\***    | Azure Resource Manager åtkomst och utvärdering av inloggnings information, skriven som oformaterad text eller enkel HTML. |
| *Information om distributions prenumeration för test enhet* |  |
| **Azure-prenumerations-ID\*** | Kan fås genom att logga in på [Microsoft Azure-portalen](https://ms.portal.azure.com) och klicka på **prenumerationer** på den vänstra meny raden. (Exempel: "a83645ac-1234-5AB6-6789-1h234g764ghty")    Den här identifieraren ska vara ett GUID för formuläret `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **Azure AD-klient-ID\***    | Azure Active Directory klient-ID.  Kan hämtas genom att logga in på [Microsoft Azure-portalen](https://ms.portal.azure.com) och klicka på **Azure Active Directory** på den vänstra meny raden och sedan på **Egenskaper** i den mittersta meny raden och sedan kopiera **katalog-ID: t** från formuläret.  Den här identifieraren bör också vara ett GUID.  Om det är tomt måste du skapa ett klient-ID för din organisation. |
| **Azure AD App-ID\***       | Identifierare för din registrerade Azure VM-lösning  |
| **Azure AD App nyckel\***      | Autentiseringsnyckel för din registrerade lösning |
|   |   |


## <a name="next-steps"></a>Nästa steg

På nästa [Marketplace](./cpp-marketplace-tab.md) -flik får du information om marknadsföring och juridisk information om din lösning.
