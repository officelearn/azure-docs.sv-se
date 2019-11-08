---
title: Test enhet för Azure Applications erbjudande | Azure Marketplace
description: Så här konfigurerar du Test Drive för Azure Application erbjudande på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 3855c600fe35c37ac15783995551a769e00532be
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826126"
---
# <a name="azure-applications-test-drive-tab"></a>Fliken test enhet i Azure-program

Använd fliken testenhet för att få en utvärderings upplevelse för dina kunder.

## <a name="test-drive-benefits"></a>Testa enhets förmåner

Att skapa en utvärderings upplevelse för dina kunder är en bra idé att se till att de kan handla tryggare. Test enheten är mest effektiv vid generering av högkvalitativa leads och ökad konvertering av dessa leads.

Det ger kunder en praktisk utvärderings version av produktens viktiga funktioner och fördelar, som demonstreras i ett verkligt implementerings scenario.

## <a name="how-a-test-drive-works"></a>Hur en testen het fungerar

En potentiell kund söker efter och identifierar ditt program på Marketplace. Kunden loggar in och godkänner användnings villkoren. I det här läget får kunden en förkonfigurerad miljö för att försöka ett fast antal timmar, medan du får ett mycket kvalificerat lead att följa upp med. Mer information finns i [Vad är Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Konfigurera en testen het

Använd följande steg för att aktivera och konfigurera en testen het.

### <a name="to-enable-a-test-drive"></a>Så här aktiverar du en testen het:

1. Under **nytt erbjudande**väljer du fliken **test enhet** .
2. Under **Test Drive**väljer du **Ja** för **att aktivera en testenhet**.

   ![Aktivera en testen het](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Så här konfigurerar du en testen het:

När du har aktiverat en testenhet fyller du i följande formulär för att ställa in test enheten:
  
 - Information
 - Teknisk konfiguration
 - Information om distributions prenumeration för test enhet

I nästa skärm bild visas alla test enhets formulär. En asterisk (*) som läggs till i fält namnet visar att det är obligatoriskt. 

![Konfigurera en testen het](./media/managed-app-configure-testdrive.png)

I följande tabell beskrivs de fält som krävs för att ställa in test enheten för det hanterade programmet.  Fälten som läggs till med en asterisk måste anges.

|      Fält         |  Beskrivning      |
|  ---------------   |  ---------------  |
| **Beskrivning\***  |  Beskriv vad som kan göras på test enheten. Du kan använda grundläggande HTML-taggar för att formatera beskrivningen. Till exempel &lt;p&gt;&lt;EM&gt;, &lt;UL&gt;, &lt;Li&gt;, &lt;OL&gt;och rubrikerna.                |
| **Användar handbok\***  |  Ladda upp en Användar handbok som dina kunder kan använda för att gå igenom test enhets upplevelsen. Det här dokumentet måste vara en. pdf-fil.    |
| **Video om prov Drive demo** |  En valfri video genom gång av test enheten. En kund kan titta på den här videon innan de tar en test enhet. Ange en URL till videon på YouTube eller Vimeo. Om du väljer **+ Lägg till video**uppmanas du att ange följande information:<ul><li>Namn</li><li>URL</li><li>Miniatyr bild (i PNG-format, 533 x 324 bild punkter)</li></ul>  |
| **Instanser\***      | Konfigurera hur många instanser du vill, i vilken region (n) och hur snabbt dina kunder kan få test enheten. Mer information finns i [publicera en testen het](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Test enhetens varaktighet (timmar)\*** | Ange ett heltal för antalet timmar. Det tillåtna intervallet är från 1 till 999. |
| **Testa ARM-mall\***     | Ladda upp en komprimerad fil (zip) som innehåller dina Azure Resource Manager mallar för din app. Mer information finns i [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Åtkomst information\***          | Ge åtkomst information när din kund får test enheten. Till exempel en URL för att komma åt test enheten och signera information. . Du kan använda grundläggande HTML-taggar för att formatera beskrivningen. Till exempel &lt;p&gt;&lt;EM&gt;, &lt;UL&gt;, &lt;Li&gt;, &lt;OL&gt;och rubrikerna. |
| **Azure-prenumerations-ID\***       | Detta ger åtkomst till Azure-tjänster och Azure Portal. Prenumerationen är den plats där resursanvändningen rapporteras och tjänsterna faktureras. Om du inte redan har en separat Azure-prenumeration för test enheter skapar du en prenumeration.  |
| **Azure AD-klient-ID\***          | Ange en befintlig klient i Azure Active Directory eller skapa en klient för den här test enheten.  |
| **Azure AD App-ID\***             | Skapa och registrera ett nytt program. Microsoft använder det här programmet för att utföra åtgärder på din test enhets instans.  |
| **Azure AD App nyckel\***            | Skapa en autentiseringsnyckel för appen och klistra in den i det här fältet.   |
|  |  |

När du har angett all information som krävs väljer du **Spara** för att slutföra konfigurationen av test enheten.


## <a name="next-steps"></a>Nästa steg

[fliken Marketplace](./cpp-marketplace-tab.md)
