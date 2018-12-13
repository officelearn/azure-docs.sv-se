---
title: Azure SaaS-erbjudande test drive programkonfiguration | Microsoft Docs
description: Konfigurera provkörning för erbjudande för SaaS-program på Azure Marketplace.
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
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d6b72a994b034b0bbc735e5cca2ce987a02af61
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197473"
---
# <a name="saas-application-test-drive-tab"></a>SaaS-program Test Drive-fliken

Använd fliken Test Drive för att tillhandahålla en utvärderingsmiljö för dina kunder.

## <a name="test-drive-benefits"></a>Test Drive-förmåner

Det är en bra idé att kontrollera att de kan köpa tryggt för att skapa en utvärderingsmiljö för dina kunder. För utvärderingsversionsalternativ som är tillgängliga är Test Drive de mest effektiva på att skapa högkvalitativa leads och ökad konvertering av dessa leads.

Provkörning förser kunder med en praktisk Migreringsprocess provversion av din produkt viktiga funktioner och fördelar, visas i ett scenario med verkliga implementering.

## <a name="how-a-test-drive-works"></a>Så här fungerar en provkörning

En potentiell kund söker och identifierar ditt program på Marketplace. Kunden loggar in och samtycker till att användningsvillkoren. Kunden får då din miljö som är förkonfigurerade för att prova i ett fast antal timmar, medan du får ett högt kvalificerade lead att följa upp med. Mer information finns i [vad är en provkörning?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/what-is-test-drive)

## <a name="publishing-steps"></a>Publicera steg

De viktigaste publishing stegen för att lägga till ett test Drive är:

1. Definiera ditt Test Drive-scenario
2. Skapa och/eller ändra dina Resource Manager-mall
3. Skapa din provkörning stegvisa manuell
4. Publicera ditt erbjudande

## <a name="setting-up-a-test-drive"></a>Hur du konfigurerar en provkörning

Det finns fyra olika typer av Provkörningar, var och en baserat på vilken typ av produkt, scenario och du är på marketplace.
|  **Typ**          |  **Beskrivning**  |  **Konfigurationsanvisningar**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure Resource Manager               |    Testköra en Azure Resource Manager är en Distributionsmall som innehåller alla Azure-resurser som utgör en lösning som skapas av utgivaren. Produkter som passar den här typen av Test Drive är sådana som använder endast Azure-resurser.               |       [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/azure-resource-manager-test-drive)            |
|       Värdbaserad             |       En värd Test Drive tar bort komplexiteten i installationen av Microsoft hosting och underhålla den tjänst som utför Test Drive användaren etablering och borttagning.             |         [Värdbaserad Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/hosted-test-drive)          |
|      Logikapp              |       En Logic App Test Drive är en Distributionsmall som är avsedd att omfatta alla komplexa lösningsarkitekturer. Alla Dynamics-program eller anpassade produkter bör använda den här typen av Test Drive.            |      [Logic App-provkörning](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/logic-app-test-drive)             |
|       Power BI             |         Testa Power BI består av en inbäddad länk till en anpassade instrumentpanel. En produkt som du vill visa en interaktiva Power BI-objekt bör använda den här typen av Test Drive. Allt du behöver ladda upp är din inbäddade Power BI-URL.          |        [Power BI-provkörning](#Power-BI-test-drive)           |

### <a name="power-bi-test-drive"></a>Power BI-provkörning

Använd följande steg för att konfigurera en testenhet.

1. Välj under nytt erbjudande **Test Drive**.
2. Välj på Test Drive **Ja**.

   ![Aktivera provkörning](./media/saas-enable-test-drive.png)

   När du aktiverar en testenhet ser du den information och teknisk konfiguration formulär som visas i nästa skärmdump.

   ![Testa konfigurationsformuläret för enhet](./media/saas-test-drive-yes.png)

3. Under **information**, ange information för följande fält:
  
   - Beskrivning – beskriver din provkörning och vad användarna kan göra med den. Du kan använda grundläggande HTML-taggar för att formatera den här beskrivningen.
   - Användarhandbok – ladda upp ett dokument i användarhandboken som dina kunder kan använda när de arbetar test drive. Den här handboken måste vara en pdf-fil.
   - Test Drive Demo Video (valfritt) – du kan ange en video (YouTube eller Vimeo) för dina kunder att titta på innan de träder Test Drive. Ange en URL till videoklippet.

4. Under **teknisk konfiguration**, ange information för följande fält:

   - Typ av Test Drive – Välj **Power BI** från den nedrullningsbara listan.
   - Länka till Power BI-instrumentpanel – ange en länk till instrumentpanelen.

5. När du har konfigurerat test drive, Välj **spara**.


## <a name="next-steps"></a>Nästa steg

[Fliken storefront information](./cpp-storefront-tab.md)