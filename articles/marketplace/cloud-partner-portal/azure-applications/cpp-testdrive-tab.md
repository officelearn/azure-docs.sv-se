---
title: Testkörning för Azure Application-erbjudande | Azure Marketplace
description: Konfigurerar testkörning för Azure Application-erbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 2f35dd69c1f29350049ed8f62dd9cbb0e60a7718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289013"
---
# <a name="azure-applications-test-drive-tab"></a>Fliken Testkörning i Azure-program

Använd fliken Provkörning för att ge dina kunder en utvärderingsupplevelse.

## <a name="test-drive-benefits"></a>Fördelar med provkörning

Att skapa en testupplevelse för dina kunder är en bra idé för att säkerställa att de kan köpa med tillförsikt. Av de tillgängliga testversionsalternativen är Test Drive det mest effektiva på att generera högkvalitativa leads och ökad konvertering av dessa leads.

Det ger kunderna en praktisk, självstyrd testversion av produktens viktigaste funktioner och fördelar, vilket visas i ett verkligt implementeringsscenario.

## <a name="how-a-test-drive-works"></a>Så här fungerar en provkörning

En potentiell kund söker och upptäcker ditt program på Marketplace. Kunden loggar in och godkänner användarvillkoren. Nu får kunden din förkonfigurerade miljö att prova i ett fast antal timmar, medan du får ett högt kvalificerat lead att följa upp med. Mer information finns i [Vad är Provkörning?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Ställa in en provkörning

Använd följande steg för att aktivera och konfigurera en provkörning.

### <a name="to-enable-a-test-drive"></a>Så här aktiverar du en provkörning:

1. Välj fliken **Provkörning** under **Nytt erbjudande.**
2. Under **Provkörning**väljer du **Ja** för Aktivera **en provkörning**.

   ![Aktivera en provkörning](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Så här konfigurerar du en provkörning:

När du har aktiverat en provkörning fyller du i följande formulär för att konfigurera provkörningen:
  
 - Information
 - Teknisk konfiguration
 - Prenumerationsinformation för distribution av testkörning

Nästa skärminspelning visar alla testkörningsformulär. En asterisk (*) som läggs till i fältnamnet anger att det krävs. 

![Konfigurera en testenhet](./media/managed-app-configure-testdrive.png)

I följande tabell beskrivs de fält som krävs för att ställa in provkörningen för det hanterade programmet.  De fält som läggs till med en asterisk krävs.

|      Field         |  Beskrivning      |
|  ---------------   |  ---------------  |
| **Beskrivning\***  |  Beskriv vad som kan göras på din provkörning. Du kan använda grundläggande HTML-taggar för att formatera den här beskrivningen. Till exempel &lt;&gt;p &lt;&gt;, &lt;&gt;em &lt;&gt;, &lt;&gt;ul , li , ol och rubriker.                |
| **Användarhandbok\***  |  Ladda upp en bruksanvisning som dina kunder kan använda för att gå igenom testkörningsupplevelsen. Det här dokumentet måste vara en PDF-fil.    |
| **Demovideo för testkörning** |  En valfri videogenomgång på testenheten. En kund kan titta på den här videon innan de tar en provkörning. Ange en webbadress till videon på YouTube eller Vimeo. Om du väljer **+ Lägg till video**uppmanas du att ange följande information:<ul><li>Namn</li><li>URL</li><li>Miniatyr (i PNG-format, 533 x 324 pixlar)</li></ul>  |
| **Instanser\***      | Konfigurera hur många instanser du vill ha, i vilken eller vilka regioner och hur snabbt dina kunder kan få provkörningen. Mer information finns i [Så här publicerar du en provkörning](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Provkörningslängd (timmar)\*** | Ange ett heltal för antalet timmar. Det tillåtna intervallet är från 1 till 999. |
| **ARM-mall för provkörning\***     | Ladda upp en komprimerad (.zip)-fil som har dina Azure Resource Manager-mallar för din app. Mer information finns i [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Få tillgång till information\***          | Ange åtkomstinformation när kunden har fått provkörningen. Till exempel en URL för att komma åt provkörningen och signera information. . Du kan använda grundläggande HTML-taggar för att formatera den här beskrivningen. Till exempel &lt;&gt;p &lt;&gt;, &lt;&gt;em &lt;&gt;, &lt;&gt;ul , li , ol och rubriker. |
| **Azure-prenumerations-ID\***       | Detta ger åtkomst till Azure-tjänster och Azure-portalen. Prenumerationen är där resursanvändning rapporteras och tjänster faktureras. Om du inte redan har en separat Azure-prenumeration för testenheter skapar du en prenumeration.  |
| **Azure AD-klient-ID\***          | Ange en befintlig klient i Azure Active Directory eller skapa en klient för den här testenheten.  |
| **Azure AD-app-ID\***             | Skapa och registrera ett nytt program. Microsoft använder det här programmet för att utföra åtgärder på testkörningsinstansen.  |
| **Azure AD-appnyckel\***            | Skapa en autentiseringsnyckel för appen och klistra in den i det här fältet.   |
|  |  |

När du har ange all nödvändig information väljer du **Spara** för att slutföra inrättandet av provkörningen.


## <a name="next-steps"></a>Nästa steg

[fliken Marketplace](./cpp-marketplace-tab.md)
