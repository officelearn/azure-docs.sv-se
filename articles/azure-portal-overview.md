---
title: "Översikt över Azure Portal | Microsoft Docs"
description: "Lär dig hur du använder Microsoft Azure Portal."
services: 
documentationcenter: 
author: davidwrede
manager: erikre
editor: jimbe
ms.assetid: 53cb9df1-c96a-4f4e-b022-18336cd3d697
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 12/16/2015
ms.author: dwrede
ms.openlocfilehash: 45decfdec01b7086d1f9d18b31cf01cec1adb34d
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="microsoft-azure-portal-overview"></a>Översikt över Microsoft Azure Portal
Microsoft Azure Portal är en central plats där du kan etablera och hantera dina Azure-resurser.  I den här kursen får du bekanta dig med portalen och lära dig hur du kan använda några av dessa viktiga funktioner:

* En **omfattande marknadsplats** där du kan bläddra igenom tusentals objekt från Microsoft och andra leverantörer som du kan köpa eller etablera.
* En **enhetlig och skalbar navigeringsmiljö** som gör det enkelt att hitta de resurser som du är intresserad av och att utföra olika hanteringsåtgärder.
* **Enhetliga hanteringssidor** (eller blad) där du kan hantera Azures många tjänster med hjälp av enhetliga inställningar, åtgärder, faktureringsinformation, hälsoövervakning, användningsdata och mycket mer.
* En **personlig upplevelse** som låter dig skapa en anpassad startskärm som visar den information som du vill se varje gång du loggar in.  Du kan också anpassa hanteringsbladen som innehåller paneler.
  
  ![Hitta rätt i användargränssnittet på Azure Portal][UIOrientation]

## <a name="before-you-get-started"></a>Innan du börjar
Du behöver ett giltig Azure-prenumeration för att kunna följa med i den här kursen.  Om du inte har någon kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).  När du har skaffat en prenumeration kan du få åtkomst till portalen på <https://portal.azure.com>.

## <a name="how-to-create-a-resource"></a>Skapa en resurs
Azure har en marknadsplats med tusentals objekt som du kan skapa från en och samma plats.  Anta att du vill skapa en ny virtuell dator med Windows Server 2012.  +NYTT-navet är ingången till en granskad uppsättning kategorier från marknadsplatsen.  Varje kategori har en liten uppsättning objekt samt en länk till den fullständiga marknadsplatsen där du hittar alla kategorier och en sökfunktion. Så här skapar du den nya virtuella datorn med Windows Server 2012:  

1. Windows Server 2012 visas på en utmärkande plats och du hittar det snabbt i kategorin Compute.  
2. Fyll i grundläggande information i ett formulär.
3. Klicka på Skapa. Den virtuella datorn börjar etableras direkt.

Meddelandenavet meddelar dig när resursen har skapats och ett hanteringsblad öppnas (du kan alltid bläddra till resurser senare).

![Portalkategorier][PortalCategories]

## <a name="how-to-find-your-resources"></a>Söka efter resurser
Du kan fästa resurser som du ofta använder på startsidan, men ibland kanske du behöver bläddra till något som du inte använder så ofta.  Du kan komma åt alla dina resurser via bläddringsnavet nedan.  Du kan filtrera efter prenumeration, välja/ändra storlek på kolumner och gå till hanteringsbladen genom att klicka på enskilda objekt.

![Bläddringsnavet][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Hantera och delegera åtkomst till en resurs
Från det här bladet kan du ansluta till de virtuella datorerna via Fjärrskrivbord, övervaka prestandarelaterade nyckeltal, kontrollera åtkomsten till den virtuella datorn med hjälp av rollbaserad åtkomst, konfigurera den virtuella datorn och utföra andra viktiga hanteringsåtgärder.  Det är fundamentalt att kunna delegera åtkomst baserat på roller vid hantering i större skala.  Klicka [här](active-directory/role-based-access-control-configure.md) om du vill veta mer. Så här delegerar du åtkomst till en resurs:

1. Bläddra till resursen.
2. Klicka på Alla inställningar i avsnittet Essentials.
3. Klicka på Användare i listan med inställningar.
4. Klicka på Lägg till i kommandofältet.
5. Välj en användare och en roll.

![Hantera en resurs][ManageResource]

## <a name="how-to-get-help"></a>Få hjälp
Om du får problem finns vi här!  Portalen innehåller en hjälp- och supportsida där du kan få hjälp.  Beroende på din [supportplan](https://azure.microsoft.com/support/plans/) kan du också skapa supportärenden direkt på portalen.  När du har skapat ett supportärende kan du hantera ärendets livscykel på portalen. Du kommer till hjälp- och supportsidan genom att gå till Bläddra -> Hjälp + support.  

![Hjälp och support][HelpSupport]

## <a name="summary"></a>Sammanfattning
Nu ska vi se vad du lärt dig i den här självstudiekursen:

* Du har lärt dig hur du registrerar dig, skaffar en prenumeration och bläddrar till portalen
* Du har lärt dig hur du hittar rätt i portalens användargränssnitt och hur du skapar och hittar resurser
* Du har lärt dig hur du skapar en resurs och hur du bläddrar bland resurser
* Du har lärt dig om strukturen eller hanteringsbladen och hur du kan hantera olika typer av resurser på ett enhetligt sätt
* Du har lärt dig hur du anpassar portalen så att den information som du är intresserad av visas överst
* Du har lärt dig hur du styr åtkomsten till resurser med hjälp av rollbaserad åtkomst (RBAC)
* Du har lärt dig hur du kan få hjälp och support

Microsoft Azure Portal gör det betydligt enklare att bygga och hantera dina program i molnet.  Håll dig uppdaterad med [hanteringsbloggen](https://azure.microsoft.com/blog/topics/management/). Vi [lyssnar alltid på feedback](https://feedback.azure.com/forums/223579-azure-preview-portal/) och gör ständigt förbättringar.  [ScottGus blogg](http://weblogs.asp.net/scottgu) är ett annat bra ställe med information om alla Azure-uppdateringar.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
