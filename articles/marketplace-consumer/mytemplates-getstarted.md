---
title: "Kom igång med privata mallar | Microsoft Docs"
description: "Lägg till, hantera och dela dina privata mallar med hjälp av Azure-portalen, Azure CLI eller PowerShell."
services: marketplace-customer
documentationcenter: 
author: VybavaRamadoss
manager: asimm
editor: 
tags: marketplace, azure-resource-manager
keywords: 
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar
ms.translationtype: Human Translation
ms.sourcegitcommit: a9b48f149427e5ceb69bcaa97b1bf08519499b6f
ms.openlocfilehash: 01657619cbe579c6818a790cc3ab95a33936a565
ms.contentlocale: sv-se
ms.lasthandoff: 02/16/2017

---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Kom igång med privata mallar på Azure Portal
Mallen [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) är en deklarativ mall som används för att definiera distributionen. Du kan definiera vilka resurser som ska distribueras för en lösning och ange parametrar och variabler som gör att du kan ange värden för olika miljöer. Mallen består av JSON och uttryck som du kan använda för att skapa värden för din distribution.

Du kan använda den nya funktionen **Mallar** i [Azure Portal](https://portal.azure.com) tillsammans med resursprovidern **Microsoft.Gallery** som ett tillägg för [Azure Marketplace](https://azure.microsoft.com/marketplace/) för att låta användare skapa, hantera och distribuera privata mallar från personliga bibliotek.

Det här dokumentet vägleder dig genom att lägga till, hantera och dela en privat **Mall** med hjälp av Azure Portal.

## <a name="guidance"></a>Riktlinjer
Följande rekommendationer hjälper dig att dra full nytta av **Mallar** när du arbetar med dina lösningar.

* En **Mall** är en inkapslande resurs som innehåller en Resource Manager-mall och ytterligare metadata. Den fungerar väldigt likt ett objekt i Marketplace. Den viktigaste skillnaden är att det är ett privat objektet i stället för de offentliga Marketplace-objekten.
* Biblioteket för **Mallar** fungerar bra för användare som behöver anpassa sina distributioner.
* **Mallar** fungerar bra för användare som behöver en enkel lagringsplats i Azure.
* Börja med en befintlig Resource Manager-mall Hitta mallar i [github](https://github.com/Azure/azure-quickstart-templates) eller [Exportera mallen](../azure-resource-manager/resource-manager-export-template.md) från en befintlig resursgrupp.
* **Mallar** är knutna till den användare som publicerar dem. Utgivarens namn är synligt för alla som har läsbehörighet till den.
* **Mallar** är Resource Manager-resurser och det går inte att byta namn på dem efter att de publicerats.

## <a name="add-a-template-resource"></a>Lägg till en mallresurs
Det finns två sätt att skapa en **Mall**-resurs i Azure-portalen.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Metod 1: Skapa en ny mallresurs från en resursgrupp som körs
1. Navigera till en befintlig resursgrupp i Azure-portalen. Välj **Exportera mall** i **Inställningar**.
2. När Resource Manager-mallen för filserverresurser exporteras, kan du använda **Spara mall** för att spara den till **Mallar**-lagringsplatsen. Hitta komplett information för att Exportera mall [här](../azure-resource-manager/resource-manager-export-template.md).
   <br /><br />
   ![Exportera resursgrupp](media/rg-export-portal1.PNG)  <br />
3. Välj kommandoknappen **Spara till mall**.
   <br /><br />
4. Ange följande information:
   
   * Namn – namnet på mallobjektet (Obs: Detta är ett Azure Resource Manager-baserat namn. Alla namngivningsbegränsningar gäller och namn kan inte ändras efter att de skapats).
   * Beskrivning – snabb sammanfattning om mallen.
     
     ![Spara mall](media/save-template-portal1.PNG)  <br />
5. Klicka på **Spara**.
   
   > [!NOTE]
   > Bladet Exportera mall visar meddelanden när den exporterade Resource Manager-mallen innehåller fel, men du kommer fortfarande att kunna spara Resource Manager-mallen till Mallar. Se till att du kontrollerar och korrigerar eventuella problem med Resource Manager-mallen innan du omdistribuerar den exporterade Resource Manager-mallen.
   > 
   > 

### <a name="method-2--add-a-new-template-resource-from-browse"></a>Metod 2: Lägg till en ny Mall-resurs från bläddra
Du kan också lägga till en ny **Mall** direkt genom att använda kommandoknappen +Lägg till i **Bläddra > Mallar**. Du måste ange ett namn, en beskrivning och Resource Manager-mallens JSON.

![Lägg till mall](media/add-template-portal1.PNG)  <br />

> [!NOTE]
> Microsoft.Gallery är en klientbaserad Azure-resursprovider. Mallresursen är knuten till den användare som skapade den. Den är inte kopplad till någon specifik prenumeration. En prenumeration behöver endast väljas när du distribuerar en mall.
> 
> 

## <a name="view-template-resources"></a>Visa mallresurser
Alla tillgängliga **mallar** syns under **Bläddra > Mallar**. Detta omfattar de **mallar** du har skapat samt de som har delats med dig med olika behörighetsnivåer. Mer information finns i avsnittet [åtkomstkontroll](#access-control-for-a-tenant-resource-provider) nedan.

![Visa mall](media/view-template-portal1.PNG)  <br />

Du kan visa information om en **mall** genom att klicka på ett objekt i listan.

![Visa mall](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Redigera en mallresurs
Du kan börja att redigera flödet för en **mall** genom att högerklicka på objektet i listan eller genom att välja kommandoknappen Redigera.

![Redigera mall](media/edit-template-portal1a.PNG)  <br />

Du kan redigera beskrivningen eller Resource Manager-mallens text. Du kan redigera namnet eftersom det är ett resursnamn för Resource Manager. När du redigerar Resource Manager-mallens JSON kommer vi att verifiera att den är en giltig JSON. Välj **OK** och sedan **Spara** för att spara den uppdaterade mallen.

![Redigera mall](media/edit-template-portal2a.PNG)  <br />

Då **mallen** sparats visas ett meddelande om bekräftelse.

![Redigera mall](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Distribuera en mallresurs
Du kan distribuera alla **mallar** som du har **läs**behörigheter på. Distributionsflödet startar standard-Azure-mallens distributionsblad. Fyll i värden för Resource Manager-mallens parametrar för att fortsätta med distributionen.

![Distribuera mallen](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Dela en mallresurs
En **mall**resurs kan delas med kollegor. Delning fungerar på samma sätt som [rolltilldelning för en resurs på Azure](../active-directory/role-based-access-control-configure.md). Ägaren till **mallen** ger behörigheter till andra användare som kan interagera med en mallresurs. Personen eller gruppen av människor som du delar **mallen** med kommer att kunna se Resource Manager-mallen och dess egenskaper.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Åtkomstkontroll för Microsoft.Gallery-resurser
| Roll | Behörigheter |
| --- | --- |
| Ägare |Tillåter fullständig behörighet för mallresursen inklusive delning |
| Läsare |Tillåter Läs och Kör (Distribuera) på mallresursen |
| Deltagare |Tillåter Redigera och Ta bort på mallresursen. Användare kan inte dela mallen med andra |

Välj **Dela** för objektet genom att högerklicka eller på vybladet för ett specifikt objekt. Detta startar en delningsupplevelse.

![Dela mall](media/share-template-portal1a.png)  <br />

 Nu kan du välja en roll och en användare eller grupp för att ge åtkomst till en viss **mall**. Tillgängliga roller är Ägare, Läsare och Deltagare. Mer information finns i avsnittet [åtkomstkontroll](#access-control-for-a-tenant-resource-provider) ovan.

![Dela mall](media/share-template-portal2b.png)  <br />

![Dela mall](media/share-template-portal3b.png)  <br />

Klicka på **Välj** och **Ok**. Du kan nu se användare eller grupper som du lade till i resursen.

![Dela mall](media/share-template-portal4b.png)  <br />

> [!NOTE]
> En mall kan endast delas med användare och grupper i samma Azure Active Directory-klientorganisation. Om du delar en mall med en e-postadress som inte är i din klientorganisation, skickas en inbjudan som ber användaren att ansluta till klientorganisationen som en gäst.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Läs om hur du skapar Resource Manager-mallar i [Webbsidemallar](../azure-resource-manager/resource-group-authoring-templates.md)
* Information om de funktioner som du kan använda i en Resource Manager-mall finns i [Mallfunktioner](../azure-resource-manager/resource-group-template-functions.md)
* Anvisningar om hur du skapar mallar finns [Metodtips för att utforma Azure Resource Manager-mallar](../azure-resource-manager/best-practices-resource-manager-design-templates.md)


