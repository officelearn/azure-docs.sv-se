---
title: Publicera ett anpassat marketplace-objekt i Azure Stack (molnoperator) | Microsoft Docs
description: Lär dig hur du publicerar ett anpassat marketplace-objekt i Azure Stack som en Azure Stack-operator.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: c16d8a282d489e7a2b5ee9908f52224aea6118d6
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713399"
---
# <a name="the-azure-stack-marketplace-overview"></a>Azure Stack Marketplace-översikt

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Marketplace är en samling tjänster, program och resurser som är anpassade för Azure Stack. Resurser omfattar nätverk, virtuella datorer, lagring och så vidare. Användare kom hit för att skapa nya resurser och distribuera nya program. Du kan se det som en perioder katalog där användare kan bläddra och välj de objekt som de vill använda. Om du vill använda ett Marketplace-objekt, måste användare prenumererar på ett erbjudande som ger åtkomst till objektet.

Som en Azure Stack-operatör du bestämma vilka objekt du lägger till (publicera) på Marketplace. Du kan publicera t.ex databaser, App Services och så vidare. Publicering gör dem synliga för alla användare. Du kan publicera anpassade objekt som du skapar. Du kan också publicera objekt från en växande [Azure Marketplace-objektlista](azure-stack-marketplace-azure-items.md). När du publicerar ett objekt på Marketplace kan kan användarna se dem inom fem minuter.

> [!Caution]  
> Alla artefakter för galleriet objektet kallas bilder och json-filerna är tillgängliga utan autentisering när du gör dem tillgängliga i Azure Stack marketplace. Läs mer om överväganden när du publicerar anpassade marketplace-objekt, [skapa och publicera ett Marketplace-objekt](azure-stack-create-and-publish-marketplace-item.md).

Om du vill öppna Marketplace i administrationskonsolen väljer **+ skapa en resurs**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace-objekt
Ett Azure Stack Marketplace-objekt är en tjänst, program eller resurs som användarna kan ladda ned och använda. Alla Azure Stack Marketplace-objekt är synliga för alla användare, inklusive administrativa objekt och planer och erbjudanden. De här objekten kräver inte en prenumeration för att visa, men är icke-funktionella till användare.

Varje Marketplace-objekt har:

* En Azure Resource Manager-mall för etablering
* Metadata, som strängar, ikoner och andra marknadsföringsrelaterade säkerheter
* Formatera informationen för att visa objektet i portalen

Alla objekt som publicerats på Marketplace använder Azure Gallery-paket (.azpkg)-formatet. Lägg till distribution eller runtime resurser (till exempel kod, zip-filer med programvara eller avbildningar av virtuella datorer) till Azure Stack separat, inte som en del av Marketplace-objekt. 

Med version 1803 och senare konverterar Azure Stack avbildningar till sparse-filer när de hämtar från Azure eller när du laddar upp anpassade avbildningar. Den här processen lägger till tid när du lägger till en bild, men sparar utrymme och snabbar upp distributionen av dessa avbildningar. Konverteringen gäller endast för nya avbildningar.  Befintlig avbildning ändras inte. 

## <a name="next-steps"></a>Nästa steg
[Hämta Marketplace-objekt](azure-stack-download-azure-marketplace-item.md)  
[Skapa och publicera ett Marketplace-objekt](azure-stack-create-and-publish-marketplace-item.md)

