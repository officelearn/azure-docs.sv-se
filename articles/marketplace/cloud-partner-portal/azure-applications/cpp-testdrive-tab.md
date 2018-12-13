---
title: Azure Application erbjudandet provkörning | Microsoft Docs
description: Så här konfigurerar du provkörning för erbjudande för Azure-program på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: de4c8aecefce334889c3fa6790c0ba42673896c7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197236"
---
# <a name="azure-applications-test-drive-tab"></a>Azure-program Test Drive-fliken

Använd fliken Test Drive för att tillhandahålla en utvärderingsmiljö för dina kunder.

## <a name="test-drive-benefits"></a>Test Drive-förmåner

Det är en bra idé att kontrollera att de kan köpa tryggt för att skapa en utvärderingsmiljö för dina kunder. För utvärderingsversionsalternativ som är tillgängliga är Test Drive de mest effektiva på att skapa högkvalitativa leads och ökad konvertering av dessa leads.

Tjänsten ger kunderna med en praktisk Migreringsprocess provversion av din produkt viktiga funktioner och fördelar, visas i ett scenario med verkliga implementering.

## <a name="how-a-test-drive-works"></a>Så här fungerar en provkörning

En potentiell kund söker och identifierar ditt program på Marketplace. Kunden loggar in och samtycker till att användningsvillkoren. Kunden får då din miljö som är förkonfigurerade för att prova i ett fast antal timmar, medan du får ett högt kvalificerade lead att följa upp med. Mer information finns i [vad är en provkörning?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Hur du konfigurerar en provkörning

Använd följande steg för att aktivera och konfigurera en testenhet.

### <a name="to-enable-a-test-drive"></a>För att aktivera en provkörning:

1. Under **nytt erbjudande**väljer den **Test Drive** fliken.
2. Under **Test Drive**väljer **Ja** för **aktivera en Test Drive**.

   ![Aktivera en provkörning](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Konfigurera en provkörning:

När du har aktiverat en testenhet ska du fylla i följande formulär du ställer in test drive:
  
 - Information
 - Teknisk konfiguration
 - Prenumerationsinformation för Test Drive-distribution

Nästa skärmdump visar alla Test Drive-formulär. En asterisk (*) läggs till fältnamnet anger att det krävs. 

![Konfigurera en provkörning](./media/managed-app-configure-testdrive.png)

I följande tabell beskrivs de fält som krävs för att ställa in provkörning för det hanterade programmet.

|    **Fält**       |  **Beskrivning**  |
|  ---------------   |  ---------------  |
|      Beskrivning              |   Beskriv vad du kan göra på din provkörning. Du kan använda grundläggande HTML-taggar för att formatera den här beskrivningen. Till exempel &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;, och rubriker.                |
|    Användarhandbok                |     Ladda upp en användarhandbok som dina kunder kan använda för att gå igenom Test Drive-upplevelse. Det här dokumentet måste vara en PDF-fil.              |
|         Test Drive demonstrationsvideon (valfritt)           |       Du kan ange en videogenomgång av din provkörning. En kund kan den här videon innan de Provkör. Ange en URL till videoklippet på YouTube eller Vimeo. Om du väljer **+ Lägg till Video**, uppmanas du att ange följande information:<ul><li>Namn</li><li>URL</li><li>Miniatyr (i PNG-format, 533 x 324 bildpunkter)</li></ul>            |
|       Instanser             |        Konfigurera hur många instanser som du vill, i vilken region, och hur snabbt dina kunder kan få Test Drive. Mer information finns i [hur du publicerar en Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
|       Driv varaktighet (timmar)             |       Ange ett heltal för antalet timmar. Det tillåtna intervallet är från 1 till 999.            |
|        Test Drive ARM-mall            |        Ladda upp en komprimerad fil (ZIP) med Azure Resource Manager-mallar för din app. Mer information finns i [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/azure-resource-manager-test-drive).            |
|        Åtkomstinformation            |         Ange åtkomstinformation när kunden får Test Drive. Till exempel en URL till åtkomst till test drive och logga information. . Du kan använda grundläggande HTML-taggar för att formatera den här beskrivningen. Till exempel &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;, och rubriker.          |
|       Åtkomst prenumerations-Id             |       Detta ger åtkomst till Azure-tjänster och Azure-portalen. Prenumerationen är där Resursanvändning rapporteras och tjänster faktureras. Om du inte redan har en separat Azure-prenumeration för Test Drives endast, skapa en prenumeration.             |
|          Azure AD-klient-Id          |        Ange en befintlig klient i Azure Active Directory eller skapa en klient för den här testkörningen.           |
|         Azure AD App-Id           |       Skapa och registrera ett nytt program. Microsoft använder det här programmet för att utföra åtgärder på din Test Drive-instans.            |
|          Azure AD App-nyckel          |         Skapa en autentiseringsnyckel för appen och klistra in den i det här fältet.          |

När du har angett all nödvändig information, Välj **spara** att slutföra konfigurationen av test drive.

## <a name="next-steps"></a>Nästa steg

[fliken Marketplace](./cpp-marketplace-tab.md)