---
title: Virtuell dator Test Drive-fliken i Cloud Partner Portal för Azure Marketplace | Microsoft Docs
description: Beskriver fliken Test Drive används för att skapa ett erbjudande för Azure Marketplace-datorn.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 97fb21dc390bd365357f6395c72aa282423c83c9
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49640012"
---
# <a name="virtual-machine-test-drive-tab"></a>Virtuell dator Test Drive-fliken

<!-- TD: The AMP tree needs a conceptual/business overview of Test Drive. I've deleted all the marketing fluff and most of overview from this topic.   See also https://azure.microsoft.com/blog/azure-marketplace-test-drive/ and https://github.com/Azure/AzureTestDrive/wiki/What-is-a-Test-Drive. --> 

Den **Test Drive** fliken den **nytt erbjudande** sidan låter dig erbjuda potentiella kunder med en praktisk, Migreringsprocess demonstration av din produkt viktiga funktioner och fördelar, visas i en standardiserad scenario.  Test Drive är en valfri funktion för de typer av erbjudanden som har stöd för Test Drive.  Test Drive kräver stöd för tillgångar att korrekt kunna implementeras.  Mer information finns i artikeln [Azure Marketplace Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Aktivera den här funktionen på den **Test Drive** klickar du på den **Ja** alternativet på **aktivera en Test Drive**.  Den **Test Drive** fliken visas fält som är tillgängliga för redigering.  En tillagda asterisk (*) på fältnamnet indikerar att det krävs.

![Test Drive-fliken i formuläret nytt erbjudande för virtuella datorer](./media/publishvm_007.png)

<br/>

I följande tabell beskrivs syftet med och innehållet i de här fälten.


|  **Fält**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
|  *Detaljer*   |  |
| **Beskrivning**           | Ger en översikt över ditt Test Drive-scenario. Den här texten visas för användaren när Test Drive etableras. Det här fältet stöder grundläggande HTML om du vill ge formaterat innehåll.  |
| **Användarhandbok**           | Ladda upp en detaljerad användarhandboken (PDF) som hjälper dig Test Drive användarna att förstå hur du använder din lösning.  |
| **Test Drive demonstrationsvideon** | Ladda upp en video som visar din lösning.  Om du väljer det här alternativet måste du ange ett namn, URL till videoklippet (som finns på YouTube eller Vimeo) och en miniatyrbild (533 x 324 bildpunkt) för videon. |
| *Teknisk konfiguration* |  |
| **instanser**             | Ange regiontillgänglighet och relativt tillgängligheten för vm-instansen (klicka på informationsikonen för mer information).  <br/>Möjliga samtidiga Test Drive-sessioner får inte överstiga kvotgränsen för prenumerationen.  Tidigare beräknas som: [antalet av regioner valts] x [frekvent instanser] + [antalet av regioner valts] x [varma instanser] + [antalet av regioner valts] x [kalla instanser] |
| **Varaktigheten för tillgänglighetstestet-enhet**   | Längsta sessionsvaraktighet i timmar. Test Drive-sessionen avslutas automatiskt när den här tidsperioden har överskridits.  |
|**Test Drive ARM-mall**| Ladda upp Azure Resource Manager-mallen som är associerade med den här testkörningen. Mer information finns i [omvandla Distributionsmall för virtuell dator för Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Åtkomstinformation**    | Åtkomst till Azure Resource Manager och utvärderingsversion inloggningsinformation, skrivs som oformaterad text eller enkel HTML-kod. |
| *Prenumerationsinformation för Test Drive-distribution* |  |
| **Azure-prenumerations-Id** | Kan hämtas genom att logga in på [Microsoft Azure-portalen](https://ms.portal.azure.com) och klicka på **prenumerationer** på den vänstra menyraden. (Exempel: ”a83645ac-1234-5ab6-6789-1h234g764ghty”)    Den här identifieraren ska vara en GUID för formuläret `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **Azure AD-klient-Id**    | Azure Active Directory-klient-ID.  Kan hämtas genom att logga in på [Microsoft Azure-portalen](https://ms.portal.azure.com) och klicka på **Azure Active Directory** på den vänstra menyraden, sedan klicka på **egenskaper** i den mellersta menyraden Kopiera sedan den **katalog-ID** i formuläret.  Den här identifieraren bör också vara ett GUID.  Om inget anges, måste du skapa en klient-ID för din organisation. |
| **Azure AD App-Id**       | Identifierare för din registrerade Virtuella Azure-lösning  |
| **Azure AD App-nyckel**      | Autentiseringsnyckeln för din registrerade lösning |
|  |  |

<br/>

I nästa [Marketplace](./cpp-marketplace-tab.md) fliken du tillhandahåller marknadsföring och juridisk information om din lösning.
